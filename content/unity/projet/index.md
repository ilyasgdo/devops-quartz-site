---
title: "Projet Unity - Génération Procédurale & Nuages"
description: "Rapport technique expert - Projet Unity combinant génération de terrain procédural et rendu de nuages volumétriques"
publish: true
tags:
  - unity
  - projet
  - procedural-generation
  - volumetric-clouds
keywords:
  - Real-time Rendering
  - Shader Programming
  - GPU Computing
  - Advanced Graphics
---

<!-- 
Expert Technical Report - Unity Project
Keywords: Unity3D, C# Programming, Shader Development, HLSL,
Real-time Graphics, Game Engine Architecture, Render Pipeline,
Procedural Content Generation, Volumetric Effects
-->

# Projet Unity - Génération Procédurale & Rendu Volumétrique

> Projet Pluridisciplinaire - ESIEE Paris 2025
> 
> 👤 **Auteurs**: Ilyas GHANDAOUI, Cyprien BOSCHER - E4FI
> 
> 📅 **Date**: Décembre 2025

## Description du Projet

Ce projet combine deux techniques graphiques avancées dans Unity :

1. **Génération de Terrain Procédural** - Création de terrains infinis avec LOD dynamique
2. **Nuages Volumétriques** - Rendu temps réel avec ray marching

## Rapports Techniques

### [Rapport Nuages Volumétriques](./nuages-volumetriques)
Implémentation de nuages volumétriques en temps réel avec :
- Ray Marching
- Noise 3D (Perlin, Worley)
- Light Scattering (Beer-Lambert, Mie)
- Optimisations GPU

### [Rapport Génération de Terrain Procédural](./terrain-procedural)
Génération de terrain procédural infini avec :
- Bruit de Perlin multi-octaves
- Level of Detail (LOD) dynamique
- Chunk-based streaming
- Mesh optimization

### [Rapport Scène Débarquement](./scene-debarquement)
Architecture de rendu et simulation avancée pour la scène 1B :
- Render Graph & Optimisation Forward+
- Simulation de Tempête de Sable (GPU-Driven)
- Hydrodynamique (Gerstner Waves & Shore Masking)

## Ressources

- **Dépôt Git** : [procedural-generation-and-volumetric-cloud](https://github.com/ilyasgdo/procedural-generation-and---volumetric-cloud)
- **Vidéo démo** : [YouTube](https://www.youtube.com/watch?v=IKLIivcP06g)
