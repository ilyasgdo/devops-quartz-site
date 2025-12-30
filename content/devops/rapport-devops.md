---
title: "Rapport DevOps - CI/CD & Kubernetes"
description: "Projet Cloud Native DevOps - Pipeline CI/CD, Tests automatisés, Docker, Kubernetes"
publish: true
tags:
  - devops
  - ci-cd
  - kubernetes
  - docker
  - github-actions
---

# Rendus Projet + TP DEVOPS

![image.png](/static/devops/image.png)

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 
> 👤 **Auteurs**: Ilyas GHANDAOUI  E4FI ESIEE Paris
> 
> 📅 **Date**: 28 décembre 2025
> 
> ### Chaque partie possède son propre rapport
> 

## Projet

[https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien/](https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien/)

# 📚 Rapport Technique CI/CD - Projet Qrious Quiz

[https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien](https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien)

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 
> 👤 **Auteurs**: Ilyas GHANDAOUI  E4FI ESIEE Paris
> 
> 📅 **Date**: 28 décembre 2025
> 

---

## 📋 Table des Matières

1. [Introduction](about:blank#1-introduction)
2. [Architecture Globale](about:blank#2-architecture-globale)
3. [Phase 1 : Application Web](about:blank#3-phase-1--application-web)
4. [Phase 2 : Intégration Continue (CI)](about:blank#4-phase-2--int%C3%A9gration-continue-ci)
5. [Phase 3 : Livraison Continue (CD)](about:blank#5-phase-3--livraison-continue-cd)
6. [Phase 4 : Déploiement Kubernetes](about:blank#6-phase-4--d%C3%A9ploiement-kubernetes)
7. [Base de Données](about:blank#7-base-de-donn%C3%A9es)
8. [Scripts d’Automatisation](about:blank#8-scripts-dautomatisation)
9. [Sécurité](about:blank#9-s%C3%A9curit%C3%A9)
10. [Guide de Déploiement](about:blank#10-guide-de-d%C3%A9ploiement)
11. [Conclusion](about:blank#11-conclusion)

---

## 1. Introduction

### 1.1 Contexte du Projet

Ce projet implémente une **architecture CI/CD complète** pour une application de quiz web. L’objectif est de démontrer les pratiques DevOps modernes avec :

- **Intégration Continue (CI)** : Tests automatisés et build Docker
- **Livraison Continue (CD)** : Déploiement automatique sur Kubernetes
- **Infrastructure locale** : Minikube pour le cluster Kubernetes

### 1.2 Technologies Utilisées

![1.png](/static/devops/1.png)

### 1.3 Objectifs Pédagogiques

| Objectif | Implémentation |
| --- | --- |
| Tests automatisés | ✅ Vitest (Frontend) + Pytest (Backend) |
| Images Docker | ✅ Multi-architecture (amd64/arm64) |
| Déploiement K8s | ✅ Minikube avec manifests YAML |
| Opérations CRUD | ✅ Questions, Réponses, Participations |
| Pipeline CI/CD | ✅ GitHub Actions |

---

## 2. Architecture Globale

### 2.1 Vue d’Ensemble du Système

![2.png](/static/devops/2.png)

### 2.2 Flux de Données

![3.png](/static/devops/3.png)

---

## 3. Phase 1 : Application Web

### 3.1 Architecture de l’Application

![4.png](/static/devops/4.png)

### 3.2 Structure des Fichiers

```
📁 Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien/
├── 📁 .github/workflows/
│   └── 📄 ci-cd.yml              # Pipeline GitHub Actions
├── 📁 k8s/
│   ├── 📄 configmap.yaml         # Variables d'environnement
│   ├── 📄 secrets.yaml           # Secrets (base64)
│   ├── 📄 backend-deployment.yaml
│   ├── 📄 frontend-deployment.yaml
│   └── 📄 pvc.yaml               # Persistent Volume Claim
├── 📁 scripts/
│   ├── 📄 start-cd.sh            # Setup complet
│   ├── 📄 setup-minikube.sh      # Config Minikube
│   ├── 📄 deploy.sh              # Déploiement K8s
│   └── 📄 auto-deploy.sh         # Watcher Docker Hub
├── 📁 quiz-api/                   
│   ├── 📄 app.py               
│   ├── 📄 models.py              
│   ├── 📄 auth.py                
│   ├── 📄 Dockerfile
│   ├── 📄 requirements.txt
│   └── 📁 tests/
│       └── 📄 test_api.py        
└── 📁 quiz-ui/                    
    ├── 📁 src/
    │   ├── 📁 views/             
    │   ├── 📁 services/          
    │   └── 📁 test/             
    ├── 📄 Dockerfile.k8s
    └── 📄 package.json
```

### 3.3 API REST Endpoints

![5.png](/static/devops/5.png)

---

## 4. Phase 2 : Intégration Continue (CI)

### 4.1 Pipeline GitHub Actions

![6.png](/static/devops/6.png)

### 4.2 Configuration du Workflow

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
push:
branches:[main, master]
pull_request:
branches:[main, master]

jobs:
test-frontend:
name: Frontend Tests (Vitest)
runs-on: ubuntu-latest
steps:
-uses: actions/checkout@v4
-uses: actions/setup-node@v4
with:
node-version:'20'
-run: npm ci
working-directory: quiz-ui
-run: npm run test:run
working-directory: quiz-ui

test-backend:
name: Backend Tests (Pytest)
runs-on: ubuntu-latest
steps:
-uses: actions/checkout@v4
-uses: actions/setup-python@v5
with:
python-version:'3.9'
-run: pip install -r requirements.txt
working-directory: quiz-api
-run: pytest tests/ -v
working-directory: quiz-api

build-and-push:
needs:[test-frontend, test-backend]
runs-on: ubuntu-latest
    # ... build Docker multi-arch
```

### 4.3 Couverture des Tests

![7.png](/static/devops/7.png)

| Catégorie | Framework | Nombre de Tests |
| --- | --- | --- |
| Frontend - Composants | Vitest | 2 |
| Frontend - Services | Vitest | 2 |
| Backend - Health Check | Pytest | 2 |
| Backend - Auth | Pytest | 3 |
| Backend - CRUD Questions | Pytest | 8 |
| Backend - Participations | Pytest | 3 |
| Backend - Delete All | Pytest | 2 |
| **Total** |  | **24 tests** |

---

## 5. Phase 3 : Livraison Continue (CD)

### 5.1 Processus de Déploiement Automatique

![8.png](/static/devops/8.png)

### 5.2 Images Docker

![9.png](/static/devops/9.png)

| Image | Base | Taille | Architectures |
| --- | --- | --- | --- |
| `ssssssss3/quiz-api` | python:3.9-alpine | ~150 MB | amd64, arm64 |
| `ssssssss3/quiz-ui` | nginx:alpine | ~25 MB | amd64, arm64 |

---

## 6. Phase 4 : Déploiement Kubernetes

### 6.1 Architecture du Cluster

![10.png](/static/devops/10.png)

### 6.2 Manifests Kubernetes

### ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
name: quiz-config
data:
FLASK_ENV:"production"
FLASK_DEBUG:"0"
VITE_API_URL:"http://quiz-backend:5000"
```

### Secrets

```yaml
apiVersion: v1
kind: Secret
metadata:
name: quiz-secrets
type: Opaque
data:
SECRET_KEY: <base64>
ADMIN_PASSWORD: <base64>
```

### 6.3 Ressources et Limites

![11.png](/static/devops/11.png)

### 6.4 Health Checks (Probes)

![12.png](/static/devops/12.png)

| Probe | Backend | Frontend |
| --- | --- | --- |
| **Liveness** | GET / :5000 every 30s | GET / :80 every 30s |
| **Readiness** | GET / :5000 every 10s | GET / :80 every 10s |
| **Initial Delay** | 15s | 5s |

---

## 7. Base de Données

### 7.1 Schéma de la Base de Données

![13.png](/static/devops/13.png)

### 7.2 Opérations CRUD

![14.png](/static/devops/14.png)

---

## 8. Scripts d’Automatisation

### 8.1 Vue d’Ensemble des Scripts

![15.png](/static/devops/15.png)

### 8.2 Détail des Scripts

| Script | But | Commande |
| --- | --- | --- |
| `start-cd.sh` | Setup complet en une commande | `./scripts/start-cd.sh` |
| `setup-minikube.sh` | Configure Minikube (4GB, 2 CPUs) | `./scripts/setup-minikube.sh` |
| `deploy.sh` | Déploie tous les manifests K8s | `./scripts/deploy.sh` |
| `auto-deploy.sh` | Watcher pour CD automatique | `./scripts/auto-deploy.sh` |

---

## 9. Sécurité

### 9.1 Matrice de Sécurité

![16.png](/static/devops/16.png)

### 9.2 Bonnes Pratiques Implémentées

| Aspect | Implémentation |
| --- | --- |
| Secrets GitHub | Variables chiffrées dans Settings |
| Secrets K8s | Encodés en base64 |
| Auth API | JWT avec expiration |
| Images Docker | Tags immuables (SHA) |
| Réseau | Backend non exposé publiquement |

---

## 10. Guide de Déploiement

### 10.1 Prérequis

![17.png](/static/devops/17.png)

### 10.2 Installation Pas à Pas

```bash
git clone https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien.git
cd Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien

./scripts/start-cd.sh

./scripts/auto-deploy.sh

minikube service quiz-frontend --url
```

### 10.3 Commandes Utiles

```bash
# Status des pods
kubectl get pods -l app=quiz

# Logs backend
kubectl logs -l component=backend -f

# Logs frontend
kubectl logs -l component=frontend -f

# Dashboard Kubernetes
minikube dashboard

# Redémarrer les déploiements
kubectl rollout restart deployment/quiz-backend
kubectl rollout restart deployment/quiz-frontend
```

---

## 11. Conclusion

### 11.1 Récapitulatif du Projet

![18.png](/static/devops/18.png)

### 11.2 Objectifs Atteints

| Objectif | Statut | Détails |
| --- | --- | --- |
| Application Web Full Stack | ✅ | Flask + Vue 3 + SQLite |
| Tests Automatisés | ✅ | 24 tests (Vitest + Pytest) |
| Pipeline CI/CD | ✅ | GitHub Actions |
| Images Docker | ✅ | Multi-arch (amd64/arm64) |
| Déploiement K8s | ✅ | Minikube avec manifests |
| Opérations CRUD | ✅ | Create, Read, Update, Delete |
| Documentation | ✅ | README + Rapport technique |

---

## 📎 Annexes

### A. Liens Utiles

- [Repository GitHub](https://github.com/ilyasgdo/Projet-Full-Stack-ESIEE-2025-Ilyas-Cyprien)
- [Docker Hub - quiz-api](https://hub.docker.com/r/ssssssss3/quiz-api)
- [Docker Hub - quiz-ui](https://hub.docker.com/r/ssssssss3/quiz-ui)

### B. Références

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [Vue.js Documentation](https://vuejs.org/)

---

## TP

[https://github.com/ilyasgdo/ESIEE_2526_devops](https://github.com/ilyasgdo/ESIEE_2526_devops)

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 
> 👤 **Auteurs**: Ilyas GHANDAOUI  E4FI ESIEE Paris
> 
> 📅 **Date**: 28 décembre 2025
> 

Depo git avec tout les tps

[https://github.com/ilyasgdo/ESIEE_2526_devops.git](https://github.com/ilyasgdo/ESIEE_2526_devops.git)

![image.png](/static/devops/image_1.png)

# TP1

## Partie 1

![image.png](/static/devops/image_2.png)

![image.png](/static/devops/image_3.png)

![image.png](/static/devops/image_4.png)

## Partie 2

![image.png](/static/devops/image_5.png)

![image.png](/static/devops/image_6.png)

![image.png](/static/devops/image_7.png)

## Partie 3

Création d’une instance EC2 :

- **AMI :** Amazon Linux
- **Type :** t2.micro
- **HTTP (port 80)** autorisé dans le *Security Group* :

![image.png](/static/devops/image_8.png)

## **4. Analyse et conclusion**

| Critère | Local | Render (PaaS) | AWS EC2 (IaaS) |
| --- | --- | --- | --- |
| Gestion de l’infra | Manuelle | Automatique | Manuelle |
| Configuration serveur | Simple | Gérée par Render | Complète |
| Facilité de déploiement | Facile | Très facile | Moyenne |
| Scalabilité | Limitée | Auto-scale possible | Configurable |
| Coût | Gratuit | Gratuit (tiers) | Payant si actif |

# TP2

## Partie 1

![image.png](/static/devops/image_9.png)

![image.png](/static/devops/image_10.png)

![image.png](/static/devops/image_11.png)

![image.png](/static/devops/image_12.png)

![image.png](/static/devops/image_13.png)

![image.png](/static/devops/image_14.png)

### EXERCISE 1:

La raison est que le groupe de sécurité nommé `"sample-app"` existe déjà (il a été créé lors de la première exécution).  les noms de nombreuses ressources AWS, y compris les groupes de sécurité, doivent être uniques.

Le script Bash n'est pas **idempotent** : il ne vérifie pas si les ressources existent déjà avant d'essayer de les créer. Il exécute simplement les commandes dans l'ordre, et la deuxième tentative de création d'une ressource identique échoue.

### EXERCICE 2:

3 instances: wsl bash -lc '/mnt/c/.../td2/scripts/bash/deploy-ec2-instance.sh --count 3’

wsl bash -lc '/mnt/c/.../deploy-ec2-instance.sh --count 5 --region eu-west-3 

![image.png](/static/devops/image_15.png)

![image.png](/static/devops/image_16.png)

![image.png](/static/devops/image_17.png)

### Nettoyage

![image.png](/static/devops/image_18.png)

![image.png](/static/devops/image_19.png)

### 

## partie 2

![image.png](/static/devops/image_20.png)

![image.png](/static/devops/image_21.png)

![image.png](/static/devops/image_22.png)

![image.png](/static/devops/image_23.png)

### EXERCICE 3

Si on relance le playbook de configuration (`configure_sample_app_playbook.yml`), **il s'exécutera à nouveau, mais il n'effectuera aucun changement sur le serveur.**

C'est le principe fondamental d'**idempotence**, qui est un atout majeur d'Ansible.

À l'exception du premier script `curl` , Ansible détecte que le serveur est déjà dans l'état désiré et ne modifie rien.

### EXERCICE 4

Variables ajoutées :

```csharp
vars:
instance_count: 3
instance_names:
- sample-app-ansible-1
- sample-app-ansible-2
- sample-app-ansible-3
```

Boucle avec loop

```csharp
loop: "{{ instance_names }}”
```

![image.png](/static/devops/image_24.png)

![image.png](/static/devops/image_25.png)

![image.png](/static/devops/image_26.png)

![image.png](/static/devops/image_27.png)

Instances créées :

| instanceid | ip publique | état |
| --- | --- | --- |
| i-03f3d15c5fdab1d11 | 3.145.72.155 | running |
| i-06dd43db0278536a8 | 3.145.173.41 | running |
| i-094b07c3d52dd0c1c | 3.133.132.186 | running |

Instance ID IP Publique État i-094b07c3d52dd0c1c 3.133.132.186 running i-03f3d15c5fdab1d11 3.145.72.155 running i-06dd43db0278536a8 3.145.173.41 running

 Security Groups :

| Nom |              ID |   Description  |
| --- | --- | --- |
| sample-app-ansible | sg-07ee22d0d205dfcee |  Instance unique  |
| sample-app-ansible-multi | sg-0113d79591cc617c7 | Instances multiples |

Key Pairs :

- ansible-ch2.key (instance unique)
- ansible-ch2-multi.key (instances multiples)

## partie 3

![image.png](/static/devops/image_28.png)

![image.png](/static/devops/image_29.png)

### EXERCICE 5

Si vous relancez la commande `packer build sample-app.pkr.hcl`, **la construction réussira et créera une deuxième AMI** 

### Explication

Cela fonctionne sans erreur grâce à la configuration du nom de l'AMI dans votre modèle Packer :

`ami_name = "sample-app-packer-$(uuidv4()}"`

La fonction `$(uuidv4()` génère un **nouvel identifiant unique universel** (UUID) à chaque fois que la commande `packer build` est exécutée. Par conséquent, la nouvelle AMI aura un nom unique (par exemple, `sample-app-packer-123e4567-e89b-12d3-a456-426614174000`), ce qui évite tout conflit de nom avec l'AMI créée lors de la première exécution.

### EXERCICE 6

On doit  simplement **ajouter un nouveau bloc `source`** pour ce provider.

Packer est conçu pour construire des artéfacts pour plusieurs plateformes en parallèle.

Voici à quoi ressemblerait un fichier `sample-app.pkr.hcl`  modifié pour construire à la fois pour AWS et pour VirtualBox  

```csharp
packer {
  required_plugins {
    amazon = {
      version = ">= 1.3.1"
      source  = "github.com/hashicorp/amazon"
    }
    virtualbox = {
      version = ">= 1.1.0"
      source  = "github.com/hashicorp/virtualbox"
    }
  }
}

source "amazon-ebs" "amazon_linux" {
  ami_name      = "sample-app-packer-${uuidv4()}"
  ami_description = "Amazon Linux 2023 AMI with a Node.js sample app."
  instance_type   = "t3.micro"
  region        = "us-east-2"
  source_ami    = ""
  ssh_username  = "ec2-user"
}

source "virtualbox-ovf" "ubuntu_vm" {
  source_path    = "bento/ubuntu-22.04" 
  ssh_username   = "bento"
  ssh_password   = "bento"
  vm_name        = "sample-app-packer-${uuidv4()}"
  output_directory = "output-virtualbox-ubuntu"
}

build {
  sources = [
    "source.amazon-ebs.amazon_linux",
    "source.virtualbox-ovf.ubuntu_vm"
  ]

  provisioner "file" {
    only        = ["source.amazon-ebs.amazon_linux"]
    source      = "app.js"
    destination = "/home/ec2-user/app.js"
  }

  provisioner "file" {
    only        = ["source.virtualbox-ovf.ubuntu_vm"]
    source      = "app.js"
    destination = "/home/bento/app.js"
  }  provisioner "shell" {
    only = ["source.amazon-ebs.amazon_linux"]
    inline = [
      "curl -fsSL https://rpm.nodesource.com/setup_21.x | sudo bash -",
      "sudo yum install -y nodejs"
    ]
    pause_before = "30s"
  }

  provisioner "shell" {
    only = ["source.virtualbox-ovf.ubuntu_vm"]
    inline = [
      "sudo apt-get update -y",
      "sudo apt-get install -y nodejs"
    ]
  }
}
```

![image.png](/static/devops/image_30.png)

### Partie 4

![image.png](/static/devops/image_31.png)

![image.png](/static/devops/image_32.png)

![image.png](/static/devops/image_33.png)

### modification

![image.png](/static/devops/image_34.png)

### suppression

![image.png](/static/devops/image_35.png)

### EXERCICE 7

Si vous exécutez `tofu apply` après avoir fait un `tofu destroy`, OpenTofu va simplement **recréer toutes les ressources** comme s'il s'agissait de la première fois.

### EXERCICE 8

```csharp
provider "aws" {                                               
  region = "us-east-2"
}

resource "aws_security_group" "sample_app" {                   
  name        = "sample-app-tofu"
  description = "Allow HTTP traffic into the sample app"
}

resource "aws_security_group_rule" "allow_http_inbound" {      
  type              = "ingress"
  protocol          = "tcp"
  from_port         = 8080
  to_port           = 8080
  security_group_id = aws_security_group.sample_app.id
  cidr_blocks       = ["0.0.0.0/0"]
}

resource "aws_instance" "sample_app" {                         
  count                  = var.instance_count
  ami                    = var.ami_id                          
  instance_type          = "t3.micro"
  vpc_security_group_ids = [aws_security_group.sample_app.id]
  user_data              = file("${path.module}/user-data.sh")

  tags = {
    Name = "sample-app-tofu-${count.index}"
    Test = "update"
  }
}

```

![image.png](/static/devops/image_36.png)

## partie 5

![image.png](/static/devops/image_37.png)

### EXERCICE 9

### 1. Dans le module `../../modules/ec2-instance/variables.tf`

`variable "instance_type" {
  description = "The EC2 instance type."
  type        = string
  default     = "t2.micro"
}

variable "port" {
  description = "The port the sample app listens on."
  type        = number
  default     = 8080
}`

### 2. Dans le module `../../modules/ec2-instance/main.tf`

`resource "aws_security_group_rule" "allow_http_inbound" {
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.port # Modifié
  to_port           = var.port # Modifié
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.sample_app.id
}`

`resource "aws_instance" "sample_app" {
  ami           = var.ami_id
  instance_type = var.instance_type 
  # ... reste de la configuration
}`

### 3. Dans le module racine `../../live/sample-app/main.tf`

```csharp
module "sample_app_1" {
  source        = "../../modules/ec2-instance"
  ami_id        = "YOUR_AMI_ID"
  name          = "sample-app-tofu-1"
  instance_type = "t2.micro" # Ajouté
  port          = 8080       # Ajouté
}

module "sample_app_2" {
  source        = "../../modules/ec2-instance"
  ami_id        = "
  name          = "sample-app-tofu-2"
  instance_type = "t3.small" 
  port          = 8080       
}
```

### EXERCICE 10

```csharp
provider "aws" {
  region = "us-east-2"
}

locals {
  instances = {
    "instance-1" = {
      name          = "sample-app-tofu-1"
      instance_type = "t2.micro"
    },
    "instance-2" = {
      name          = "sample-app-tofu-2"
      instance_type = "t3.small"
    }
  }
}

module "sample_app" {
  for_each = local.instances 

  source = "../../modules/ec2-instance"
  ami_id = "" 
  
  name          = each.value.name
  instance_type = each.value.instance_type
  port          = 8080
}
```

Désormais, `tofu apply` créera les deux instances en se basant sur la carte `local.instances`

## partie 6

- Nous utiliserons le module EC2 officiel de la communauté AWS : `terraform-aws-modules/ec2-instance/aws`.
    - **Modification `main.tf` :**
    
    ```csharp
    provider "aws" {
      region = "us-east-2"
    }
    
    resource "aws_security_group" "sg_public_module" {
      name        = "sg-for-public-module"
      description = "Allow HTTP and SSH"
    
      ingress {
        from_port   = 8080
        to_port     = 8080
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }
      
      ingress {
        from_port   = 22
        to_port     = 22
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"] 
      }
    }
    
    module "ec2_from_registry" {
      source  = "terraform-aws-modules/ec2-instance/aws"
      version = "~> 5.0"
    
      name = "instance-from-registry"
      ami  = "" 
      instance_type = "t2.micro"
      
      vpc_security_group_ids = [aws_security_group.sg_public_module.id]
    
      tags = {
        Terraform = "true"
        Module    = "Public"
      }
    }
    ```
    

![image.png](/static/devops/image_38.png)

## TP 5

### partie 1

![image.png](/static/devops/image_39.png)

![image.png](/static/devops/image_40.png)

![image.png](/static/devops/image_41.png)

![image.png](/static/devops/image_42.png)

![image.png](/static/devops/image_43.png)

![image.png](/static/devops/image_44.png)

### partie 2

![image.png](/static/devops/image_45.png)

![image.png](/static/devops/image_46.png)

![image.png](/static/devops/image_47.png)

![image.png](/static/devops/image_48.png)

### partie 3

![image.png](/static/devops/image_49.png)

![image.png](/static/devops/image_50.png)

![image.png](/static/devops/image_51.png)

![image.png](/static/devops/image_52.png)

### partie 4

![image.png](/static/devops/image_53.png)

![image.png](/static/devops/image_54.png)

### partie 5

![image.png](/static/devops/image_55.png)

![image.png](/static/devops/image_56.png)

![image.png](/static/devops/image_57.png)

![image.png](/static/devops/image_58.png)

![image.png](/static/devops/image_59.png)

![image.png](/static/devops/image_60.png)

![image.png](/static/devops/image_61.png)

Génial ça fonctionne !!!!!!!!!!!!!!!!!!