# Træfik Setup Remote

## Requirements

- [DigitalOcean](https://www.digitalocean.com/) account
- [Droplet with Docker preinstalled (Marketplace)](https://marketplace.digitalocean.com/apps/docker)
- [DockerHub](https://hub.docker.com/search?q=) DockerHub account
- DockerHub Token
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
- Check the logs of the individual container with `docker logs <container_name>`
  or `docker logs --follow <container_name>`
- Did you remember to add your domain to the DigitalOcean DNS servers?
- Did you remember to add a wildcard DNS record for your domain?
- Did you remember to create the acme.json file and set the correct permissions?
- Run your docker compose file with `docker-compose up` (without the -d flag) and check the output logs for errors

## Setup

### Part 1: Setup Project

- create a folder called `remote-docker-setup`
- inside the folder create a file called `docker-compose.yml`
- add the following inside the file:

### Part 2: sql script

- create a folder called `db`
- inside the db folder add a file called `init.sql`
- add the following inside the `init.sql` file

```SQL
-- create role
CREATE ROLE dev WITH LOGIN CREATEDB PASSWORD 'ax2';

-- create databases
CREATE DATABASE company;

\c company;

-- create tables
CREATE TABLE companies
(
  company_id   SERIAL PRIMARY KEY,
  company_name VARCHAR(255) NOT NULL
);

CREATE TABLE contacts
(
  contact_id   SERIAL PRIMARY KEY,
  company_id   INT,
  contact_name VARCHAR(255) NOT NULL,
  phone        VARCHAR(25),
  email        VARCHAR(100),
  CONSTRAINT fk_company
    FOREIGN KEY (company_id)
      REFERENCES companies (company_id)
);

-- insert data
INSERT INTO companies(company_name)
VALUES ('BlueBird Inc'),
       ('Dolphin LLC');

INSERT INTO contacts(company_id, contact_name, phone, email)
VALUES (1, 'John Doe', '(408)-111-1234', 'john.doe@bluebird.dev'),
       (1, 'Jane Doe', '(408)-111-1235', 'jane.doe@bluebird.dev'),
       (2, 'David Wright', '(408)-222-1234', 'david.wright@dolphin.dev');

```

### Part 3: Traefik

- add the following inside the compose file
- remember to add a valid email address `<YOUR_EMAIL_ADDRESS>`

```YML
version: "3.3"

services:

  traefik:
    image: "traefik:v2.10"
    container_name: "traefik"
    command:
      - "--api.insecure=true" # enables the Traefik API. True by default
      - "--providers.docker=true" # enables the Docker provider
      - "--providers.docker.exposedbydefault=false" # prevents Traefik from creating routes for containers that don't have a traefik.enable=true label
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=<YOUR_EMAIL_ADDRESS>"# Email address used for registration
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    labels:
      - "com.centurylinklabs.watchtower.enable=true"
    ports:
      - "443:443" # sets the port for HTTP
      - "8080:8080" # sets the port for HTTPS
    volumes:
      - "./letsencrypt:/letsencrypt" # Copies the Let's Encrypt certificate locally for ease of backing up
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
    networks:
      - backend

networks:
  backend:
    driver: bridge
```

### Part 4: Create git repo

- create a new git repo for your `remote-docker-setup` project

### Part 5: Droplet

- log into your droplet via terminal
- clone the `remote-docker-setup` project

### Part 6: Frontend

```YML
  europe:
    image: "webtrade/europemap"
    container_name: "europemap_container"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.europe.rule=Host(`europe.<YOUR_DOMAIN_NAME>`)"
      - "traefik.http.routers.europe.entrypoints=websecure"
      - "traefik.http.routers.europe.tls.certresolver=myresolver"
      - "com.centurylinklabs.watchtower.enable=true"
``` 

### Part 7: DB

```YML
  db:
    image: postgres:latest
    container_name: db
    restart: unless-stopped
    networks:
      - backend
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: ax2
    labels:
      # Watchtower configuration
      com.centurylinklabs.watchtower.enable: "false" # disables Watchtower for this container
    volumes:
      - ./data:/var/lib/postgresql/data/
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
```

### Part 8: create new db that fits to your api

You can do this in three ways:

- inside the db docker terminal
- inside your local pgadmin tool
- inside Intellij's db plugin

### Part 9: api

```YML
  api:
    image: tyskerdocker/javalinapi:latest
    container_name: api
    environment:
      - CONNECTION_STR=jdbc:postgresql://db:5432/
      - DB_USERNAME=dev
      - DB_PASSWORD=ax2
      - DEPLOYED=TRUE
      - SECRET_KEY=841D8A6C80CBA4FCAD32D5367C18C53B
      - TOKEN_EXPIRE_TIME=1800000
      - ISSUER=cphbusiness.dk
    ports:
      - "7070:7070"
    networks:
      - backend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.api.rule=Host(`api.<YOUR_DOMAIN_NAME>`)"
      - "traefik.http.routers.api.entrypoints=websecure"
      - "traefik.http.routers.api.tls.certresolver=myresolver"
      - "com.centurylinklabs.watchtower.enable=true"
```

### Part 10: watchtower

```YML
  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    environment:
      REPO_USER: <YOUR_DOCKERHUB_USERNAME>
      REPO_PASS: <YOUR_DOCKERHUB_TOKEN>
    labels:
      com.centurylinklabs.watchtower.enable: "false"
    networks:
      - backend
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 600 --cleanup --debug # checks for updates every 600 seconds (10 min), cleans up old images, and outputs debug logs
``` 

## Access

### Run Docker

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

### Reset DB data installation

(-v) // remove volumes

```bash
 docker-compose down -v 
```

```bash
 sudo  rm -rf ./data
```

### Connect your remote database to your local pgAdmin container (or to the Intellij db plugin)

```bash
  <your_domain>:5432
```

***

<img src="./utility/3sem-setup-remote.drawio.png" alt="3 semester local environment setup">
