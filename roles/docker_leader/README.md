Role Name
=========

Implements a docker role to be a docker swarm leader (the server which init the swarm). This role creates a fact on the server that stores the command line to add workers and managers to the swarm (with join tokens)

Requirements
------------

docker role executed

Role Variables
--------------

None

Dependencies
------------

docker role

Example Playbook
----------------

```yaml
- hosts: all
  tasks:
   - name: Install docker on all servers
     include_role: docker

- hosts: leader
  tasks:
   - name: Initialize swarm on leader
     include_role: docker_leader

- hosts: workers
  tasks:
   - name: Add workers to swarm cluster
     include_role: docker_worker

- hosts: managers
  tasks:
   - name: Add managers to swarm cluster
     include_role: docker_manager
```

License
-------

BSD

Author Information
------------------

Implemented by José Ramón Méndez Reboredo. Please note that this is part of the materials for the subject AAS on the MSC on Computer Science (University of Vigo)
