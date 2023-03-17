---
title: "Gatus"
linkTitle: "Gatus"
date: 2023-03-17T00:12:00-05:00
description: >
  A simple tool for monitoring that also can export prometheus metrics.
---

## Overview

[Github repository](https://github.com/TwiN/gatus)

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  In the same directory create the config.yaml file.
1.  Modify the configuration to suit.
1.  `docker-compose up`
1.  [View the results](http://localhost:8888)


## Example Docker-compose File

Example docker-compose.yml

```docker
# docker-compose.yaml

version: "3"

services:
  gatus:
    image: twinproduction/gatus:v2.7.0
    restart: always
    ports:
      - ${GATUS_PORT:-8888}:8080
    volumes:
      - "./config.yaml:/config/config.yaml"
    networks:
      - public
```

## Example Configuration File

```yaml
# config.yaml

metrics: false # whether to expose metrics at /metrics (prometheus compatible)
services:
  - name: Local Dev
    group: Local Environments
    url: "http://localhost:8080/api/v1/health"
    interval: 30s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"  # 1000ms
  - name: Dev
    group: Internal Environments
    url: "https://dev.example.com/api/v1/health"
    interval: 30s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"  # 1000ms
      - "[CERTIFICATE_EXPIRATION] > 1440h"  # expires within 2 months
  - name: Staging
    group: Internal Environments
    url: "https://staging.example.com/api/v1/health"
    interval: 30s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"  # 1000ms
      - "[CERTIFICATE_EXPIRATION] > 1440h"  # expires within 2 months
  - name: Dev
    group: Partner Environments
    url: "https://dev.partner.example.com/api/v1/health"
    interval: 30s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"  # 1000ms
      - "[CERTIFICATE_EXPIRATION] > 1440h"  # expires within 2 months
  - name: Staging
    group: Partner Environments
    url: "https://staging.partner.example.com/api/v1/health"
    interval: 30s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"  # 1000ms
      - "[CERTIFICATE_EXPIRATION] > 1440h"  # expires within 2 months
```

<!-- ## References -->

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

