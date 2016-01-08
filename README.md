# Ansible Role: Telegraf

[![Build Status](https://img.shields.io/travis/rwanyoike/ansible-role-telegraf.svg)](https://travis-ci.org/rwanyoike/ansible-role-telegraf) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/rwanyoike/ansible-role-telegraf/master/LICENSE)

Installs and configures Telegraf on RHEL/CentOS ~~or Debian/Ubuntu~~.

Tested with Telegraf 0.3.0_beta2.

## Requirements

None

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Version that should be installed.
telegraf_version: 0.3.0_beta2

# Use the included config template.
telegraf_config_use_template: true
# Where config files will be stored.
telegraf_config_dir: /etc/opt/telegraf
# Config [tags] section.
telegraf_config_tags: []
# Config [agent] section.
telegraf_config_agent:
  # Default data collection interval for all plugins.
  - interval = "10s"
  # Rounds collection interval to 'interval'
  # ie, if interval="10s" then always collect on :00, :10, :20, etc.
  - round_interval = true
  # Default data flushing interval for all outputs. You should not set this
  # below interval. Maximum flush_interval will be flush_interval +
  # flush_jitter.
  - flush_interval = "10s"
  # Jitter the flush interval by a random amount. This is primarily to avoid
  # large write spikes for users running a large number of telegraf instances.
  # ie, a jitter of 5s and interval 10s means flushes will happen every 10-15s.
  - flush_jitter = "0s"
  # Run telegraf in debug mode.
  - debug = false
  # Override default hostname, if empty use os.Hostname().
  - hostname = ""
# Config [outputs] section.
telegraf_config_outputs:
  - output: influxdb
    config:
      # The full HTTP or UDP endpoint URL for your InfluxDB instance. Multiple
      # urls can be specified but it is assumed that they are part of the same
      # cluster, this means that only ONE of the urls will be written to each
      # interval. urls = ["udp://localhost:8089"] # UDP endpoint example
      - urls = ["http://localhost:8086"]
      # The target database for metrics (telegraf will create it if not
      # exists).
      - database = "telegraf"
      # Precision of writes, valid values are n, u, ms, s, m, and h. Note:
      # using second precision greatly helps InfluxDB compression.
      - precision = "s"
# Config [plugins] section.
telegraf_config_plugins:
  # Read metrics about cpu usage.
  - plugin: cpu
    config:
      # Whether to report per-cpu stats or not.
      - percpu = true
      # Whether to report total system cpu stats or not.
      - totalcpu = true
      # Comment this line if you want the raw CPU time metrics.
      - drop = ["time_*"]
  # Read metrics about disk usage by mount point.
  - plugin: disk
  # Read metrics about disk IO by device.
  - plugin: diskio
  # Read metrics about memory usage.
  - plugin: mem
  # Read metrics about swap memory usage.
  - plugin: swap
  # Read metrics about system load & uptime.
  - plugin: system
```

Default values for the `telegraf.conf.j2` template are based on the [0.3.0 sample config](https://github.com/influxdata/telegraf/blob/0.3.0/etc/telegraf.conf).

## Dependencies

None

## Example Playbook

```yaml
- hosts: webservers

  vars_files:
    - vars/main.yml

  roles:
    - role: rwanyoike.telegraf
```

Inside `vars/main.yml`:

```yaml
telegraf_config_tags:
  - dc = "us-east-1"

# ... etc ...
```

## License

MIT

## Author Information

This role was created in 2015 by [Raymond Wanyoike](https://github.com/rwanyoike).
