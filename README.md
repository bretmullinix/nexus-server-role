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
       
    1. If you plan on running the **molecule** scenario, you must perform the following in the **roles** directory:
    
        1. Create the **files** directory
        1. cd **files**
        1. Create a file with the same name as your AWS private key name.  The file must have no extension.
        1. Add the AWS private key to the file created in the previous step. 

Role Variables
--------------

1. **defaults/main.yml** variables:

    1. **yum\_installs** --> The list of software packages to install.
     
    1. **yum\_backend** --> The backend to use for **yum**.  In the case of Centos 8, the backend is **dnf**.  
       The backends are listed in the [Ansible yum module documentation](
       https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html). 
        
1. **vars/main.yml** variables:

    1. **open\_nexus\_ports** --> The list of ports to open for clients to use the Nexus Server.
    1. **nexus\_software\_sha1\_checksum** --> The original checksum of the Nexus Server software included with this
       role.  If you would like to replace the software with another version, please follow the instructions
       [here](https://github.com/bretmullinix/openshift-idm-cluster-on-aws/tree/master/part6-install-nexus/readme#change-nexus-software).
    1. **nexus\_file\_name** --> The downloaded Sonatype Nexus software file name.
    1. **nexus\_file** --> The full file name of the downloaded Sonatype Nexus software including the extension.
    
Dependencies
------------

None.

Example Playbook
----------------

    ---
    - name: Create Sonatype Nexus Server
      hosts: nexus_server
      become: true
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

The ansible playbook command was:  `ansible-playbook  create_nexus_server.yml`

The **create\_nexus\_server.yml** contains the playbook example code above.

License
-------

BSD

Author Information
------------------

This role was created in 2020 by Bret Mullinix.