# ansible_AAS
Ansible playbooks and roles for AAS and SASO subjects (Advanced System Administration/Security in Operating System Administration)

These ansible roles are part of the materials for the AAS (Advanced System Administration) and SASO (Security in Operating System Administration) subjects on the MSC on Computer Science Engineering (Máster en Ingeniería Informática) from Escuela Superior de Ingeniería Informática (University of Vigo).

All of these ansible files works in Debian 10 (buster, stable). Therefore, you can run the Ansible playbooks using WSL on Windows 10 or Debian 10. All roles have been developed in ansible 2.8. In order to install the latest ansible 2.8 version you should run as root the following commands:

```bash
apt-get install python-pip3
pip3 install ansible
```
Please note that configurations of target computers are configured to have test user with sudo capabilities with no password. So before you can use these scripts you should install a minimal debian 9 system (perhaps using debian stable netinstall image) and execute the following commands:

```bash
apt-get install ssh sudo
export EDITOR=vi && visudo
```
And add the following line: 

```
test	ALL=(ALL:ALL) NOPASSWD:ALL
```

Then from the ansible host you can install your ssh key using 
```
ssh-copy-id test@<destination>
```

This work includes roles to work with:
* Docker swarm
* LDAP

Note: In a real environment, passwords should be stored in Ansible vault files. However, this content has been designed exclusivelly for training purposes.

## DOCKER SWARM
There are three roles that are combined together to setup a docker swarm cluster. The playbook install_docker_swarm.yml automates the configuration of a leader, several additional managers and workers. To this end, 'leader' should be the name of a host in the inventory. Moreover, the managers group design to all nodes that will act as managers. Finally, a group of workers should be added to add standar worker nodes.

## LDAP
To install an ldap server working over SSL a certificate is required. You can use a certificate authority but in order to teach about certificates and certificate authorities these playbooks create a certificate authority using OpenSSL through Ansible cripto modules. All generated files for the certificate authority (private keys and certificates for the CA and all clients) are stored locally (storing the private CA key in a server makes no sense) and required files are copied to destination server.

These playbooks also creates an entry in /etc/hosts to match the common name (CN) of the certificate created for the slapd server. 

There are lots of preconfigured packages through debconf. In order to create these configurations, the packages can be downloaded using apt and decompresed using ar and tar to get the templates file. See an example below:

```bash
 cd /tmp && apt-get download libpam-ldap libnss-ldap
 ar x libnss-ldap_265-5_amd64.deb 
 tar xvf control.tar.gz 
 grep -e "^Template:" -e "^Type: " -e "^Default: " -e "^Description: " templates  | \
     sed -e "s/Template/  - question/" -e "s/^Type/    type/" -e "s/^Default/   \
      value/" -e "s/^Description: \(.*$\)/    escription: \"\1\"/" 
  
```

The output should be similar to the following one. Then you should change the values of fill them as required.

```yaml
  - question: libnss-ldap/confperm 
    type: boolean 
    value: false 
    description: "Make the configuration file readable/writeable by its owner only?" 
  - question: libnss-ldap/nsswitch 
    type: note 
    description: "nsswitch.conf not managed automatically" 
  - question: shared/ldapns/base-dn 
    type: string 
    value: dc=example,dc=net 
    description: "Distinguished name of the search base:" 
  - question: libnss-ldap/dblogin 
    type: boolean 
    value: false 
    description: "Does the LDAP database require login?" 
  - question: libnss-ldap/override 
    type: boolean 
    value: true 
    description: "Automatically update libnss-ldap's configuration file?" 
  - question: libnss-ldap/binddn 
    type: string 
   value: cn=proxyuser,dc=example,dc=net 
    description: "Unprivileged database user:" 
  - question: libnss-ldap/bindpw 
    type: password 
    description: "Password for database login account:" 
  - question: shared/ldapns/ldap_version 
    type: select 
    value: 3 
    description: "LDAP version to use:" 
  - question: shared/ldapns/ldap-server 
    type: string 
    value: ldap://127.0.0.1/ 
    description: "LDAP server Uniform Resource Identifier:" 
  - question: libnss-ldap/dbrootlogin 
    type: boolean 
    value: true 
    description: "Special LDAP privileges for root?" 
  - question: libnss-ldap/rootbinddn 
    type: string 
    value: cn=manager,dc=example,dc=net 
    description: "LDAP account for root:" 
  - question: libnss-ldap/rootbindpw 
    type: password 
    description: "LDAP root account password:"
```

The role openldap creates an ldap server and can be invoked using install_ldap_server.yml playbook. The role ldap-client that is invoked using install_ldap_client.yml playbook installs and configures jxplorer (graphical ldap client) and standard ldap tools (ldapsearch, ldapmodify, ldapadd). Finally, the role pam-ldap-client installs pam modules for authentication through openldap with SSL support.

Please note that all of these roles use variables from ldap_config_vars.yml files as general configuration. So changes should be naturally done in this file.
