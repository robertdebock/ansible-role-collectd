# [collectd](#collectd)

Install and configure collectd on your system.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-collectd.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-collectd)|[![github](https://github.com/robertdebock/ansible-role-collectd/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-collectd/actions)|[![quality](https://img.shields.io/ansible/quality/46251)](https://galaxy.ansible.com/robertdebock/collectd)|[![downloads](https://img.shields.io/ansible/role/d/46251)](https://galaxy.ansible.com/robertdebock/collectd)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-collectd.svg)](https://github.com/robertdebock/ansible-role-collectd/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes
  vars:
    collectd_plugin_logging: logfile
    collectd_basic_plugins:
      - cpu
      - interface
      - load
      - memory
    collectd_plugins:
      - name: write_http
        config: |
          <Node "test">
            URL "127.0.0.1:8080/test.collectd"
            Format "JSON"
            StoreRates true
          </Node>
      - name: postgresql
        config: |
          <Query tickets>
            Statement "SELECT count(t.id) AS count FROM tickets t WHERE t.closed is null;"
            <Result>
              Type gauge
              InstancePrefix "tickets"
              ValuesFrom "count"
            </Result>
          </Query>
          <Database "test">
            Host "psql-database.hostname.com"
            Port "5432"
            User "my_psqladminuser"
            Password "my_passwd"
            SSLMode "prefer"
            Query tickets
          </Database>

  roles:
    - role: robertdebock.collectd
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.epel
```

For verification `molecule/resources/verify.yml` runs after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: no

  tasks:
    - name: check if connection still works
      ping:
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for collectd

collectd_conf_hostname: "{{ ansible_hostname }}"
collectd_conf_fqdnlookup: "false"
collectd_conf_basedir: /var/lib/collectd
collectd_conf_pidfile: /var/run/collectd.pid
collectd_conf_typesdb: /usr/share/collectd/types.db

collectd_conf_autoloadplugin: "false"
collectd_conf_collectinternalstats: "false"

collectd_conf_interval: 10
collectd_conf_maxreadinterval: 86400
collectd_conf_timeout: 2
collectd_conf_readthreads: 5
collectd_conf_writethreads: 5

collectd_conf_include_dir: /etc/collectd.d
collectd_conf_fnmatch_filters:
  - "*.conf"

#### Logging Configuration

collectd_plugin_logging: syslog

collectd_plugin_logging_directory: "/var/log/collectd"

collectd_plugin_logfile_loglevel: "info"
collectd_plugin_logfile_file: "{{ collectd_plugin_logging_directory }}/collectd.log"
collectd_plugin_logfile_timestamp: "true"
collectd_plugin_logfile_printseverity: "false"

collectd_plugin_logstash_loglevel: "info"
collectd_plugin_logstash_file: "{{ collectd_plugin_logging_directory }}/collectd.json.log"

collectd_plugin_syslog_loglevel: "info"
# collectd_plugin_syslog_notifylevel: ""

# Use 'collectd_basic_plugins' to enable plugins not requiring additional
# configuration.
collectd_basic_plugins:
  - cpu
  - interface
  - load
  - memory
  # - aggregation
  # - amqp
  # - apache
  # - apcups
  # - apple_sensors
  # - aquaero
  # - ascent
  # - barometer
  # - battery
  # - bind
  # - ceph
  # - cgroups
  # - chrony
  # - conntrack
  # - contextswitch
  # - cpu
  # - cpufreq
  # - cpusleep
  # - csv
  # - curl
  # - curl_json
  # - curl_xml
  # - dbi
  # - df
  # - disk
  # - dns
  # - dpdkevents
  # - dpdkstat
  # - drbd
  # - email
  # - entropy
  # - ethstat
  # - exec
  # - fhcount
  # - filecount
  # - fscache
  # - gmond
  # - gps
  # - grpc
  # - hddtemp
  # - hugepages
  # - intel_pmu
  # - intel_rdt
  # - interface
  # - ipc
  # - ipmi
  # - iptables
  # - ipvs
  # - irq
  # - java
  # - load
  # - lpar
  # - lua
  # - lvm
  # - madwifi
  # - mbmon
  # - mcelog
  # - md
  # - memcachec
  # - memcached
  # - memory
  # - mic
  # - modbus
  # - mqtt
  # - multimeter
  # - mysql
  # - netapp
  # - netlink
  # - network
  # - nfs
  # - nginx
  # - notify_desktop
  # - notify_email
  # - notify_nagios
  # - ntpd
  # - numa
  # - nut
  # - olsrd
  # - onewire
  # - openldap
  # - openvpn
  # - oracle
  # - ovs_events
  # - ovs_stats
  # - perl
  # - pinba
  # - ping
  # - postgresql
  # - powerdns
  # - processes
  # - protocols
  # - python
  # - redis
  # - routeros
  # - rrdcached
  # - rrdtool
  # - sensors
  # - serial
  # - sigrok
  # - smart
  # - snmp
  # - snmp_agent
  # - statsd
  # - swap
  # - table
  # - tail
  # - tail_csv
  # - tape
  # - tcpconns
  # - teamspeak2
  # - ted
  # - thermal
  # - tokyotyrant
  # - turbostat
  # - unixsock
  # - uptime
  # - users
  # - uuid
  # - varnish
  # - virt
  # - vmem
  # - vserver
  # - wireless
  # - write_graphite
  # - write_http
  # - write_kafka
  # - write_log
  # - write_mongodb
  # - write_prometheus
  # - write_redis
  # - write_riemann
  # - write_sensu
  # - write_tsdb
  # - xencpu
  # - xmms
  # - zfs_arc
  # - zone
  # - zookeeper

# Use 'collectd_plugins' to enable plugins requiring additional configuration.
collectd_plugins: []
# examples:
#  - name: example
#    interval: 120 #seconds
#    flush_interval: 600 #seconds
#    flush_timeout:
#    config: |4
#      Something: yes
#      <Nested block>
#        NestedKey: "value"
#      </Nested>
#  - name: write_http
#    config: |4
#      <Node "oms">
#         URL "127.0.0.1:26000/oms.collectd"
#         Format "JSON"
#         StoreRates yes
#      </Node>
#  - name: postgresql
#    config: |4
#      <Query tickets>
#          Statement "SELECT count(t.id) AS count FROM tickets t WHERE t.closed is null;"
#          <Result>
#            Type gauge
#            InstancePrefix "tickets"
#            ValuesFrom "count"
#          </Result>
#      </Query>
#      <Database "test">
#        Host "psql-database.hostname.com"
#        Port "5432"
#        User "my_psqladminuser"
#        Password "my_passwd"
#        SSLMode "prefer"
#        Query tickets
#      </Database>
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.epel

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/collectd.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|alpine|all|
|amazon|2018.03|
|el|8|
|debian|buster, bullseye|
|fedora|31, 32|
|opensuse|all|
|ubuntu|focal, bionic, xenial|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.

## [Exceptions](#exceptions)

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| centos:7 | Plugin dependency collectd-write_http is missing. |


## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-collectd) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-collectd/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [jellevandehaterd](https://github.com/jellevandehaterd)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
