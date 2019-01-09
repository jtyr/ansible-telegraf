telegraf
========

Ansible role which installs and configures Telegraf.

The configuration of the role is done in such way that it should not be necessary
to change the role for any kind of configuration. All can be done either by
changing role parameters or by declaring completely new configuration as a
variable. That makes this role absolutely universal. See the examples below for
more details.

Please report any issues or send PR.


Examples
--------

```yaml
---

- name Example of how to use the role with default parameters
  hosts: myhost1
  roles:
    - telegraf

- name Example of how to change the default InfluxDB output parameters
  hosts: myhost2
  vars:
    # Set new InfluxDB URL
    telegraf_config_outputs_influxdb_urls:
      - http://192.168.12.34:8086
    # Set the DB name
    telegraf_config_outputs_influxdb_database: mydb
  roles:
    - telegraf

- name Example of how to change the default InfluxDB output to Prometheus
  hosts: myhost2
  vars:
    telegraf_config_outputs:
      - prometheus_client:
          - listen: :9273
  roles:
    - telegraf

- name Example of how to add additional parameter to the diskio input
  hosts: myhost2
  vars:
    telegraf_config_inputs_diskio:
      sevices:
        - sda
        - sdb
      skip_serial_number: no
  roles:
    - telegraf

- name Example of how to add another inputs
  hosts: myhost2
  vars:
    telegraf_config_inputs__custom:
      - apache:
          - urls:
              - http://localhost/server-status?auto
      - docker:
          - endpoint: unix:///var/run/docker.sock
            timeout: 5s
      - net:
          - interfaces:
              - eth0
      - netstat: [{}]
  roles:
    - telegraf
```


Role variables
--------------

List of variables used by the role:

```yaml
# YUM repo URL
telegraf_yum_repo_url: "{{ influxdata_yum_repo_url | default('https://repos.influxdata.com/centos/$releasever/$basearch/stable') }}"

# YUM repo GPG key URL
telegraf_yum_repo_gpgkey: "{{ influxdata_yum_repo_gpgkey | default('https://repos.influxdata.com/influxdb.key') }}"

# Additional YUM repo params
telegraf_yum_repo_params: "{{ influxdata_yum_repo_params | default({}) }}"

# APT repo string
telegraf_apt_repo_string: "{{ influxdata_apt_repo_string | default('deb https://repos.influxdata.com/ubuntu ' ~ ansible_facts.distribution_release ~ ' stable') }}"

# GPG key for the APT repo
telegraf_apt_repo_key: "{{ influxdata_apt_repo_key | default('https://repos.influxdata.com/influxdb.key') }}"

# Additional APT repo params
telegraf_apt_repo_params: "{{ influxdata_apt_repo_params | default({}) }}"

# Package to be installed (explicit version can be specified here)
telegraf_pkg: telegraf

# Name of the telegraf service
telegraf_service: telegraf

# Config file permissions
telegraf_owner: telegraf
telegraf_group: telegraf
telegraf_mode: 0640

# Location of the telegraf config file
telegraf_config_file: /etc/telegraf/telegraf.conf


# Default options of the global_tags table
telegraf_config_global_tags__default: {}

# Custom options of the global_tags table
telegraf_config_global_tags__custom: {}

# Final options of the global_tags table
telegraf_config_global_tags: "{{
  telegraf_config_global_tags__default | combine(
  telegraf_config_global_tags__custom) }}"


# Values of the default options of the agent table
telegraf_config_agent_interval: 10s
telegraf_config_agent_round_interval: yes
telegraf_config_agent_metric_batch_size: 1000
telegraf_config_agent_metric_buffer_limit: 10000
telegraf_config_agent_collection_jitter: 0s
telegraf_config_agent_flush_interval: 10s
telegraf_config_agent_flush_jitter: 0s
telegraf_config_agent_precision: ""
telegraf_config_agent_debug: no
telegraf_config_agent_quiet: no
telegraf_config_agent_logfile: ""
telegraf_config_agent_hostname: ""
telegraf_config_agent_omit_hostname: no

# Default options of the agent table
telegraf_config_agent__default:
  interval: "{{ telegraf_config_agent_interval }}"
  round_interval: "{{ telegraf_config_agent_round_interval }}"
  metric_batch_size: "{{ telegraf_config_agent_metric_batch_size }}"
  metric_buffer_limit: "{{ telegraf_config_agent_metric_buffer_limit }}"
  collection_jitter: "{{ telegraf_config_agent_collection_jitter }}"
  flush_interval: "{{ telegraf_config_agent_flush_interval }}"
  flush_jitter: "{{ telegraf_config_agent_flush_jitter }}"
  precision: "{{ telegraf_config_agent_precision }}"
  debug: "{{ telegraf_config_agent_debug }}"
  quiet: "{{ telegraf_config_agent_quiet }}"
  logfile: "{{ telegraf_config_agent_logfile }}"
  hostname: "{{ telegraf_config_agent_hostname }}"
  omit_hostname: "{{ telegraf_config_agent_omit_hostname }}"

# Custom options of the agent table
telegraf_config_agent__custom: {}

# Final options of the agent table
telegraf_config_agent: "{{
  telegraf_config_agent__default | combine(
  telegraf_config_agent__custom) }}"


# Values of the options of the default influxdb output table
telegraf_config_outputs_influxdb_urls:
  - http://127.0.0.1:8086
telegraf_config_outputs_influxdb_database: telegraf
telegraf_config_outputs_influxdb_retention_policy: ""
telegraf_config_outputs_influxdb_write_consistency: any
telegraf_config_outputs_influxdb_timeout: 5s

# Default options of the first item of the influxdb output table
telegraf_config_outputs_influxdb__default:
  urls: "{{ telegraf_config_outputs_influxdb_urls }}"
  database: "{{ telegraf_config_outputs_influxdb_database }}"
  retention_policy: "{{ telegraf_config_outputs_influxdb_retention_policy }}"
  write_consistency: "{{ telegraf_config_outputs_influxdb_write_consistency }}"
  timeout: "{{ telegraf_config_outputs_influxdb_timeout }}"

# Custom options of the influxdb output table
telegraf_config_outputs_influxdb__custom: {}

# Default item of the influxdb output table
telegraf_config_outputs_influxdb_item__default:
  - "{{
        telegraf_config_outputs_influxdb__default | combine(
        telegraf_config_outputs_influxdb__custom) }}"

# Custom item of the influxdb output table
telegraf_config_outputs_influxdb_item__custom: []

# Final options of the influxdb output table
telegraf_config_outputs_influxdb: "{{
  telegraf_config_outputs_influxdb_item__default +
  telegraf_config_outputs_influxdb_item__custom }}"


# Default outputs
telegraf_config_outputs__default:
  - influxdb: "{{ telegraf_config_outputs_influxdb }}"

# Custom outputs
telegraf_config_outputs__custom: []

# Final outputs
telegraf_config_outputs: "{{
  telegraf_config_outputs__default +
  telegraf_config_outputs__custom }}"


# Values of the options of the default cpu input table
telegraf_config_inputs_cpu_percpu: yes
telegraf_config_inputs_cpu_totalcpu: yes
telegraf_config_inputs_cpu_collect_cpu_time: no
telegraf_config_inputs_cpu_report_active: no

# Default options of the cpu input table
telegraf_config_inputs_cpu__default:
  percpu: "{{ telegraf_config_inputs_cpu_percpu }}"
  totalcpu: "{{ telegraf_config_inputs_cpu_totalcpu }}"
  collect_cpu_time: "{{ telegraf_config_inputs_cpu_collect_cpu_time }}"
  report_active: "{{ telegraf_config_inputs_cpu_report_active }}"

# Custom options of the cpu input table
telegraf_config_inputs_cpu__custom: {}

# Default item of the cpu input table
telegraf_config_inputs_cpu_item__default:
  - "{{
        telegraf_config_inputs_cpu__default | combine(
        telegraf_config_inputs_cpu__custom) }}"

# Custom item of the cpu input table
telegraf_config_inputs_cpu_item__custom: []

# Final options of the cpu input table
telegraf_config_inputs_cpu: "{{
  telegraf_config_inputs_cpu_item__default +
  telegraf_config_inputs_cpu_item__custom }}"


# Default values of the options of the default disk input table
telegraf_config_inputs_disk_ignore_fs__default:
  - tmpfs
  - devtmpfs
  - devfs

# Custom values of the options of the default disk input table
telegraf_config_inputs_disk_ignore_fs__custom: []

# Final values of the options of the default disk input table
telegraf_config_inputs_disk_ignore_fs: "{{
  telegraf_config_inputs_disk_ignore_fs__default +
  telegraf_config_inputs_disk_ignore_fs__custom }}"

# Default options of the disk input table
telegraf_config_inputs_disk__default:
  ignore_fs: "{{ telegraf_config_inputs_disk_ignore_fs }}"

# Custom options of the disk input table
telegraf_config_inputs_disk__custom: {}

# Default item of the disk input table
telegraf_config_inputs_disk_item__default:
  - "{{
        telegraf_config_inputs_disk__default | combine(
        telegraf_config_inputs_disk__custom) }}"

# Custom item of the disk input table
telegraf_config_inputs_disk_item__custom: []

# Final options of the disk input table
telegraf_config_inputs_disk: "{{
  telegraf_config_inputs_disk_item__default +
  telegraf_config_inputs_disk_item__custom }}"


# Default options of the diskio input table
telegraf_config_inputs_diskio__default: {}

# Custom options of the diskio input table
telegraf_config_inputs_diskio__custom: {}

# Default item of the diskio input table
telegraf_config_inputs_diskio_item__default:
  - "{{
        telegraf_config_inputs_diskio__default | combine(
        telegraf_config_inputs_diskio__custom) }}"

# Custom item of the diskio input table
telegraf_config_inputs_diskio_item__custom: []

# Final options of the diskio input table
telegraf_config_inputs_diskio: "{{
  telegraf_config_inputs_diskio_item__default +
  telegraf_config_inputs_diskio_item__custom }}"


# Default options of the kernel input table
telegraf_config_inputs_kernel__default: {}

# Custom options of the kernel input table
telegraf_config_inputs_kernel__custom: {}

# Default item of the kernel input table
telegraf_config_inputs_kernel_item__default:
  - "{{
        telegraf_config_inputs_kernel__default | combine(
        telegraf_config_inputs_kernel__custom) }}"

# Custom item of the kernel input table
telegraf_config_inputs_kernel_item__custom: []

# Final options of the kernel input table
telegraf_config_inputs_kernel: "{{
  telegraf_config_inputs_kernel_item__default +
  telegraf_config_inputs_kernel_item__custom }}"


# Default options of the mem input table
telegraf_config_inputs_mem__default: {}

# Custom options of the mem input table
telegraf_config_inputs_mem__custom: {}

# Default item of the mem input table
telegraf_config_inputs_mem_item__default:
  - "{{
        telegraf_config_inputs_mem__default | combine(
        telegraf_config_inputs_mem__custom) }}"

# Custom item of the mem input table
telegraf_config_inputs_mem_item__custom: []

# Final options of the mem input table
telegraf_config_inputs_mem: "{{
  telegraf_config_inputs_mem_item__default +
  telegraf_config_inputs_mem_item__custom }}"


# Default options of the processes input table
telegraf_config_inputs_processes__default: {}

# Custom options of the processes input table
telegraf_config_inputs_processes__custom: {}

# Default item of the processes input table
telegraf_config_inputs_processes_item__default:
  - "{{
        telegraf_config_inputs_processes__default | combine(
        telegraf_config_inputs_processes__custom) }}"

# Custom item of the processes input table
telegraf_config_inputs_processes_item__custom: []

# Final options of the processes input table
telegraf_config_inputs_processes: "{{
  telegraf_config_inputs_processes_item__default +
  telegraf_config_inputs_processes_item__custom }}"


# Default options of the swap input table
telegraf_config_inputs_swap__default: {}

# Custom options of the swap input table
telegraf_config_inputs_swap__custom: {}

# Default item of the swap input table
telegraf_config_inputs_swap_item__default:
  - "{{
        telegraf_config_inputs_swap__default | combine(
        telegraf_config_inputs_swap__custom) }}"

# Custom item of the swap input table
telegraf_config_inputs_swap_item__custom: []

# Final options of the swap input table
telegraf_config_inputs_swap: "{{
  telegraf_config_inputs_swap_item__default +
  telegraf_config_inputs_swap_item__custom }}"


# Default options of the system input table
telegraf_config_inputs_system__default: {}

# Custom options of the system input table
telegraf_config_inputs_system__custom: {}

# Default item of the system input table
telegraf_config_inputs_system_item__default:
  - "{{
        telegraf_config_inputs_system__default | combine(
        telegraf_config_inputs_system__custom) }}"

# Custom item of the system input table
telegraf_config_inputs_system_item__custom: []

# Final options of the system input table
telegraf_config_inputs_system: "{{
  telegraf_config_inputs_system_item__default +
  telegraf_config_inputs_system_item__custom }}"


# Default inputs
telegraf_config_inputs__default:
  - cpu: "{{ telegraf_config_inputs_cpu }}"
  - disk: "{{ telegraf_config_inputs_disk }}"
  - diskio: "{{ telegraf_config_inputs_diskio }}"
  - kernel: "{{ telegraf_config_inputs_kernel }}"
  - mem: "{{ telegraf_config_inputs_mem }}"
  - processes: "{{ telegraf_config_inputs_processes }}"
  - swap: "{{ telegraf_config_inputs_swap }}"
  - system: "{{ telegraf_config_inputs_system }}"

# Custom inputs
telegraf_config_inputs__custom: []

# Final inputs
telegraf_config_inputs: "{{
  telegraf_config_inputs__default +
  telegraf_config_inputs__custom }}"


# Default config
telegraf_config__default:
  global_tags: "{{ telegraf_config_global_tags }}"
  agent: "{{ telegraf_config_agent }}"
  outputs: "{{ telegraf_config_outputs }}"
  inputs: "{{ telegraf_config_inputs }}"

# Custom config
telegraf_config__custom: {}

# Final config
telegraf_config: "{{
  telegraf_config__default | combine(
  telegraf_config__custom) }}"
```

The above `telegraf_config` creates the following configuration file:

```toml
[agent]
collection_jitter = "0s"
debug = false
flush_interval = "10s"
flush_jitter = "0s"
hostname = ""
interval = "10s"
logfile = ""
metric_batch_size = 1000
metric_buffer_limit = 10000
omit_hostname = false
precision = ""
quiet = false
round_interval = true

[global_tags]

[[inputs.cpu]]
collect_cpu_time = false
percpu = true
report_active = false
totalcpu = true

[[inputs.disk]]
ignore_fs = ["tmpfs", "devtmpfs", "devfs"]

[[inputs.diskio]]

[[inputs.kernel]]

[[inputs.mem]]

[[inputs.processes]]

[[inputs.swap]]

[[inputs.system]]

[[outputs.influxdb]]
database = "telegraf"
retention_policy = ""
timeout = "5s"
urls = ["http://127.0.0.1:8086"]
write_consistency = "any"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`influxdb`](https://github.com/jtyr/ansible-influxdb) (optional)
- [`prometheus`](https://github.com/jtyr/ansible-prometheus) (optional)


License
-------

MIT


Author
------

Jiri Tyr
