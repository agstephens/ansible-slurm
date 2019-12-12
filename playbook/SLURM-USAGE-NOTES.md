# SLURM usage

## Set-up

ssh slurmworker1

mkdir usage

cd usage

cat hostname.sh

```
#!/bin/bash
hostname > ~/host.txt
```

## Run job

sbatch ~/usage/hostname.sh

## Check queue


 for i in `seq 1 10`; do sbatch ~/usage/hostname.sh ; done

 squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 5     debug hostname     root PD       0:00      1 (None)
                 6     debug hostname     root PD       0:00      1 (Priority)
                 7     debug hostname     root PD       0:00      1 (Priority)
                 8     debug hostname     root PD       0:00      1 (Priority)
                 9     debug hostname     root PD       0:00      1 (Priority)
                10     debug hostname     root PD       0:00      1 (Priority)

## Cancel job

scancel 8
