Role Name
=========

Adds a node to a docker swarm cluster with the role of worker.

Requirements
------------

docker shoudl be installed (docker role). A leader server should be defined (through using docker_leader)d.

Role Variables
--------------

None. The values are taken from a docker leader host trough facts. The name of the docker leader should be leader 

Dependencies
------------

docker should be installed (docker role)

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
