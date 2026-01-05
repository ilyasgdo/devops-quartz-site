---
title: "Simulation d'Eau"
weight: 3
---

## 3. Synthèse de Surface Hydrodynamique

Le rendu de l'eau repose sur la **Sommation d'Ondes Trochoïdales de Gerstner** (Compute Shader `WaterSimulation.compute`).

*   **Déplacement de Vertex** : Somme d'octaves d'ondes sur le GPU.
    ![Rendu des Vagues](./images/Sea_Waves.png)

*   **Shore Masking** : Modulation de l'amplitude via une texture de masque (`RFloat`) pour éviter le clipping avec le terrain et les quais.
    ![Écume et Rivage](./images/sea_foam.png)
*   **Shading** : BRDF GGX pour le spéculaire, approximation de Fresnel (Schlick) et réfraction via l'*Opaque Texture*.
    ![Réalisme de l'Eau](./images/water_deep_realism.png)

![Création et Atténuation des Vagues](./images/Création%20et%20Atténuation%20des%20Vagues.png)
