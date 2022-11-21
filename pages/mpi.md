---
title: "Basic MPI Example"
keywords: mpi
sidebar: main_sidebar
permalink: mpi.html
summary: This is a very basic example on using MPI
---

To use MPI you have to use two main things, `module load` to load the MPI packages in the environment, and Slurm `sbatch` to submit the MPI job to the cluster.

First, on the master node, load MPI compiler and compile your application. There is an example for you to use as a guide as you see in the next code snippet:

```bash
module purge                  # Make sure that the environment is clean and no other modules are loaded
module load gnu9              # Load the gnu9 package, it is required for MPI
module load openmpi4          # Load open MPI, note that we have other MPI variants such as mpich and mvapich2

mpicc -O3 /opt/ohpc/pub/examples/mpi/hello.c
```

This will create a `a.out` file in your current directory.

Second, create the following Slurm batch script and name it `mpi.sh` for example:
```bash
#!/bin/bash

#SBATCH -J test               # Job name
#SBATCH -o mpi.job.out         # Name of stdout output file (%j expands to jobId)
#SBATCH -N 4                  # Total number of nodes requested
#SBATCH -n 16                 # Total number of mpi tasks requested
#SBATCH -t 01:30:00           # Run time (hh:mm:ss) - 1.5 hours


module purge                  # Make sure that the environment is clean and no other modules are loaded
module load gnu9              # Load the gnu9 package, it is required for MPI
module load openmpi4          # Load open MPI, note that we have also mpich and mvapich2
module load prun              

# Prun is a useful wrapper for MPI and slurm, it will take care of selecting the correct MPI flavour the you loaded before and pass it the correct configurations from slurm. You can use MPI without prun, but you will have to either call mpirun or mpiexec.hydra depending on you selection above.

# Launch MPI-based executable
prun a.out
```

The third and final step, is to submit the job script to Slurm:
```bash
sbatch mpi.sh
```

The results of the job will be stored in the `mpi.job.out` file you specified for Slurm.
An example output:
```
==========================================
SLURM_CLUSTER_NAME = linux
SLURM_ARRAY_JOB_ID = 
SLURM_ARRAY_TASK_ID = 
SLURM_ARRAY_TASK_COUNT = 
SLURM_ARRAY_TASK_MAX = 
SLURM_ARRAY_TASK_MIN = 
SLURM_JOB_ACCOUNT = test
SLURM_JOB_ID = 869
SLURM_JOB_NAME = test
SLURM_JOB_NODELIST = cn[01-04]
SLURM_JOB_USER = nobody
SLURM_JOB_UID = 1507
SLURM_JOB_PARTITION = standard
SLURM_TASK_PID = 5664
SLURM_SUBMIT_DIR = /home/nfs/testuser
SLURM_CPUS_ON_NODE = 24
SLURM_NTASKS = 16
SLURM_TASK_PID = 5664
==========================================
[prun] Master compute host = cn01
[prun] Resource manager = slurm
[prun] Launch cmd = mpirun a.out (family=openmpi4)

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn02

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn02

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn02

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn02

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn04

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn04

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn04

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn04

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn01

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn01

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn01

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn01

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn03

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn03

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn03

 Hello, world (1 procs total)
    --> Process #   0 of   1 is alive. -> cn03
```
