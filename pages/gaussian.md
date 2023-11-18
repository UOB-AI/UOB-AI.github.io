---
title: "Gaussian"
keywords: gaussian
sidebar: main_sidebar
permalink: gaussian.html
summary: Using the computational chemistry software package Gaussian 16
published: true
---

{% include warning.html content="Gaussian 16 currently works only on the Intel compute nodes cn0[1-8]. On the AMD nodes it will crash." %}

## How To Use Gaussian 16 On The Cluster

### Join the users group

Gaussian is a proprietary computational chemistry software package. Therefore, to use the package on the cluster you need to be part of the `gaussian` users group. To get into the group you can contact the Chemistry Department in the College of Science.

### Slurm Example

Slurm is our cluster management and job scheduling system. You can read more about it [here](slurm.html) and [here](https://slurm.schedmd.com/overview.html).

This is an example Slurm batch script that you can start with and modify as you see fit.

```bash
#!/bin/bash
#
#SBATCH --job-name=g16
#SBATCH --output=g16.%j.out
#SBATCH --partition=compute
#
#SBATCH --time=10:00:00

# Setting up the environment
export g16root="/data/software/packages/Gaussian-16.B.01"
export GAUSS_SCRDIR="/data/datasets/$UID/g16scr/$SLURM_JOB_ID"
source $g16root/g16/bsd/g16.profile

mkdir -p $GAUSS_SCRDIR

# Running the program
g16 input.gjf
```

Write the above script in a file and name it `g16.sbatch` for example.
Submit the job using `sbatch`:
```bash
sbatch g16.sbatch
```
