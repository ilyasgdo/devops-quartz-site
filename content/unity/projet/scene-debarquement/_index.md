---
title: "Rapport Scène Débarquement"
description: "Rapport technique expert - Architecture de rendu URP, simulation volumétrique et effets de surface pour la scène de débarquement."
publish: true
tags:
  - unity
  - urp
  - graphic-programming
  - water-simulation
  - volumetric-fog
  - optimization
keywords:
  - Universal Render Pipeline
  - Compute Shader
  - Gerstner Waves
  - Volumetric Fog
  - GPU Profiling
---

# Architecture de Rendu & Simulation Intégrée

> **Projet** : Operation Dragoon - Beneath the Sand
>
> **Module** : Rendu & Optimisation Scène 1B (Débarquement)
>
> **Auteur** : Cyprien BOSCHER

Cette section détaille l'implémentation bas-niveau des systèmes graphiques et de simulation actifs dans la scène de débarquement. L'objectif est de maintenir une fidélité visuelle cinématographique (`60fps` cible) tout en gérant une géométrie complexe et des effets volumétriques multiples.

**🎥 [Voir la démo technique sur YouTube](https://www.youtube.com/watch?v=qGEkDp0cx1Y)**

## Sommaire Technique

Explorez les modules techniques ci-dessous :

1.  **[Architecture Render Graph](./architecture-rendu)** - Pipeline URP et gestion mémoire.
2.  **[Effets Volumétriques](./effets-volumetriques)** - Tempête de sable 6-Way et Brouillard Raymarched.
3.  **[Simulation d'Eau](./simulation-eau)** - Vagues Gerstner GPU et Masquage.
4.  **[Post-Traitement](./post-traitement)** - Heat Haze et Lens Flares.
5.  **[Optimisation](./optimisation)** - Batching, LODs et Budget Frame.
6.  **[Gameplay & Composition](./gameplay-composition)** - Logique de jeu et Structure de scène.

---
*Naviguez via le menu latéral ou les liens ci-dessus pour plonger les détails d'implémentation.*
