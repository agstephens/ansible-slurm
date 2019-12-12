SSH set-up:

 - Make sure ALL servers have:
  ~/.ssh/id_rsa # 0400 



My playbook:

 - copied roles from .roles to ./roles
 - removed "grycap." prefix from roles names

 - added setup-env.sh, to set ANSIBLE_CONFIG env var, points to
 - /root/ansible-slurm.cfg

 - created vagrant inventory: inventories/hosts-vagrant


 - destory and recreate: slurm-server VM
 - login to it and append these lines to: /etc/hosts
192.168.50.74 slurmserver slurmserver.localdomain
192.168.50.76 slurmworker1 slurmworker1.localdomain

 - source setup-env.sh
 - ansible-playbook -i inventories/hosts-vagrant server-playbook.yml

 - It worked!!!
 - then did:

```

```
mkdir server-cache
scp slurmserver:/etc/munge/munge.key server-cache/
ssh slurmworker1 mkdir /etc/munge
scp server-cache/munge.key slurmworker1:/etc/munge/
ssh slurmworker1 chown -Rf munge.munge /etc/munge !!! DIDN'T WORK - no user: munge
ssh slurmworker1 chmod 400 /etc/munge/munge.key

scp slurmserver:/etc/slurm/slurm.conf server-cache/slurm.conf
ssh slurmworker1 mkdir /etc/slurm
scp server-cache/slurm.conf slurmworker1:/etc/slurm/slurm.conf
ssh slurmworker1 chown -Rf slurm.slurm /etc/slurm !!! DIDN'T WORK - no user: slurm
```

 - ansible-playbook -i inventories/hosts-vagrant worker-playbook.yml

FAILED AT TASK: 
TASK [munge : Make sure the munge service is started] ************************************************************************************************************
fatal: [192.168.50.76]: FAILED! => {"changed": false, "msg": "Unable to start service munge: Job for munge.service failed because the control process exited with error code. See \"systemctl status munge.service\" and \"journalctl -xe\" for details.\n"}


 - I ran:
scp server-cache/munge.key slurmworker1:/etc/munge/
ssh slurmworker1 chown -Rf munge.munge /etc/munge
ssh slurmworker1 chmod 400 /etc/munge/munge.key

 - re-ran: ansible-playbook -i inventories/hosts-vagrant worker-playbook.yml

## Adding multiple nodes with different names
Need to put them in the slurm.conf file separately, for each line of:
NodeName ...
PartitionName ...
