freehck.cassandra
=========

Install cassandra on baremetal

Description
-----------

This role installs a cassandra on baremetal. All you need is to specify 

Role Variables
--------------

variable name | default value | comment
------------- | ------------- | -------
cassandra_version | 3.11.6  | version to be installed
cassandra_version_sha256 | ce34edebd1b6bb35216ae97bd06d3efc338c05b273b78267556a99f85d30e45b | SHA256 checksum to check the downloaded file
cassandra_force_upgrade | false | if false and /opt/cassandra exists it won't be replaced
cassandra_user | cassandra | user the cassandra will be running with
cassandra_group | cassandra | group the cassandra will be running with
cassandra_seeds_group_name | cassandra_seeds | group for cassandra hosts is used to generate list of nodes ip addresses
cassandra_cluster_name | cassandra | cluster name
cassandra_path_install | /opt | path the cassanda will be installed
cassandra_path_data | /opt/cassandra-data/data | array of paths the cassanda will store data in
cassandra_path_logs | /opt/cassandra-data/logs | path the cassanda will store logs
cassandra_path_caches | /opt/cassandra-data/caches | path the cassanda will store caches
cassandra_node_iface_ext | eth0 | external network interface name (rpc_address, client connection)
cassandra_node_iface_int | eth0 | internal network interface name (listen_address, node communication)
cassandra_mem_gb | 0 | RAM available to cassandra in GB
cassandra_mem_pc | 50 | RAM available to cassandra in percent. Ignored if cassandra_mem_gb provided

Example
-------

###### inventory

    cass01 ansible_host=10.10.10.11
    cass02 ansible_host=10.10.10.12
    cass03 ansible_host=10.10.10.13

    [cassandra_seeds]
    cass0[1:3]


###### group_vars/cassandra_seeds.yml

    cassandra_version: 3.11.6
    cassandra_iface: eth0
    cassandra_node_iface_ext: "{{ cassandra_iface }}"
    cassandra_node_iface_int: "{{ cassandra_iface }}"
    cassandra_install_openjdk: true
    cassandra_seeds_group_name: cassandra_seeds


###### playbook.yml

    - hosts: cassandra_seeds
      become: true
      max_fail_percentage: 0
      roles:
        - role: freehck.cassandra

Tests
-----

This role provides you with a Vagrant-based test suite under `tests/` directory.

To get a fully operational Kubernetes cluster, type following command in terminal:

    cd tests && make

When tests have passed, you can check your installation with following commands:

    vagrant ssh cass01
    /opt/cassandra/bin/cqlsh 10.10.10.11 -u cassandra -p cassandra --request-timeout=60
    CREATE KEYSPACE test WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'} AND durable_writes = true;
    DESCRIBE keyspaces;
    DROP KEYSPACE test;

Install
-------

This role can be installed from [Ansible Galaxy](https://galaxy.ansible.com/):

`ansible-galaxy install freehck.cassandra`

License
-------

MIT

Author Information
------------------

Dmitrii Kashin, <freehck@freehck.ru>
