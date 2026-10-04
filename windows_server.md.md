# Configuration Active Directory (Windows Server)

## 1. Objectif
Déployer et configurer les services d'annuaire au sein du réseau isolé pour assurer :
- La centralisation de la gestion des identités et des machines.
- L'authentification unique (SSO) et la gestion des stratégies de groupe (GPO).
- La résolution de noms DNS interne pour le domaine du laboratoire.

## 2. Configuration IP et Paramétrage Préalable de la VM
Avant d'installer les rôles Active Directory, la machine Windows Server doit disposer d'une adresse IP statique et pointer vers elle-même (ou vers le routeur amont pour le transit) pour le DNS, en attendant la promotion.

| Paramètre | Valeur |
| :--- | :--- |
| **Nom de la machine** | `SRV-AD-01` (par exemple) |
| **Adresse IP statique** | `192.168.10.10/24` |
| **Passerelle par défaut** | `192.168.10.1` (pfSense) |
| **Serveur DNS préféré** | `127.0.0.1` ou `192.168.10.1` |

---

## 3. Installation du Rôle Active Directory Domain Services (AD DS)
1. Ouvrir le **Gestionnaire de serveur** (Server Manager).
2. Cliquer sur **Ajouter des rôles et des fonctionnalités** (Add roles and features).
3. Dans l'assistant, avancer jusqu'à l'étape **Rôles de serveurs** (Server Roles).
4. Cocher la case **Services d'annuaire Active Directory** (Active Directory Domain Services).
5. Cliquer sur *Ajouter des fonctionnalités* (Add Features) lorsque la fenêtre contextuelle apparaît, puis valider et lancer l'installation.

![Installation du rôle AD DS](Images/ad/ad-ds-install.png)

---

## 4. Promotion du Serveur en Contrôleur de Domaine (DC)
Une fois le rôle installé, une notification (triangle jaune) apparaît dans le Gestionnaire de serveur pour promouvoir le serveur.

1. Cliquer sur **Promouvoir ce serveur en contrôleur de domaine** (Promote this server to a domain controller).
2. Dans l'assistant de déploiement d'Active Directory :
   * Sélectionner **Ajouter une nouvelle forêt** (Add a new forest).
   * **Nom de domaine racine** : Indiquer le nom de ton domaine (ex: `local.LAB1` ou `entreprise.lan`).
3. Dans les options du contrôleur de domaine :
   * Laisser le niveau fonctionnel de la forêt et du domaine par défaut.
   * S'assurer que les cases **Serveur DNS (Domain Name System)** et **Catalogue global (GC)** sont bien cochées.
   * Saisir un mot de passe pour le mode de restauration des services d'annuaire (**DSRM**).
4. Ignorer l'avertissement éventuel concernant la délégation DNS.
5. Valider les chemins d'accès, vérifier les prérequis, puis cliquer sur **Installer**.
6. Le serveur va automatiquement redémarrer pour finaliser la promotion.

![Promotion du DC et configuration DNS](Images/ad/dns-manager.png)

---

## 5. Vérification post-installation
Après le redémarrage, vérifier le bon fonctionnement des services essentiels :
* **Vérification du service DNS :** Ouvrir le gestionnaire DNS et s'assurer que les zones de recherche directe et inversée pour le domaine sont présentes et dynamiques.
* **Vérification de l'annuaire :** Ouvrir *Utilisateurs et ordinateurs Active Directory* (`dsa.msc`) pour valider la structure par défaut (conteneurs *Users*, *Computers*, etc.).

---
> *Note : Les étapes de jonction des clients et des autres serveurs (comme le serveur Samba/Ubuntu) ainsi que les tests d'authentification centralisée sont documentés dans la section dédiée aux clients et à la validation.*