---
title: "TP6 - AWS Organizations & Kubernetes"
description: "Rapport technique expert - AWS Organizations, IAM Roles, Kubernetes local orchestration"
publish: true
tags:
  - devops
  - tp
  - aws
  - organizations
  - iam
  - kubernetes
  - docker-desktop
keywords:
  - AWS Organizations
  - Multi-Account Strategy
  - IAM Role Assumption
  - Kubernetes Orchestration
  - Microservices Communication
---

<!-- 
Expert Technical Report - AWS Organizations & Kubernetes
Keywords: AWS Organizations, Account Management, IAM Roles, AssumeRole,
Cross-Account Access, Service Control Policies (SCP), Organizational Units (OU),
Kubernetes, Docker Desktop, Service Discovery, Pod Communication,
Microservices Architecture, Container Orchestration
-->

# TP6 - AWS Organizations & Kubernetes

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 👤 **Auteurs** : Ilyas GHANDAOUI & Cyprien BOSCHER - E4FI
> 
> 👨‍🏫 **Enseignant** : Badr TAJINI

---

## Partie 1 - AWS Organizations

![AWS Organizations Setup](/static/devops/tp6/image.png)

![Organization Structure](/static/devops/tp6/image_1.png)

![Account Configuration](/static/devops/tp6/screenco.png)

![Account Verification](/static/devops/tp6/screenco_1.png)

### Réflexions et Apprentissages

La mise en place de l'organisation AWS m'a permis de comprendre l'importance d'isoler les environnements pour la sécurité, mais je me suis rapidement heurté à la gestion stricte des identifiants par Amazon. 

**Défis rencontrés :**
- **Unicité des adresses email** : Contournement via des alias email
- **Destruction des comptes enfants** : AWS interdit la suppression programmatique sans moyen de paiement
- **Gestion de l'existant** : Adaptation du code pour éviter la recréation de ressources existantes

---

## Partie 2 - IAM Roles & AssumeRole

![IAM User Creation](/static/devops/tp6/image_2.png)

![Administrator Access](/static/devops/tp6/image_3.png)

![Access Keys](/static/devops/tp6/image_4.png)

### Problématique du Compte Root

Nous avons dû créer un nouvel utilisateur IAM avec des droits d'administrateur, car **le compte root n'a pas l'autorisation de se "travestir"** (AssumeRole). 

Après avoir généré et configuré une nouvelle clé d'accès pour cet utilisateur, le déploiement a réussi :

![Deployment Success](/static/devops/tp6/image_5.png)

![Role Configuration](/static/devops/tp6/image_6.png)

![Cross-Account Access](/static/devops/tp6/image_7.png)

![Assume Role Test](/static/devops/tp6/image_8.png)

![Credentials Setup](/static/devops/tp6/image_9.png)

![Environment Variables](/static/devops/tp6/image_10.png)

![PowerShell Configuration](/static/devops/tp6/image_11.png)

![Role Assumption](/static/devops/tp6/image_12.png)

![Access Verification](/static/devops/tp6/image_13.png)

![Account Switch](/static/devops/tp6/image_14.png)

![Final Verification](/static/devops/tp6/image_15.png)

![Cross-Account Resources](/static/devops/tp6/image_16.png)

![Resource Access](/static/devops/tp6/image_17.png)

![Deployment Complete](/static/devops/tp6/image_18.png)

![Infrastructure State](/static/devops/tp6/image_19.png)

![Final Configuration](/static/devops/tp6/image_20.png)

![Validation](/static/devops/tp6/image_21.png)

### Leçons Apprises

Cette étape a été marquée par un **blocage technique majeur** lié aux droits IAM :

**Points clés :**
- Le **compte root** a l'interdiction formelle d'assumer des rôles vers les sous-comptes
- Nécessité de créer un **utilisateur IAM Admin** dédié
- Adaptation de la syntaxe des variables d'environnement pour **PowerShell**
- Compréhension approfondie du mécanisme **AssumeRole**

---

## Partie 3 - Kubernetes Local & Microservices

![Kubernetes Setup](/static/devops/tp6/image_22.png)

![Docker Desktop K8s](/static/devops/tp6/image_23.png)

![Service Deployment](/static/devops/tp6/image_24.png)

![Pod Communication](/static/devops/tp6/image_25.png)

![Service Discovery](/static/devops/tp6/image_26.png)

### Orchestration et Communication

Cette dernière partie sur l'orchestration locale a illustré concrètement la **communication entre microservices**.

**Découvertes importantes :**

1. **Kubernetes sur Docker Desktop** : N'est pas activé par défaut
2. **Isolation réseau** : Le Backend est inaccessible depuis l'extérieur (comportement normal)
3. **Service Discovery** : Seul le Frontend peut communiquer avec le Backend via les Services Kubernetes
4. **Architecture microservices** : Compréhension du modèle de communication interne

**Architecture réseau :**
```
Internet → Frontend Service (LoadBalancer/NodePort)
              ↓
          Frontend Pod
              ↓ (ClusterIP)
          Backend Service
              ↓
          Backend Pod
```

---

## Conclusion

### Compétences Acquises

| Domaine | Compétences |
|---------|-------------|
| **AWS Organizations** | Multi-account strategy, Account isolation, Email alias management |
| **IAM** | Role assumption, Cross-account access, Root vs IAM user limitations |
| **Kubernetes** | Local orchestration, Service discovery, Pod communication |
| **Microservices** | Network isolation, Service mesh basics, Container networking |

### Défis Surmontés

1. ✅ Gestion des contraintes AWS (email, destruction de comptes)
2. ✅ Compréhension des limitations du compte root
3. ✅ Configuration IAM pour AssumeRole
4. ✅ Activation et configuration de Kubernetes sur Docker Desktop
5. ✅ Compréhension de l'isolation réseau des microservices
