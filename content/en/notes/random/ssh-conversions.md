---
title: "SSH Converstions"
date: 2024-06-10T10:10:00-05:00
weight: 30
---

## Convert PKCS8/PEM into ssh authorized_keys

[Reference link](https://stackoverflow.com/questions/1011572/convert-pem-key-to-ssh-rsa-format#6112463)

```bash
ssh-keygen -f key.pub -i -m PKCS8
```

See `man ssh-keygen` for other possible key formats.


