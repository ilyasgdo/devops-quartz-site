---
title: "Rapport Génération de Terrain Procédural"
description: "Rapport technique expert - Génération procédurale, Algorithmes de bruit, LOD dynamique, Mesh optimization"
publish: true
tags:
  - unity
  - procedural-generation
  - perlin-noise
  - terrain
  - mesh-generation
  - level-of-detail
  - compute-shaders
keywords:
  - Procedural Content Generation
  - Noise Algorithms
  - Mesh Optimization
  - GPU Tessellation
  - Infinite Terrain
  - Chunk-based Loading
---

<!-- 
Expert Technical Report - Procedural Terrain Generation
Keywords: Procedural Generation, Perlin Noise, Simplex Noise, Worley Noise,
Fractal Brownian Motion (fBM), Octaves, Lacunarity, Persistence,
Height Maps, Normal Maps, Slope-based Texturing, Triplanar Mapping,
Level of Detail (LOD), Quadtree, Chunked Terrain, Terrain Streaming,
Mesh Generation, Vertex Buffers, Index Buffers, GPU Instancing,
Compute Shaders, Tessellation, Displacement Mapping, Parallax Occlusion,
Threading, Job System, Burst Compiler, SIMD, Cache Optimization,
Biome Generation, Erosion Simulation, Hydraulic Erosion, Thermal Erosion
-->

# Rapport Génération de Terrain Procedural

# Rapport Technique : Système de Terrain Procédural Infini

**Projet** : Operation Dragoon - Beneath the Sand

**Date** :  9 Décembre 2025

**Auteur** : Ilyas Ghandaoui

**Depo git des scripts** :  [https://github.com/ilyasgdo/procedural-generation-and---volumetric-cloud/tree/main/ARessourcesPlaneScene/Scripts](https://github.com/ilyasgdo/procedural-generation-and---volumetric-cloud/tree/main/ARessourcesPlaneScene/Scripts)

---

## Introduction

Ce rapport détaille l’implémentation technique d’un **système de terrain procédural infini** pour Unity. Le système combine des techniques avancées de génération procédurale, d’optimisation de mémoire et de rendu adaptatif pour créer un monde ouvert en temps réel.

---

## 1. Architecture Globale du Système

![1.png](/static/unity/terrain-procedural/1.png)

### Inventaire des Fichiers (17 scripts C#)

| Catégorie | Fichiers | Lignes de Code |
| --- | --- | --- |
| **Core** | ChunkManager, TerrainChunk, CoordinateUtils | ~590 |
| **Terrain** | NoiseGenerator, MeshGenerator | ~345 |
| **Biomes** | BiomeGenerator | ~235 |
| **Végétation** | VegetationPlacer, LSystemParser, TreeMeshGenerator | ~930 |
| **Grottes** | CellularAutomata, MarchingCubes, MCTables | ~585 |
| **Total** | 12 fichiers  | **~2685 lignes** |

---

## 2. Système de Chunks Infinis

### 2.1 Principe du Monde Infini

Le monde est divisé en **chunks** de taille fixe (240×240 unités) qui sont générés et détruits dynamiquement autour du joueur.

![2.png](/static/unity/terrain-procedural/2.png)

### 2.2 Système de Coordonnées

![3.png](/static/unity/terrain-procedural/3.png)

| Constante | Valeur | Description |
| --- | --- | --- |
| **CHUNK_SIZE** | 240 unités | Taille de chaque chunk |
| **viewDistanceInChunks** | 5 | Chunks visibles par direction |
| **updateInterval** | 0.25s | Interval de mise à jour |

### 2.3 Object Pooling

Technique d’optimisation mémoire pour réutiliser les chunks au lieu de les détruire/recréer.

![4.png](/static/unity/terrain-procedural/4.png)

**Avantages** :
- Zéro allocation pendant le jeu
- Réduction du garbage collection
- Temps de création de chunk réduit

---

## 3. Système LOD (Level of Detail)

### 3.1 Résolutions Multiples

Le système génère des maillages à 5 niveaux de détail différents.

![5.png](/static/unity/terrain-procedural/5.png)

| LOD | Résolution | Vertices/Chunk | Distance Max |
| --- | --- | --- | --- |
| 0 | 241×241 | 58 081 | 300m |
| 1 | 121×121 | 14 641 | 600m |
| 2 | 61×61 | 3 721 | 1000m |
| 3 | 31×31 | 961 | 1500m |
| 4 | 13×13 | 169 | 2500m+ |

### 3.2 Algorithme de Sélection LOD

![6.png](/static/unity/terrain-procedural/6.png)

### 3.3 Cache de Maillages

Chaque chunk maintient un tableau de maillages pré-calculés pour éviter la régénération.

![7.png](/static/unity/terrain-procedural/7.png)

---

## 4. Génération de Bruit Procédural

### 4.1 Bruit de Perlin avec Seed

Base de la génération de hauteur avec décalage par seed pour variété.

![8.png](/static/unity/terrain-procedural/8.png)

### 4.2 Fractal Brownian Motion (FBM)

Superposition de couches de bruit à fréquences croissantes.

![9.png](/static/unity/terrain-procedural/9.png)

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **octaves** | 8 | Nombre de couches de détail |
| **persistence** | 0.5 | Réduction d’amplitude par octave |
| **lacunarity** | 2.0 | Multiplication de fréquence |
| **scale** | 0.002 | Échelle globale du bruit |
| **heightMultiplier** | 300 | Amplitude maximale du terrain |

### 4.3 Domain Warping (Déformation de Domaine)

Technique avancée qui déforme l’espace avant l’échantillonnage pour créer des formes plus organiques.

![10.png](/static/unity/terrain-procedural/10.png)

**Effet** : Crée des crêtes sinueuses et des vallées moins régulières que le bruit standard.

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **warpStrength** | 50 | Intensité de la déformation |
| **warpScale** | 0.001 | Échelle du bruit de déformation |

### 4.4 Courbe de Hauteur (AnimationCurve)

Remapping non-linéaire des valeurs de bruit pour sculpter le profil du terrain.

**Effet** : Plus de terrain plat à basse altitude, montagnes abruptes aux valeurs hautes.

![11.png](/static/unity/terrain-procedural/11.png)

---

## 5. Système de Biomes par Voronoi

### 5.1 Diagramme de Voronoi

Partitionne l’espace en cellules, chacune avec un biome déterministe.

![12.png](/static/unity/terrain-procedural/12.png)

### 5.2 Types de Biomes

![13.png](/static/unity/terrain-procedural/13.png)

### 5.3 Blending Entre Biomes

Le système calcule des poids pour une transition douce via les **vertex colors**.

![14.png](/static/unity/terrain-procedural/14.png)

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **cellSize** | 500m | Taille des cellules Voronoi |
| **blendDistance** | 100m | Zone de transition |
| **snowHeight** | 250m | Altitude début neige |
| **rockSlopeThreshold** | 0.4 | Pente forçant la roche |

---

## 6. Génération de Maillage

### 6.1 Pipeline de Génération

![15.png](/static/unity/terrain-procedural/15.png)

### 6.2 Calcul des Normales par Face

![16.png](/static/unity/terrain-procedural/16.png)

Moyenne des normales des faces adjacentes pour chaque vertex.

### 6.3 Données de Pente (UV2)

Stocke pente et hauteur normalisée pour le shader de terrain.

| Canal UV2 | Donnée | Calcul |
| --- | --- | --- |
| **X** | Pente | `1 - abs(dot(normal, up))` |
| **Y** | Hauteur normalisée | `(y - minY) / range` |

### 6.4 Interpolation Bilinéaire des Hauteurs

Pour le placement d’objets entre les vertices du maillage.

![17.png](/static/unity/terrain-procedural/17.png)

---

## 7. L-Systems pour Arbres Procéduraux

### 7.1 Grammaire L-System

Système de réécriture de chaînes avec règles stochastiques.

![18.png](/static/unity/terrain-procedural/18.png)

### 7.2 Règles Stochastiques

Plusieurs remplacements possibles avec probabilités.

| Preset | Symbole | Remplacements |
| --- | --- | --- |
| **SimpleTree** | X | `F[+X][-X]FX` (33%), `F[+X]FX` (33%), `F[-X]FX` (33%) |
| **BushyTree** | X | `F-[[X]+X]+F[+FX]-X` (50%), `F+[[X]-X]-F[-FX]+X` (50%) |
| **PineTree** | X | `F[+X]F[-X]+X` (50%), `F[-X]F[+X]-X` (50%) |

### 7.3 Alphabet Turtle Graphics

![19.png](/static/unity/terrain-procedural/19.png)

### 7.4 Interpréteur Turtle 3D

![20.png](/static/unity/terrain-procedural/20.png)

### 7.5 Génération de Géométrie

![21.png](/static/unity/terrain-procedural/21.png)

**Paramètres de Branches** :

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **branchLength** | 2.0 | Longueur de base |
| **branchRadius** | 0.15 | Rayon initial |
| **radiusDecay** | 0.85 | Réduction par niveau |
| **branchSegments** | 6 | Faces du cylindre |

---

## 8. Automates Cellulaires 3D

### 8.1 Règles Type “Game of Life”

Génère des structures de grottes organiques en 3D.

![22.png](/static/unity/terrain-procedural/22.png)

### 8.2 Paramètres de Grotte

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **initialFillProbability** | 0.45 | % initial de cellules solides |
| **smoothingIterations** | 5 | Passes de lissage |
| **birthLimit** | 13 | Voisins pour devenir solide |
| **deathLimit** | 14 | Voisins pour rester solide |

### 8.3 Génération d’Arches

Méthode spéciale pour tunnels traversables.

![23.png](/static/unity/terrain-procedural/23.png)

---

## 9. Marching Cubes

### 9.1 Principe de l’Algorithme

Convertit une grille de voxels booléens en maillage polygonal.

![24.png](/static/unity/terrain-procedural/24.png)

### 9.2 Index de Cube (8 bits)

![25.png](/static/unity/terrain-procedural/25.png)

### 9.3 Tables de Lookup

| Table | Taille | Contenu |
| --- | --- | --- |
| **EdgeTable** | 256 entrées | Bitmask des 12 arêtes intersectées |
| **TriTable** | 256×16 | Liste des triangles (indices d’arêtes) |

### 9.4 Numérotation des Arêtes

```
       4----4----5
      /|        /|
     7 |       5 |
    /  8      /  9
   7----6----6   |
   |   |     |   |
   |   0----0|---1
  11  /      10 /
   | 3        | 1
   |/         |/
   3----2----2
```

---

## 10. Système de Végétation

### 10.1 Placement Dynamique

![26.png](/static/unity/terrain-procedural/26.png)

### 10.2 Types d’Arbres par Biome

![27.png](/static/unity/terrain-procedural/27.png)

| Biome | Types Principaux | Probabilités |
| --- | --- | --- |
| **Grassland** | Oak (35%), Birch (25%), Bush (40%) |  |
| **Desert** | Palm (70%), Bush (30%) |  |
| **Rocky** | Pine (65%), Bush (35%) |  |
| **Snow** | Pine (65%), Bush (35%) |  |

### 10.3 Génération de Forêts

![28.png](/static/unity/terrain-procedural/28.png)

| Paramètre | Valeur | Description |
| --- | --- | --- |
| **maxTrees** | 120 | Arbres individuels max |
| **maxForests** | 8 | Forêts max |
| **treesPerForest** | 25 | Arbres par forêt |
| **forestRadius** | 80m | Rayon de forêt |
| **minTreeDistance** | 12m | Espacement minimum |

---

## 11. Synthèse des Techniques Avancées

![29.png](/static/unity/terrain-procedural/29.png)

---

## 12. Tableau Récapitulatif des Complexités

| Technique | Complexité Calcul | Complexité Mémoire | Fichier |
| --- | --- | --- | --- |
| **FBM** | O(octaves) par sample | O(1) | NoiseGenerator.cs |
| **Domain Warping** | O(2 × FBM) | O(1) | NoiseGenerator.cs |
| **Voronoi** | O(9 cellules) | O(1) | BiomeGenerator.cs |
| **Mesh Generation** | O(vertices²) | O(vertices) | MeshGenerator.cs |
| **LOD Switch** | O(1) | O(5 meshes/chunk) | TerrainChunk.cs |
| **L-System** | O(string length × iterations) | O(string) | LSystemParser.cs |
| **Cellular Automata** | O(voxels × iterations) | O(voxels) | CellularAutomata.cs |
| **Marching Cubes** | O(voxels) | O(triangles) | MarchingCubes.cs |
| **Object Pooling** | O(1) réutilisation | O(pool size) | ChunkManager.cs |

---

## 13. Performances Estimées

| Opération | Temps Typique | Fréquence |
| --- | --- | --- |
| Génération Chunk (LOD 0) | ~50-100ms | À la demande |
| Switch LOD | ~5-10ms | 0.25s interval |
| Spawn Arbre | ~2-5ms | 4/update |
| Marching Cubes (50³) | ~20-40ms | À la demande |
| Cellular Automata (5 iter) | ~10-20ms | À la demande |

---

## Conclusion

Ce système de terrain procédural implémente **24 techniques avancées** réparties en 6 catégories :

1. **Gestion de Chunks** : Pooling, LOD multi-niveaux, distance culling
2. **Bruit Procédural** : FBM, Domain Warping, Height Curves
3. **Biomes** : Voronoi, blending par poids, overrides pente/altitude
4. **Maillage** : Multi-résolution, normales, UV2 slope data
5. **Végétation** : L-Systems stochastiques, Turtle Graphics 3D
6. **Grottes** : Automates cellulaires 3D, Marching Cubes

Le système offre un équilibre optimal entre qualité visuelle et performance temps réel pour un simulateur de vol.