---
title: "Account & Storage Management"
keywords: keyword
sidebar: main_sidebar
permalink: account.html
summary: Commands to manage your account
published: true
---

## Change your password

Use the passwd command to change your password. It will ask you for the current password and the new password.
```bash
passwd
```

## Storage quota
You can check you storage quota by using:

```bash
df -kh ~
```

Sample output
```bash
Filesystem                               Size  Used Avail Use% Mounted on
............:/ifs/data/adhari/zone1/nfs  5.0G     0  5.0G   0% /home/nfs
```

### File Sizes
To check the file and directory sizes in the current directory:

```bash
# Don't forget the dot in the end of the command
du -hd1 .
```

To further debug your storage problem, refer to the following issue: [#14](https://github.com/UOB-AI/UOB-AI.github.io/issues/14)

If you need more storage, you can use the shared datasets directory `/data/datasets/`.

If you need more private storage, contact us on [ailab@uob.edu.bh](mailto:ailab@uob.edu.bh).

## How to fix "No Space Left" Errors
As mentioned above, to check your storage quota you can use the following command:
```bash
df -kh ~
```
Running this on your home directory, I got the following output:
```
Filesystem                               Size  Used Avail Use% Mounted on
10.240.240.3:/ifs/data/adhari/zone1/nfs  5.0G  5.0G     0 100% /home/nfs
```
Which shows that you used all your available storage for your home directory, which is 5 GB.

The first thing you need to do if you filled your home directory is to run the following command to check what is the root of the problem:
```bash
du -hd1 ~
```
Running this on your home directory will give you the following:
```
32K	/home/nfs/username/.ipynb_checkpoints
64K	/home/nfs/username/.config
423M	/home/nfs/username/SpectralNet
64K	/home/nfs/username/.nv
320K	/home/nfs/username/.ipython
80K	/home/nfs/username/.ssh
96K	/home/nfs/username/.mozilla
1.4M	/home/nfs/username/.cache
4.6G	/home/nfs/username/.local
288K	/home/nfs/username/.jupyter
50M	/home/nfs/username/.conda
5.0G	/home/nfs/username/
```
This shows that the size of your `.local` folder is almost 5 GB. This folder usually contains the packages you installed.

To inspect the `.local` directory in details, we can run:
```bash
du -h ~/.local | sort -h
```
Notice that the `d1` from the `du` command is removed, which limit the output to one level of directory depth. Now the command will print the sizes of the directories under `.local` and all the subdirectories. And then we pipe the output of the `du` command using `|` to the `sort` command, which will sort the directories in ascending order.
And this is part of the output from your account:
```
...
117M	/home/nfs/username/.local/lib/python3.9/site-packages/triton/_C
137M	/home/nfs/username/.local/lib/python3.9/site-packages/triton
153M	/home/nfs/username/.local/lib/python3.9/site-packages/cmake/data/share/cmake-3.26
154M	/home/nfs/username/.local/lib/python3.9/site-packages/cmake/data/share
170M	/home/nfs/username/.local/lib/python3.9/site-packages/sympy
196M	/home/nfs/username/.local/lib/python3.9/site-packages/cmake
196M	/home/nfs/username/.local/lib/python3.9/site-packages/cmake/data
234M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cusolver
234M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cusolver/lib
322M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cusparse
322M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cusparse/lib
324M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/nccl/lib
325M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/nccl
343M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cufft/lib
344M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cufft
603M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cublas/lib
604M	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cublas
793M	/home/nfs/username/.local/share/Trash/files
794M	/home/nfs/username/.local/share/Trash
805M	/home/nfs/username/.local/share
1.1G	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cudnn
1.1G	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia/cudnn/lib
3.1G	/home/nfs/username/.local/lib/python3.9/site-packages/nvidia
3.8G	/home/nfs/username/.local/lib
3.8G	/home/nfs/username/.local/lib/python3.9
3.8G	/home/nfs/username/.local/lib/python3.9/site-packages
4.6G	/home/nfs/username/.local
```

You can see that almost all the large directories are python packages, specifically nvidia packages.
These packages you should uninstall because they are already available in the Conda environments.
If you run the following command, you will get all the packages installed in your `.local` directory:
```bash
conda activate
pip list --user
```
This is part of the output:
```
nvidia-cublas-cu11       11.10.3.66
nvidia-cuda-cupti-cu11   11.7.101
nvidia-cuda-nvrtc-cu11   11.7.99
nvidia-cuda-runtime-cu11 11.7.99
nvidia-cudnn-cu11        8.5.0.96
nvidia-cufft-cu11        10.9.0.58
nvidia-curand-cu11       10.2.10.91
nvidia-cusolver-cu11     11.4.0.1
nvidia-cusparse-cu11     11.7.4.91
nvidia-nccl-cu11         2.14.3
nvidia-nvtx-cu11         11.7.91
```
All the packages that start with `nvidia-` are not needed. Or large packages that already exist in the Conda environments, such as Pytorch or Tensorflow. You can uninstall them by running:
```bash
pip uninstall nvidia-cublas-cu11 nvidia-cudnn-cu11 ...
```

This will solve most of your problem.
Now, if we go back to the output of the last `du` command, we can find another problematic directory which is:
```
/home/nfs/username/.local/share/Trash/files
```
This directory contains the files you delete using the JupyterLab delete button.
![Screenshot from 2023-07-06 14-03-36](https://github.com/UOB-AI/UOB-AI.github.io/assets/7252022/edaa5cc2-d29f-4d09-973b-9ad3938f69cb)

To empty the trash you can run:
```bash
rm -rf /home/nfs/username/.local/share/Trash/files
```
The final thing is the `/data/datasets` directory.
Because the home directory space is limited, we offer a larger directory for you to store your data and models.
To use this directory, you can either `cd` to it in the terminal, and create a directory for your project.
```bash
cd /data/datasets
mkdir myproject
```
And then point your code to use that directory to download and store your files.
Or you can create a symbolic link (shortcut) from your project directory to your home directory to make it easier for you:
```bash
mkdir /data/datasets/myproject
ln -s /data/datasets/myproject ~
```
This should solve all your problems.
