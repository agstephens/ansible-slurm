FOR TOMORROW:

 - separate worker node
 - separate server node (wiped)

Add hosts on ANSIBLE (source) server:

 echo "192.168.50.74 slurmserver slurmserver.localdomain" >> /etc/hosts
 echo "192.168.50.76 slurmworker1 slurmworker1.localdomain" >> /etc/hosts

NOTE: Do the same thing on both servers - so the /etc/hosts is updated!!!


Working with: https://galaxy.ansible.com/grycap/slurm


ansible-galaxy install grycap.slurm


I had a problem with these lines in `/root/.ansible/roles/grycap.ssh/tasks/front.yaml`:


```
#- name: Copy generated public key
#  local_action: command cp /home/{{ssh_user}}/.ssh/id_rsa.pub /tmp/{{ssh_user}}_id_rsa.pub creates=/tmp/{{ssh_user}}_id_rsa.pub
```

I logged remotely and found that the first file existed so I copied it to the second location manually.
I did it all on the target server, not locally.
And I copied the key locally to here as well: /tmp/{{ssh_user}}_id_rsa.pub (/tmp/slurm_id_rsa.pub)


I copied his to here:

```
cp /root/.ansible/roles/grycap.slurm/defaults/main.yml group_vars/all
```

And then overwrote some content.

## Running the playbooks


### Server playbook

 ansible-playbook -i inventory server-playbook.yml

!!!Worked!!!


### Worker playbook 

 ansible-playbook -i inventory wns-playbook.yml

!!!Failed on running munge!!!

I had to do this:

```
mkdir server-cache
scp slurmserver:/etc/munge/munge.key server-cache/
ssh slurmworker1 mkdir /etc/munge
scp server-cache/munge.key slurmworker1:/etc/munge/
ssh slurmworker1 chown -Rf munge.munge /etc/munge
ssh slurmworker1 chmod 400 /etc/munge/munge.key

scp slurmserver:/etc/slurm/slurm.conf server-cache/slurm.conf
ssh slurmworker1 mkdir /etc/slurm
scp server-cache/slurm.conf slurmworker1:/etc/slurm/slurm.conf
ssh slurmworker1 chown -Rf slurm.slurm /etc/slurm
```


In:  /root/.ansible/roles/grycap.slurm/tasks/wn.yaml

commented out:

```
#  - name: copy slurm.conf file from the frontend
#    copy: src=/etc/slurm/slurm.conf dest=/etc/slurm/slurm.conf
#    when: ansible_os_family == "RedHat"
```

re-ran: ansible-playbook -i inventory wns-playbook.yml

!!!It works!

## How to test it???
