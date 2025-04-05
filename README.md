# Docker

# Requirement

```bash
apt update && apt upgrade
apt install docker-compose-v2 
```

# docker commands

```bash
# démarrer le conteneur
docker compose up -d

# statut des conteneur
docker ps

# stopper les conteneurs
docker compose down

# afficher les logs des conteneurs
docker logs <conteneur_name>
```

# Vaultwarden installation

## Requirement

```bash
apt update && apt upgrade
```

## WEB UI

Interface utilisateur : `http://localhost:80`

Interface admin : `http://localhost/admin`

# Nginx Proxy Manager

URL : `http://localhost:81`

Login par défaut :

* Email : `admin@example.com`
* Mot de passe : `changeme`

# Jenkins

Jenkins sera disponible sur `http://localhost:8080`.

# Portainer

Accède à l'interface : `http://localhost:9000`

Le port 8000 dans Portainer est utilisé pour la connexion d'agents distants via le Portainer Agent, notamment dans les architectures multi-hôtes (gestion de plusieurs machines Docker à partir d'un seul Portainer).

# Prometheus x Grafana

### Structure du répertoire

```none
prometheus-grafana/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
```

Accès : 

* Prometheus : `http://localhost:9090`
* Grafana : `http://localhost:3000`

# Gitea

Gitea : `http://localhost:3000`
