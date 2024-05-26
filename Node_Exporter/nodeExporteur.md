Node Exporter est un composant de l'écosystème Prometheus qui collecte des métriques matérielles et d'exploitation du système d'exploitation des nœuds (serveurs). Il est principalement utilisé pour surveiller les ressources système, telles que la mémoire, le processeur, le stockage et les interfaces réseau. Node Exporter fonctionne comme un agent sur les machines surveillées et expose les métriques sous forme d'un endpoint HTTP que Prometheus peut scraper (récupérer).

### Fonctionnalités principales de Node Exporter :

- **Surveillance des ressources système** :

  - **CPU** : Utilisation du CPU par cœur, par utilisateur/système et par processus.
  - **Mémoire** : Utilisation de la mémoire, y compris les statistiques sur la mémoire swap.
  - **Disques** : Utilisation des disques, temps d'attente et temps de service.
  - **Réseau** : Statistiques sur les interfaces réseau, paquets envoyés/recus, erreurs, etc.
  - **Système de fichiers** : Utilisation du système de fichiers, espace disponible, etc.

- **Simplicité d'installation** : Node Exporter est un binaire autonome qui peut être facilement déployé sur les serveurs.
- **Extensibilité** : Il permet l'ajout de nouveaux collecteurs de métriques via des modules ou des scripts personnalisés.
- **Support multi-plateforme** : Fonctionne sur Linux, Windows et d'autres systèmes d'exploitation UNIX-like.

### Comment installer et utiliser Node Exporter :

#### Installation de Node Exporter :

1. **Téléchargement du binaire** :
   Téléchargez la dernière version de Node Exporter à partir de la page des [releases GitHub](https://github.com/prometheus/node_exporter/releases).

   Exemple pour Linux :

   ```sh
   wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
   tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz
   cd node_exporter-1.3.1.linux-amd64
   ```

2. **Exécution de Node Exporter** :
   Démarrez Node Exporter :

   ```sh
   ./node_exporter
   ```

   Par défaut, Node Exporter écoute sur le port 9100. Vous pouvez vérifier cela en visitant `http://<votre_ip>:9100/metrics` dans votre navigateur.

#### Intégration avec Prometheus :

1. **Configurer Prometheus pour scraper Node Exporter** :
   Ajoutez une nouvelle section dans votre fichier de configuration `prometheus.yml` pour inclure Node Exporter comme cible de scraping.

   Exemple :

   ```yaml
   scrape_configs:
     - job_name: "node_exporter"
       static_configs:
         - targets: ["<votre_ip>:9100"]
   ```

2. **Redémarrer Prometheus** :
   Après avoir mis à jour la configuration, redémarrez Prometheus pour appliquer les changements.

### Visualisation dans Grafana :

1. **Ajouter une source de données Prometheus** :
   Configurez Prometheus comme source de données dans Grafana si ce n'est pas déjà fait.

2. **Importer un tableau de bord Node Exporter** :
   Il existe des tableaux de bord prédéfinis pour Node Exporter disponibles sur le site de Grafana. Vous pouvez les importer en utilisant leur ID. Par exemple, le tableau de bord avec l'ID `1860` est un tableau de bord populaire pour Node Exporter.

   - Allez dans `Create` (icône de plus) > `Import`.
   - Entrez l'ID `1860` et suivez les instructions pour l'importer.

En résumé, Node Exporter est un outil essentiel pour la surveillance des ressources système, offrant une grande visibilité sur les performances et l'état des serveurs, et il s'intègre parfaitement avec Prometheus et Grafana pour une surveillance complète et des visualisations puissantes.

---

### Node Exporter associer à notre projet :

Pourquoi ajouter Node Exporter :

Ajouter Node Exporter à votre infrastructure de surveillance présente plusieurs avantages :

Surveillance des ressources système : Node Exporter fournit des métriques détaillées sur l'utilisation du CPU, de la mémoire, des disques et des interfaces réseau. Cela permet de détecter et de diagnostiquer les problèmes de performances et de capacité.
Intégration avec Prometheus et Grafana : Node Exporter s'intègre facilement avec Prometheus pour la collecte des métriques et avec Grafana pour la visualisation, permettant une surveillance complète et centralisée.
Alertes proactives : En collectant des métriques système avec Node Exporter, vous pouvez configurer des alertes dans Prometheus pour être averti en cas de problèmes, comme une utilisation excessive de la mémoire ou du CPU.

Pour utiliser Docker, Prometheus, Grafana et Node Exporter sur une machine Windows, vous pouvez suivre les étapes suivantes. Ces instructions vous guideront sur la façon de configurer tout l'environnement de surveillance en utilisant Docker sur Windows.

### 1. Installation de Docker sur Windows

1. **Télécharger et installer Docker Desktop pour Windows :**
   - Téléchargez Docker Desktop depuis [Docker Hub](https://www.docker.com/products/docker-desktop).
   - Suivez les instructions d'installation.
   - Assurez-vous que Docker Desktop est en cours d'exécution après l'installation.

### 2. Configuration de Prometheus

1. **Créer un Réseau Docker Personnalisé :**

   - Ouvrez un terminal PowerShell ou une invite de commande.

   ```sh
   docker network create monitoring
   ```

2. **Créer un fichier de configuration `prometheus.yml` :**

   - Créez un fichier `prometheus.yml` dans un répertoire de votre choix, par exemple `C:\docker\prometheus`.
   - Ajoutez la configuration suivante au fichier :

     ```yaml
     global:
       scrape_interval: 15s

     scrape_configs:
       - job_name: "prometheus"
         static_configs:
           - targets: ["localhost:9090"]
       - job_name: "node_exporter"
         static_configs:
           - targets: ["node_exporter:9100"]
     ```

3. **Lancer le conteneur Prometheus :**
   ```sh
   docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v C:\docker\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
   ```

### 3. Installation et Configuration de Grafana

1. **Lancer le conteneur Grafana :**

   ```sh
   docker run -d --name=grafana --network=monitoring -p 3000:3000 grafana/grafana
   ```

2. **Configurer Prometheus comme Source de Données dans Grafana :**
   - Accédez à Grafana en ouvrant votre navigateur et en allant à `http://localhost:3000`.
   - Connectez-vous avec les identifiants par défaut (`admin`/`admin`).
   - Allez dans `Plugins and data` dans le menu principal.
   - Sélectionnez `Data Sources`.
   - Cliquez sur `Add data source`.
   - Choisissez `Prometheus` dans la liste des sources de données.
   - Configurez l'URL à `http://prometheus:9090` et cliquez sur `Save & Test`.

### 4. Ajout de Node Exporter pour la Collecte des Métriques Système

1. **Lancer Node Exporter dans un conteneur Docker :**
   ```sh
   docker run -d --name=node_exporter --network=monitoring -p 9100:9100 prom/node-exporter
   ```

### 5. Création et Personnalisation des Tableaux de Bord Grafana

1. **Créer des Tableaux de Bord Grafana :**

   - Allez dans `Create` (icône de plus) > `Dashboard` > `Add new panel`.
   - Configurez une requête Prometheus pour obtenir des métriques, par exemple :
     ```promql
     up
     ```

2. **Importer des Tableaux de Bord Prédéfinis :**
   - Allez dans `Create` (icône de plus) > `Import`.
   - Entrez l'ID `1860` (tableau de bord populaire pour Node Exporter) et cliquez sur `Load`.
   - Sélectionnez la source de données Prometheus que vous avez configurée et cliquez sur `Import`.

### Récapitulatif du Document Mis à Jour

````markdown
## Surveillance avec Docker, Prometheus et Grafana

### 1. Installation de Docker sur Windows

1. **Télécharger et installer Docker Desktop pour Windows :**
   - Téléchargez Docker Desktop depuis [Docker Hub](https://www.docker.com/products/docker-desktop).
   - Suivez les instructions d'installation.
   - Assurez-vous que Docker Desktop est en cours d'exécution après l'installation.

### 2. Installation et Configuration de Prometheus

1. **Créer un Réseau Docker Personnalisé :**
   ```sh
   docker network create monitoring
   ```
````

2. **Créer un fichier de configuration `prometheus.yml` :**

   - Créez un fichier `prometheus.yml` dans un répertoire de votre choix, par exemple `C:\docker\prometheus`.
   - Ajoutez la configuration suivante au fichier :

     ```yaml
     global:
       scrape_interval: 15s

     scrape_configs:
       - job_name: "prometheus"
         static_configs:
           - targets: ["localhost:9090"]
       - job_name: "node_exporter"
         static_configs:
           - targets: ["node_exporter:9100"]
     ```

3. **Lancer le conteneur Prometheus :**
   ```sh
   docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v C:\docker\prometheus\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
   ```

### 3. Installation et Configuration de Grafana

1. **Lancer le conteneur Grafana :**

   ```sh
   docker run -d --name=grafana --network=monitoring -p 3000:3000 grafana/grafana
   ```

2. **Configurer Prometheus comme Source de Données dans Grafana :**
   - Accédez à Grafana en ouvrant votre navigateur et en allant à `http://localhost:3000`.
   - Connectez-vous avec les identifiants par défaut (`admin`/`admin`).
   - Allez dans `Plugins and data` dans le menu principal.
   - Sélectionnez `Data Sources`.
   - Cliquez sur `Add data source`.
   - Choisissez `Prometheus` dans la liste des sources de données.
   - Configurez l'URL à `http://prometheus:9090` et cliquez sur `Save & Test`.

### 4. Ajout de Node Exporter pour la Collecte des Métriques Système

1. **Lancer Node Exporter dans un conteneur Docker :**
   ```sh
   docker run -d --name=node_exporter --network=monitoring -p 9100:9100 prom/node-exporter
   ```

### 5. Création et Personnalisation des Tableaux de Bord Grafana

1. **Créer des Tableaux de Bord Grafana :**

   - Allez dans `Create` (icône de plus) > `Dashboard` > `Add new panel`.
   - Configurez une requête Prometheus pour obtenir des métriques, par exemple :
     ```promql
     up
     ```

2. **Importer des Tableaux de Bord Prédéfinis :**
   - Allez dans `Create` (icône de plus) > `Import`.
   - Entrez l'ID `1860` (tableau de bord populaire pour Node Exporter) et cliquez sur `Load`.
   - Sélectionnez la source de données Prometheus que vous avez configurée et cliquez sur `Import`.

### Pourquoi utiliser Prometheus et Grafana

- **Prometheus** est idéal pour la surveillance en raison de sa capacité à gérer un grand volume de métriques, sa flexibilité dans la configuration des collectes de métriques et sa puissante fonctionnalité d'alerte.
- **Grafana** est préféré pour la visualisation des données en raison de son interface utilisateur conviviale, sa capacité à intégrer plusieurs sources de données, et ses fonctionnalités avancées de tableau de bord qui facilitent l'analyse et la visualisation des métriques collectées.

Avec cette configuration, vous devriez maintenant avoir un système de surveillance fonctionnel avec Docker, Prometheus, Grafana et Node Exporter, prêt à surveiller vos serveurs et applications.

```

```
