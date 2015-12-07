# Ansible Role: Telegraf

[![Build Status](https://img.shields.io/travis/rwanyoike/ansible-role-telegraf.svg)](https://travis-ci.org/rwanyoike/ansible-role-telegraf) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/rwanyoike/ansible-role-telegraf/master/LICENSE)

Installs and configures [Telegraf](https://github.com/influxdb/telegraf) on RHEL/CentOS ~~or Debian/Ubuntu~~.

Tested with Telegraf 0.2.x.

## Requirements

None

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Version that should be installed
telegraf_version: 0.2.3
```

See [https://github.com/influxdb/telegraf/tags](https://github.com/influxdb/telegraf/tags) for a listing of available versions (e.g. `0.2.2`, `0.2.3`).

```yaml
# Use the included config template
telegraf_config_use_template: true
# Where config files will be stored
telegraf_config_dir: /etc/opt/telegraf
```

Whether to use the included Telegraf config template. Set this to `false` and copy your own `telegraf.conf` file into the `telegraf_config_dir` if you'd like to use a more complicated setup. If this variable is set to `true`, the setup will be derived from defined variables.

```yaml
# Config [tags] section
telegraf_config_tags: []
  # - dc = "us-east-1"

# Config [agent] section
telegraf_config_agent:
  # Default data collection interval for all plugins
  - interval = "10s"
  # Rounds collection interval to 'interval'
  # ie, if interval="10s" then always collect on :00, :10, :20, etc.
  - round_interval = true
  # Default data flushing interval for all outputs. You should not set this
  # below interval. Maximum flush_interval will be flush_interval +
  # flush_jitter
  - flush_interval = "10s"
  # Jitter the flush interval by a random amount. This is primarily to avoid
  # large write spikes for users running a large number of telegraf instances.
  # ie, a jitter of 5s and interval 10s means flushes will happen every 10-15s
  - flush_jitter = "0s"
  # Run telegraf in debug mode
  - debug = false
  # Override default hostname, if empty use os.Hostname()
  - hostname = ""

# Config [outputs] section
telegraf_config_outputs:
  - output: influxdb
    config:
      # The full HTTP or UDP endpoint URL for your InfluxDB instance. Multiple
      # urls can be specified but it is assumed that they are part of the same
      # cluster, this means that only ONE of the urls will be written to each
      # interval. urls = ["udp://localhost:8089"] # UDP endpoint example
      - urls = ["http://localhost:8086"]
      # The target database for metrics (telegraf will create it if not exists)
      - database = "telegraf"
      # Precision of writes, valid values are n, u, ms, s, m, and h
      # note: using second precision greatly helps InfluxDB compression
      - precision = "s"

# Config [plugins] section
telegraf_config_plugins:
  # Read metrics about cpu usage
  - plugin: cpu
    config:
      # Whether to report per-cpu stats or not
      - percpu = true
      # Whether to report total system cpu stats or not
      - totalcpu = true
      # Comment this line if you want the raw CPU time metrics
      - drop = ["cpu_time"]
  # Read metrics about disk usage by mount point
  - plugin: disk
  # Read metrics about disk IO by device
  - plugin: io
  # Read metrics about memory usage
  - plugin: mem
  # Read metrics about swap memory usage
  - plugin: swap
  # Read metrics about system load & uptime
  - plugin: system

# Other sections
telegraf_config_other: []
```

Settings for the `telegraf.conf` template that will be copied to the `telegraf_config_dir`. Based on the [v0.2.3 sample config](https://github.com/influxdb/telegraf/blob/v0.2.3/etc/telegraf.conf).

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

telegraf_config_other:
  - section: statsd
    config:
      - service_address = ":8125"

# ... etc ...
```

## License

MIT

## Author Information

This role was created in 2015 by [Raymond Wanyoike](https://github.com/rwanyoike).
