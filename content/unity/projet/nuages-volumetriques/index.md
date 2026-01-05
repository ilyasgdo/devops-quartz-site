---
title: "Rapport Nuages Volumétriques"
description: "Rapport technique expert - Rendu volumétrique temps réel, Ray Marching, Shaders HLSL/GLSL, Graphics Programming"
publish: true
tags:
  - unity
  - volumetric-clouds
  - ray-marching
  - shaders
  - real-time-rendering
  - graphics-programming
  - hlsl
keywords:
  - Ray Marching Algorithm
  - Volumetric Rendering
  - Physically Based Rendering
  - Shader Programming
  - GPU Computing
  - Real-time Graphics
---

<!-- 
Expert Technical Report - Volumetric Cloud Rendering
Keywords: Ray Marching, Volumetric Rendering, Noise Functions, Perlin Noise,
Worley Noise, Fractal Brownian Motion (fBM), Density Functions,
Beer-Lambert Law, Light Scattering, Mie Scattering, Rayleigh Scattering,
Compute Shaders, Fragment Shaders, HLSL, GLSL, Shader Graph,
Render Pipeline, Scriptable Render Pipeline (SRP), Universal Render Pipeline (URP),
Post-Processing Effects, Temporal Anti-Aliasing (TAA), Depth Buffer,
Screen Space Effects, 3D Textures, Noise Textures, LOD (Level of Detail),
Performance Optimization, GPU Profiling, Frame Rate Optimization
-->

# Rapport NuagesVolumetriques

# Rapport Technique : Système de Nuages Volumétriques

**Projet** : Operation Dragoon - Beneath the Sand

**Date** : 7 Décembre 2025

**Auteurs** : Ilyas GHANDAOUI & Cyprien BOSCHER - E4FI

**Enseignant** : Badr TAJINI

**Dépôt git** : [https://github.com/ilyasgdo/procedural-generation-and---volumetric-cloud/tree/main/ARessourcesPlaneScene](https://github.com/ilyasgdo/procedural-generation-and---volumetric-cloud/tree/main/ARessourcesPlaneScene)
**Video** : [https://www.youtube.com/watch?v=IKLIivcP06g](https://www.youtube.com/watch?v=IKLIivcP06g)

---

## Introduction

Ce rapport détaille l’implémentation technique d’un système de **rendu de nuages volumétriques en temps réel** pour Unity Universal Render Pipeline. L’approche combine des techniques de graphisme avancées issues de l’industrie du jeu vidéo AAA pour produire des nuages photoréalistes à haute performance.

---

## 1. Architecture Globale du Système

Le système repose sur **trois composants principaux** :
- **VolumetricCloudsRendererFeature.cs** : Intégration dans le pipeline URP
- **VolumetricClouds.shader** : Shader haute qualité avec Worley Noise
- **VolumetricCloudsFullscreen.shader** : Version optimisée fullscreen

![1.png](/static/unity/nuages-volumetriques/1.png)

---

## 2. Ray Marching Volumétrique

### Principe Fondamental

Le **Ray Marching** est une technique de rendu qui simule le comportement de la lumière dans un milieu participatif (nuages, brouillard). Contrairement à la rasterisation classique, on traverse le volume pixel par pixel.

![2.png](/static/unity/nuages-volumetriques/2.png)

### Paramètres Clés

| Paramètre | Plage | Impact |
| --- | --- | --- |
| **Max Steps** | 32-256 | Qualité vs Performance |
| **Step Size** | 8-15 unités | Précision vs Vitesse |
| **Max Distance** | 2000-10000 | Portée de rendu |

### Mécanisme d’Accumulation

![3.png](/static/unity/nuages-volumetriques/3.png)

---

## 3. Système de Bruit Procédural Multi-Échelles

### Architecture du Bruit

![4.png](/static/unity/nuages-volumetriques/4.png)

### 3.1 Fonctions de Hachage (Hash Functions)

Les fonctions de hachage transforment des coordonnées 3D en valeurs pseudo-aléatoires reproductibles.

**Technique utilisée** : Multiplication par nombres premiers + fonction sinus + grande constante fractionnaire

![5.png](/static/unity/nuages-volumetriques/5.png)

| Fonction | Entrée | Sortie | Usage |
| --- | --- | --- | --- |
| **hash31** | float3 | float | Bruit de base |
| **hash33** | float3 | float3 | Worley noise |

---

### 3.2 Value Noise 3D avec Interpolation Trilinéaire

Le Value Noise génère du bruit continu en interpolant des valeurs aléatoires aux coins d’un cube.

![6.png](/static/unity/nuages-volumetriques/6.png)

**Smoothstep** : Fonction de lissage polynomiale qui élimine les discontinuités visibles
- Formule : `f(t) = 3t² - 2t³`
- Propriété : Dérivée nulle aux extrémités → transitions douces

---

### 3.3 Fractal Brownian Motion (FBM)

Le FBM superpose plusieurs octaves de bruit à différentes fréquences pour créer des détails multi-échelles.

![7.png](/static/unity/nuages-volumetriques/7.png)

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **Lacunarité** | 2.0 | Facteur de multiplication de fréquence |
| **Persistance** | 0.5 | Facteur de réduction d’amplitude |
| **Octaves** | 3-6 | Nombre de couches de détail |

---

### 3.4 Worley Noise (Bruit Cellulaire)

Le Worley Noise génère des motifs cellulaires utilisés pour les bords “mousseux” des nuages.

![8.png](/static/unity/nuages-volumetriques/8.png)

**Caractéristiques** :
- Génère des patterns organiques de type cellulaire
- L’inversion (1 - distance) crée des zones de haute densité aux centres des cellules
- Contribution de 15% au bruit final pour les textures de bord

---

## 4. Fonction de Densité Implicite (SDF-Like)

### Modélisation de la Couche Nuageuse

![9.png](/static/unity/nuages-volumetriques/9.png)

### Gradient de Hauteur Parabolique

Le gradient `4h(1-h)` crée une distribution de densité maximale au centre de la couche :

![10.png](/static/unity/nuages-volumetriques/10.png)

### Seuil de Couverture (Coverage Threshold)

Le seuil de couverture crée des “trous” dans les nuages en éliminant les valeurs de bruit faibles :

| Bruit | Coverage (0.45) | Densité résultante |
| --- | --- | --- |
| 0.3 | < 0.45 | 0 (pas de nuage) |
| 0.6 | > 0.45 | ~0.27 (nuage léger) |
| 0.9 | > 0.45 | ~0.82 (nuage dense) |

---

## 5. Modèle d’Éclairage Physique

### 5.1 Loi de Beer-Lambert

Modèle fondamental de l’absorption lumineuse dans les milieux participatifs.

**Équation** : `Transmittance = exp(-absorption × densité × distance)`

![11.png](/static/unity/nuages-volumetriques/11.png)

### 5.2 Échantillonnage de Lumière Secondaire

Pour chaque point du ray marching, un second ray marching vers la lumière calcule l’ombre propre (self-shadowing).

![12.png](/static/unity/nuages-volumetriques/12.png)

### 5.3 Composition Couleur Finale

![13.png](/static/unity/nuages-volumetriques/13.png)

---

## 6. Intersection Rayon-Volume

### 6.1 Algorithme Ray-Box (Slab Method)

Le shader haute qualité utilise l’intersection rayon-boîte AABB (Axis-Aligned Bounding Box) :

![14.png](/static/unity/nuages-volumetriques/14.png)

### 6.2 Intersection Ray-Plane (Version Fullscreen)

La version optimisée utilise une intersection plan horizontal simple :

![15.png](/static/unity/nuages-volumetriques/15.png)

---

## 7. Techniques d’Anti-Aliasing et Optimisation

### 7.1 Jitter Temporel (Dithering Stochastique)

Technique pour éliminer le banding visible causé par l’échantillonnage discret.

![16.png](/static/unity/nuages-volumetriques/16.png)

**Avantage** : Qualité perçue équivalente à 2× plus d’échantillons sans coût supplémentaire.

### 7.2 Early Termination

Sortie anticipée du ray marching quand le résultat est déterminé :

![17.png](/static/unity/nuages-volumetriques/17.png)

### 7.3 Intégration avec le Depth Buffer

Les nuages respectent la géométrie de la scène via le depth buffer :

![18.png](/static/unity/nuages-volumetriques/18.png)

---

## 8. Animation Procédurale

### Système de Vent

![19.png](/static/unity/nuages-volumetriques/19.png)

| Axe | Direction par défaut | Effet |
| --- | --- | --- |
| X | 1.0 | Mouvement principal Est-Ouest |
| Y | 0.0 | Pas de dérive verticale |
| Z | 0.5 | Mouvement secondaire Nord-Sud |

**Caractéristique clé** : Le bruit est échantillonné à la position animée, mais le gradient de hauteur reste fixe → nuages se déplacent horizontalement sans “monter”.

---

## 9. Intégration Pipeline URP

### Architecture Renderer Feature

![20.png](/static/unity/nuages-volumetriques/20.png)

### Double Blit Pattern

![21.png](/static/unity/nuages-volumetriques/21.png)

---

## 10. Tableau Comparatif des Deux Shaders

![22.png](/static/unity/nuages-volumetriques/22.png)

| Critère | Shader Géométrique | Shader Fullscreen |
| --- | --- | --- |
| **Worley Noise** | ✅ 3 boucles imbriquées | ❌ Non inclus |
| **Intersection** | Ray-Box AABB | Ray-Plane horizontal |
| **Light Samples** | 6 échantillons | 1 échantillon |
| **FBM Octaves** | Configurable 1-6 | Fixe à 4 |
| **Usage** | Qualité maximale | Vitesse optimale |
| **Cas d’usage** | Cinématiques, vues proches | Gameplay, grandes distances |

---

## 11. Synthèse des Techniques Avancées

![23.png](/static/unity/nuages-volumetriques/23.png)

---

## Conclusion

Cette implémentation de nuages volumétriques combine **12 techniques avancées** de rendu graphique :

1. **Ray Marching** avec accumulation de transmittance
2. **Fonctions de Hachage** pseudo-aléatoires 3D
3. **Value Noise 3D** avec interpolation trilinéaire
4. **Smoothstep** pour transitions continues
5. **Fractal Brownian Motion** multi-octaves
6. **Worley Noise** pour textures cellulaires
7. **Loi de Beer-Lambert** pour absorption physique
8. **Secondary Ray Marching** pour self-shadowing
9. **Intersection Ray-Box/Ray-Plane**
10. **Jitter Stochastique** anti-aliasing
11. **Early Termination** pour optimisation
12. **Intégration URP** via ScriptableRendererFeature

Le système offre un équilibre entre qualité visuelle photoréaliste et performance temps réel adaptée au jeu vidéo.