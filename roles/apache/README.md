Role apache
=========

This role installs and configures the apache2 module and the php module for apache. 
It creates several new websites and configures them.
Developed and tested for Debian-10.

Requirements
------------

Collection community.general will be needed to enable the php module of apache2.
This collection will be loaded using the requirements.yml file.

Templates
------------

The use of templates is required. Folder apache/templates should contain the below files to refill:
 - index.php.j2   : Main page for the new websites
 - new_websites.j2: Server configuration

Role Variables
--------------

The default variables needed are the websites to create. The schedule for website variables are the next:

websites: 
  site1: # The key for each site
    directory: <path> # The location where the website will be created
    server_name: <nameserver>    # The full name of the server
    index_file: <path/file_name> # The full path and name of the index file
    template_index_file: <template_name> # Name of the template to refill the index_file
    admin: <email_address> # Email address of amdin of this website

Tags
------------

There are tags to select the set of tasks to execute.

tags:
  - apache # Execute all tasks
  - apache_packages # Execute the installation of the required packages
  - apache_modules  # Enable the php module of apache2
  - apache_websites # Install and configure the new websites

Example:
  ansible-playbook --tags <tag_name> myplaybook.yml


Example Playbook
----------------

- hosts: servers
 - name: Install apache
      import_role:
        name: apache
      vars: 
        websites:
          site1:
            directory: /var/www/site1
            server_name: www.site1.com
            index_file: /var/www/site1/index.php
            template_index_file: index.php.j2
            admin: user@domain.com


License
-------

BSD

Author Information
------------------

Name: Iván Martínez Estévez
email: ivan.martinez.estevez@uvigo.es
organization: Universidade de Vigo