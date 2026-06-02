# Les Infrastructures Web

## Les matières

1. [Infrastructure Simple (1 serveur)](#1-infrastructure-simple-1-serveur)
2. [Infrastructure Distribuée avec Load Balancer](#2-infrastructure-distribuée-avec-load-balancer)
3. [Infrastructure en Cluster Haute Disponibilité](#3-infrastructure-en-cluster-haute-disponibilité)
4. [Infrastructure avec Réplication Master-Replica](#4-infrastructure-avec-réplication-master-replica)

---

## 1. Infrastructure Simple (1 serveur)

### Description

C'est l'infrastructure web la plus basique. Un seul serveur héberge tous les composants nécessaires au fonctionnement du site web.

### Composants

#### DNS (Domain Name System)

- **Rôle** : Traduit le nom de domaine (www.foobar.com) en adresse IP (8.8.8.8)
- **Fonctionnement** : Quand le client tape www.foobar.com, le DNS retourne l'adresse IP du serveur

#### L'utilisateur

- L'utilisateur qui accède au site web depuis son navigateur
- Envoie des requêtes HTTP et reçoit des réponses HTTP

#### Serveur LAMP

Un seul serveur contient toute la stack :

- **L**inux : Système d'exploitation
- **A**pache : Serveur web (Nginx dans ce cas)
- **M**ySQL : Base de données SQL
- **P**HP : Langage de programmation côté serveur

**Composants du serveur :**

- **Application files** : Les fichiers du code source de l'application
- **Web Server (Nginx)** : Reçoit les requêtes HTTP et sert les pages web
- **Application Server** : Exécute le code de l'application (PHP, Python, etc.)
- **Database (SQL)** : Stocke les données de l'application

### Flux de communication

1. Le client tape www.foobar.com dans son navigateur
2. Le DNS résout le nom en adresse IP 8.8.8.8
3. Le client envoie une requête HTTP à 8.8.8.8
4. Le serveur web (Nginx) reçoit la requête
5. L'application server traite la requête et interroge la base de données si nécessaire
6. Le serveur renvoie la réponse HTTP au client

---

## 2. Infrastructure Distribuée avec Load Balancer

### Description

Infrastructure avancée avec haute disponibilité, sécurité renforcée et capacité de montée en charge. Elle utilise plusieurs serveurs répartis géographiquement ou logiquement.

### Nouveaux Composants

#### SSL Certificate

- **Rôle** : Chiffrement HTTPS pour sécuriser les communications
- **Avantage** : Protège les données sensibles entre le client et le serveur

#### Load Balancer (SPOF - HAproxy)

- **Rôle** : Distribue les requêtes entre plusieurs serveurs
- **Algorithmes possibles** :
  - Round-robin : Distribution à tour de rôle
  - Least connections : Vers le serveur le moins chargé
  - IP Hash : Basé sur l'IP du client
- **Configuration** : Active-Active (tous les serveurs actifs simultanément)

#### Firewalls

- **Rôle** : Protection contre les intrusions et attaques
- **Niveaux** :
  - Avant les serveurs web
  - Entre serveurs web et serveurs d'application
  - Protection de la base de données

#### Monitoring

- **Rôle** : Surveillance en temps réel de la santé des serveurs
- **Fonction** : Détecte les pannes, surveille les performances, génère des alertes

#### Serveurs multiples (Alpha, Beta, Delta)

Trois serveurs identiques avec stack LAMP complète pour assurer la redondance

#### Réplication de base de données

- **Primary (Master)** : Base de données principale qui reçoit les écritures
- **Replica** : Copie en lecture seule pour répartir la charge des requêtes de lecture

### Architecture

**Serveurs :**

- **Server Alpha** : LAMP + Monitoring + Replica DB
- **Server Beta** : LAMP + Monitoring + Primary DB
- **Server Delta** : LAMP + Monitoring + Replica DB

### Flux de communication

1. Client demande www.foobar.com
2. DNS résout vers 8.8.8.8
3. Connexion SSL établie pour HTTPS
4. Load balancer reçoit la requête
5. Load balancer distribue vers un des serveurs (Alpha, Beta ou Delta)
6. Le firewall valide la requête
7. Le serveur web traite la requête
8. L'application server interroge la base de données
9. Réponse renvoyée au client via le load balancer
10. Monitoring surveille tout le processus

---

## 3. Infrastructure en Cluster Haute Disponibilité

### Description

Architecture d'entreprise à trois tiers (3-tier architecture) avec séparation complète des responsabilités. C'est la solution la plus robuste et scalable.

### Architecture à 3 tiers

#### Tier 1 : Load Balancer en Cluster

- **Deux load balancers** : Configuration Active-Active ou Active-Passive
- **Rôle** : Distribution du trafic et élimination du SPOF
- **Cluster HAproxy** : Haute disponibilité garantie

#### Tier 2 : Serveurs Web (Server 1)

- **Rôle** : Uniquement servir les pages web
- **Nginx** : Serveur web
- **Firewall** : Protection dédiée
- **Monitoring** : Surveillance individuelle
- **3 instances** : Redondance complète

#### Tier 3 : Serveurs d'Application (Server 2)

- **Rôle** : Exécution de la logique métier
- **Application files** : Code de l'application
- **Application Server** : Traitement des requêtes
- **Firewall** : Protection dédiée
- **Monitoring** : Surveillance individuelle
- **3 instances** : Haute disponibilité

#### Tier 4 : Serveurs de Base de Données (Server 3)

- **Rôle** : Stockage et gestion des données
- **Database SQL** : Bases de données répliquées
- **Firewall** : Protection dédiée
- **Monitoring** : Surveillance individuelle
- **3 instances** : Réplication et haute disponibilité

### Flux de communication

1. Client → DNS → Résolution en 8.8.8.8
2. Client → SSL Certificate → Connexion sécurisée HTTPS
3. Client → Load Balancer Cluster → Distribution intelligente
4. Load Balancer → Firewall → Server 1 (Web Server)
5. Server 1 → Firewall → Server 2 (Application Server)
6. Server 2 → Firewall → Server 3 (Database)
7. Réponse : Server 3 → Server 2 → Server 1 → Load Balancer → Client
8. Monitoring surveille chaque composant en temps réel

---

## 4. Infrastructure avec Réplication Master-Replica

### Description

Infrastructure intermédiaire avec load balancer et réplication de base de données. Bon compromis entre coût et fiabilité.

### Composants

#### DNS

- Résout www.foobar.com vers 8.8.8.8

#### Load Balancer

- **HAproxy** : Distribution des requêtes
- **Algorithme** : Round-robin ou least connections
- Élimine partiellement le SPOF (peut être doublé)

#### Deux Serveurs LAMP identiques

Chaque serveur contient :

- **Application files** : Code de l'application
- **Web Server (Nginx)** : Serveur web
- **Application Server** : Traitement logique
- **Database (SQL)** : Base de données locale

#### Réplication Master-Replica

**Master (Serveur du haut)**

- Base de données **principale**
- Reçoit toutes les **opérations d'écriture** (INSERT, UPDATE, DELETE)
- Synchronise les changements vers le Replica

**Replica (Serveur du bas)**

- Base de données **secondaire**
- Reçoit les **opérations de lecture** (SELECT)
- Synchronisation automatique depuis le Master
- Peut devenir Master en cas de panne

### Flux de communication

#### Flux de lecture

1. Client → DNS → Load Balancer
2. Load Balancer → Serveur avec Replica
3. Application lit depuis Replica
4. Réponse renvoyée au client

#### Flux d'écriture

1. Client → DNS → Load Balancer
2. Load Balancer → Serveur avec Master
3. Application écrit sur Master
4. Master réplique vers Replica
5. Confirmation renvoyée au client

### Différence Master vs Replica

| Caractéristique | Master | Replica |
|----------------|--------|---------|
| **Écriture** |  Oui |  Non |
| **Lecture** | Oui | Oui |
| **Source de vérité** | Principal | Secondaire |
| **Synchronisation** | Vers Replica | Depuis Master |
| **Charge** | Écriture + Lecture | Lecture seule |
| **Promotion** | - | Peut devenir Master |

---

## Comparaison des 4 Infrastructures

| Critère | Simple | Distribuée | Cluster HA | Master-Replica |
|---------|--------|------------|-----------|----------------|
| **Serveurs** | 1 | 3+ | 9+ | 2 |
| **Coût** | Très faible | Élevé | Très élevé | Moyen |
| **Complexité** | Faible | Moyenne | Très élevée | Moyenne |
| **Disponibilité** | Faible | Haute | Très haute | Haute |
| **SPOF** | Oui | Load balancer | Non | Load balancer |
| **Scalabilité** | Aucune | Bonne | Excellente | Bonne |
| **Sécurité** | Basique | Élevée | Maximale | Moyenne |
| **Monitoring** | Non | Oui | Complet | Optionnel |

---

## Glossaire

- **LAMP** : Linux, Apache/Nginx, MySQL, PHP
- **DNS** : Domain Name System - Annuaire d'Internet
- **SPOF** : Single Point Of Failure - Point unique de défaillance
- **SSL** : Secure Sockets Layer - Protocole de sécurité
- **HTTPS** : HTTP Secure - HTTP chiffré
- **Load Balancer** : Répartiteur de charge
- **HAproxy** : High Availability Proxy - Load balancer open source
- **Firewall** : Pare-feu - Protection réseau
- **Monitoring** : Surveillance et supervision
- **Master-Replica** : Architecture de réplication de base de données
- **Cluster** : Groupe de serveurs travaillant ensemble
- **Tier** : Niveau ou couche dans une architecture
- **Failover** : Basculement automatique en cas de panne
- **Split-brain** : Situation où deux serveurs pensent être le Master
- **Round-robin** : Algorithme de distribution à tour de rôle
- **Active-Active** : Tous les serveurs sont actifs simultanément
- **Active-Passive** : Un serveur actif, les autres en attente

---

## Bonnes Pratiques

### Sécurité

1. Toujours utiliser HTTPS (SSL/TLS)
2. Mettre des firewalls à chaque niveau
3. Limiter les accès SSH
4. Utiliser des clés SSH au lieu de mots de passe
5. Mettre à jour régulièrement les systèmes

### Performance

1. Utiliser un CDN pour les fichiers statiques
2. Mettre en cache les résultats de base de données
3. Optimiser les requêtes SQL
4. Compresser les fichiers (gzip)
5. Utiliser HTTP/2 ou HTTP/3

### Fiabilité

1. Toujours avoir des backups automatiques
2. Tester les procédures de restauration
3. Monitorer en temps réel
4. Avoir un plan de reprise après sinistre
5. Documenter l'infrastructure

### Scalabilité

1. Concevoir pour la croissance dès le départ
2. Utiliser l'auto-scaling si possible
3. Séparer les responsabilités (microservices)
4. Utiliser des conteneurs (Docker, Kubernetes)
5. Prévoir la montée en charge

---

## Conclusion

Le choix de l'infrastructure dépend de :

- **Budget disponible**
- **Trafic attendu**
- **Criticité de l'application**
- **Compétences de l'équipe**
- **Objectifs de disponibilité**
---