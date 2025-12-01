# PROJET IoT Dashboard
## Module : Chaîne d'acquisition et traitement numérique 3

---

## Sommaire

1. [Objectifs](#objectifs)
2. [Procédure de travail pour ce TP](#procédure-de-travail-pour-ce-tp)
3. [Cahier des charges](#cahier-des-charges)
4. [Logiciels à mettre en œuvre](#logiciels-à-mettre-en-œuvre)
5. [Parcours d'une grandeur physique](#parcours-dune-grandeur-physique)
6. [Le système IoT CIEL](#le-système-iot-ciel)
7. [Annexes](#annexes)

---

## ⚠️ Règles importantes

- **Le travail sur Docker doit exclusivement être réalisé à partir du compte TP-Docker.**
- **Toute installation locale sur les postes est strictement interdite**, sauf autorisation expresse de l'enseignant.
- **La notation de cette activité sera dépendante de l'historique de vos commits.**

---

## 1. Objectifs

- ✓ Traiter des données représentant des grandeurs physiques
- ✓ Réaliser des communications avec le protocole MQTT
- ✓ Utiliser des formats de sérialisation de données (JSON)
- ✓ Utiliser la containerisation Docker
- ✓ Être capable de présenter et d'expliquer à l'oral, sans support, le fonctionnement de la solution réalisée

---

## 2. Procédure de travail pour ce TP

Vous devez adopter une **démarche de type projet**. Les étapes adaptées pour cette activité sont les suivantes :

1. **Respecter le cahier des charges** initial et le compléter, si nécessaire, avec l'enseignant
2. **Identifier et spécifier les données** à traiter : grandeur physique, unité, conversion
3. **Concevoir une maquette de l'interface utilisateur (UI)** répondant au besoin
4. **Analyser les traitements** à réaliser sur les données
5. **Étudier le(s) logiciel(s)** à mettre en œuvre (fonctionnalités, installation, paramétrage)
6. **Réaliser un diagramme de déploiement** de la solution
7. **Élaborer un diagramme** explicitant les flux de données
8. **Développer la solution** en utilisant la containerisation
9. **Valider la solution** en effectuant les tests de recette
10. **Présenter une démonstration** de votre solution (tests de recette devant le professeur)

### Gestion de version avec Git et GitHub

Les fichiers de déploiement de la ou des solutions doivent être présents dans votre dépôt GitHub et ne doivent contenir que :

- Le(s) fichier(s) `docker-compose.yml`
- Les éventuels `Dockerfile`
- Les dossiers de volumes et fichiers de configuration nécessaires au fonctionnement du docker-compose
- **Aucune donnée ni fichier binaire ne doit être inclus**

---

## 3. Cahier des charges

### Objectif général

Réaliser un **tableau de bord (Dashboard)** pour visualiser les données issues de capteurs sur des objets connectés (IoT).

### Fonctionnalités minimales

#### Affichage pour chaque grandeur :

- **Localisation des capteurs** : afficher la position géographique des capteurs
- **Valeur instantanée** : affichage numérique et sous forme de jauge
- **Évolution temporelle des mesures** : regroupées par grandeurs (physiques ou de même type)
- **Alarmes** : alertes visuelles et sonores en cas de dépassement de seuil

#### Réglages :

- **Seuil d'alarme** : configurable pour chaque mesure

---

## 4. Logiciels à mettre en œuvre

Plusieurs logiciels peuvent être mis en œuvre, du plus simple au plus complexe :

1. **Node-RED Dashboard** (simple et rapide)
2. **ThingsBoard IoT Gateway + ThingsBoard** (intermédiaire)
3. **Telegraf + InfluxDB + Grafana** (avancé)

En fonction de votre avancement, réaliser plusieurs solutions avec ces différents logiciels.

---

## 5. Parcours d'une grandeur physique

### 1. Capteur (Sensor)

La grandeur physique (température, pression, vibration, etc.) est mesurée par un capteur ou un instrument de mesure.

### 2. Numérisation (Digitization)

Transformation de la grandeur physique en signal numérique exploitable.

### 3. Transmission (Transmission / Communication)

Passage des données du capteur vers le réseau (Wi-Fi, LoRa, ZigBee, 4G/5G, etc.), souvent via une passerelle IoT (IoT Gateway).

### 4. Acquisition / Collecte (Data Ingestion)

- Les données brutes sont acquises par une passerelle ou transmises directement via un protocole (MQTT, HTTP, CoAP, etc.)
- Cette étape garantit l'entrée des données dans le système

### 5. Traitement (Processing)

- Les données sont **validées** (contrôle qualité, suppression des valeurs aberrantes)
- Elles sont **transformées** (unités, normalisation, agrégation)
- Des algorithmes peuvent effectuer de la **détection d'événements** ou de l'**analyse en temps réel**

### 6. Stockage / Rétention (Data Storage / Retention)

- Les données traitées sont stockées dans une **base de données de séries temporelles** (Time Series Database)
- **Politique de rétention** : garder les données brutes à court terme, les moyennes ou agrégats à long terme

### 7. Visualisation / Affichage (Visualization / Dashboarding / Reporting)

- Les données sont affichées dans un **tableau de bord (dashboard)** ou un outil de reporting
- **Représentations possibles** : courbes, histogrammes, jauges, cartes thermiques (heatmaps)
- **Possibilité de générer des alertes** en cas de dépassement de seuil

---

## 6. Le système IoT CIEL

### 6.1 Architecture

Le système est basé sur **ChirpStack** (implémentation open-source de LoRaWAN® Network Server), représenté en violet dans le diagramme d'architecture.

### 6.2 Applications installées sur le serveur CIEL

- **Mosquitto** : Broker MQTT
- **ChirpStack** : Serveur LoRaWAN (abonne les messages MQTT, gère la logique LoRaWAN)
  - PostgreSQL
  - Redis
- **ChirpStack Gateway Bridge** : traduit les trames LoRa vers MQTT
  - Option 1 : Pour les passerelles classiques utilisant le protocole Semtech UDP (legacy)
  - Option 2 : Pour les passerelles modernes utilisant le protocole Basic Station (Semtech)
- **ChirpStack REST API** : API REST pour l'intégration
- **MQTTX Web** : client MQTT en WebSocket
- **MQTT Logger** : Mosquitto utilisé pour faire un log de tous les messages
- **MQTT LogWeb** : affiche une page web des logs de mqtt_logger
- **Modbus to MQTT** : Client Modbus, envoi périodique en MQTT

### 6.3 Serveur MQTT

- **IP** : 172.17.50.232
- **Port** : 1883

#### 6.3.1 Topics MQTT

**Règles générales pour les Topics :**

- Utiliser `/` comme séparateur logique
- Éviter les caractères spéciaux
- Ne pas commencer ou finir par `/`
- Ne pas utiliser de caractères réservés (`+`, `#`) dans les noms de topic eux-mêmes
  - `+` pour un niveau : `usine1/+/température` → reçoit toutes les températures de toutes les machines
  - `#` pour plusieurs niveaux : `usine1/#` → reçoit tous les topics de l'usine 1

**Arborescence pour les devices de la section CIEL :**

```
ciel/<salle>/<type_capteur>/<id_capteur>
```

Exemple : `ciel/a120/sono/sono1`

### 6.4 Devices IoT

#### 6.4.1 Sonomètre

- **Sono1** : `ciel/a120/sono/sono1` (IP : 172.17.50.207)
- **Sono2** : `ciel/a120/sono/sono2` (IP : 172.17.50.208)

#### 6.4.2 Capteur CO2 KF-800F

- **Capteur CO2 en salle A120** : `ciel/a120/co2/co21`

**4 registres disponibles :**

- **Working mode** : 0-3
  - 0 : off
  - 1 : auto
  - 2 : manual
  - 3 : timing
- **Current Wind speed** : 0, 1, 3
  - 0 : stop
  - 1 : low speed
  - 3 : high speed
- **CO2** : 350-3000 ppm
- **Température** : 0-50 °C

#### 6.4.3 Passerelle LoRa

Topics :
- `eu868/gateway/a84041fdfe27505c/state/`
- `eu868/gateway/a84041fdfe27505c/event/`
- `eu868/gateway/a84041fdfe27505c/command/`

#### 6.4.4 Capteur de position GPS LoRa (Liligo + GPS)

Topic :
- `Application/7eb7a482-3b04-45bd-85b5-993237156415/device/70b3d57ed80027b2/event/up`

---

## 7. Annexes

### 7.1 Accès à des machines sur le réseau local à partir du container

**Problème :** Depuis le conteneur, l'accès à Internet fonctionne, mais l'accès aux machines du réseau local n'est pas possible.

**Explication :** Ce comportement s'explique par le modèle réseau de WSL2 : celui-ci utilise une interface virtuelle isolée du réseau local et connectée à Internet via un mécanisme de traduction d'adresses (NAT). Ainsi, le trafic sortant vers Internet est autorisé, mais le routage vers les autres machines du réseau local n'est pas pris en charge par défaut.

**Solution :** Créer un proxy sur WSL pour le serveur MQTT

**Flux :**
```
Container MQTT client → (connecte à Windows :1883)
                    ↓
            Proxy TCP (socat)
                    ↓
        Broker MQTT LAN (172.17.50.232:1883)
```

**Étapes de configuration :**

1. **Sur la session tp-docker → Dans un PowerShell en administrateur :**

```powershell
wsl --update --web-download
wsl –install -d Debian
```

Identifiants :
- Utilisateur : `eleve`
- Mot de passe : `eleve`

2. **Quitter et installer socat :**

```bash
exit
wsl -d Debian
sudo apt install socat
nohup socat TCP-LISTEN:1883,fork,reuseaddr TCP:172.17.50.232:1883 &
```

3. **Si blocage Docker Desktop :** Faire la mise à jour proposée. Il faut redémarrer le poste pour qu'elle se réalise.

4. **Dans le nœud Node-RED :** Utiliser `host.docker.internal` pour accéder au proxy

---

## Démarrage rapide

1. Assurez-vous que Docker Desktop est en cours d'exécution
2. Depuis la racine du dépôt, exécutez :
   ```bash
   docker compose up -d
   ```
3. Les services seront disponibles à :
   - **Mosquitto** : `tcp://localhost:1883`
   - **Node-RED** : `http://localhost:1880`
   - **InfluxDB** (si utilisé) : `http://localhost:8086`

---

## Structure des dossiers

- `mosquitto/` : contient `mosquitto.conf` et les dossiers de volumes `data/`, `log/`
- `influxdb/` : contient les dossiers de volumes vides `data/`, `config/`
- `nodered/` : contient le dossier de volume `data/` (Node-RED y écrira `flows.json` à l'exécution)
- `docker-compose.yml` : fichier de configuration Docker Compose

---

## Prochaines étapes

- Construire les flux Node-RED pour l'ingestion, la validation, le stockage et les alarmes
- Concevoir l'interface du Dashboard
- Tester avec les capteurs réels du système CIEL
- Optionnellement ajouter Grafana pour des tableaux de bord avancés
- Exporter les flux Node-RED pour les versionner dans le dépôt (JSON)
