---
title: "Quantum ESPRESSO"
keywords: Quantum ESPRESSO
sidebar: main_sidebar
permalink: qe.html
summary: Using the computational chemistry software package Quantum ESPRESSO
published: true
---

# using srun

You need to create a directory on our beegfs storage where you have more capacity.
The home directory is limited to 5GB.
```bash
mkdir /data/datasets/$USER
cd /data/datasets/$USER
```

alocate compute resources using SLURM
This will give you an interactive shell on one of the compute nodes (Intel CPUs + T4 GPUs)
A better way to do it is by using sbatch see below.
```bash
srun -t 12:00:00 -p standard --pty /bin/bash
```


load the correct spack installation for the current compute node.
```bash
source /data/software/packages/spack/setup-env.sh
```

load nvhpc

```bash
spack load nvhpc@24.1
```

load quantum-espresso
```
spack load quantum-espresso /3qnajq7
```

now you can run quantum-espresso binaries such as pw.x with mpirun

```bash
mpirun pw.x < inputfile
```

# using sbatch

put an sbatch file sample and instructions on how to use it.
# Tutorial: Running Quantum ESPRESSO on HPC Cluster

## Part 1: Setting Up Your Workspace

### Create a Working Directory

Your home directory is limited to 5GB, so you need to use the shared storage for larger datasets.

```bash
mkdir /data/datasets/$USER
cd /data/datasets/$USER
```

This directory has significantly more capacity for your computational work.

---

## Part 2: Interactive Computing with srun

### Allocate Compute Resources

Use `srun` to request an interactive shell on a compute node. This gives you direct access to Intel CPUs and T4 GPUs.

```bash
srun -t 12:00:00 -p standard --pty /bin/bash
```

**Parameters explained:**
- `-t 12:00:00` — Request 12 hours of compute time
- `-p standard` — Use the standard partition
- `--pty /bin/bash` — Launch an interactive bash shell

You'll now have a shell prompt on a compute node.

### Load Required Software

**Step 1: Load Spack environment**

Spack is a package manager that manages software installations. Load it first:

```bash
source /data/software/packages/spack/setup-env.sh
```

**Step 2: Load NVHPC compiler suite**

```bash
spack load nvhpc@24.1
```

**Step 3: Load Quantum ESPRESSO**

```bash
spack load quantum-espresso /3qnajq7
```

### Run Quantum ESPRESSO

Now you can execute Quantum ESPRESSO calculations:

```bash
mpirun pw.x < inputfile
```

Replace `inputfile` with your actual input file.

---

## Part 3: Batch Processing with sbatch

For longer or multiple jobs, use `sbatch` instead of interactive mode. This submits jobs to the queue without requiring you to stay connected.
### CD to your workspace

Create it if it doesn't exist:

```bash
cd /data/datasets/$USER
```

### Create a Batch Script

Create a file named `job.sh`:

```bash
#!/bin/bash
#SBATCH -t 12:00:00
#SBATCH -p standard
#SBATCH -J qe_job
#SBATCH -o output.log
#SBATCH -e error.log

source /data/software/packages/spack/setup-env.sh
spack load nvhpc@24.1
spack load quantum-espresso /3qnajq7

cd /data/datasets/$USER
mpirun pw.x < inputfile
```

**Script parameters:**
- `#SBATCH -t 12:00:00` — Walltime limit (12 hours)
- `#SBATCH -p standard` — Partition to use
- `#SBATCH -J qe_job` — Job name
- `#SBATCH -o output.log` — Standard output file
- `#SBATCH -e error.log` — Error output file

### Submit Your Job

```bash
sbatch job.sh
```

Check job status:

```bash
squeue -u $USER
```

View results in `output.log` and `error.log` once the job completes.

