# Galaxy Patch (OpenStack)

Ansible playbook and roles to patch an OpenStack Galaxy deployment

This code (two Ansible roles and a `site.yaml`) are used to *increase* the size
of an existing Galaxy/Condor cluster. It simply creates `exec` instances
and then *patches* them by: -

1.  Templating `auto.data`
1.  Templating `condor_config.local`
1.  Templating `data.autofs`
1.  Restarting the `nfs`, `autofs` and `condor` services

## Usage

1.  Clone onto a suitable *bastion* in the cluster
1.  Inspect the variables (in `group_vars/all/main.yaml`) and adjust accordingly
1.  Set your keystone environment variables
1.  Run the playbook with `ansible-playbook site.yaml`

## Key variables

-   `exec_type`
-   `exec_count` - the number of new exec instances to create
-   `exec_offset` - the numerical offset of the first exec instance.
    If you're adding to a cluster and you have instances `0` to `10`
    this would normally be `11`
-   `create_exec_instances` - if `no` new instances are not created
    but the *patching* is still applied to all exec instances in the cluster

---
