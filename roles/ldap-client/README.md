ldap_client
=========

This role implements an ldap client that can be access to a ldapserver populated with role openldap by using jexplorer and standard ldap tools (ldapsearch, ldapmodify...)

Requirements
------------

A ldap server shoudl be populated by using openldap role. Aditionally, the certificates should be correctly populated with CA role. 

Role Variables
--------------

This role shares the variables used by other roles such as openldap and CA. The role works with a stucture containing the following variables

```yaml
ldap_server_ip: "{{ hostvars['ldap_server'].ansible_ssh_host }}"
ldap_server_domain: "ldap.esei.uvigo.es"
ldap_organization_domain: "esei.uvigo.es"
ldap_base_dn: "{{ ldap_organization_domain | replace('.', ',dc=') | regex_replace('^', 'dc=')  }}"
ldap_organization_name: "ESEI"
ldap_admin_pw: "test"
files_dir: "/opt/ssl"
CA: 
  ca_cn: "{{ ldap_organization_domain }}"
  ca_cert_file: "{{ files_dir }}/ca/ca.pem"
  ca_csr_file: "{{ files_dir }}/ca/ca.csr"
  ca_priv_key: "{{ files_dir }}/ca/ca.key"
  clients:
    - client_cn: "{{ ldap_server_domain }}"
      client_cert_file: "{{ files_dir }}/ldap/ldap.pem"
      client_csr_file: "{{ files_dir }}/ldap/ldap.csr"
      client_priv_key: "{{ files_dir }}/ldap/ldap.key"
```
* ldap_server_ip is the IP (IPv4) of the ldap server  
* ldap_server_domain is the domain name for the ldap server. Please note that this will be one of the client certificates
* ldap_organization_domain: is the domain of the organization where LDAP server will be deployed. This domain is used as common name (CN) for the certificate authority
* ldap_base_dn is used to populate the root dn of the LDAP server
* ldap_organization_name is the fully qualified name of the organization where the LDAP server will be deployed. It is used to create the certificate authority.
* ldap_admin_pw is the password that will be used for setup a LDAP server
* CA: This contains the information about the CA that will be populated and their clients.
* CA.cn is the common name of the CA
* ca.ca_cert_file is the path where the certificate for CA will be stored
* ca.client_csr_file is the path for the intermediate CSR file generated during the signing process.
* ca.ca_priv_key is the private key for the CA
* ca.clients is an array describing the properties of al clients of the CA. In general we only populated one client but more clients can be easilly populated by adding elements to this array.
* ca.clients[i].client_cn, is the common name for the client
* ca.clients[i].client_cert_file is the path for the file where the client certificate will be stored.
* ca.clients[i].client_csr_file is the path for the intermediate file storing the CSR (certificate singning request)
* ca.clients[i].client_priv_key is the file where the private key (no passwd) for the client will be stored. 

Dependencies
------------

No dependencies

Example Playbook
----------------

```yaml
- hosts: ldap_client
  gather_facts: yes
  vars:
    ldap_server_ip: "{{ hostvars['ldap_server'].ansible_ssh_host }}"
    ldap_server_domain: "ldap.esei.uvigo.es"
    ldap_organization_domain: "esei.uvigo.es"
    ldap_base_dn: "{{ ldap_organization_domain | replace('.', ',dc=') | regex_replace('^', 'dc=')  }}"
    ldap_organization_name: "ESEI"
    ldap_admin_pw: "test"
    files_dir: "/opt/ssl"
    CA: 
      ca_cn: "{{ ldap_organization_domain }}"
      ca_cert_file: "{{ files_dir }}/ca/ca.pem"
      ca_csr_file: "{{ files_dir }}/ca/ca.csr"
      ca_priv_key: "{{ files_dir }}/ca/ca.key"
      clients:
        - client_cn: "{{ ldap_server_domain }}"
          client_cert_file: "{{ files_dir }}/ldap/ldap.pem"
          client_csr_file: "{{ files_dir }}/ldap/ldap.csr"
          client_priv_key: "{{ files_dir }}/ldap/ldap.key"
  tasks:
   - name: Install Open LDAP
     include_role: 
        name: ldap-client
```

License
-------

BSD

Author Information
------------------

Implemented by José Ramón Méndez Reboredo. Please note that this is part of the materials for the subject AAS on the MSC on Computer Science (University of Vigo)
