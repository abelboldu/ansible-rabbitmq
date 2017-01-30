Rabbitmq 
========

[![Build Status](https://travis-ci.org/abelboldu/ansible-rabbitmq.svg?branch=master)](https://travis-ci.org/abelboldu/ansible-rabbitmq) on master branch

Playbook to install and configure RabbitMQ message broker.

Standalone install, based on Mayeu.RabbitMQ role, without SSL or federation
support.

## Installation

Use Ansible galaxy to install this playbook:

    $ ansible-galaxy install abelboldu.rabbitmq

## Supported system

Ubuntu 14.04 (Trusty), Ubuntu 16.04 (Xenial) and CentOS 7

## Role Variables

### Environment

Use `rabbitmq_conf_env` so set Environment variables such as NODENAME,
NODE_PORT, NODE_IP_ADDRESS, etc.

Example:

```yaml
rabbitmq_conf_env:
  NODENAME: rabbit1

```

### Configuration file

`rabbitmq_tcp_address` Listening address for the tcp interface

`rabbitmq_tcp_port` Listening port for the tcp interface

`rabbitmq_cluster` A boolean variable, when set to true, the role adds all nodes in a play group to a cluster setup in a configuration file. It depends on a `ansible_play_hosts` magic variable, found in ansible 2.2 or later

`rabbitmq_erlang_cookie` Only used when `rabbitmq_cluster` is used, to identify members of a cluster

### Plugins

`rabbitmq_plugins` List of plugins to activate.

### Vhosts and users

`rabbitmq_vhost_definitions` List of vhosts to create (see below).
`rabbitmq_users_definitions` List of users, and associated vhost and password (see below).

Defining the vhosts configuration

```yaml
rabbitmq_vhost_definitions:
  - name:    vhost1
    node:    node_name #Optional, defaults to "rabbit"
    tracing: yes       #Optional, defaults to "no"
```

Defining the users configuration:

```yaml
rabbitmq_users_definitions:
  - vhost:    vhost1
    user:     user1
    password: password1
    node:     node_name  # Optional, defaults to "rabbit"
    configure_priv: "^resource.*" # Optional, defaults to ".*"
    read_priv: "^$" # Disallow reading.
    write_priv: "^$" # Disallow writing.
  - vhost:    vhost1
    user:     user2
    password: password2
    force:    no
    tags:                # Optional, user tags
    - administrator
```

## File descriptors

`rabbitmq_fd_limit` Set it to a some value to override 1024 default (systemd supported)

## Testing

There is some tests that try to provision a VM using Vagrant. Just launch them
with:

    $ cd tests
    $ vagrant up



## License

BSD
