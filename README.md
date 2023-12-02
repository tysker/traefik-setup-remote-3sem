# Træfik Setup Remote

## Requirements

- [DigitalOcean](https://www.digitalocean.com/) account
- [Droplet with Docker preinstalled (Marketplace)](https://marketplace.digitalocean.com/apps/docker)
- [DockerHub](https://hub.docker.com/search?q=) Docker-Hub account
- [Domain](https://www.namecheap.com/) name (could be any provider)
- Your domain is pointing to DigitalOcean DNS servers
- Wildcard DNS record for your domain (*.your_domain.com)

## Features

- [Traefik](https://doc.traefik.io/traefik/) as a reverse proxy
- [Let's Encrypt](https://letsencrypt.org/) for SSL certificates
- [Traefik Dashboard](https://docs.traefik.io/operations/dashboard/) for monitoring
- [PostgreSQL](https://www.postgresql.org/) for database
- [Docker](https://www.docker.com/) for containerization
- [Docker Compose](https://docs.docker.com/compose/) for container orchestration
- [DigitalOcean](https://www.digitalocean.com/) for cloud hosting

## Debugging

- Check if all containers are running with `docker ps -a`
- Check if all env variables are set in .env file and are correct
- Check if docker compose has read all environment variables with `docker-compose config`
- Check the logs of the individual container with `docker logs <container_name>` or `docker logs --follow <container_name>`
- Did you remember to add your domain to the DigitalOcean DNS servers?
- Did you remember to add a wildcard DNS record for your domain?
- Did you remember to create the acme.json file and set the correct permissions?
- Run your docker compose file with `docker-compose up` (without the -d flag) and check the output logs for errors

## Setup

### 1. Clone the repository

```bash
  git clone https://github.com/tysker/3sem-traefik-setup-remote.git
```

- Rename the folder to your project name (optional)
- Delete the .git folder
- Delete the Readme.md, traefik, utility and images folder
- Create a new repository on GitHub
- Initialize a new git repository inside your project folder
- Add your new repository as a remote and push your code

### 3. Generate digital ocean token

- To generate a digital ocean token, go to [DigitalOcean](https://cloud.digitalocean.com/account/api/tokens) and create a new token.

<img src="./images/digital_token.png">

- Copy the token and save it somewhere safe. You will need it later.

### 4. Install apache2-utils inside your droplet terminal

```bash
  sudo apt-get install apache2-utils
``` 

### 5. Generate a traefik hashed dashboard login (droplet linux terminal )

```bash
  echo $(htpasswd -nb <your_username> <your_password>) | sed -e s/\\$/\\$\\$/g
```

<img src="./images/encrypted-password.png">

- Copy the output and save it somewhere safe. You will need it later.

### 6. Clone the repository into your droplet

- remember to push your code to your repository before cloning it into your droplet

### 7. Inside the project set permissions for the acme.json file and folder

```bash
  chmod 600 ./acme
  chmod 600 ./acme/acme.json
```

### 8. Create a .env file at the root of your project and add the following environment variables

```bash
# Lets-encrypt - Digital Ocean
PROVIDER=digitalocean
EMAIL=<your_email>
ACME_STORAGE=/etc/traefik/acme/acme.json
DO_AUTH_TOKEN=<your_digital_ocean_token> (see step 3)

# Traefik
TRAFIK_DOMAIN=traefik.<your_domain>
DASHBOARD_AUTH=<your_traefik_dashboard_login> (see step 4 and 5)

# API
API_DOMAIN=api.<your_domain_name>
# Postgres
POSTGRES_USER=<your_postgres_user>
POSTGRES_PASSWORD=<your_postgres_password>

# REST API SETUP
CONNECTION_STR=jdbc:postgresql://db:5432/
DB_USERNAME=<your_postgres_user>
DB_PASSWORD=<your_postgres_password>
DEPLOYED=TRUE
SECRET_KEY=super_secret_key (has to include at least 32 characters letters and numbers)
TOKEN_EXPIRE_TIME=1800000 (30 minutes)
ISSUER=<your_domain>

```

## How to use it

###  Run Docker

```bash
  docker-compose up -d
```

### Stop Docker

```bash
  docker-compose down
```

### Access Traefik Dashboard through browser

```bash
  traefik.<your_domain>
```

### Access Your Rest Api

```bash
  <your_domain>/<your_api_path> (example: api.3sem.dk/api or restapi.3sem.dk/api)
```

***

### Reset DB data installation

(-v) // remove volumes
```bash
 docker-compose down -v 
```

```bash
 sudo  rm -rf ./data
```

### Connect your remote database to your local pgAdmin container

```bash
  <your_domain>:5432
```

***

<img src="./utility/3sem-setup-remote.drawio.png" alt="3 semester local environment setup">
