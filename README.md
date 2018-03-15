mysql
=====

A MySQL role capable of setting up group replication and integrating with ProxySQL.

Requirements
------------

No outside requirements.

Role Variables
--------------

```
# Type: boolean
# Default: false
# Description: Whether to install MySQL from the upstream MySQL repositories
# instead of the distribution-provided package.
mysql_upstream_package: true

# Type: boolean
# Default: false
# Description: Whether to configure group replication between hosts (requires
# `mysql_upstream_package`).
mysql_group_replication: true

# Type: boolean
# Default: false
# Description: Whether to configure group replication with a multi-primary
# configuration.
mysql_group_replication_multi_primary: true

# Type: boolean
# Default: false
# Description: Whether to set up the requirements for ProxySQL to monitor and
# interact with the group (requires `mysql_upstream_package` and
# `mysql_group_replication`).
mysql_proxysql_backend_config: true

# Type: boolean
# Default: false
# Description: Whether to bind to private network.
mysql_private_networking: true

# Type: boolean
# Default: false
# Description: Whether to disallow unencrypted connections.
mysql_repl_require_ssl: true

# Type: string
# Default: (none)
# Description: The password to set for the MySQL `root` account.
mysql_root_pass: adminpass

# Type: string
# Default: (none)
# Description: The username to set for the MySQL replication user.
mysql_repl_user: replicationuser

# Type: string
# Default: (none)
# Description: The password to set for the MySQL replication user.
mysql_repl_pass: replicationpass

# Type: list of dictionaries
# Default: (none)
# Description: A list of dictionaries defining the databases to create.  Each
# dictionary should specify the `name` of the database and optionally provide a
# `datafile` that contains the SQL to be imported into that database.  The
# `datafile` can be added to a `files` directory so Ansible can deploy it to
# the hosts.
mysql_databases:
  - name: testdatabase
    datafile: testdatabase.sql
  - name: otherdatabase

# Type: list of dictionaries
# Default: (none)
# Description: A list of dictionaries defining the users to create.  The
# dictionaries should specify the `name`, `password`, `priv`, and `host`
# attributes.  These values correspond directly to the `mysql_users` module.
mysql_users:
  - name: testuser
    password: testpass
    priv: "testdatabase.*:ALL"
    host: "127.0.0.1"

  - name: monitoruser
    password: monitorpass
    priv: "sys.*:SELECT"
    host: "%"
```

Dependencies
------------

None

Example Playbook
----------------

```
---
- name: Set up MySQL nodes for group replication
  hosts: mysql_nodes
  vars:
    mysql_upstream_package: true
    mysql_group_replication: true
    mysql_group_replication_multi_primary: true
    mysql_proxysql_backend_config: true
    mysql_repl_require_ssl: true
    mysql_root_pass: sammytheshark
    mysql_repl_user: repl
    mysql_repl_pass: replpassword
    mysql_databases:
      - name: "test"
        datafile: "test.sql"
    mysql_users:
      - name: "testuser"
        password: "testpassword"
        priv: "test.*:ALL"
        host: "%"
  roles:
    - role: mysql
```

License
-------

MIT

Author Information
------------------

DigitalOcean Community
