---
title: "Architecture Render Graph"
weight: 1
---

## 1. Architecture Render Graph & Extensibilité

La dorsale de rendu exploite le **Render Graph de l'Universal Render Pipeline (URP)**, utilisant un Graphe Acyclique Dirigé (DAG) pour l'ordonnancement des trames.

*   **Gestion Mémoire** : Utilisation de l'**Aliasing de Ressources Transitoires** pour réduire l'empreinte VRAM (réutilisation des blocs mémoire entre les passes disjointes).
*   **Éclairage Forward+** : Partitionnement spatial (clustering) pour le *culling* des lumières dynamiques.
*   **Custom Render Features** : Injection de passes (ex: *Heat Haze*) à des points précis (ex: `BeforePostProcess`) via des ScriptableRenderFeatures.

![Flux de Travail Render Graph](./images/Flux%20de%20Travail%20et%20Réutilisation%20Mémoire.svg)
