### Rôle des Services

- **Nextcloud** : Fournit l'interface web de Nextcloud.
- **Prometheus** : Utilisé pour surveiller les métriques.
- **Grafana** : Utilisé pour visualiser les métriques surveillées par Prometheus.
- **Elasticsearch** : Utilisé pour stocker et rechercher les logs et les données.
- **Logstash** : Utilisé pour collecter, traiter et envoyer les logs à Elasticsearch.
- **Kibana** : Utilisé pour visualiser les données stockées dans Elasticsearch.
- **ML (Machine Learning)** : Exécute le script de détection des anomalies.

### Inclusion de Logstash

Logstash est déjà inclus dans le fichier `docker-compose.yml` avec les paramètres nécessaires. Il collecte les logs et les envoie à Elasticsearch.

### Exemple Complet de `docker-compose.yml`

Voici le fichier `docker-compose.yml` complet avec des commentaires pour chaque section :

```yaml
version: "3.7"

networks:
  monitoring:
    driver: bridge

services:
  nextcloud:
    image: nextcloud
    ports:
      - "8080:80"
    volumes:
      - nextcloud:/var/www/html
    depends_on:
      - db
    networks:
      - monitoring

  db:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD: example
    volumes:
      - db:/var/lib/mysql
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    networks:
      - monitoring

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
      - esdata:/usr/share/elasticsearch/data
    networks:
      - monitoring

  logstash:
    image: docker.elastic.co/logstash/logstash:7.10.0
    ports:
      - "5000:5000"
      - "9600:9600"
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    networks:
      - monitoring
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.0
    ports:
      - "5601:5601"
    networks:
      - monitoring
    depends_on:
      - elasticsearch

  ml:
    image: ml-script:latest
    depends_on:
      - elasticsearch
      - prometheus
    networks:
      - monitoring

volumes:
  nextcloud:
  db:
  esdata:
```

### Détails des Configurations

1. **Logstash Configuration (logstash.conf)** :
   - Assurez-vous que votre fichier `logstash.conf` est correctement configuré pour lire les logs et les envoyer à Elasticsearch.

2. **Elasticsearch et Kibana** :
   - Elasticsearch collecte les logs envoyés par Logstash.
   - Kibana permet de visualiser ces logs.

3. **ML Service** :
   - Utilise les données d'Elasticsearch et de Prometheus pour la détection des anomalies.

### Exécuter les Services

1. **Construire l'image Docker du service de machine learning :**

   ```sh
   docker build -t ml-script:latest .
   ```

2. **Lancer tous les services :**

   ```sh
   docker-compose up -d
   ```

3. **Accéder à Grafana :**

   Ouvrez un navigateur web et allez à `http://localhost:3000` pour accéder à Grafana.

4. **Vérifier Elasticsearch et Kibana :**

   - **Elasticsearch** : `http://localhost:9200`
   - **Kibana** : `http://localhost:5601`
