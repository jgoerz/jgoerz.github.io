---
title: "Using curl/wget to check status"
date: 2024-02-01T10:10:00-05:00
weight: 50
---


## Curl example

```bash
#!/usr/bin/env bash

echo "Waiting till server is responsive. Timeout in 60 seconds."
count=0
while [ "$result" != "0" ]
do
  sleep 3

  curl --silent --output /dev/null "http://${serverIP}:${serverPort}"
  result=$?

  count=$((count+1))
  if [ $count -gt 20 ]; then
    echo ""
    echo "server was unresponsive for 60 seconds, exiting"
    echo ""
    exit 1
  fi
done
echo "Server is responsive. Continuing"
```

## Wget example


```bash
#!/usr/bin/env bash

echo "Waiting till server is responsive. Timeout in 60 seconds."
count=0
while [ "$result" != "200"]
do
  sleep 3

  result=$(wget --spider --server-response --output-document=/dev/null http://${serverIP}:${serverPort} 2>&1 | grep 'HTTP/' | awk '{print $2}')

  count=$((count+1))
  if [ $count -gt 20 ]; then
    echo ""
    echo "server was unresponsive for 60 seconds, exiting"
    echo ""
    exit 1
  fi
done

echo "Server is responsive. Continuing"
```
