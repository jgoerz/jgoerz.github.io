---
title: "Setup DB access via bastion host"
date: 2024-01-30T10:10:00-05:00
weight: 40
---


## Create a read-only database user for MySQL

### Setup user on bastion host

```bash
#!/usr/bin/env bash
# Edit these as appropriate
SSH_USER=
BASTION_HOST_OR_IP_ADDRESS=localhost
NEW_USER=

scp -i ~/.ssh/<bastion-host-key> <public-key-of-new-user>.pub ${SSH_USER}@HOST_OR_IP_ADDRESS:~/key.pub

ssh ${SSH_USER}@${BASTION_HOST_OR_IP_ADDRESS}
sudo adduser --disabled-password ${NEW_USER}
sudo mkdir /home/${NEW_USER}/.ssh
sudo chmod 0700 /home/${NEW_USER}/.ssh
sudo cp ~/key.pub /home/${NEW_USER}/.ssh/authorized_keys
sudo chown -R ${NEW_USER}.${NEW_USER} /home/${NEW_USER}/.ssh
```

### Setup user on DB

Generate a random password:

```bash
ruby -e "require 'securerandom'; puts SecureRandom.hex()"
```

Now go to database and create a read only user:

```sql
CREATE USER NEW_USER identified by '<password>';
GRANT SELECT, SHOW VIEW, PROCESS ON *.* TO 'NEW_USER'@'%' identified by '<password>';

-- Confirm the privileges are read-only
show grants for NEW_USER;
```

Give the username and password to the individual.  Access to database should be
via bastion host.
