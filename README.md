collectd
=========

Install and configure collectd on your system.

|Travis|GitHub|Quality|Downloads|
|------|------|-------|---------|
|[![travis](https://travis-ci.org/robertdebock/ansible-role-collectd.svg?branch=master)](https://travis-ci.org/robertdebock/ansible-role-collectd)|[![github](https://github.com/robertdebock/ansible-role-collectd/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-collectd/actions)|![quality](https://img.shields.io/ansible/quality/46251)|![downloads](https://img.shields.io/ansible/role/d/46251)|

Example Playbook
----------------

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes
  vars:
    _collectd_postgres_plugin_dependencies:
      default:
        - collectd-postgresql
      Debian: []
    _collectd_write_http_dependencies:
      default: []
      Alpine:
        - collectd-write_http
      RedHat:
        - collectd-write_http
    collectd_plugin_logging: logfile
    collectd_basic_plugins:
      - cpu
      - interface
      - load
      - memory
    collectd_plugins:
      - name: write_http
        dependencies: "{{ _collectd_write_http_dependencies[ansible_os_family] | default(_collectd_write_http_dependencies['default']) }}"
        config: |
          <Node "test">
            URL "127.0.0.1:8080/test.collectd"
            Format "JSON"
            StoreRates true
          </Node>
      - name: postgresql
        dependencies: "{{ _collectd_postgres_plugin_dependencies[ansible_os_family] | default(_collectd_postgres_plugin_dependencies['default']) }}"
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
- name: Converge
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.epel
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    - name: check if connection still works
      ping:
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

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

# Use 'collectd_basic_plugins' to enable plugins not requiring additional configuration and/or dependencies to work
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

# Use 'collectd_plugins' to enable plugins requiring additional configuration and/or dependencies to work
collectd_plugins: []
# examples:
#  - name: example
#    interval: 120 #seconds
#    flush_interval: 600 #seconds
#    flush_timeout:
#    config: |4
#      Something: true
#      <Nested block>
#        NestedKey: "value"
#      </Nested>
#  - name: write_http
#    config: |4
#      <Node "oms">
#         URL "127.0.0.1:26000/oms.collectd"
#         Format "JSON"
#         StoreRates true
#      </Node>
#  - name: postgresql
#    dependencies:
#      - collectd-postgresql
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

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.epel

```

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/collectd.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tags|
|---------|----|
|alpine|all|
|debian|all|
|el|7, 8|
|fedora|all|
|opensuse|all|
|ubuntu|bionic|

The minimum version of Ansible required is 2.7 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.



Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-collectd) are done on every commit, pull request, release and periodically.

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

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
