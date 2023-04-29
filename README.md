# 20220575-devops-tp2

### Prerequisites
- Docker installed
- API key provided by OpenWeather

### Running the docker image:
1. In your 1st terminal, run:
```
docker run -p 8081:8081 --env API_KEY=<your-api-key> dzung17/devops-tp2:0.0.1
```
2. In your 2nd terminal, run:
```
curl "http://localhost:8081/?lat=<your-latitude>&lon=<your-longitude>"
```
