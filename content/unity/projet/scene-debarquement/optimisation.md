---
title: "Optimisation"
weight: 5
---

## 5. Optimisation & Culling

Pour maintenir le budget de 16.6ms :

*   **Batching Statique Sémantique** : Analyse automatisée du graphe de scène pour marquer les objets statiques (`Rock`, `Ruin`).
*   **Hystérésis LOD** : Cross-fading (Dithering) pour des transitions LOD imperceptibles.
*   **Analyse VRAM** : Heuristiques pour identifier les textures > 10MB et appliquer la compression BC7/ASTC.

![Le Trieur d'Objets](./images/Le%20Trieur%20d%27Objets.png)
