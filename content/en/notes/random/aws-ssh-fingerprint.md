---
title: "SSH conversions"
date: 2023-08-10T10:10:00-05:00
weight: 30
---



## AWS key fingerprint

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

## Convert PKCS8/PEM into ssh authorized_keys

[Reference link](https://stackoverflow.com/questions/1011572/convert-pem-key-to-ssh-rsa-format#6112463)

```bash
ssh-keygen -f key.pub -i -m PKCS8
```

See `man ssh-keygen` for other possible key formats.

