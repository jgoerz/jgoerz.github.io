---
title: "MinIO"
linkTitle: "MinIO"
description: >
  An Amazon S3 API compatible storage engine.
---
<!-- date: 2023-03-18T00h12:00-05:00 -->

## Overview

[Github repository](https://github.com/minio/minio)

Steps to use:

1.  In a directory create the docker-compose.yml file.
1.  Modify the configuration to suit.
1.  `docker-compose up`
1.  [Visit localhost:9000](http://localhost:9000)
1.  Login using credentials from compose file.


## Example docker-compose file

```yaml
version: "3"

services:
  minio:
    image: quay.io/minio/minio
    command: server --console-address ":9001" /data
    ports:
      - 9000:9000
      - 9001:9001
    volumes:
      - minio_storage:/data
    environment:
      MINIO_ROOT_USER: minio
      MINIO_ROOT_PASSWORD: minio123
volumes:
  minio_storage: {}

```

## References

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->


1. Sefidian, Amir Masoud.  "Deploy Standalone (Single Node) MiniIO server using Docker Compose" April 8, 2022, [URL](http://www.sefidian.com/2022/04/08/deploy-standalone-minio-using-docker-compose)
