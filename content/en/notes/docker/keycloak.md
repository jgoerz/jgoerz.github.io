---
title: "Keycloak"
linkTitle: "Keycloak"
date: 2023-03-17T00:12:00-05:00
weight: 45
description: >
  An IDP that can be used in production as well as for local development.
---

## Overview

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  In the same directory create a directory called `pg-data`
1.  Modify the configuration to suit.
1.  `docker-compose up`
1.  [Login to keycloak](http://localhost:8180) username: admin, password: admin


## Example Docker-compose File

```docker
# docker-compose.yaml
version: "3"

services:
  keycloak:
    image: quay.io/keycloak/keycloak:12.0.4
    ports:
      - ${KEYCLOAK_PORT:-8180}:8080
      # - ${KEYCLOAK_PORT:-10000}:8443
    environment:
      KEYCLOAK_USER: admin
      KEYCLOAK_PASSWORD: admin
      DB_VENDOR: postgres
      DB_ADDR: keycloak-db
      DB_USER: keycloak
      DB_PASSWORD: keycloak
    networks:
      - public
  keycloak-db:
    image: postgres:9.6
    # ports:
    #   - 5432:5432
    environment:
      POSTGRES_USER: keycloak
      POSTGRES_DB: keycloak
      POSTGRES_PASSWORD: keycloak
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - ./pg-data:/var/lib/postgresql/data
    networks:
      - public

networks:
  public:
    external:
      name: keycloak_network
```


## Notes

- Account console && admin console
	- admin: configure & manage keycloak
	- account: allow account holders to manage their accounts
- Admin console
	- Realm
		- Fully isolated from other realms
		- Has its own configuration
		- Has its own set of applications & users
		- Can be used to divide up different use cases: internal vs. external, etc.
		- Constraints:
			- Initial name should not have invalid URL characters
			- Favor hyphens over underscores
	- Group
		- Group level attributes that all users in that group inherit
		- Group level roles that all users in that group inherit
	- Role
		- Can be composites
		- Composite roles grant all roles in that composite role
		- Constraints:
			- Can be difficult to manage
			- Can incur a performance penalty if overused
- Account console
	- Allow users to:
		- Update their profile
		- Update their password
		- Enabling second factor authentication
		- View applications to which they have authenticated
		- View open sessions
		- Remotely sign out of open sessions
	- URL: https://basedomain.com:port/auth/realms/$REALM_NAME/account
		- Also available in the Clients left menu
- Securing an application
	- AuthN
		- Create the user
	- AuthZ
		- Create the appropriate global role in Keycloak
		- Ensure the role is applied to the user
		- Register the client/app in the correct realm
- Bridging from an existing user/pw store
	- Active Directory?
  - Application?


<!-- ## References -->

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

