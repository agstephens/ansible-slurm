# Ansible SLURM playbook

## Modes

This playbook can install a SLURM cluster:
 - one server node
 - separate worker node(s)

Or you can install on one server:
 - single-server mode

## Credit

The original playbook has been adapted from:

https://galaxy.ansible.com/grycap/slurm

Which was initially installed with:

```
ansible-galaxy install grycap.slurm
```

## Running the playbooks

Make sure Ansible is installed first.

## 1. Single-server deployment

Edit the file: 

`inventories/hosts-vagrant-single-server`

Make sure the entries match your IP and hostname.

Run this setup playbook once:

```
ansible-playbook -i inventories/hosts-vagrant-single-server vagrant-single-server-setup-playbook.yml
```

Now install SLURM:

```
ansible-playbook -i inventories/hosts-vagrant-single-server single-server-playbook.yml
```

See below for testing and troubleshooting.

## 2. Cluster deployment



### Server playbook

 ansible-playbook -i inventory server-playbook.yml


### Worker playbook 

 ansible-playbook -i inventory wns-playbook.yml


## Testing the installation

The installation sets up a basic queue and adds a script that you can test with:

```
[root@localhost playbook]# ssh oneoff
Last login: Tue Jan  7 12:36:42 2020 from 192.168.50.70
$ sbatch ~/hostname.sh  # Submits basic script
Submitted batch job 2

$squeue                 # shows job in queue (R=RUNNING)
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 2     debug hostname     root  R       0:02      1 oneoff

$ sleep 10 && squeue    # Wait a while and check queue status
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)

$ cat host.txt          # Show output of script
oneoff
```

## Troubleshooting

### Log files

SLURM log files/dirs are set in:

```
[root@oneoff slurm]# grep Log slurm.conf
SlurmctldLogFile=/tmp/slurmctld.log
SlurmdLogFile=/tmp/slurmd.log
#SlurmSchedLogFile=
#SlurmSchedLogLevel=

[root@oneoff slurm]# ls /tmp/slurm*
/tmp/slurmctld.log  /tmp/slurmd.log

/tmp/slurmd:
cred_state
```
