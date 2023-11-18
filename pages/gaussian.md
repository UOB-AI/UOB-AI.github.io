---
title: "Gaussian"
keywords: gaussian
sidebar: main_sidebar
permalink: gaussian.html
summary: Using the computational chemistry software package Gaussian 16
published: false
---

{% include warning.html content="Gaussian 16 currently works only on the Intel compute nodes cn0[1-8]. On the AMD nodes it will crash." %}

## How To Use Gaussian 16 On The Cluster

### Join the users group

Gaussian is a propriatry computational chemistry software package. Therefore, to ue the package on the cluster you need to be part of the `gaussian` users group. To get into the group you can contact the Chemistry Department in the College of Science.

### Edit your .bashrc file

To make things easier for you. You can add the following environment variables in the `.bashrc` file in your home directory. If the file doesn't exist create it.
You can edit the file using any cli text editor of your choice such as `nano` or `vim`. Or you can edit it through the file explorer in the Open OnDemand portal.

TODO add a notice here about dor files

```bsah
nano ~/.bashrc
```

Then add the following lines to the end of the file and save it.

```bsah
export g16root="/home/nfs/asubah/gaussian/"
export GAUSS_SCRDIR="/home/nfs/asubah/gaussian/scr"
source $g16root/g16/bsd/g16.profile
```

