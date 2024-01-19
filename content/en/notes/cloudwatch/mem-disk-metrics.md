---
title: "Memory and Disk Metrics"
date: 2024-01-18T10:10:00-05:00
weight: 20
---


## Overview

Deploy the cloudwatch agent to EC2s and mount the appropriate configuration
file to indicate metrics to track.  Note that reboots have caused issues.  It
appears that the docker container is started before the EBS volume can be
attached and the configuration file has a directory mount over the file on the
file system.  This requires manual deletion of the directory and placing the
file in its place.  The container will then pick up the configuration file.


## Docker Image and Configuration

Example docker compose usage:

```yaml
version: '2'
services:
  cloudwatch-agent:
    image: amazon/cloudwatch-agent:1.300028.1b210-amd64
    environment:
      AWS_ACCESS_KEY_ID: A12345678
      AWS_REGION: us-east-1
      AWS_SECRET_ACCESS_KEY: JKHJHOPIH096634BNTGN893736
    volumes:
    - /root/cloudwatch-agent-config.json:/opt/aws/amazon-cloudwatch-agent/bin/default_linux_config.json
```
The configuration file referenced in the compose file above is below.   Note
that metrics_collection_interval of 5 minutes is free tier.  Intervals less
than that incur fees.

```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "region": "us-east-1",
    "logfile": "",
    "debug": false,
    "run_as_user": "root"
  },

  "metrics": {
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "aggregation_dimensions" : [
      ["InstanceId"]
    ],
    "metrics_collected": {
      "cpu": {
        "resources": [ "*" ],
        "measurement": [
          {"name": "cpu_usage_active", "unit": "Percent"},
          {"name": "cpu_usage_iowait", "unit": "Percent"},
          {"name": "cpu_usage_user", "unit": "Percent"},
          {"name": "cpu_usage_system", "unit": "Percent"}
        ],
        "totalcpu": true
      },
      "disk": {
        "resources": [ "*" ],
        "measurement": [ "disk_used_percent" ],
        "drop_device": false
      },
      "mem": {
        "measurement": [
          {"name": "mem_total"},
          {"name": "mem_used_percent", "unit": "Percent"},
          {"name": "mem_free", "unit": "Percent"}
        ]
      }
    }
  }
}
```

## References

- [Github repository](https://github.com/aws/amazon-cloudwatch-agent)
