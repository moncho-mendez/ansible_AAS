Role mariadb
=========

This role installs mariadb-server-10.3 module and configures mysql. It creates a set of users and gives to them all privileges on the DB.
Developed and tested for Debian-10.

Requirements
------------

Collection community.mysql.mysql_user will be needed to create and configure the users of the DB.
This collection will be loaded using the requirements.yml file.

This role uses an encrypted file called vault.yml to load the password for the root mysql user.

Role Variables
--------------

The default variables needed are the mysql and mariadb configuration. The variables are the next:

mysql_root_user: "root" # The root user for MySQL
mysql_unix_socket: "/var/run/mysqld/mysqld.sock" # The host where users will be created
mariadb_conf_file:  /etc/mysql/mariadb.conf.d/50-server.cnf # The mariadb configuration
mariadb_address: <ip_address> # Ip address where mariadb will listen

Tags
------------

There are tags to select the set of tasks to execute.

tags:
  - mysql # Execute all tasks
  - mysql_packages # Install the required packages
  - mysql_config   # Configure  mariadb 
  - mysql_users    # Create and configure the users for de DB

Example:
  ansible-playbook --tags <tag_name> myplaybook.yml

Example Playbook
----------------

- hosts: ejemplo1
  tasks:
    - name: Load vault values
      include_vars: vault.yml
      tags:
        - mysql
        - mysql_users
        - mysql_packages
        - mysql_config
    - name: Test if passwords were succefully loaded
      debug:
        msg: "root passwd: {{ vault_mysql_passwd }}"
      tags: 
        - mysql
        - mysql_users
        - mysql_packages
        - mysql_config
    - name: Install mysql
      import_role: 
        name: mariadb
      vars: 
          mysql_root_user: "root" # The root user for MySQL
          mysql_root_password: "{{ vault_mysql_passwd }}" # The password for MySQL root
          mysql_unix_socket: "/var/run/mysqld/mysqld.sock" # The host where users will be created
          mariadb_conf_file:  /etc/mysql/mariadb.conf.d/50-server.cnf # The mariadb configuration
          mariadb_address: "127.0.0.1" # Address where mariadb will listen
          mysql_new_users: # The users to be created
            myuser: 
              password: "mypass"
              privs: "*.*:ALL "
              host: "%"


License
-------

BSD

Author Information
------------------

Name: Iván Martínez Estévez
email: ivan.martinez.estevez@uvigo.es
organization: Universidade de Vigo