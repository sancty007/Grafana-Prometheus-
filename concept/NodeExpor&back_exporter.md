Node Exporter et Blackbox Exporter servent à des fins différentes dans la collecte de métriques pour Prometheus. Voici une explication de leurs rôles et de leurs différences :

### Node Exporter

- **Rôle** : Node Exporter est utilisé pour collecter des métriques système détaillées de l'hôte sur lequel il est exécuté. Il est particulièrement utile pour surveiller les ressources système telles que le CPU, la mémoire, le disque et les interfaces réseau.
- **Métriques collectées** :
  - Utilisation du CPU
  - Utilisation de la mémoire
  - Utilisation du disque
  - Statistiques du réseau
  - Statistiques des systèmes de fichiers
  - D'autres métriques système de bas niveau
- **Usage typique** : Surveillance de la santé et de la performance des serveurs et des machines physiques ou virtuelles.

### Blackbox Exporter

- **Rôle** : Blackbox Exporter est utilisé pour surveiller la disponibilité et la performance des points de terminaison externes (comme des URL HTTP/HTTPS, des services TCP, etc.) en effectuant des requêtes de type "probes". Il teste si des services externes sont accessibles et mesurent la latence de réponse.
- **Métriques collectées** :
  - Disponibilité (succès ou échec des probes)
  - Latence des requêtes
  - Code de statut HTTP (pour les probes HTTP/HTTPS)
  - Temps de réponse DNS, TCP, ICMP, etc.
- **Usage typique** : Surveillance de la disponibilité des applications web, des API, des services réseau, et autres points de terminaison externes.

### Dans Votre Configuration

#### Node Exporter

- **Configuration** :
  ```yaml
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
  ```
- **But** : Collecte des métriques système de la machine exécutant Nextcloud (ou toute autre machine dont vous voulez surveiller les ressources).

#### Blackbox Exporter

- **Configuration** :
  ```yaml
  - job_name: "blackbox"
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets: ["localhost:3084"]
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: blackbox_exporter:9115
  ```
- **But** : Surveillance de la disponibilité et des performances de l'application Nextcloud via HTTP.

### Résumé des Différences

- **Node Exporter** : Surveillance interne des ressources système de l'hôte.
- **Blackbox Exporter** : Surveillance externe de la disponibilité et de la performance des services via des probes.

Les deux exportateurs fournissent des vues complémentaires sur la santé de votre infrastructure. Node Exporter vous informe sur l'état et l'utilisation des ressources de vos serveurs, tandis que Blackbox Exporter vous informe sur la disponibilité et la performance des services que ces serveurs exposent.

### Exemple d'Utilisation Complète

- **Fichier `prometheus.yml` mis à jour** :

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
        - targets: ["localhost:3084"]
      relabel_configs:
        - source_labels: [__address__]
          target_label: __param_target
        - source_labels: [__param_target]
          target_label: instance
        - target_label: __address__
          replacement: blackbox_exporter:9115

    - job_name: "node"
      static_configs:
        - targets: ["localhost:9100"]
  ```

En utilisant cette configuration, vous pouvez surveiller à la fois les métriques internes du serveur via Node Exporter et les métriques externes de disponibilité de votre application Nextcloud via Blackbox Exporter.
