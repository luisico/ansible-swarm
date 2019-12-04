Docker
======
Bootstrap Docker Swarm clusters.

This roles bootstraps a Docker Swarm cluster in two steps. First, a manager node is used to initialize the swarm, and then, all other nodes (managers and workers) join the swarm. The role supports bootstrapping multiple Swarm cluster in the same play. In this case each node defines a `swarm_name` variable that uniquely identifies the cluster (see example below).

Requirements
------------
See `meta/main.yml`.

Role Variables
--------------
See `defaults/main.yml`.

Dependencies
------------
Docker engine needs to installed, ie using role `luisico.docker`.

Example Playbook
----------------
The following example bootstrap a single swarm cluster:
```
- name: Init swarm clusters
  hosts: managers
  roles:
    - role: swarm
      swarm_node: init

- name: Join other swarm nodes
  hosts: all
  roles:
    - swarm
```

In the following example two swarm clusters are bootstrapped:

```
- name: Init swarm clusters
  hosts:
    - cluster1-managers[0]
    - cluster2-managers[0]
  roles:
    - role: swarm
      swarm_node: init

- name: Join other swarm nodes
  hosts: swarm-servers
  roles:
    - swarm
```

In the first play, the swarm is initialized with the first manager server of each cluster. The second play joins all other swarm servers to its respective cluster. The corresponding inventory can be seen below. Note the definition of `swarm_name` for each cluster group, and of `swarm_node` for managers and worker groups.

```
[cluster1:children]
cluster1-managers
cluster1-workers

[cluster1:vars]
swarm_name=cluster1

[cluster1-managers]
node1-1.dev
node1-2.dev

[cluster1-workers]
node1-3.dev

[cluster2:children]
cluster2-managers
cluster2-workers

[cluster2:vars]
swarm_name=cluster2

[cluster2-managers]
node2-1.dev
node2-2.dev

[cluster2-workers]
node2-3.dev

[swarm-servers:children]
swarm-managers
swarm-workers

[swarm-managers:children]
cluster1-managers
cluster2-managers

[swarm-managers:vars]
swarm_node=manager

[swarm-workers:children]
cluster1-workers
cluster2-workers

[swarm-workers:vars]
swarm_node=worker
```

TODO
----
- listen address is required

License
-------
Released under the [MIT license](https://opensource.org/licenses/MIT).

Author Information
------------------
Luis Gracia while at [The Rockefeller University](https://www.rockefeller.edu):
- lgracia [at] rockefeller.edu
- GitHub at [luisico](https://github.com/luisico)
- Galaxy at [luisico](https://galaxy.ansible.com/luisico)
