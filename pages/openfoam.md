---
title: "OpenFoam Tutorial"
keywords: openfoam, smiulation, fluid, dynamics, cfd
sidebar: main_sidebar
permalink: openfoam.html
summary: Instrucitons on how to use OpenFoam on the cluster
published: True
author: Ali Marzooq
---
This is a brief tutorial on how to use __OpenFoam__ on the cluster to run CFD simulations:

First, you have to setup your case, define the problem, create the mesh, define the boundary conditions, initial conditions, setup the solvers and solution schemes and the control dictionary. 

After that, copy the case file into your folder in the cluster, or into `/data/datasets/` folder (I recommend this as you have more storage here). There are multiple ways to do [this](files.html), I prefer using rsync from my terminal window (I use Ubuntu on WSL): 

```bash
rsync source destination 
rsync -avhz <case file path> user@hayrat.uob.edu.bh:<desitnation path> 
# for example: 
rsync -avhz /home/amarzooq/OpenFOAM/amarzooq-v2106/run/run/tutorial_case ammarzooq@hayrat.uob.edu.bh:/home/nfs/datasets/myfolder/folder

```

After copying the case, login to the cluster either from terminal or from OnDemand.

From OnDemand : [https://hayrat.uob.edu.bh/](https://hayrat.uob.edu.bh/)
Use [JupyterLab](jupyter.html), and choose the partition, number of hours and nodes. 

or from terminal: 
```bash
ssh username@hayrat.uob.edu.bh
# type your password 
srun --partition gpu --time=04:00:00 --pty /bin/bash # here you choose the partition, the time and nodes 
# Now initiate openfoam
source /data/software/spack/share/spack/setup-env.sh
spack load openfoam
```

From here, we will continue the procedure using OnDemand. 

First, you will need to initiate OpenFoam: 
```bash
spack load openfoam
```

Now, go to your case file and if you want to parallelly process your case you should ensure that you have a `decomposeParDict` file in your system directory. 
This `decomposeParDict` specifies how many processes you want to run your case with, each process will run on a separate core -as far as I understand-. 

An example of such file is: 

```c++
FoamFile
{
  version         2.0;
  format          ascii;
  class           dictionary;
  object          decomposeParDict;
}

numberOfSubdomains 28;
method          simple;

simpleCoeffs
{
  n       (4 2 2); // divide the domain by 4 regions in x, 2 in y and 2 in z 
  delta   0.001;
}
```

There are multiple methods to divide the case, refer to the [documentation for more information](https://doc.cfd.direct/openfoam/user-guide-v6/running-applications-parallel).

After setting up the case and the `decomposeParDict`, you are ready to run the case. The example below illustrates how to do this by using `pisoFoam` solver:

```bash
# Generally, this is my procedure to do the final steps for running the case
# check boundary conditions and initial conditions in 0 and constant directories  
# check your fvSchemes and FvSolutions
# check your controlDict > define the required functions like Cd,Cl

checkMesh # to check your mesh 
decomposePar # decomposes the case for parallel processing
renumberMesh -overwrite # This command will renumber the mesh to minimize its bandwidth, in other words, it will make the linear solver run faster (at least for the first time-steps)
mpirun -np 16 pisoFoam -parallel > log.pisofoam # This runs the case and saves a log file of the results, 16 is number of the processors 
# After the simulation finishes

```

```bash
# Copy the case back to your PC
# Download file from the cluster - needed later
rsync -avhz ammarzooq@hayrat.uob.edu.bh:/home/nfs/username/data.csv /home/username
```

A nice way to see the computational resources you are using is via: 
[https://hayrat.uob.edu.bh/stats/general](https://hayrat.uob.edu.bh/stats/general)
