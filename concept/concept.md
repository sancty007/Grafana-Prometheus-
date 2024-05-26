La commande `docker run` que vous avez fournie permet de lancer un conteneur Prometheus en arrière-plan. Voici une explication détaillée de chaque composant de cette commande :

```sh
docker run -d --name=prometheus --network=monitoring -p 9090:9090 -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

##### Afficher les conteurs avec :

```bash
 docker ps -a
```

##### Acceder à un conteneur :

```bash
docker exec -it <nom_conteneur> /bin/bash
```

###### Redémarer un conteneur :

```bash
 docker start prometheus
```

##### comment supprimer le conteneur :

```bash
 docker rm <container_id_or_name>
```

### Détail de la commande

1.  **`docker run`** :

    - Cette commande est utilisée pour créer et exécuter un nouveau conteneur Docker à partir d'une image spécifiée.

2.  **`-d`** :

    - L'option `-d` (détaché) permet de lancer le conteneur en arrière-plan, rendant ainsi le terminal disponible pour d'autres commandes.

3.  **`--name=prometheus`** :

    - Cette option donne un nom au conteneur, ici `prometheus`. Cela rend plus facile la gestion et l'identification du conteneur.

4.  **`--network=monitoring`** :

    - Cette option connecte le conteneur au réseau Docker nommé `monitoring`. Utiliser des réseaux Docker personnalisés facilite la communication entre les conteneurs.

5.  **`-p 9090:9090`** :

    - L'option `-p` fait le mapping des ports. Ici, le port 9090 de l'hôte est mappé au port 9090 du conteneur. Cela permet d'accéder à Prometheus via `http://localhost:9090` depuis l'hôte.

6.  **`-v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml`** :

    - L'option `-v` (volume) monte un fichier ou un répertoire de l'hôte dans le conteneur. Dans ce cas, le fichier `prometheus.yml` situé dans le répertoire courant de l'hôte est monté à l'emplacement `/etc/prometheus/prometheus.yml` dans le conteneur. Cela permet à Prometheus de lire sa configuration depuis ce fichier sur l'hôte.

    ##### Afficher les volumes :

    ```bash
        docker volume ls
    ```

    ##### des informations détaillées sur un volume spécifique

    ```bash
       docker volume inspect my_volume
    ```

    - `$(pwd)` est une commande shell qui renvoie le répertoire courant (print working directory).

7.  **`prom/prometheus`** :
    - C'est l'image Docker utilisée pour créer le conteneur. `prom/prometheus` est l'image officielle de Prometheus disponible sur Docker Hub.

### Résumé de la commande

Cette commande lance un conteneur Prometheus en arrière-plan avec les caractéristiques suivantes :

- Le conteneur est nommé `prometheus`.
- Il est connecté au réseau Docker `monitoring`.
- Le port 9090 du conteneur est mappé au port 9090 de l'hôte, permettant l'accès à l'interface web de Prometheus.
- Un fichier de configuration `prometheus.yml` du répertoire courant de l'hôte est monté dans le conteneur à l'emplacement `/etc/prometheus/prometheus.yml`, permettant à Prometheus d'utiliser cette configuration.

En résumé, cette commande configure et exécute Prometheus de manière à ce qu'il soit facilement accessible et correctement configuré selon les paramètres définis dans le fichier `prometheus.yml`.

---
