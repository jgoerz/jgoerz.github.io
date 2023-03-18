---
title: "Postgres"
linkTitle: "Postgres"
description: >
  An example docker-compose setup for Postgres.
---
<!-- date: 2023-03-17T00:12:00-05:00 -->

## Overview

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  In the same directory create two subdirectories: `pgdata`, `pgadmin-data`.
1.  `docker-compose up`
1.  Login to database using pgAdmin.
1.  Use the value of `PGADMIN_DEFAULT_EMAIL` as the username and the value of
    `PGADMIN_DEFAULT_PASSWORD` as the password.
1.  Once logged in _add a new server_.  Ensure you use the service name of the
    postgres database from the docker-compose file as the hostname in the
    configuration.  In the example below, this is `postgresdb`.

## Example docker-compose.yml

```docker
version: "3"

services:
  postgresdb:
    image: docker.io/postgres:14
    # command: ["postgres", "-c", "config_file=/etc/postgresql.conf"]
    restart: always
    ports:
      - 5432:5432
      # - ${POSTGRES_PORT:-5432}:5432
    volumes:
      - ./pgdata:/var/lib/postgresql/data
      # - ./postgresql.conf:/etc/postgresql.conf
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: password
      POSTGRES_DB: example_db
    networks:
      - public

  db-ui:
    image: dpage/pgadmin4:6
    depends_on: 
      - postgresdb
    ports:
      - 9999:80
    volumes:
      - pgadmin-data:/var/lib/pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: password
      PGADMIN_DISABLE_POSTFIX: "true"
    networks:
      - public

networks:
  public:
    name: develop_network
```

## References


<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

1. "Container Deployment."  pgadmin.org, March 17, 2023, [URL](https://www.pgadmin.org/docs/pgadmin4/6.21/container_deployment.html)
1. Salimon, Samuel.  "Setting up PgAdmin Docker Connection: 3 Critical Steps." February 15th, 2022, [URL](https://hevodata.com/learn/pgadmin-docker)
