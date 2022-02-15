# This role is archived and obsolete - use https://github.com/UCLALibrary/uclalib_role_solr instead.

# UCLALib Ansible Role: Solr

Deploys Apache Solr on RHEL servers

## Dependencies

* uclalib_role_java
* uclalib_role_apache
* uclalib_role_tomcat
* uclalib_role_iptables

This role is capable of deploying Solr in the following configurations:

* Standalone: a single Solr instance
* Master: a Solr Master instance
* Slave: a Solr Slave instance sending replication requests to a Solr Master

## Variables

solr_version - defines the version of Solr to use (e.g. 4.2.0, etc.)

solr_url - defines the URL to download Solr

solr_base - defines the location of Solr Tomcat installation directory

solr_home - defines the location of Solr Index home directory

solr_cores - the dictionary list of all Solr cores to be used in this instance
  * ident - defines the names of the cores to configure within this Solr instance
  * type - defines the external application using the solr core (e.g. default, drupal, etc).
    * the `type` variable is used in conjunction with application specific solr configuration files and the solrconfig.xml file. You will need to manually put the appropriate files in the `files` and `templates` directory for this role.
      * the naming convention for solr configuration files is: `appname_solr-conf`
      * the naming convention for the solrconfig.xml file is: `solrconfig_appname.j2`

solr_instance_type - define the type of solr instane to deploy (0 = standalone ; 1 = master ; 2 = slave)

solr_master_host - defines the hostname of the Solr Master (only necessary for a master/slave config)

## Sample Variable Definition Formats

#### Standalone Solr Instance
```ansible
tomcat_applications:
  - app_name: solr
    shut_port: 8006
    conn_port: 8081
    rproxy_path: solr
solr_cores:
  - ident: core1
    type: default
  - ident: core2
    type: drupal
  - ident: core3
    type: default
```
#### Master Solr Instance
```ansible
tomcat_applications:
  - app_name: solr
    shut_port: 8006
    conn_port: 8081
    rproxy_path: solr
solr_cores:
  - ident: core1
    type: default
  - ident: core2
    type: drupal
  - ident: core3
    type: default
solr_instance_type: 1
```
#### Slave Solr Instance
```ansible
tomcat_applications:
  - app_name: solr
    shut_port: 8006
    conn_port: 8081
    rproxy_path: solr
solr_cores:
  - ident: core1
    type: default
  - ident: core2
    type: drupal
  - ident: core3
    type: default
solr_instance_type: 2
solr_master_host: solr-master.library.ucla.edu
```

The variable definitions should be placed in the playbook under the `vars` statement.
Example:
```ansible
---
- name: uclalib_solr.yml
  sudo: true
  hosts: test
  vars:
    iptables_allowed_input_rules:
      - src_ip: 164.67.0.0/16
        dest_port: 80
        protocol: tcp
    tomcat_applications:
      - app_name: solr
        shut_port: 8008
        conn_port: 8083
        rproxy_path: solr
    solr_cores:
      - ident: core1
        type: default
      - ident: core2
        type: drupal
      - ident: core3
        type: default
    solr_instance_type: 1

    roles:
      - { role: uclalib_role_java }
      - { role: uclalib_role_apache }
      - { role: uclalib_role_tomcat }
      - { role: uclalib_role_solr }
      - { role: uclalib_role_iptables }
```
