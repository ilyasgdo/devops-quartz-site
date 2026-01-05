---
title: "Gameplay & Composition"
weight: 6
---

## 6. Logique Gameplay & Composition

### Animation & Interactivité
*   **LCVPs (Bateaux)** : Animation Kynématique synchronisée avec le masque d'eau.
*   **Combat Feedback** : Projection 3D -> Écran pour les Hitmarkers.
*   **Vent Global** : Singleton `WindZoneController` diffusant un vecteur de vent (Perlin Noise) à tous les shaders (Herbe, Arbres, Sable).

### Structure de la Scène
*   **Environment** : Séparation `Static` / `Dynamic` pour optimiser le batching.
*   **Calques (Layers)** : Utilisation stricte des calques pour le filtrage des lumières et des effets.

![Système de Calques](./images/Le%20système%20de%20Calques%20(Layers).png)
