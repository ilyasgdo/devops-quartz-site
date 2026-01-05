---
title: "TP2 - AWS, Ansible, Packer, OpenTofu"
description: "Rapport technique expert - Infrastructure as Code, Configuration Management, Image Building, Provisioning Automation"
publish: true
tags:
  - devops
  - tp
  - aws
  - ansible
  - packer
  - terraform
  - opentofu
  - infrastructure-as-code
  - configuration-management
  - immutable-infrastructure
keywords:
  - Infrastructure as Code
  - Configuration Management
  - Immutable Infrastructure
  - Golden Image Pattern
  - Declarative Infrastructure
  - State Management
  - Drift Detection
---

<!-- 
Expert Technical Report - Infrastructure as Code & Automation
Keywords: Infrastructure as Code (IaC), Configuration Management, Ansible Playbooks,
Packer Templates, OpenTofu/Terraform, HashiCorp Configuration Language (HCL),
AWS SDK, AWS CLI, EC2 Instances, AMI (Amazon Machine Image), 
Idempotency, Declarative Configuration, Imperative vs Declarative,
State Files, Remote State Backend, State Locking, Modules,
Provider Plugins, Resource Graph, Dependency Management,
Immutable Infrastructure, Golden Image, Baking vs Frying,
Configuration Drift, Compliance as Code, Policy as Code
-->

# TP2 - AWS, Ansible, Packer, OpenTofu

> Projet Cloud Native DevOps - ESIEE Paris 2025
> 
> 👤 **Auteurs** : Ilyas GHANDAOUI, Cyprien BOSCHER & Lorenzo BAVARD - E4FI
> 
> 👨‍🏫 **Enseignant** : Badr TAJINI

## Partie 1 - Scripts Bash AWS

![AWS CLI](/static/devops/image_9.png)

![AWS Instances](/static/devops/image_10.png)

![Security Groups](/static/devops/image_11.png)

![Résultats](/static/devops/image_12.png)

![Console AWS](/static/devops/image_13.png)

![Dashboard](/static/devops/image_14.png)

### EXERCICE 1

La raison est que le groupe de sécurité nommé `"sample-app"` existe déjà (il a été créé lors de la première exécution). Les noms de nombreuses ressources AWS, y compris les groupes de sécurité, doivent être uniques.

Le script Bash n'est pas **idempotent** : il ne vérifie pas si les ressources existent déjà avant d'essayer de les créer. Il exécute simplement les commandes dans l'ordre, et la deuxième tentative de création d'une ressource identique échoue.

### EXERCICE 2

3 instances: `wsl bash -lc '/mnt/c/.../td2/scripts/bash/deploy-ec2-instance.sh --count 3'`

`wsl bash -lc '/mnt/c/.../deploy-ec2-instance.sh --count 5 --region eu-west-3`

![Instances créées](/static/devops/image_15.png)

![Liste instances](/static/devops/image_16.png)

![Dashboard instances](/static/devops/image_17.png)

### Nettoyage

![Nettoyage 1](/static/devops/image_18.png)

![Nettoyage 2](/static/devops/image_19.png)

---

## Partie 2 - Ansible

![Ansible Setup](/static/devops/image_20.png)

![Ansible Playbook](/static/devops/image_21.png)

![Ansible Résultat](/static/devops/image_22.png)

![Ansible Dashboard](/static/devops/image_23.png)

### EXERCICE 3

Si on relance le playbook de configuration (`configure_sample_app_playbook.yml`), **il s'exécutera à nouveau, mais il n'effectuera aucun changement sur le serveur.**

C'est le principe fondamental d'**idempotence**, qui est un atout majeur d'Ansible.

À l'exception du premier script `curl`, Ansible détecte que le serveur est déjà dans l'état désiré et ne modifie rien.

### EXERCICE 4

Variables ajoutées :

```yaml
vars:
  instance_count: 3
  instance_names:
    - sample-app-ansible-1
    - sample-app-ansible-2
    - sample-app-ansible-3
```

Boucle avec loop:

```yaml
loop: "{{ instance_names }}"
```

![Ansible Multi](/static/devops/image_24.png)

![Ansible Multi 2](/static/devops/image_25.png)

![Ansible Multi 3](/static/devops/image_26.png)

![Ansible Multi 4](/static/devops/image_27.png)

Instances créées :

| Instance ID | IP Publique | État |
| --- | --- | --- |
| i-03f3d15c5fdab1d11 | 3.145.72.155 | running |
| i-06dd43db0278536a8 | 3.145.173.41 | running |
| i-094b07c3d52dd0c1c | 3.133.132.186 | running |

Security Groups :

| Nom | ID | Description |
| --- | --- | --- |
| sample-app-ansible | sg-07ee22d0d205dfcee | Instance unique |
| sample-app-ansible-multi | sg-0113d79591cc617c7 | Instances multiples |

Key Pairs :
- ansible-ch2.key (instance unique)
- ansible-ch2-multi.key (instances multiples)

---

## Partie 3 - Packer

![Packer Build](/static/devops/image_28.png)

![Packer Result](/static/devops/image_29.png)

### EXERCICE 5

Si vous relancez la commande `packer build sample-app.pkr.hcl`, **la construction réussira et créera une deuxième AMI**.

### Explication

Cela fonctionne sans erreur grâce à la configuration du nom de l'AMI dans votre modèle Packer :

`ami_name = "sample-app-packer-${uuidv4()}"`

La fonction `${uuidv4()}` génère un **nouvel identifiant unique universel** (UUID) à chaque fois que la commande `packer build` est exécutée.

### EXERCICE 6

On doit simplement **ajouter un nouveau bloc `source`** pour ce provider.

Packer est conçu pour construire des artéfacts pour plusieurs plateformes en parallèle.

```hcl
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
  ami_name        = "sample-app-packer-${uuidv4()}"
  ami_description = "Amazon Linux 2023 AMI with a Node.js sample app."
  instance_type   = "t3.micro"
  region          = "us-east-2"
  source_ami      = ""
  ssh_username    = "ec2-user"
}

source "virtualbox-ovf" "ubuntu_vm" {
  source_path      = "bento/ubuntu-22.04"
  ssh_username     = "bento"
  ssh_password     = "bento"
  vm_name          = "sample-app-packer-${uuidv4()}"
  output_directory = "output-virtualbox-ubuntu"
}

build {
  sources = [
    "source.amazon-ebs.amazon_linux",
    "source.virtualbox-ovf.ubuntu_vm"
  ]
  # ... provisioners
}
```

![Packer Multi](/static/devops/image_30.png)

---

## Partie 4 - OpenTofu/Terraform Basics

![Tofu Init](/static/devops/image_31.png)

![Tofu Plan](/static/devops/image_32.png)

![Tofu Apply](/static/devops/image_33.png)

### Modification

![Tofu Modify](/static/devops/image_34.png)

### Suppression

![Tofu Destroy](/static/devops/image_35.png)

### EXERCICE 7

Si vous exécutez `tofu apply` après avoir fait un `tofu destroy`, OpenTofu va simplement **recréer toutes les ressources** comme s'il s'agissait de la première fois.

### EXERCICE 8

```hcl
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

![Tofu Variables](/static/devops/image_36.png)

---

## Partie 5 - Modules OpenTofu

![Modules](/static/devops/image_37.png)

### EXERCICE 9

#### 1. Dans le module `../../modules/ec2-instance/variables.tf`

```hcl
variable "instance_type" {
  description = "The EC2 instance type."
  type        = string
  default     = "t2.micro"
}

variable "port" {
  description = "The port the sample app listens on."
  type        = number
  default     = 8080
}
```

#### 2. Dans le module `../../modules/ec2-instance/main.tf`

```hcl
resource "aws_security_group_rule" "allow_http_inbound" {
  type              = "ingress"
  protocol          = "tcp"
  from_port         = var.port
  to_port           = var.port
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.sample_app.id
}
```

### EXERCICE 10

```hcl
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

---

## Partie 6 - Modules Publics Terraform Registry

Utilisation du module EC2 officiel de la communauté AWS : `terraform-aws-modules/ec2-instance/aws`.

```hcl
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

  name          = "instance-from-registry"
  ami           = ""
  instance_type = "t2.micro"

  vpc_security_group_ids = [aws_security_group.sg_public_module.id]

  tags = {
    Terraform = "true"
    Module    = "Public"
  }
}
```

![Registry Module](/static/devops/image_38.png)
