Pour visualiser les données collectées par Blackbox Exporter dans Grafana, suivez ces étapes :

### Étape 1 : Assurez-vous que Blackbox Exporter collecte les données

Vérifiez d'abord que Blackbox Exporter collecte correctement les données. Vous pouvez le faire en visitant l'URL suivante dans votre navigateur :

```
http://localhost:9115/metrics
```

### Étape 2 : Vérifiez les métriques dans Prometheus

Accédez à l'interface web de Prometheus à l'adresse suivante :

```
http://localhost:9090
```

Utilisez des requêtes PromQL pour vérifier les métriques collectées par Blackbox Exporter. Par exemple :

```
probe_success{job="blackbox"}
```

Cela vous montrera si les sondes réussissent.

### Étape 3 : Ajouter Prometheus comme source de données dans Grafana

1. **Accédez à Grafana** :

   - Ouvrez votre navigateur et allez à `http://localhost:3000`.
   - Connectez-vous avec les identifiants par défaut (`admin`/`admin`).

2. **Ajoutez Prometheus comme source de données** :
   - Allez dans `Configuration` > `Data Sources` > `Add data source`.
   - Sélectionnez `Prometheus`.
   - Configurez l'URL comme `http://prometheus:9090` et cliquez sur `Save & Test`.

### Étape 4 : Créez un Tableau de Bord dans Grafana

1. **Créez un nouveau tableau de bord** :

   - Cliquez sur le signe `+` (Create) dans le menu de gauche.
   - Sélectionnez `Dashboard` > `Add new panel`.

2. **Ajoutez des Panels pour visualiser les données de Blackbox Exporter** :
   - Dans le panel, allez à l'onglet `Query`.
   - Sélectionnez votre source de données Prometheus.
   - Utilisez des requêtes PromQL pour obtenir les métriques collectées par Blackbox Exporter.

### Exemples de Requêtes PromQL

1. **Pour vérifier si la sonde réussit** :

```promql
probe_success{job="blackbox"}
```

2. **Pour vérifier le temps de réponse HTTP** :

```promql
probe_http_duration_seconds{job="blackbox"}
```

3. **Pour vérifier le code de réponse HTTP** :

```promql
probe_http_status_code{job="blackbox"}
```

### Étape 5 : Configurer les Panels

1. **Configuration du Panel** :

   - Dans le panel, configurez les visualisations (graph, singlestat, gauge, etc.) en fonction des données que vous souhaitez afficher.
   - Vous pouvez ajuster les options d'affichage, les titres, les unités, et autres paramètres pour mieux représenter vos données.

2. **Enregistrer le Tableau de Bord** :
   - Une fois les panels configurés, enregistrez votre tableau de bord en cliquant sur le bouton `Save` en haut de la page.

### Exemple de Tableau de Bord pour Blackbox Exporter

Voici un exemple de tableau de bord que vous pouvez créer pour visualiser les métriques collectées par Blackbox Exporter :

1. **Panel pour le succès de la sonde** :

   - Type de visualisation : Singlestat ou Gauge.
   - Requête : `probe_success{job="blackbox"}`.
   - Options de visualisation : Afficher 1 pour succès et 0 pour échec.

2. **Panel pour le temps de réponse HTTP** :

   - Type de visualisation : Graph.
   - Requête : `probe_http_duration_seconds{job="blackbox"}`.
   - Options de visualisation : Afficher les valeurs en secondes.

3. **Panel pour le code de réponse HTTP** :
   - Type de visualisation : Table.
   - Requête : `probe_http_status_code{job="blackbox"}`.
   - Options de visualisation : Afficher les codes de réponse avec les instances correspondantes.

### Conclusion

En suivant ces étapes, vous pouvez visualiser les données collectées par Blackbox Exporter dans Grafana. Les requêtes PromQL vous permettent de récupérer les métriques spécifiques, et les options de visualisation de Grafana vous permettent de créer des tableaux de bord personnalisés pour surveiller la disponibilité et les performances des services surveillés par Blackbox Exporter.
