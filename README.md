# Ansible Role: Telegraf

Installs and configures Telegraf on RHEL/CentOS.

[Telegraf](https://github.com/influxdb/telegraf) is an agent written in Go for
collecting metrics from the system it's running on, or from other services, and
writing them into InfluxDB.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see
`defaults/main.yml`):

```yaml
telegraf_version: 0.2.3
```

Telegraf version that should be installed. See [https://github.com/influxdb/telegraf/tags](https://github.com/influxdb/telegraf/tags)
for a listing of available versions (e.g. `0.2.2`, `0.2.3`).

```yaml
telegraf_config_path: /etc/opt/telegraf
```

The path in which Telegraf config files will be stored.

```yaml
telegraf_config_use_template: true
```

Whether to use the included Telegraf config template. Set this to `false` and
copy your own `telegraf.conf` file into the `telegraf_config_path` if you'd
like to use a more complicated setup. If this variable is set to `true`, the
setup will be derived from defined variables.

```yaml
telegraf_config_template_path: telegraf.conf.j2
```

The config file to be copied (if `telegraf_config_use_template` is `true`).
Defaults to the template inside `templates/telegraf.conf.j2`. This path should
be relative to the directory of your playbook.

```yaml
# Tags section
telegraf_config_tags: []

# Agent section
telegraf_config_agent:
  - interval = "10s"
  - round_interval = true
  - flush_interval = "10s"
  - flush_jitter = "5s"
  - flush_retries = 2
  - debug = false
  - hostname = ""

# Outputs section
telegraf_config_outputs:
  - output: influxdb
    config:
      - urls = ["http://localhost:8086"]
      - database = "telegraf"
      - precision = "s"

# Plugins section
telegraf_config_plugins:
  - plugin: cpu
    config:
      - percpu = true
      - totalcpu = true
      - drop = ["cpu_time"]
  - plugin: disk
  - plugin: io
  - plugin: mem
  - plugin: swap
  - plugin: system

# Service section
telegraf_config_service_plugins: []
```

Settings for the `telegraf.conf` template that will be copied to the `telegraf_config_path`.
Refer to the sample [v0.2.3](https://github.com/influxdb/telegraf/blob/v0.2.3/etc/telegraf.conf)
config in for more information.

## Dependencies

None.

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

MIT.

## Author Information

This role was created in 2015 by [Raymond Wanyoike](https://github.com/rwanyoike).
