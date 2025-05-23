# Provision, configure, and start a Liberty instance on CICS

This project provides a sample playbook and roles which can be used to provision, configure, and start a[WebSphere Liberty server](https://www.ibm.com/cloud/websphere-liberty) instance as a CICS resource.
/!\ at the moment it is deeply tied to the aap&zos techzone instance and thus really overfitted to the Wazi instance it contains.

It is a good practice to review the playbook sample contents before executing
them. It will help you understand the requirements in terms of space, location,
names, authority, and the artifacts that will be created and cleaned up.
Although samples are written to operate without the need for the user’s
configuration, flexibility is written into the samples because it is not easy
to determine if a sample has access to the host’s resources. Review the
playbook notes sections for additional details and configuration.

## Playbook Summary

- [**provision.yml**](provision.yml) - Handles the following tasks:
  
- [**deprovision.yml**](roles/deprov/README.md) - Handles the following tasks:


## Role Summary


## Permissions



## Getting Started

If you are unfamiliar with playbooks, you can review our
[detailed configuration guide](https://github.com/IBM/z_ansible_collections_samples/blob/master/docs/share/zos_core/configuration_guide.md) or
continue with getting started below.

Optionally, you can use the sample
[host_setup](https://github.com/IBM/z_ansible_collections_samples/blob/master/zos_administration/host_setup/README.md)
to discover and create your **inventory** and **host_vars** artifacts. Please
note that when you use the **host_setup**, it will generate a configuration
for the most common dependencies, some playbooks require more customized
configurations, in this case, you can review the sample documentation and
add the additional required variables.

1. Update [inventory.yml](inventory.yml) with the information about your systems

```yaml
zsystem:
  hosts:
    zos:
      ansible_host: zos_target_address
      ansible_user: zos_target_username
```

2. Update the environment variables for the z/OS&reg; system in [host_vars/zos_host.yml](host_vars/zos_host.yml)

> **_NOTE:_**  if `WLP_USER_DIR` is not specified, it will default to `${wlp.install.dir}/usr`.
 
```yaml
# the path to the root of IBM python installation
PYZ: "/usr/lpp/IBM/cyp/v3r8/pyz"

# the path to root of ZOAU installation
ZOAU: "/usr/lpp/IBM/zoautil"

# the path to the root of JAVA installation
JAVA: "/usr/lpp/java170/J7.0_64"

# the path to user directory that contains the server instance configuration
WLP_USER_DIR: '/u/oeusr01'

# the path to the root of Liberty installation
liberty_path: '/usr/lpp/zWAS/WAS900/Liberty/V19R00'
```

3. Add a name for the zFS dataset that will be mounted to the user directory in [host_vars/zos_host.yml](host_vars/zos_host.yml) 

```yaml
data_set_name: targetuser.liberty.zfs
```

4. Update user information for creating STARTED profiles in [host_vars/zos_host.yml](host_vars/zos_host.yml)
> **_NOTE:_**  Ensure you have the correct permissions to write to the `PROC_LIB` you specified.

```yaml
TARGET_USER: target_user
USER_GROUP: user_group

#Procedure library for JCL Procedure template
PROC_LIB: USER.PRIVATE.PROCLIB
```

5. Update custom server variables based on the desired behavior for the Liberty instance in [host_vars/zos_host.yml](host_vars/zos_host.yml)

```yaml
CUSTOMIZE: no
ANGEL: no
AUTHORIZED_SERVICES: no
```

6. Customize the availability of authorized services on the z/OS&reg; system for the server.
Within the `authorized_services` role, there are variables with boolean values in [authorized_services/defaults/main.yml](authorized_services/defaults/main.yml) to represent which z/OS&reg; authorized services to enable.
```yaml
SAFCRED: yes
ZOSWLM: yes
TXRSS: yes
ZOSDUMP: yes
LOCAL_ADAPTER: no
PRODMGR: no
ZOSAIO: no
```

7. Run playbook

> **_NOTE:_**  Running the commands with tags will have precedence over the conditionals within the playbook

To start the server and angel process (if enabled):
```bash
ansible-playbook -i inventory.yml provision.yml
```

To just start the server:
```bash
ansible-playbook -i inventory.yml provision.yml --skip-tags "start_angel"
```

To just stop the server and keep the angel process:
```bash
ansible-playbook -i inventory.yml deprovision.yml --skip-tags "stop_angel"
```

To just stop the server and angel process:
```bash
ansible-playbook -i inventory.yml deprovision.yml --tags "stop,stop_angel"
```

## Copyright
© Copyright IBM Corporation 2021

## License
Licensed under [Apache License, Version 2.0](https://opensource.org/licenses/Apache-2.0)

## Author Information
Alexis Pouillieute - @Alexis

## Support
Please refer to the [support section](https://github.com/IBM/z_ansible_collections_samples/blob/master/README.md#support) for more details.
x@x@