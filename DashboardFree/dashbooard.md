Pour votre challenge de surveillance avec Prometheus et Grafana, vous pouvez utiliser des tableaux de bord (dashboards) préconfigurés disponibles sur la plateforme Grafana. Ces tableaux de bord couvrent une variété d'applications et de services. Voici quelques tableaux de bord complets et populaires que vous pouvez utiliser :

### 1. Node Exporter Full

**Description :** Ce tableau de bord fournit des métriques détaillées sur les performances des serveurs et des machines. Il affiche des informations sur l'utilisation du CPU, de la mémoire, des disques, du réseau, et plus encore.

**Tableau de bord :** [Node Exporter Full](https://grafana.com/grafana/dashboards/1860)

### 2. Prometheus 2.0 Stats

**Description :** Ce tableau de bord affiche des statistiques et des métriques sur le serveur Prometheus lui-même, y compris l'utilisation des ressources, le taux de scrape, les erreurs, et la durée des requêtes.

**Tableau de bord :** [Prometheus 2.0 Stats](https://grafana.com/grafana/dashboards/3662)

### 3. Docker and System Monitoring

**Description :** Ce tableau de bord est conçu pour surveiller les conteneurs Docker ainsi que le système hôte. Il fournit des informations sur l'utilisation des ressources des conteneurs, l'état des conteneurs, et les métriques système.

**Tableau de bord :** [Docker and System Monitoring](https://grafana.com/grafana/dashboards/893)

### 4. Kubernetes Cluster Monitoring (via Prometheus)

**Description :** Ce tableau de bord surveille un cluster Kubernetes en utilisant les métriques collectées par Prometheus. Il affiche des informations sur les pods, les nœuds, les namespaces, les volumes, et plus encore.

**Tableau de bord :** [Kubernetes Cluster Monitoring (via Prometheus)](https://grafana.com/grafana/dashboards/6417)

### 5. MySQL Overview

**Description :** Ce tableau de bord fournit une vue d'ensemble des métriques de performance MySQL, y compris les statistiques de requêtes, l'utilisation des tables, les connexions, et les performances des disques.

**Tableau de bord :** [MySQL Overview](https://grafana.com/grafana/dashboards/7362)

### 6. NGINX Log Monitoring

**Description :** Ce tableau de bord surveille les logs de NGINX, fournissant des informations sur les requêtes, les erreurs, les temps de réponse, et plus encore.

**Tableau de bord :** [NGINX Log Monitoring](https://grafana.com/grafana/dashboards/10000)

### Comment Importer un Tableau de Bord dans Grafana

1. **Accédez à Grafana** : Ouvrez votre navigateur et allez à `http://localhost:3000`.

2. **Connectez-vous** : Utilisez les identifiants par défaut (`admin`/`admin`).

3. **Accédez à l'Importation de Tableaux de Bord** :

   - Cliquez sur l'icône "+" dans la barre latérale gauche.
   - Sélectionnez "Import".

4. **Importer le Tableau de Bord** :
   - Dans le champ "Import via grafana.com", entrez l'ID du tableau de bord que vous souhaitez importer (par exemple, `1860` pour Node Exporter Full).
   - Cliquez sur "Load".
   - Sélectionnez la source de données (par exemple, Prometheus) et cliquez sur "Import".

### Exemples de Tableaux de Bord et Comment les Utiliser

#### Node Exporter Full

- **URL :** [https://grafana.com/grafana/dashboards/1860](https://grafana.com/grafana/dashboards/1860)
- **ID :** 1860

```bash
# Ouvrir Grafana dans le navigateur
http://localhost:3000

# Importer le tableau de bord
1. Cliquez sur l'icône "+" > "Import".
2. Entrez l'ID 1860 et cliquez sur "Load".
3. Sélectionnez votre source de données Prometheus et cliquez sur "Import".
```

#### Prometheus 2.0 Stats

- **URL :** [https://grafana.com/grafana/dashboards/3662](https://grafana.com/grafana/dashboards/3662)
- **ID :** 3662

```bash
# Ouvrir Grafana dans le navigateur
http://localhost:3000

# Importer le tableau de bord
1. Cliquez sur l'icône "+" > "Import".
2. Entrez l'ID 3662 et cliquez sur "Load".
3. Sélectionnez votre source de données Prometheus et cliquez sur "Import".
```

Ces tableaux de bord vous fourniront une vue complète de vos systèmes et services, vous permettant de surveiller les performances, détecter les problèmes, et prendre des mesures correctives rapidement.
