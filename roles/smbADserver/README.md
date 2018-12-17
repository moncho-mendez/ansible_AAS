smbADserver
=========

This is a implementation of Samba 4 PDC (Primary Domain Controller) using internal LDAP server and DNS. The role has been inspired in the following document https://www.sysadminsdecuba.com/2018/02/pdc-sencillo-con-samba-4-en-debian-9/. It susbstitures a LDAP server (such as OpenLDAP).

Requirements
------------

None. 

Role Variables
--------------

```yaml
    ldap_server_domain: "ldap.esei.uvigo.es"
    ldap_organization_domain: "esei.uvigo.es"
    ldap_base_dn: "{{ ldap_organization_domain | replace('.', ',dc=') | regex_replace('^', 'dc=')  }}"
    ldap_organization_name: "ESEI"
    ldap_admin_pw: "test"
```

* ldap_server_domain is the domain name for the ldap server. Please note that this will be one of the client certificates
* ldap_organization_domain: is the domain of the organization where LDAP server will be deployed. This domain is used as common name (CN) for the certificate authority
* ldap_base_dn is used to populate the root dn of the LDAP server
* ldap_organization_name is the fully qualified name of the organization where the LDAP server will be deployed. It is used to create the certificate authority.
* ldap_admin_pw is the password that will be used for setup a LDAP server

Dependencies
------------

none

Example Playbook
----------------

```yaml
- hosts: smbADserver
  vars:
    ldap_server_domain: "ldap.esei.uvigo.es"
    ldap_organization_domain: "esei.uvigo.es"
    ldap_base_dn: "{{ ldap_organization_domain | replace('.', ',dc=') | regex_replace('^', 'dc=')  }}"
    ldap_organization_name: "ESEI"
    ldap_admin_pw: "test"
  tasks:
   - name: Install Samba working as an Active Directory Server and PDC
     import_role: 
        name: smbADserver
```

License
-------

BSD

Author Information
------------------

Implemented by José Ramón Méndez Reboredo. Please note that this is part of the materials for the subject AAS on the MSC on Computer Science (University of Vigo)
