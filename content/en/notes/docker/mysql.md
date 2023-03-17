---
title: "MySQL"
linkTitle: "MySQL"
date: 2023-03-17T00:12:00-05:00
weight: 50
description: >
  An example docker-compose setup for MySQL.
---

## Overview

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  In the same directory create the following directory tree:
```
└── mysql
    ├── data
    ├── conf.d
    │   └── my.cnf
    └── initdb.d
        └── any-mysql-dump-file.sql
```
1.  Modify the configuration to suit.
1.  `docker-compose up`
1.  Login to database using your preferred client.
  - [Sequel Ace](https://apps.apple.com/us/app/sequel-ace/id1518036000) (MacOS only)
  - [MySQL Workbench](https://www.mysql.com/products/workbench/)
  - Many others...

## Example docker-compose.yml

- You can override default configuration by placing `my.cnf` in `./mysql/conf.d`.
- If the `data` directory is empty, the image will bootstrap the database with
  whatever is in `initdb.d`.
- You can use mysqldump to backup your data if you're doing destructive things.
  e.g. migration testing

```
version: "3"

services:
  database:
    image: mysql:8
    # The platform is necessary on an Apple M1, as mysql doesn't have an ARM image
    # platform: linux/x86_64
    ports:
      - ${DBW_PORT:-3306}:3306
    volumes:
      - ./mysql/data:/var/lib/mysql
      - ./mysql/initdb.d:/docker-entrypoint-initdb.d
      - ./mysql/conf.d:/etc/mysql/conf.d
      # - ./internal/testdata/mysql/secrets:/run/secrets
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_USER: app_user
      MYSQL_PASSWORD: app-user-password
      MYSQL_DATABASE: app_test
```

<!-- ## References -->

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

