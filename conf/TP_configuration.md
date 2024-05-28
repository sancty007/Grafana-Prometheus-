1 - installation de docker
2 - installation de prometeuse , fonctionnement , pourquoi prometeuse
pourquoi grafana que les autres (entre astuce )

Excellent, cela signifie que Docker est maintenant correctement installé et fonctionne sur votre système Windows ! Voici la suite pour continuer à travailler avec Docker, Prometheus et Grafana dans un environnement Ubuntu sur Docker.

### Étape Suivante : Installer Prometheus et Grafana avec Docker

1. **Créer un Réseau Docker Personnalisé :**

   - Pour que les conteneurs Docker puissent communiquer entre eux, il est utile de créer un réseau Docker personnalisé.

   ```bash
   docker network create monitoring
   ```

   **Verifier les reseaux disponible :**

   ```bash
    docker network ls
   ```

   **supprimer un reseaux disponible :**

   ```bash
    docker network rm my_network
   ```

   ***

2. **Lancer Prometheus :**

   - Créez un fichier de configuration `prometheus.yml` sur votre machine hôte :

     ```yaml
     global:
       scrape_interval: 15s

     scrape_configs:
       - job_name: "prometheus"
         static_configs:
           - targets: ["localhost:9090"]
     ```

   - Lancer le conteneur Prometheus en utilisant ce fichier de configuration :

     ```bash
     docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
     ```

     ```bash
      docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v prometheus.yml:/etc/prometheus.yml prom/prometheus
     ```

     ```powershell
      docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v ${PWD}\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus

     ```

3. **Lancer Grafana :**

   - Lancer le conteneur Grafana et connectez-le au même réseau Docker :
     ```bash
     docker run -d --name=grafana --network=monitoring -p 3000:3000 grafana/grafana
     ```

Voici la mise à jour de la section "Configurer Prometheus comme Source de Données dans Grafana" :

4. **Configurer Prometheus comme Source de Données dans Grafana :**

   - Accédez à Grafana en ouvrant votre navigateur et en allant à `http://localhost:3000`.
   - Connectez-vous avec les identifiants par défaut (`admin`/`admin`).
   - Ajoutez Prometheus comme source de données :
     - Allez dans `Plugins and data` (ou `Configuration` dans certaines versions) dans le menu principal.
     - Sélectionnez `Data Sources`.
     - Cliquez sur `Add data source`.
     - Choisissez `Prometheus` dans la liste des sources de données.
     - Configurez l'URL à `http://prometheus:9090` et cliquez sur `Save & Test`.

5. **Créer des Tableaux de Bord Grafana :**

   - Une fois Prometheus ajouté comme source de données, vous pouvez commencer à créer des tableaux de bord pour visualiser les données.
   - Allez dans `Create` (icône de plus) > `Dashboard` > `Add new panel`.
   - Configurez une requête Prometheus pour obtenir des métriques, par exemple :

   ```promql
     up
   ```

### Étape Optionnelle : Utiliser des Exportateurs pour Plus de Métriques

1. **Blackbox Exporter :**

   - Le Blackbox Exporter est utilisé pour surveiller la disponibilité des points de terminaison HTTP.
   - Lancer le Blackbox Exporter :

     ```bash
     docker run -d --name=blackbox_exporter --network=monitoring -p 9115:9115 prom/blackbox-exporter
     ```

   - Mettre à jour votre fichier `prometheus.yml` pour inclure le Blackbox Exporter :

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
               - http://example.com # l'URL du site web
         relabel_configs:
           - source_labels: [__address__]
             target_label: __param_target
           - source_labels: [__param_target]
             target_label: instance
           - target_label: __address__
             replacement: blackbox_exporter:9115 # Adresse de l'exporter
     ```

   - Redémarrer le conteneur Prometheus pour appliquer les changements :
     ```bash
     docker restart prometheus
     ```

### Documentation et Ressources Supplémentaires

- **Documentation Prometheus :**

  - [Prometheus Getting Started](https://prometheus.io/docs/introduction/first_steps/)
  - [Prometheus Configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

- **Documentation Grafana :**

  - [Grafana Getting Started](https://grafana.com/docs/grafana/latest/getting-started/)
  - [Adding Data Sources](https://grafana.com/docs/grafana/latest/datasources/add-a-data-source/)

- **Exportateurs :**
  - [Prometheus Exporters](https://prometheus.io/docs/instrumenting/exporters/)

En suivant ces étapes, vous devriez avoir un système de surveillance fonctionnel avec Prometheus et Grafana. Bonne chance avec votre projet, et n'hésitez pas à revenir si vous avez d'autres questions ou besoin de plus d'assistance !
