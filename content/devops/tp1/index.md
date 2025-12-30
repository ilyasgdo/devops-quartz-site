---
title: "TP1 - Introduction au Cloud et Déploiement"
description: "TP1 DevOps - Déploiement local, PaaS (Render), IaaS (AWS EC2)"
publish: true
tags:
  - devops
  - tp
  - aws
  - cloud
---

# TP1 - Introduction au Cloud et Déploiement

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 👤 **Auteur**: Ilyas GHANDAOUI E4FI
> 
> 📅 **Date**: 28 décembre 2025

Dépôt git avec tous les TPs: [https://github.com/ilyasgdo/ESIEE_2526_devops.git](https://github.com/ilyasgdo/ESIEE_2526_devops.git)

![Dépôt GitHub](/static/devops/image_1.png)

## Partie 1

![TP1 Partie 1](/static/devops/image_2.png)

![TP1 Partie 1-2](/static/devops/image_3.png)

![TP1 Partie 1-3](/static/devops/image_4.png)

## Partie 2

![TP1 Partie 2](/static/devops/image_5.png)

![TP1 Partie 2-2](/static/devops/image_6.png)

![TP1 Partie 2-3](/static/devops/image_7.png)

## Partie 3

Création d'une instance EC2 :

- **AMI :** Amazon Linux
- **Type :** t2.micro
- **HTTP (port 80)** autorisé dans le *Security Group* :

![EC2 Instance](/static/devops/image_8.png)

## 4. Analyse et conclusion

| Critère | Local | Render (PaaS) | AWS EC2 (IaaS) |
| --- | --- | --- | --- |
| Gestion de l'infra | Manuelle | Automatique | Manuelle |
| Configuration serveur | Simple | Gérée par Render | Complète |
| Facilité de déploiement | Facile | Très facile | Moyenne |
| Scalabilité | Limitée | Auto-scale possible | Configurable |
| Coût | Gratuit | Gratuit (tiers) | Payant si actif |
