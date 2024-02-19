# How to use Docker Compose for n8n Automations 

## Introduction

This guide will show you how to use Docker Compose to run n8n in a containerized environment.

## Wait, what is n8n?

n8n is a free zapier alternative.

What is Zapier? Zapier is a tool that allows you to connect apps you use every day to automate tasks and save time.

For example you want to get a call whenever you get a new email from a particular email address.

Zapier allow you to do that - but it's not free.

n8n is free - but with a catch. You need to host it yourself.

Today we will see how to host n8n using Docker Compose.

## Prerequisites

- Docker
- Docker Compose

## Step 1: Create a Docker Compose file

Create a new file called `docker-compose.yml` and add the following content. Don't worry about what it does, we will explain it later.

```yaml
version: '3'
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=user
      - N8N_BASIC_AUTH_PASSWORD=password
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=db
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=n8n
      - NODE_FUNCTION_ALLOW_EXTERNAL=axios,qs
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: postgres:12
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=n8n
      - POSTGRES_DB=n8n
    restart: unless-stopped

volumes:
  db-data:
```

This file defines two services: `n8n` and `db`. The `n8n` service runs the n8n container, and the `db` service runs a PostgreSQL database.

## Step 2: Start n8n

Run the following command to start n8n:

```bash
docker-compose up -d
```

This command will start the n8n and PostgreSQL containers in the background.

Let's analyze the `docker-compose.yml` file:

- The `n8n` service is based on the `n8nio/n8n` image. It exposes port `5678` and sets the environment variables for basic authentication and database configuration.
- The `db` service is based on the `postgres:12` image. It creates a volume to persist the database data and sets the environment variables for the database user, password, and database name.
- The `depends_on` directive ensures that the `db` service starts before the `n8n` service.
- The `restart` directive ensures that the containers are restarted unless stopped.
- The `volumes` directive creates a volume for the database data.

One interesting line is:

```yaml
- NODE_FUNCTION_ALLOW_EXTERNAL=axios,qs
```

This is an optional environment variable that allows n8n to use the `axios` and `qs` libraries in Function nodes. 

Let's say you need to make some external API call's using axios. You don't have that available by default in n8n. You can add it to the `NODE_FUNCTION_ALLOW_EXTERNAL` environment variable.


## Step 3: Access n8n

Open your web browser and go to `http://localhost:5678`. You will be prompted to enter your username and password. Use the credentials you specified in the `docker-compose.yml` file.

## Conclusion

That's it! Now you have n8n running in a containerized environment using Docker Compose. 

You can deploy it in any environment that supports Docker, such as your local machine, a virtual machine, or a cloud provider.

