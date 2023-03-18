---
title: "Prometheus"
linkTitle: "Prometheus"
description: >
  An example docker setup for prometheus.
---
<!-- date: 2023-03-17T00:12:00-05:00 -->

## Overview

Steps to use:

1.  In a directory create the run.sh file.
1.  In the same directory create a directory called `config`
1.  In the same directory create a file called `prometheus.yml`
1.  Modify the configuration to suit.
1.  `chmod u+x` and `./run.sh`
1.  [Visit localhost:9090](http://localhost:9090)
1.  Select Status => Targets

## Example run.sh

```bash
#!/usr/bin/env bash

docker run --rm -it \
  -p 9090:9090 \
  -v ${PWD}/config:/config \
  prom/prometheus:v2.32.1 --config.file="/config/prometheus.yml"
```

## Example Configuration


```yaml
global:
  scrape_interval:     15s
  evaluation_interval: 15s

#rule_files:
  # - "first.rules"
  # - "second.rules"

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'dev-servers'
    # follow_redirects: false
    #   metrics_path: '/api/v1/allmetrics'  only needed if the default path is not /metrics
    static_configs:
      - targets: ['dev.example.com', 'staging.example.com'] 
```

<!-- ## References -->

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

