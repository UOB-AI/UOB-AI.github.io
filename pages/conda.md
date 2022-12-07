---
title: "Conda"
keywords: conda, python, environments
sidebar: main_sidebar
permalink: conda.html
summary: Conda package manager
published: true
---

> Conda is an open source package management system and environment management system that runs on Windows, macOS, Linux and z/OS. Conda quickly installs, runs and updates packages and their dependencies. Conda easily creates, saves, loads and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

In our cluster we use conda to create and manage python environments. Conda should be available for you automatically as soon as you login through the terminal. To initialise conda in your own shell configuration file, which is optional, run the following command:
```bash
# This assumes that you are using bash
conda init

# To initialise for zsh
conda init zsh
```

**To list all the available environments:**
```bash
conda env list
```

This should produce the following output:
```
# conda environments:
#
base                  *  /data/software/miniconda3
jupyter                  /data/software/miniconda3/envs/jupyter
programl                 /data/software/miniconda3/envs/programl
pytorch                  /data/software/miniconda3/envs/pytorch
rapids-22-10-py39        /data/software/miniconda3/envs/rapids-22-10-py39
rapids-22.04             /data/software/miniconda3/envs/rapids-22.04
tf2                      /data/software/miniconda3/envs/tf2
tf2-gpu                  /data/software/miniconda3/envs/tf2-gpu
tf2-rapids-py39          /data/software/miniconda3/envs/tf2-rapids-py39
torch                    /data/software/miniconda3/envs/torch
```

You can notice that we host our environments on `/data/software/miniconda3/env/`. Which is a network file system (nfs) that is available for the master node and all computing nodes. The `base` environment which is marked with a `*` symbol is the default environment.

**To activate an environment:**
```bash
# Using environment name
conda activate <environment-name>

# Example
conda activate pytorch

# Or using the environment path
conda activate /data/software/miniconda3/envs/pytorch
```

**To list the packages available in an environment after you activate it:**
```bash
conda list
```

**To deactivate an environment:**
```bash
conda deactivate
```

**To install extra packages to the environment use:**
```bash
pip install <package-name>
```
Note this will install the package to your user space.

**To add the activated environment to JupyterLab as a kernel:**
```bash
# Make sure you activate the environment first or this won't work
# Make sure to change <environment-name> and <display-name> to what you want
python -m ipykernel install --user --name <environment-name> --display-name "<display-name>"

# Example
python -m ipykernel install --user --name tf2-rapids-py39 --display-name "TF2 RAPIDS Py39"
```

After that you will find the environment in the kernel drop-down menu in JupyterLab.

_For more information about conda refer to its [documentation page](https://docs.conda.io/projects/conda/en/stable/user-guide/index.html)._
