---
title: "SSH port forwarding"
date: 2024-01-26T10:10:00-05:00
weight: 20
---

## Forward from local machine to destination host

Any traffic sent over local_port shows up at dest_host:dest_port.


```
ssh -N -L local_port:dest_host_or_ip:dest_port dest_user@dest_host
```

Add it to ~/.ssh/config
- SessionType none is equivalent to `-N`
- LocalForward is equivalent to `-L`

```
Host friendly-name
    Hostname dest_host_or_ip
    User dest_user
    SessionType none
    LocalForward local_port dest_host_or_ip:dest_port
    IdentityFile ~/.ssh/id_rsa
```

## Forward from local machine to destination host through jump host

Any traffic sent over local_port goes through jump_host and is sent to
dest_host:dest_port.

```
ssh -N -L local_port:dest_host_or_ip:dest_port -J jump_user@jump_host
```

Add it to ~/.ssh/config
- SessionType none is equivalent to `-N`
- LocalForward is equivalent to `-L`

```
Host friendly-name
    Hostname dest_host_or_ip
    User dest_user
    SessionType none
    ProxyJump jump_user@jump_host
    LocalForward local_port dest_host_or_ip:dest_port
    IdentityFile ~/.ssh/id_rsa
```

## Forward from local machine to destination host through jump host and internal host

Note: Useful when destination is a container and internal host has a route to container.

Any traffic sent over local_port goes through jump_host and is sent to
dest_host:dest_port from internal_host.

Note that the destination host doesn't need to be the same as internal_host.
The destination host needs to be reachable from the internal_host.  You may
also need to add the `-i`  argument if one ssh key is not used for the entire
chain.

```
ssh -N -L local_port:dest_host_or_ip:dest_port -J jump_user@jump_host internal_user@internal_host
```

Add it to ~/.ssh/config
- SessionType none is equivalent to `-N`
- LocalForward is equivalent to `-L`

```
Host friendly-name
    Hostname internal_host
    User internal_user
    SessionType none
    ProxyJump jump_user@jump_host
    LocalForward local_port dest_host_or_ip:dest_port
    IdentityFile ~/.ssh/id_rsa
```
