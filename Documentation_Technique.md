

## Sommaire

1. [Introduction](#1-introduction)
2. [Installation d'OPNsense](#2-installation-dopnsense)

   2.1. [Configuration Matérielle Virtuelle](#2.1-configuration-matérielle-virtuelle)

   2.2. [Étapes d'Installation d'OPNsense](#2.2-étapes-dinstallation-dopnsense)
3. [Configuration Initiale d'OPNsense](#3-configuration-initiale-dopnsense)

   3.1. [Accès à l'Interface Web](#3.1-accès-à-linterface-web)

   3.2. [Assistant de Configuration Initiale](#3.2-assistant-de-configuration-initiale)
4. [Configuration des Interfaces Réseau](#4-configuration-des-interfaces-réseau)

   4.1. [Interface ADMIN](#4.1-interface-admin)

   4.2. [Interface CLIENT](#4.2-interface-client)

   4.3. [Configuration du Serveur DHCP sur Windows Server 2019](#4.3-configuration-du-serveur-dhcp-sur-windows-server-2019)

   4.4. [Configuration de la Zone CLIENT](#4.4-configuration-de-la-zone-client)
5. [Installation de Windows Server 2019 sur VirtualBox](#5-installation-de-windows-server-2019-sur-virtualbox)

   5.1. [Étape 1 : Télécharger l'ISO de Windows Server 2019](#5.1-étape-1-télécharger-liso-de-windows-server-2019)

   5.2. [Étape 2 : Créer une Machine Virtuelle dans VirtualBox](#5.2-étape-2-créer-une-machine-virtuelle-dans-virtualbox)
6. [Installation des Machines Clientes](#6-installation-des-machines-clientes)

   6.1. [Client Windows](#6.1-client-windows)
   
   6.2. [Client Linux](#6.2-client-linux)
7. [Configuration d'Active Directory et DNS](#7-configuration-dactive-directory-et-dns)
   
   7.1. [Installation du Rôle AD DS](#7.1-installation-du-rôle-ad-ds)
   
   7.2. [Configuration du DNS](#7.2-configuration-du-dns)
8. [Adhésion des Machines Clientes au Domaine](#8-adhésion-des-machines-clientes-au-domaine)
9. [Configuration des Règles de Pare-feu](#9-configuration-des-règles-de-pare-feu)
10. [Optimisation et Sécurisation Avancée]()
## 1. Introduction

Cette documentation guide les experts à travers la configuration d'un réseau sécurisé en utilisant OPNsense comme pare-feu, Windows Server 2019 pour Active Directory, serveur DNS et serveur DHCP, et VirtualBox pour les machines clientes.

## 2. Installation d'OPNsense

### 2.1 Configuration Matérielle Virtuelle

Avant de commencer l'installation, assurez-vous que chaque machine virtuelle répond aux exigences matérielles virtuelles suivantes :

| Machine                   | Système d'exploitation | RAM | Stockage | Interfaces Réseau           | Adresse IP           |
| ------------------------- | ---------------------- | --- | -------- | --------------------------- | -------------------- |
| OPNsense (Firewall)       | OPNsense (BSD)         | 2Go | 10Go     | WAN (NAT), ADMIN (Host-Only), CLIENT | WAN: DHCP, ADMIN: 10.10.10.1/28, CLIENT: 10.10.11.1/24 |
| Windows Server 2019       | Windows Server 2019    | 4Go | 32Go     | ADMIN (Host-Only)            | ADMIN: 10.10.10.2    |
| Client Windows            | Windows                | 4Go | 32Go     | CLIENT (Host-Only)           | DHCP (10.10.11.x)    |

### 2.2 Étapes d'Installation d'OPNsense

#### 2.2.1 Étape 1 : Télécharger l'ISO d'OPNsense

Téléchargez l'ISO d'OPNsense depuis [https://opnsense.org/download/](https://opnsense.org/download/).

#### 2.2.2 Étape 2 : Créer des Machines Virtuelles dans VirtualBox

1. Ouvrez VirtualBox et créez une nouvelle machine virtuelle pour OPNsense.
2. Configurez la machine virtuelle avec les paramètres appropriés, y compris la RAM et le stockage.
3. Attachez l'ISO d'OPNsense téléchargé en tant que support d'amorçage.
4. Démarrez la machine virtuelle et procédez à l'installation.

#### 2.2.3 Étape 3 : Mise en place d'OPNsense

Suivez les instructions à l'écran pour mettre en place OPNsense sur chaque machine virtuelle. Lors de l'installation, configurez les interfaces WAN, ADMIN et CLIENT en fonction des spécifications dans le tableau ci-dessus.

## 3. Configuration Initiale d'OPNsense

### 3.1 Accès à l'Interface Web

Après l'installation, accédez à l'interface web d'OPNsense en utilisant l'adresse IP ADMIN attribuée (par exemple, [https://10.10.10.1](https://10.10.10.1)).

- Nom d'utilisateur : `root`
- Mot de passe : `opnsense`

### 3.2 Assistant de Configuration Initiale

À travers l'assistant de configuration initiale, définissez le mot de passe administrateur, configurez les interfaces WAN, ADMIN et CLIENT, et activez éventuellement l'accès SSH.

## 4. Configuration des Interfaces Réseau

### 4.1 Interface ADMIN

Dans l'interface web d'OPNsense, accédez à **Interfaces** > **ADMIN**. Configurez l'interface ADMIN avec une adresse IP (par exemple, `10.10.10.1`) et définissez le masque de sous-réseau (par exemple, `255.255.255.240`).

### 4.2 Interface CLIENT

Dans l'interface OPNsense, accédez à **Interfaces** > **Assigner**. Ajoutez une nouvelle interface pour le réseau CLIENT avec le protocole DHCP activé (par exemple, `10.10.11.1/24`).

### 4.3 Configuration du Serveur DHCP sur Windows Server 2019

1. Sur la machine Windows Server 2019, ouvrez **Server Manager**.
2.  Ajoutez le rôle de **Serveur DHCP**.
3. Créez une nouvelle plage pour la zone ADMIN.
   - Plage d'adresses : 10.10.10.3 à 10.10.10.14
   - Masque de sous-réseau : 255.255.255.240
   - Passerelle par défaut : 10.10.10.1 (IP de l'interface ADMIN d'OPNsense)
   - Serveurs DNS : 10.10.10.2 (IP de Windows Server)

### 4.4 Configuration de la Zone CLIENT

1. Créez une plage pour la zone CLIENT.
   - Plage d'adresses : 10.10.11.1 à 10.10.11.254
   - Masque de sous-réseau : 255.255.255.0
   - Passerelle par défaut : 10.10.11.1 (IP de l'interface CLIENT d'OPNsense)
   - Serveurs DNS : 10.10.10.2 (IP de Windows Server)

## 5. Installation de Windows Server 2019 sur VirtualBox

#### 5.1 Étape 1 : Télécharger l'ISO de Windows Server 2019

Téléchargez l'ISO de Windows Server 2019 depuis [https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server-2019](https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server-2019).

#### 5.2 Étape 2 : Créer une Machine Virtuelle dans VirtualBox

1. Ouvrez VirtualBox et créez une nouvelle machine virtuelle pour le serveur Windows.
2. Configurez la machine avec des paramètres appropriés, y compris la RAM, et le stockage.
3. Attachez l'ISO de Windows Server 2019 en tant que support d'amorçage.
4. Démarrez la machine virtuelle et procédez à l'installation.

## 6. Installation des Machines Clientes

### 6.1 Client Windows

#### 6.1.1 Étape 1 : Télécharger l'ISO de Windows

Téléchargez l'ISO de Windows depuis [https://www.microsoft.com/fr-fr/software-download/windows10](https://www.microsoft.com/fr-fr/software-download/windows10).

#### 6.1.2 Étape 2 : Créer une Machine Virtuelle dans VirtualBox

1. Ouvrez VirtualBox et créez une nouvelle machine virtuelle pour le Client Windows.
2. Configurez la machine avec des paramètres appropriés, y compris la RAM, et le stockage.
3. Attachez l'ISO de Windows en tant que support d'amorçage.
4. Démarrez la machine virtuelle et procédez à l'installation de Windows.

## 7. Configuration d'Active Directory et DNS

### 7.1 Installation du Rôle AD DS

Installez le rôle Active Directory Domain Services (AD DS) sur Windows Server 2019. Promouvez le serveur en contrôleur de domaine, en choisissant le nom de domaine (par exemple, `ad.guardiaproject.local`).

### 7.2 Configuration du DNS

Installez le rôle de serveur DNS sur Windows Server. Configurez les paramètres DNS pour utiliser l'adresse IP du serveur (par exemple, `10.10.10.2`).

## 8. Adhésion des Machines Clientes au Domaine

Configurez le DNS sur les machines clientes pour utiliser l'adresse IP du serveur Windows et rejoignez-les au domaine Active Directory (par exemple, `ad.guardiaproject.local`).

## 9. Configuration des Règles de Pare-feu

Dans l'interface OPNsense, accédez à **Firewall** > **Règles**. Créez des règles pour permettre le trafic nécessaire.

## 10. Optimisation et Sécurisation Avancée

Pour aller plus loin dans la sécurisation du réseau, envisagez les actions suivantes :
- Mise en place de VPN
- Filtrage avancé des paquets
- Surveillance du trafic réseau
- Gestion des logs et des alertes
- Mise en place de VLANs pour une segmentation plus poussée
