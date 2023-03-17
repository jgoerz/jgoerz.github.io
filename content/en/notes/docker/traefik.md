---
title: "Traefik"
linkTitle: "Traefik"
date: 2023-03-17T00:12:00-05:00
description: >
  An example docker-compose setup for traefik.
---


## Overview

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  `docker-compose up`
1.  [Visit localhost:8080](http://localhost:8080)

## Example Docker-compose File

Example docker-compose.yml

```docker
version: "3"

services:
  proxy:
    image: traefik:1.7
    restart: always
    command: --api --docker --docker.domain=localhost --logLevel=DEBUG
    networks:
      - webgateway
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /dev/null:/traefik.toml

networks:
  webgateway:
    driver: bridge
```

<!-- ## References -->

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

