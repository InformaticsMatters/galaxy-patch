# Galaxy Patch (OpenStack)

Ansible playbook and roles to patch an OpenStack Galaxy deployment

This code (two Ansible roles and a `site.yaml`) are used to *increase* the size
of an existing Galaxy/Condor cluster. It simply creates `exec` instances
and then *patches* them by: -

1.  Templating `auto.data`. **Importantly** The NFS exports must already be
    available - this play does not setup the NFS server's devices, mounts or
    exports
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

Normally run from the DLS bastion as `mauro`: -

    $ sudo -iu mauro
    $ cd galaxy-patch
    $ git pull

Set your keystone environment variables (here we're using a script that 
prompts you for your username and password), check the parameters,
and run the playbook with: -
 
    $ source ~/keystone_v3_for_anyone.sh
    [...]
    [check parameters.yaml] 
    $ ansible-playbook site.yaml -e @parameters.yaml

>   You may need to run the playbook a number of times - especially if your
    instances have different base names. At the time of writing we have
    used `pulsar-exec-node`, `pulsar-exec-node-b`, `pulsar-exec-node-gpu`,
    `pulsar-exec-node-gpu-b` and `pulsar-submit-machine` as a base-names
    for our instances.

>   The machines that match your chosen `exec_base_name`
    are expected to be named `<exec_base_name>-[0-9]*`. If the machine names
    do not match, no machines will be placed in the inventory and nothing
    will be run.

For all our current machines, using the command-line for non-GPU nodes: -

    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-a -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-b -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-c -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-d -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-e -e exec_is_gpu=no
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-submit-machine -e exec_is_gpu=no

and for GPU nodes: -

    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-cuda -e exec_is_gpu=yes
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-cuda-a -e exec_is_gpu=yes
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-cuda-b -e exec_is_gpu=yes
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-cuda-x4 -e exec_is_gpu=yes
    $ ansible-playbook site.yaml -e exec_base_name=pulsar-exec-node-cuda-x2 -e exec_is_gpu=yes

---
