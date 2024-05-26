Pour installer Prometheus et Grafana et les configurer pour surveiller un site web, suivez ces étapes détaillées. Nous utiliserons Docker pour simplifier le processus, mais vous pouvez également installer ces outils directement sur votre serveur.

### Étape 1 : Préparation de l'environnement

Assurez-vous d'avoir Docker installé sur votre machine. Si ce n'est pas le cas, vous pouvez télécharger et installer Docker depuis [leur site officiel](https://www.docker.com/get-started).

### Étape 2 : Installation de Prometheus

1. **Créer un répertoire pour Prometheus :**

   ```bash
   mkdir prometheus
   cd prometheus
   ```

2. **Créer un fichier de configuration `prometheus.yml` :**

   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: "prometheus"
       static_configs:
         - targets: ["localhost:9090"]

     - job_name: "my_website"
       static_configs:
         - targets: ["mywebsite.com"] # Remplacez par l'URL de votre site web
   ```

3. **Démarrer Prometheus avec Docker :**
   ```bash
   docker run -d \
     -p 9090:9090 \
     -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml \
     --name prometheus \
     prom/prometheus
   ```

### Étape 3 : Installation de Grafana

1. **Démarrer Grafana avec Docker :**

   ```bash
   docker run -d \
     -p 3000:3000 \
     --name grafana \
     grafana/grafana
   ```

2. **Accéder à Grafana :**
   - Ouvrez votre navigateur et allez à `http://localhost:3000`.
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

### Étape 5 : Surveillance d'un site web avec Blackbox Exporter

Le Blackbox Exporter est utilisé pour surveiller la disponibilité et la performance des endpoints HTTP, HTTPS, DNS, TCP, etc.

1. **Démarrer le Blackbox Exporter avec Docker :**

   ```bash
   docker run -d \
     -p 9115:9115 \
     --name blackbox_exporter \
     prom/blackbox-exporter
   ```

2. **Configurer Prometheus pour scrapper le Blackbox Exporter :**

   - Ajoutez la configuration suivante à votre `prometheus.yml` :

     ```yaml
     scrape_configs:
       - job_name: "prometheus"
         static_configs:
           - targets: ["localhost:9090"]

       - job_name: "blackbox"
         metrics_path: /probe
         params:
           module: [http_2xx] # Protocole que vous souhaitez tester
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

3. **Redémarrer Prometheus :**
   ```bash
   docker stop prometheus
   docker rm prometheus
   docker run -d \
     -p 9090:9090 \
     -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml \
     --name prometheus \
     prom/prometheus
   ```

### Étape 6 : Création de Panneaux Grafana pour le Blackbox Exporter

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

Vous avez maintenant installé et configuré Prometheus et Grafana pour surveiller un site web. Vous pouvez utiliser le Blackbox Exporter pour vérifier la disponibilité et la performance de votre site web et afficher ces informations dans Grafana. Cela vous permet de détecter rapidement les problèmes et d'analyser les performances de votre site en temps réel.
