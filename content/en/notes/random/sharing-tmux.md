---
title: "tmux"
date: 2024-01-30T10:10:00-05:00
weight: 60
---



Share a remote terminal

```bash
# Me
$ tmux -S /tmp/pair
$ chmod 777 /tmp/pair

# Another user
$ tmux -S /tmp/pair attach


# Disconnect
$ Ctrl-b d
```

