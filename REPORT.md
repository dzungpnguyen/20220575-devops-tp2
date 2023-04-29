# Rapport

## Les étapes

### Etape 1 : Transformer le wrapper du TP1 en API
Le `main.py` est en général une application web qui extrait des données en temps réel en basant sur la latitude et la longitude.<br>
Premièrement, je crée une nouvelle instance de Flask app :
```
app = Flask(__name__)
```
En appliquant le principe du wrapper du TP1, je crée une fonction `get_weather_by_lat_lon()` qui extrait des données sur OpenWeather. En ajoutant le décorateur, j'indique que cette fonction devrait être appelée lors de la requête de l'URL root :
```
@app.route('/')
def get_weather_by_lat_lon():
    # get data from api endpoint
```
La latitude et la longitude sont recupérées à partir des arguments passant dans l'URL. La clé API est appelée comme une variable d'environnment :
```
lat = request.args.get('lat')
lon = request.args.get('lon')
api_key = os.getenv('API_KEY')
```
En fin, la méthode `app.run()` faire tourner le server avec les spécifications suivantes :
- Le mode debug est ON => fournir des informations supplémentaires sur des erreurs et recharger automatiquement le server s'il y a des modifications dans le code.
- L'application sera accessible depuis n'importe quel appareil sur le réseau.
- L'application tournera sur la porte 8081
```
app.run(debug=True, host='0.0.0.0', port=8081)
```

### Etape 2 : Tester le wrapper en local
Je teste le wrapper en exécutant le code brut dans `main.py` avec l'URL : [http://127.0.0.1:8081?lat=12.3&lon=45.6](http://127.0.0.1:8081?lat=12.3&lon=45.6)

### Etape 3 : Ecrire Dockerfile
Dockerfile est utilisé pour créer une image Docker qui peut faire tourner le `main.py`. Voici l'explication de chaque ligne du `Dockerfile` :

```
FROM ubuntu:latest
```
L'image de base pour notre image Docker est la dernière version d'Ubuntu.

```
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -yq && \
    apt-get install -y python3 python3-pip && \
    rm -rf /var/lib/apt/lists/*
```
Mettre à jour les packages, installer python3 et pip et enlever les fichiers non nécessaires.

```
COPY main.py .
```
Copier le `main.py` local vers l'image Docker.

```
RUN pip3 install --trusted-host pypi.python.org requests flask
```
Installer les packages nécessaires pour le wrapper.

```
CMD ["python3", "main.py"]
```
La commande à défaut à exécuter lorsque le container commence à tourner est d'exécuter le `main.py` avec python3.

### Etape 4 : Configurer le workflow pour build et push l'image sur Docker
Le workflow est configuré dans `./github/workflows/deploy-to-docker.yml` :
- Le déclencheur est un push sur la branche "main".
- Le job "build-and-push-image" est exécuté sur une image Ubuntu.
- Les étapes exécutées dans le job :
    - Vérifier le code en exécutant Hadolint.
    - Build l'image Docker.
    - Se connecter à Docker Hub en utilisant les secrets stockés dans les variables d'environnement du dépôt GitHub.
    - Push l'image vers Docker Hub.

### Etape 5 : Pull l'image pour utiliser
Dans le 1er terminal :
```
docker pull dzung17/devops-tp1:0.0.2

docker run -p 8081:8081 --env API_KEY=<your-api-key> dzung17/devops-tp2:0.0.2
```

Dans le 2e terminal :
```
curl "http://localhost:8081/?lat=<your-latitude>&lon=<your-longitude>"
```