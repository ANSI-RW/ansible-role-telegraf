# Ansible Role: Telegraf

[![Build Status](https://img.shields.io/travis/ANSI-RW/ansible-role-telegraf.svg)](https://travis-ci.org/ANSI-RW/ansible-role-telegraf) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/ANSI-RW/ansible-role-telegraf/master/LICENSE)

Installs and configures Telegraf on RHEL/CentOS or Debian/Ubuntu.

## Requirements

None

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Version that should be installed
telegraf_version: 0.11.1
# Use the included config template
telegraf_conf_use_template: true

telegraf_conf_global_tags: []

telegraf_conf_agent_interval: 10s
telegraf_conf_agent_round_interval: "true"
telegraf_conf_agent_metric_buffer_limit: 1000
telegraf_conf_agent_flush_buffer_when_full: "true"
telegraf_conf_agent_collection_jitter: 0s
telegraf_conf_agent_flush_interval: 10s
telegraf_conf_agent_flush_jitter: 0s
telegraf_conf_agent_debug: "false"
telegraf_conf_agent_quiet: "false"
telegraf_conf_agent_hostname: ""
# Other [agent] config
telegraf_conf_agent_other: []

# [outputs] section config
telegraf_conf_outputs:
  influxdb:
    - urls = ["http://localhost:8086"]
    - database = "telegraf"
    - retention_policy = "default"
    - precision = "s"
    - timeout = "5s"

# [inputs] section config
telegraf_conf_inputs:
  cpu:
    - percpu = true
    - totalcpu = true
    - fielddrop = ["time_*"]
  disk:
    - ignore_fs = ["tmpfs", "devtmpfs"]
  diskio: []
  kernel: []
  mem: []
  processes: []
  swap: []
  system: []
```

Default values for the `telegraf.conf.j2` template are based on the [0.11.1 config](https://github.com/influxdata/telegraf/blob/0.11.1/etc/telegraf.conf).

## Dependencies

None

## Example Playbook

```yaml
- hosts: servers

  vars_files:
    - vars/main.yml

  roles:
    - { role: ANSI-RW.telegraf }
```

Inside `vars/main.yml`:

```yaml
telegraf_conf_global_tags:
  - dc = "us-east-1"
  - rack = "1a"

# ... etc ...
```

## License

MIT
