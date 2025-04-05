# Docker

Requirement

apt update && apt upgrade
apt install docker-compose-v2 

docker commands

# démarrer le conteneur
docker compose up -d

# statut des conteneur
docker ps

# stopper les conteneurs
docker compose down

# afficher les logs des conteneurs
docker logs <conteneur_name>

Vaultwarden installation

Requirement

apt update && apt upgrade

docker-compose.yml

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    ports:
      - "80:80"     # Interface Web
      - "3012:3012"   # Interface admin
    volumes:
      - /apps/vaultwarden/bw-data:/data
      - /etc/localtime:/etc/localtime:ro
    env_file:
      - docker.env

docker.env

openssl rand -hex 32

ADMIN_TOKEN=
WEBSOCKET_ENABLED=true
SIGNUPS_ALLOWED=true
TZ=Europe/Paris

WEB UI

Interface utilisateur : http://localhost:80

Interface admin : http://localhost/admin

Nginx Proxy Manager

docker-compose.yml

services:
  nginx-proxy-manager:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - '80:80'      # HTTP
      - '81:81'      # Interface web
      - '443:443'    # HTTPS
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm_password"
      DB_MYSQL_NAME: "npm_db"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
  db:
    image: 'mariadb:10.5'
    container_name: npm-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 'root_password'
      MYSQL_DATABASE: 'npm_db'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm_password'
    volumes:
      - ./data/mysql:/var/lib/mysql

URL : http://localhost:81

Login par défaut :

Email : admin@example.com

Mot de passe : changeme

Jenkins

docker-compose.yml

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"    # Interface web
      - "50000:50000"  # Port pour les agents Jenkins
    volumes:
      - jenkins_home:/var/jenkins_home
    environment:
      - JAVA_OPTS=-Djenkins.install.runSetupWizard=false
volumes:
  jenkins_home:

Jenkins sera disponible sur http://localhost:8080.

Portainer

docker-compose.yml

services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: always
    ports:
      - "9000:9000"         # Interface web
      - "8000:8000"         # Port pour l'agent Edge (optionnel)
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock     # Permet de contrôler Docker
      - portainer_data:/data                          # Données de Portainer
volumes:
  portainer_data:

Accède à l'interface : http://localhost:9000

Le port 8000 dans Portainer est utilisé pour la connexion d’agents distants via le Portainer Agent, notamment dans les architectures multi-hôtes (gestion de plusieurs machines Docker à partir d’un seul Portainer).

Prometheus x Grafana

Structure du répertoire

prometheus-grafana/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml

docker-compose.yml

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    restart: unless-stopped
    volumes:
      - grafana-storage:/var/lib/grafana

volumes:
  grafana-storage:

prometheus.yml

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['prometheus:9090']

Accès : 

Prometheus : http://localhost:9090

Grafana : http://localhost:3000

Gitea

docker-compose.yml

networks:
  gitea:
    external: false

services:
  server:
    image: docker.gitea.com/gitea:1.23.6
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=postgres
      - GITEA__database__HOST=db:5432
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=gitea
      - GITEA__database__PASSWD=gitea
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
    depends_on:
      - db

  db:
    image: docker.io/library/postgres:14
    restart: always
    environment:
      - POSTGRES_USER=gitea
      - POSTGRES_PASSWORD=gitea
      - POSTGRES_DB=gitea
    networks:
      - gitea
    volumes:
      - ./postgres:/var/lib/postgresql/data

Gitea : http://localhost:3000
