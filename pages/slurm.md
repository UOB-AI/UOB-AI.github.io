---
title: "Slurm"
keywords: Slurm
sidebar: main_sidebar
permalink: slurm.html
summary: Using the Job Scheduler Slurm
published: True
---

# Slurm Workload Manager Basics

The Benefit AI Lab Cluster uses slurm as a scheduler and workload manager. As a warning, note that on a cluster, you do not run the computations on the login node. Computations belong on
the compute nodes, when, and where they will be run is decided by the scheduler (like slurm). In the Benefit AI Lab cluster, this is the master node: `hayrat`.

After logging in to hayrat you can submit a job using slurm, and it will run it on the compute or GPU nodes that you specify in the submission script.

The workload manager tries to distribute the resources based on the cluster rules. Resources available for slurm include:
- CPU cores
- RAM
- GPUs

You can request these resources nicely through slurm using the shell script and slurm `sbatch` or `srun` commands. But the ultimate decision is taken by the workload manager.

The system administrator also divides the cluster into partitions, and each user group will have some of these partitions available to them based on their privileges. A partition is a set of compute nodes (computers dedicated to... computing) grouped logically based on either physical properties of the hardware or job scheduling policies. Once the submitted job is executed, the output of the jobs is then written into disk (or storage).

# Gathering Cluster Information

Slurm offers the `sinfo` command to get an overview of the resources offered by the cluster. By default, `sinfo` lists the partitions that are available on the cluster.

```bash
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
standard*    up   12:00:00      4   idle cn[01-04]
compute      up 1-00:00:00      8   idle cn[01-08]
gpu          up 3-00:00:00      2  alloc gpu[01-02]
```

As you can see from the result of the basic `sinfo` command you can see that there are three partitions in this cluster: standard with 4 compute nodes `cn01` to `cn04` (which is the default), then compute with eight nodes, and finally `gpu` with the two GPU nodes.

You can output node information using `sinfo –Nl`. With the `-l` argument, more information about the nodes is provided, among which the number of “CPUs” (CPUS), which is the number of processing units that the jobs can use. It should generally correspond to the number of sockets (S) times number of cores per socket (C) times number of hardware threads per core (T in the S:C:T column) but can be lower in the case some CPUs are reserved for system use.

```bash
NODELIST   NODES PARTITION       STATE CPUS    S:C:T MEMORY TMP_DISK WEIGHT AVAIL_FE REASON              
cn01           1 standard*        idle 24     2:12:1 385443        0      1   (null) none                
cn01           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn02           1 standard*        idle 24     2:12:1 385443        0      1   (null) none                
cn02           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn03           1 standard*        idle 24     2:12:1 385443        0      1   (null) none                
cn03           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn04           1 standard*        idle 24     2:12:1 385443        0      1   (null) none                
cn04           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn05           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn06           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn07           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
cn08           1   compute        idle 24     2:12:1 385443        0      1   (null) none                
gpu01          1       gpu   allocated 192    2:48:2 103162        0      1 gpu,cent none                
gpu02          1       gpu   allocated 192    2:48:2 103162        0      1 gpu,cent none
```

The other columns report the volatile working memory (RAM – MEMORY), the size of the local temporary disk (also called local scratch space – TMP_DISK), and the node “weight” (an internal parameter specifying preferences in nodes for allocations when there are multiple possibilities).

# Running Jobs on Slurm

Jobs are made of one or multiple sequential steps, each consisting in one or multiple parallel tasks that will be dispatched to possibly distinct nodes. Each task will be allocated CPUs, memory, and possible other generic resources in an exclusive manner by slurm.

Two jobs cannot share the same resource unless explicitly forced by the admins, but that is generally not the case. Therefore, jobs can only start when all needed resources are free and not needed by another higher priority job. Jobs are indeed assigned a priority when they are submitted, which can depend upon multiple factors.

For the scheduling process to work properly, you will need to describe your job before you submit
it:

- What are the steps (i.e. which program must be run and how) ;
- How many tasks there will be ;
- What resource each task needs (CPU, memory, etc.), and
- For how long the job is supposed to run.

All of these, along with potentially additional job parameters submission options, can be described in a submission script. The expression #SBATCH is used to specify these parameters in the script.

{% include warning.html content="The #SBATCH directives must appear at the top of the submission file, before any other line except for the very first line which should be the shebang (e.g. #!/bin/bash)." %}


As an example, the following should be entered in a file named `submit.sh`:

```bash
#~/bin/bash
#
#SBATCH --job-name=test
#SBATCH --output=res.txt
#SBATCH --partition=standard
#
#SBATCH --time=10:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem-per-cpu=100

srun hostname
srun sleep 60
```

This job does not do a lot. It will only display the `hostname` on the compute node it is running on and then sleep for 60 minutes. Note that these programs are run using the Slurm command `srun`.
You should use an editor like nano to write this job in the file (here called submit.sh) and save.
Then, to run this job, you can use the `sbatch` command as follows:

```bash
$ sbatch submit.sh 
Submitted batch job 4306
$ squeue --me
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              4306   compute     test  username  R       0:02      1 cn01
```

Note that the `squeue` command can show you the job queue and its current compute node and status

As we specified in the job script that we want our output to be stored in a file called `res.txt`, after the job is finished, you can view the output as follows:

```bash
==========================================
SLURM_CLUSTER_NAME = linux
SLURM_ARRAY_JOB_ID = 
SLURM_ARRAY_TASK_ID = 
SLURM_ARRAY_TASK_COUNT = 
SLURM_ARRAY_TASK_MAX = 
SLURM_ARRAY_TASK_MIN = 
SLURM_JOB_ACCOUNT = faculty
SLURM_JOB_ID = 4306
SLURM_JOB_NAME = test
SLURM_JOB_NODELIST = cn01
SLURM_JOB_USER = test
SLURM_JOB_UID = 1132
SLURM_JOB_PARTITION = compute
SLURM_TASK_PID = 2206380
SLURM_SUBMIT_DIR = /home/nfs/test
SLURM_CPUS_ON_NODE = 24
SLURM_NTASKS = 1
SLURM_TASK_PID = 2206380
==========================================
cn01
```

The `res.txt` file contains a brief report on the job, plus the output which is in this case just the name of the compute node (result from running the `hostname` command).

This was just a basic example, and there are many other commands that you can use to control your submitted jobs such as: `srun`, `scancel`, and `sview`. For more information please refer to the slurm user documentation https://slurm.schedmd.com/documentation.html.
