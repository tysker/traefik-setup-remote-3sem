# Træfik Setup Remote

## Requirements

- [DigitalOcean](https://www.digitalocean.com/) account
- [DockerHub](https://hub.docker.com/search?q=) DockerHub account
- [Domain](https://www.namecheap.com/) name (could be any provider)
- Your domain is pointing to DigitalOcean DNS servers
- Wildcard DNS record for your domain (*.your_domain.com)

## Features

- [Traefik](https://traefik.io/) as a reverse proxy
- [Let's Encrypt](https://letsencrypt.org/) for SSL certificates
- [Traefik Dashboard](https://docs.traefik.io/operations/dashboard/) for monitoring
- [PostgreSQL](https://www.postgresql.org/) for database
- [pgAdmin](https://www.pgadmin.org/) for database management
- [Docker](https://www.docker.com/) for containerization
- [Docker Compose](https://docs.docker.com/compose/) for container orchestration
- [DigitalOcean](https://www.digitalocean.com/) for cloud hosting

## Setup

### 1. Clone the repository into your server

```bash
  git clone https://github.com/tysker/3sem-traefik-setup-remote.git
```

### 2. Create .env file and add your environment variables

```bash
# Lets-encrypt - Digital Ocean
PROVIDER=digitalocean
EMAIL=<your_email>
ACME_STORAGE=/etc/traefik/acme/acme.json
DO_AUTH_TOKEN=<your_token>

# Traefik
TRAFIK_DOMAIN=traefik.<your_domain>
DASHBOARD_AUTH=<your_dashboard_auth>

# API
API_DOMAIN=<your_api_domain>

# Postgres
POSTGRES_USER=<your_postgres_user>
POSTGRES_PASSWORD=<your_postgres_password>

# PgAdmin
PGADMIN_DOMAIN=pgadmin.<your_domain>
PGADMIN_DEFAULT_EMAIL=<your_pgadmin_email>
PGADMIN_DEFAULT_PASSWORD=<your_pgadmin_password>

# Watchtower
REPO_PASS=<your_dockerhub_token>
REPO_USER=<your_dockerhub_username>

```

To generate your token, go to [DigitalOcean](https://cloud.digitalocean.com/account/api/tokens) and create a new token.

### 3. Create acme directory and an acme.json file

```bash
  mkdir ./acme
  touch ./acme/acme.json
```

```bash
  chmod 600 ./acme/acme.json
```

### 4. How to generate DASHBOARD_AUTH

```bash
  echo $(htpasswd -nb <your_username> <your_password>) | sed -e s/\\$/\\$\\$/g
```

### 5. Run Docker

```bash
  docker-compose up -d
```

### 6. Stop Docker

```bash
  docker-compose down
```

### 7. Access Traefik Dashboard through browser

```bash
  traefik.<your_domain>
```

### 8. Access Your Rest Api

```bash
  <your_domain>/<your_api_path>
```

***

### Reset DB data installation

(-v) // remove volumes
```bash
 docker-compose down -v 
```

```bash
 sudo  rm -rf ./data
 sudo  rm -rf ./pgadmin-data
```

