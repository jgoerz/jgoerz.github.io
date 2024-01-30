---
title: "SSH key fingerprint"
date: 2023-08-10T10:10:00-05:00
weight: 30
---

A script to generate the fingerprint as used for AWS ssh keys

```bash
#!/usr/bin/env bash

if [ "$#" != "1" ]; then
  echo "need the path to the private key"
  exit 1
fi

private_key_file_path=$1

ssh-keygen \
  -ef ${private_key_file_path} \
  -m PEM | \
  openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
  # openssl rsa -RSAPublicKey_in -outform DER | openssl sha256 -c
```
