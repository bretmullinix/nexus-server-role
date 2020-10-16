Ansible Role: Sonatype Nexus Server (Disconnected)
=========

The ansible role is used to install the Sonatype Nexus server on a Centos 8 server.  If you have another OS, you will need
to adapt the role for your OS.

Requirements
------------

1.  Centos 8 Operating System
1.  Python Virtual Environment with the following installed:

    1. **ansible==2.9**
 
1. If you intend to use the **molecule** tests, do the following:

    1. Install the following in your Python virtual environment:
    
        1. **molecule==3.0.4**
        1. **molecule[docker]**
        1. **boto**
        1. **boto3**
    
    1. You will need an Amazon AWS account and permission to create a new EC2 instance to run the
       molecule test.
       
    1. If you plan on running the **molecule** scenario, you must perform the following in the 
       **roles/nexus-server-role** directory:
    
        1. cd **files**
        1. Make the directory **private\_keys**
        1. cd **private\_keys**
        1. Create a file with the same name as your AWS private key name.  The file must have no extension.
        1. Add the AWS private key to the file created in the previous step. 

Role Variables
--------------

1. **defaults/main.yml** variables:

    1. **yum\_installs** --> The list of software packages to install.
     
    1. **yum\_backend** --> The backend to use for **yum**.  In the case of Centos 8, the backend is **dnf**.  
       The backends are listed in the [Ansible yum module documentation](
       https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html). 
    
    1. **domain\_name** --> The domain name of the target Nexus server.
    
    1. **fqdn** --> The fully qualified domain name of the target Nexus server.
    
    1. **use\_ssl** --> Defaults to true.  If set to true, the Nexus server will be configured
       with a self signed certificate and operate over https and port 8443.  Otherwise, if the
       value is set to false, the Nexus server will be configured to operate over http.
       
    1. **nexus\_jetty\_keystore\_password** --> The SSL Java keystore password.  The Java keystore
       is used by the Nexus Jetty server to run Nexus using SSL.  The password is encrypted with
       ansible\_vault.
        
1. **vars/main.yml** variables:

    1. **open\_nexus\_ports** --> The list of ports to open for clients to use the Nexus Server.

    1. **nexus\_software\_sha1\_checksum** --> The original checksum of the Nexus Server software included with this
       role.  If you would like to replace the software with another version, please follow the instructions
       [here](https://github.com/bretmullinix/openshift-idm-cluster-on-aws/blob/master/part6-install-nexus/readme.md#change-nexus-software).

    1. **nexus\_file\_name** --> The downloaded Sonatype Nexus software file name.

    1. **nexus\_file** --> The full file name of the downloaded Sonatype Nexus software including the extension.

    1. **nexus\_ssl\_dir** --> The directory on the target Nexus server where the SSL certificates will be
       placed.  Note:  The location of this directory might change if you change the nexus software included 
       in this ansible role.

    1. **nexus\_properties\_dir** --> The directory on the target Nexus server where the nexus properties exist.  
       Note:  The location of this directory might change if you change the nexus software included 
       in this ansible role.

    1. **nexus\_jetty\_config\_dir** --> The directory on the target Nexus server where the Jetty config exist.  
       Note:  The location of this directory might change if you change the nexus software included 
       in this ansible role.
    
Dependencies
------------

None.

Example Playbook
----------------

    ---
    - name: Create Sonatype Nexus Server
      hosts: nexus_server
      become: true
      vars:
        yum_installs:
          - name: "java-1.8.0-openjdk-devel"
            install_name: "java-1.8.0-openjdk"
          - name: "tar"
            install_name: "tar"
        yum_backend: dnf        
        domain_name: example.com
        fqdn: nexus-server.{{ domain_name }}
        use_ssl: true
        nexus_jetty_keystore_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          33616131656163343934323331393566323832663732623366393435653136636637613262373839
          3535613034346235376637383235313663393164643735650a653964653664396134653337383935
          64376439383833666337376262356261383939643361393432656634356435376365656536663930
          3366666434623463370a663132656530386163376362636566366435343330653064306635373763
          3438
      roles:
        - nexus-server-role


The playbook runs with a custom **ansible.cfg** with the following values set:

```text
inventory = inventory
remote_user = centos
private_key_file = ./my_keypair # Replace this private key file with yours.
```

The playbook **inventory** file contains the following:

```text
[all]
nexus_server ansible_host=100.25.40.101 # Replace this IP address with your target server.
```
You will need to change the ip address to your target server ip address.

The ansible playbook command was:  `ansible-playbook  create_nexus_server.yml --ask-vault-pass`

The **create\_nexus\_server.yml** contains the playbook example code above.

License
-------

BSD

Author Information
------------------

This role was created in 2020 by Bret Mullinix.