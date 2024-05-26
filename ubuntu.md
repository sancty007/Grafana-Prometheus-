Bien sûr ! Voici un guide détaillé pour installer Prometheus et Grafana sur un serveur Ubuntu et les configurer pour surveiller un site web.

### Étape 1 : Préparation de l'environnement

1. **Mettez à jour votre système :**

   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

2. **Installez Docker (optionnel, si vous voulez utiliser Docker) :**
   ```bash
   sudo apt install -y docker.io
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

### Étape 2 : Installation de Prometheus

1. **Télécharger et installer Prometheus :**

   ```bash
   cd /opt
   sudo mkdir prometheus
   cd prometheus
   sudo wget https://github.com/prometheus/prometheus/releases/download/v2.44.0/prometheus-2.44.0.linux-amd64.tar.gz
   sudo tar -xvf prometheus-2.44.0.linux-amd64.tar.gz
   cd prometheus-2.44.0.linux-amd64
   ```

2. **Configurer Prometheus :**
   Créez un fichier de configuration `prometheus.yml` :

   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: "prometheus"
       static_configs:
         - targets: ["localhost:9090"]

     - job_name: "my_website"
       metrics_path: /probe
       params:
         module: [http_2xx]
       static_configs:
         - targets: ["http://mywebsite.com"] # Remplacez par l'URL de votre site web
       relabel_configs:
         - source_labels: [__address__]
           target_label: __param_target
         - source_labels: [__param_target]
           target_label: instance
         - target_label: __address__
           replacement: localhost:9115 # Adresse de l'exporter
   ```

3. **Créer un service systemd pour Prometheus :**

   ```bash
   sudo nano /etc/systemd/system/prometheus.service
   ```

   Ajoutez le contenu suivant :

   ```ini
   [Unit]
   Description=Prometheus
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=root
   ExecStart=/opt/prometheus/prometheus-2.44.0.linux-amd64/prometheus --config.file=/opt/prometheus/prometheus-2.44.0.linux-amd64/prometheus.yml
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

4. **Démarrer et activer le service Prometheus :**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start prometheus
   sudo systemctl enable prometheus
   ```

### Étape 3 : Installation de Grafana

1. **Ajouter le dépôt Grafana :**

   ```bash
   sudo apt install -y software-properties-common
   sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
   sudo apt update
   sudo apt install -y grafana
   ```

2. **Démarrer et activer Grafana :**

   ```bash
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

3. **Accéder à Grafana :**
   - Ouvrez votre navigateur et allez à `http://<your_server_ip>:3000`.
   - Connectez-vous avec les identifiants par défaut (admin/admin) et changez le mot de passe lors de la première connexion.

### Étape 4 : Configuration de Grafana

1. **Ajouter Prometheus comme source de données :**

   - Allez dans `Configuration` > `Data Sources`.
   - Cliquez sur `Add data source`.
   - Sélectionnez `Prometheus`.
   - Configurez l'URL de Prometheus (`http://localhost:9090`) et sauvegardez.

2. **Créer un tableau de bord :**
   - Allez dans `Create` > `Dashboard`.
   - Cliquez sur `Add Panel`.
   - Dans la section de requête, sélectionnez Prometheus comme source de données.
   - Ajoutez une requête pour surveiller une métrique de votre site web.

### Étape 5 : Installation et Configuration du Blackbox Exporter

1. **Télécharger et installer le Blackbox Exporter :**

   ```bash
   cd /opt
   sudo mkdir blackbox_exporter
   cd blackbox_exporter
   sudo wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.23.0/blackbox_exporter-0.23.0.linux-amd64.tar.gz
   sudo tar -xvf blackbox_exporter-0.23.0.linux-amd64.tar.gz
   cd blackbox_exporter-0.23.0.linux-amd64
   ```

2. **Créer un service systemd pour le Blackbox Exporter :**

   ```bash
   sudo nano /etc/systemd/system/blackbox_exporter.service
   ```

   Ajoutez le contenu suivant :

   ```ini
   [Unit]
   Description=Blackbox Exporter
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=root
   ExecStart=/opt/blackbox_exporter/blackbox_exporter-0.23.0.linux-amd64/blackbox_exporter --config.file=/opt/blackbox_exporter/blackbox_exporter-0.23.0.linux-amd64/blackbox.yml
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

3. **Créer le fichier de configuration pour le Blackbox Exporter :**

   ```bash
   sudo nano /opt/blackbox_exporter/blackbox_exporter-0.23.0.linux-amd64/blackbox.yml
   ```

   Ajoutez le contenu suivant :

   ```yaml
   modules:
     http_2xx:
       prober: http
       timeout: 5s
       http:
         valid_http_versions: ["HTTP/1.1", "HTTP/2"]
         valid_status_codes: [] # Accept any 2xx response code
         method: GET
   ```

4. **Démarrer et activer le service Blackbox Exporter :**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start blackbox_exporter
   sudo systemctl enable blackbox_exporter
   ```

### Étape 6 : Mise à jour de la Configuration de Prometheus

1. **Mettre à jour le fichier `prometheus.yml` pour inclure le Blackbox Exporter :**

   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: "prometheus"
       static_configs:
         - targets: ["localhost:9090"]

     - job_name: "blackbox"
       metrics_path: /probe
       params:
         module: [http_2xx]
       static_configs:
         - targets:
             - http://mywebsite.com # Remplacez par l'URL de votre site web
       relabel_configs:
         - source_labels: [__address__]
           target_label: __param_target
         - source_labels: [__param_target]
           target_label: instance
         - target_label: __address__
           replacement: localhost:9115 # Adresse de l'exporter
   ```

2. **Redémarrer Prometheus :**
   ```bash
   sudo systemctl restart prometheus
   ```

### Étape 7 : Création de Panneaux Grafana pour le Blackbox Exporter

Pour accéder à Grafana après l'avoir installé sur un serveur Ubuntu, suivez les étapes ci-dessous :

### Accéder à Grafana

1. **Ouvrir un navigateur web :**

   - Sur votre machine locale, ouvrez un navigateur web (Chrome, Firefox, Edge, etc.).

2. **Entrer l'adresse IP du serveur et le port de Grafana :**

   - Dans la barre d'adresse du navigateur, entrez l'adresse IP de votre serveur Ubuntu suivi du port 3000. Par exemple :

     ```
     http://<adresse_ip_du_serveur>:3000
     ```

   - Remplacez `<adresse_ip_du_serveur>` par l'adresse IP réelle de votre serveur. Par exemple, si votre adresse IP est `192.168.1.10`, vous devrez entrer :

     ```
     http://192.168.1.10:3000
     ```

### Première Connexion à Grafana

1. **Page de connexion Grafana :**

   - Vous devriez voir la page de connexion de Grafana.

2. **Utiliser les identifiants par défaut :**

   - **Nom d'utilisateur :** `admin`
   - **Mot de passe :** `admin`

3. **Changer le mot de passe :**
   - Lors de la première connexion, Grafana vous demandera de changer le mot de passe. Entrez un nouveau mot de passe et confirmez-le.

### Ajouter un Nouveau Panneau dans Grafana

1. **Ajouter Prometheus comme source de données :**

   - Allez dans `Configuration` (l'icône d'engrenage) > `Data Sources`.
   - Cliquez sur `Add data source`.
   - Sélectionnez `Prometheus`.
   - Configurez l'URL de Prometheus (http://localhost:9090 si Grafana et Prometheus sont sur la même machine, sinon utilisez l'adresse IP correcte de votre serveur Prometheus) et cliquez sur `Save & Test`.

2. **Créer un tableau de bord :**

   - Allez dans `Create` (l'icône plus) > `Dashboard`.
   - Cliquez sur `Add new panel`.

3. **Configurer une requête pour surveiller des métriques :**

   - Dans la section `Query`, sélectionnez `Prometheus` comme source de données.
   - Ajoutez une requête pour surveiller une métrique. Par exemple, pour surveiller la disponibilité de votre site web avec le Blackbox Exporter :
     ```promql
     probe_success{job="blackbox"}
     ```
   - Pour surveiller la latence :
     ```promql
     probe_duration_seconds{job="blackbox"}
     ```

4. **Configurer le graphique :**

   - Configurez les options du graphique selon vos besoins (titre, type de visualisation, etc.).

5. **Sauvegarder et visualiser le tableau de bord :**
   - Cliquez sur `Apply` pour ajouter le panneau au tableau de bord.
   - Cliquez sur l'icône de disquette pour sauvegarder le tableau de bord. Donnez-lui un nom et cliquez sur `Save`.

### Résolution des Problèmes de Connexion

Si vous ne pouvez pas accéder à Grafana, vérifiez les points suivants :

1. **Firewall :**

   - Assurez-vous que le port 3000 est ouvert sur votre serveur Ubuntu. Vous pouvez le faire avec `ufw` :
     ```bash
     sudo ufw allow 3000/tcp
     ```

2. **Service Grafana :**

   - Vérifiez que le service Grafana est en cours d'exécution :
     ```bash
     sudo systemctl status grafana-server
     ```

3. **Adresse IP correcte :**
   - Assurez-vous d'utiliser la bonne adresse IP de votre serveur Ubuntu.

En suivant ces étapes, vous devriez pouvoir accéder à Grafana, configurer Prometheus comme source de données, et créer des tableaux de bord pour surveiller vos métriques.

1. **Accéder à Grafana et ajouter un nouveau panneau :**

   - Allez dans `Create` > `Dashboard` > `Add Panel`.

2. **Configurer une requête pour surveiller la disponibilité :**

   - Dans la section de requête, sélectionnez Prometheus comme source de données.
   - Ajoutez la requête suivante pour vérifier l'état du site web :
     ```promql
     probe_success{job="blackbox"}
     ```

3. **Configurer une requête pour surveiller la latence :**

   - Ajoutez une autre requête pour vérifier la latence du site web :
     ```promql
     probe_duration_seconds{job="blackbox"}
     ```

4. **Sauvegarder et visualiser votre tableau de bord :**
   - Configurez les graphiques comme vous le souhaitez et sauvegardez le tableau de bord.

### Conclusion

Vous avez maintenant installé et configuré Prometheus et Grafana sur un serveur Ubuntu pour surveiller un site web. Vous utilisez le Blackbox Exporter pour vérifier la disponibilité et la performance de votre site web et afficher ces informations dans Grafana. Cela vous permet de détecter rapidement les problèmes et d'analyser les performances de votre site en temps réel.
