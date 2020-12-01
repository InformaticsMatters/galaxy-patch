# Galaxy Patch (OpenStack)

Ansible playbook and roles to patch an OpenStack Galaxy deployment

This code (two Ansible roles and a `site.yaml`) are used to *increase* the size
of an existing Galaxy/Condor cluster. It simply creates `exec` instances
and then *patches* them by: -

1.  Templating `auto.data`
2.  Templating `condor_config.local`
3.  Templating `data.autofs`
4.  Restarting the `nfs`, `autofs` and `condor` services

### Significant variables

-   `exec_base_name`
-   `exec_type`
-   `exec_count` - the number of new exec instances to create
-   `exec_offset` - the numerical offset of the first exec instance.
    If you're adding to a cluster and you have instances `0` to `10`
    this would normally be `11`
-   `create_exec_instances` - if `no` new instances are not created
    but the *patching* is still applied to all exec instances in the cluster

## Usage

1.  Clone onto a suitable *bastion* in the cluster
2.  Inspect the variables (in `group_vars/all/main.yaml`) and create
    a local `parameters.yaml` file for any values you need to adjust
3.  Inspect the templates to ensure they reflect your needs
    (specifically the mounts in `auto.data.j2`)

Set your keystone environment variables (here we're using a script that 
prompts you for your username and password) and run the playbook with: -
 
    $ source ~/keystone_v3_for_anyone.sh
    [...]
    $ ansible-playbook site.yaml -e @parameters.yaml

>   You may need to run the playbook a number of times - especially if your
    instances have different base names.

---
