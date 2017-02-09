Rabbitmq 
========

[![Build Status](https://travis-ci.org/abelboldu/ansible-rabbitmq.svg?branch=master)](https://travis-ci.org/abelboldu/ansible-rabbitmq) on master branch

Playbook that installs and configures RabbitMQ message broker.

Supports standalone or simple cluster deployment, it is based on
Mayeu.RabbitMQ role, without SSL or federation support.

## Installation

Use Ansible galaxy to install this playbook:

    $ ansible-galaxy install openstack-ansible-galaxy.rabbitmq

## Supported system

Ubuntu 14.04 (Trusty), Ubuntu 16.04 (Xenial) and CentOS 7

## Role Variables

### Environment

Use `rabbitmq_conf_env` so set Environment variables such as NODENAME,
HOSTNAME, RABBITMQ_USE_LONGNAME, NODE_PORT, NODE_IP_ADDRESS, etc.

Example:

```yaml
rabbitmq_conf_env:
  NODENAME: rabbit1

```

### Configuration file

`rabbitmq_tcp_address` - listening address for the tcp interface, such
as `0.0.0.0`.

`rabbitmq_tcp_port` - listening port for the tcp interface, such as `5672`.

`rabbitmq_cluster` - a boolean variable, when set to `True` the role will add
all nodes in a play group to a cluster setup in a configuration file. It
depends on a `ansible_play_hosts` magic variable, found in ansible 2.2
or later.

`rabbitmq_erlang_cookie` - only used when `rabbitmq_cluster` is used, to
identify members of a single cluster.

### Plugins

`rabbitmq_plugins` - list of plugins to activate.

### Users

`rabbitmq_users` - list of users, and associated vhost and password.
Example on defining the users configuration:

```yaml
rabbitmq_users:
  - user:     user1
    password: password1             # Optional, defaults to ""
    vhost:    vhost1                # Optional, defaults to "/"
    node:     node_name             # Optional, defaults to "rabbit"
    configure_priv: "^resource.*"   # Optional, defaults to ".*"
    read_priv: "^$"                 # Disallow reading (defaults to ".*")
    write_priv: "^$"                # Disallow writing (defaults to ".*")
  - user:     user2
    password: password2
    vhost:    vhost1
    force:    no
    tags:                           # Optional, user tags
    - administrator
  - user:     guest
    state:    absent                # Optional, removes user (defaults to "present")
```

### Vhosts

`rabbitmq_vhosts` - list of vhosts to create. Example on defining the
vhosts configuration:

```yaml
rabbitmq_vhosts:
  - name:     vhost1
    node:     node_name             # Optional, defaults to "rabbit"
    tracing:  yes                   # Optional, defaults to "no"
    state:    present               # Optional, defaults to "present"
```

### Policies

`rabbitmq_policies` - list of policies to be created (or removed if
`state: absent` is set). Example on defining the policies configuration:

```yaml
rabbitmq_policies:
  - name:     HA Policy
    vhost:    '/'                   # Optional, defaults to "/"
    pattern:  '.*'                  # Optional, defaults to ".*"
    tags:                           # Optional, defaults to "{}"
      ha-mode: all
      ha-sync-mode: automatic
    state:    present               # Optional, defaults to "present"
```

### Cluster setup

This role supports setting up a simple cluster by adding all the nodes in a
play group that uses the role. It adds the nodes to `cluster_nodes` section
in rabbitmq.conf file. All the nodes are `disc` nodes. The role also sets the
same "Erlang Cookie" to all the nodes belonging to a cluster. In this way
nodes join the cluster automatically during the bootstrap.

For the initial deployment, it is advised to serialize the node deployment in
a way that, at first, a single node is deployed, followed by all the other
nodes in the second run. This would result in a consistent cluster setup.
Playbook example:

```yaml
  - hosts: rabbitmq
    become: True
    serial:
      - 1
      - '100%'
    roles:
      - rabbitmq
```

### File descriptors

`rabbitmq_fd_limit` - set this to some numeric value to override 1024
default. Currently only supports systemd.

## Testing

There is some tests that try to provision a VM using Vagrant. Just launch them
with:

    $ cd tests
    $ vagrant up



## License

BSD
