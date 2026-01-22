---
title: "Account & Storage Management"
keywords: keyword
sidebar: main_sidebar
permalink: account.html
summary: Commands to manage your account, check storage quotas, and troubleshoot common storage issues
published: true
---

{% include warning.html content="**Critical Storage Limitation:** Your home directory (`~/` or `/home/$USER`) on the cluster is limited to **5GB**. A single virtual environment with modern data science packages (like PyTorch or TensorFlow) can easily exceed this limit. You **must** create all project directories and virtual environments under `/data/datasets/$USER`, which has a much larger storage quota." %}

## Change Your Password

Use the `passwd` command to change your password. It will prompt you for your current password followed by the new password.

```bash
passwd
```

## Storage Quota

### Check Your Home Directory Quota

You can check your storage quota using:

```bash
df -kh ~
```

Sample output:
```bash
Filesystem                               Size  Used Avail Use% Mounted on
............:/ifs/data/adhari/zone1/nfs  5.0G     0  5.0G   0% /home/nfs
```

### Check File and Directory Sizes

To check the file and directory sizes in the current directory:

```bash
# Don't forget the dot at the end of the command
du -hd1 .
```

To see the largest directories sorted by size:

```bash
du -h ~ | sort -h | tail -20
```

To find the largest files in your home directory:

```bash
find ~ -type f -exec du -h {} + 2>/dev/null | sort -h | tail -20
```

### Additional Storage Commands

| Command | Description |
|---------|-------------|
| `df -kh ~` | Check home directory quota |
| `df -kh /data/datasets` | Check datasets directory quota |
| `du -hd1 .` | Show sizes of items in current directory |
| `du -sh ~/` | Show total size of home directory |
| `du -h ~ \| sort -h` | List all directories sorted by size |
| `find ~ -size +100M` | Find files larger than 100MB |

For additional debugging of storage problems, refer to: [Issue #14](https://github.com/UOB-AI/UOB-AI.github.io/issues/14)

## Storage Options

### Home Directory (`~/`)
- **Quota:** 5GB
- **Use for:** Configuration files, small scripts, SSH keys
- **Avoid:** Large datasets, virtual environments, model checkpoints

### Datasets Directory (`/data/datasets/$USER`)
- **Quota:** Much larger (contact admin for specific limits)
- **Use for:** Projects, virtual environments, datasets, model weights, checkpoints
- **Access:** Create your own subdirectory

If you need additional private storage beyond these options, contact us at [ailab@uob.edu.bh](mailto:ailab@uob.edu.bh).

## How to Fix "No Space Left on Device" Errors

If you encounter "No space left on device" errors, your home directory is likely full. Follow the steps below to diagnose and resolve the issue.

### Step 1: Check Your Current Usage

First, verify that your home directory is full:

```bash
df -kh ~
```

Example output showing a full home directory:
```
Filesystem                               Size  Used Avail Use% Mounted on
10.240.240.3:/ifs/data/adhari/zone1/nfs  5.0G  5.0G     0 100% /home/nfs
```

### Step 2: Identify Large Directories

Run the following command to see what's consuming space:

```bash
du -hd1 ~
```

Example output:
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

### Step 3: Investigate the Largest Directories

To drill down into a specific directory (e.g., `.local`):

```bash
du -h ~/.local | sort -h
```

This removes the depth limit and sorts by size. Example output:

<details>
<summary>Click to expand example output</summary>

<pre>
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
</pre>

</details>

### Step 4: Common Culprits and Solutions

#### 4.1 Remove Redundant Python Packages

Python packages installed with `pip install --user` go to `~/.local` and can consume gigabytes of space. Many of these packages (especially NVIDIA libraries) are already available in the cluster's Conda environments.

List user-installed packages:

```bash
conda activate
pip list --user
```

Common packages to uninstall (already available in Conda environments):

```bash
pip uninstall nvidia-cublas-cu11 nvidia-cuda-cupti-cu11 nvidia-cuda-nvrtc-cu11 \
    nvidia-cuda-runtime-cu11 nvidia-cudnn-cu11 nvidia-cufft-cu11 \
    nvidia-curand-cu11 nvidia-cusolver-cu11 nvidia-cusparse-cu11 \
    nvidia-nccl-cu11 nvidia-nvtx-cu11
```

To remove **all** user-installed packages (use with caution):

```bash
pip freeze --user | xargs pip uninstall -y
```

#### 4.2 Empty the Trash

Files deleted through JupyterLab's interface are moved to trash, not permanently deleted:

![JupyterLab delete button](https://github.com/UOB-AI/UOB-AI.github.io/assets/7252022/edaa5cc2-d29f-4d09-973b-9ad3938f69cb)

To permanently delete trashed files:

```bash
rm -rf ~/.local/share/Trash/*
```

#### 4.3 Clear Cache Directories

Various applications store cache files that can accumulate over time:

```bash
# Clear pip cache
pip cache purge

# Clear conda cache (if using conda in home directory)
conda clean --all

# Clear general cache
rm -rf ~/.cache/*

# Clear Jupyter checkpoints
find ~ -name ".ipynb_checkpoints" -type d -exec rm -rf {} + 2>/dev/null
```

#### 4.4 Remove Old Virtual Environments

If you have virtual environments in your home directory:

```bash
# List potential virtual environment directories
ls -la ~/venv* ~/env* ~/.virtualenvs 2>/dev/null

# Remove a virtual environment
rm -rf ~/venv_name
```

#### 4.5 Move Large Files to `/data/datasets`

Move existing large files to the datasets directory:

```bash
# Create your personal directory
mkdir -p /data/datasets/$USER

# Move a large directory
mv ~/large_project /data/datasets/$USER/

# Create a symbolic link for easy access
ln -s /data/datasets/$USER/large_project ~/large_project
```

### Summary of Quick Fixes

| Problem | Solution |
|---------|----------|
| Large `~/.local` directory | Uninstall user-installed pip packages |
| Trash taking up space | `rm -rf ~/.local/share/Trash/*` |
| Cache files | `pip cache purge` and `rm -rf ~/.cache/*` |
| Virtual environments in `~` | Move to `/data/datasets/$USER` |
| Large datasets/models | Move to `/data/datasets/$USER` |
| Jupyter checkpoints | `find ~ -name ".ipynb_checkpoints" -exec rm -rf {} +` |

## Best Practices: Using `/data/datasets`

To avoid storage issues, **always** set up your projects in `/data/datasets/$USER` from the start.

### Initial Setup

```bash
# Create your personal directory (one-time setup)
mkdir -p /data/datasets/$USER

# Navigate to your directory
cd /data/datasets/$USER
```

### Create a New Project

```bash
# Create project directory
mkdir -p /data/datasets/$USER/myproject
cd /data/datasets/$USER/myproject

# Create a virtual environment here (not in home directory!)
python -m venv venv
source venv/bin/activate
```

### Create Symbolic Links for Convenience

If you prefer accessing your projects from your home directory:

```bash
# Create a link to your project
ln -s /data/datasets/$USER/myproject ~/myproject

# Create a link to the entire datasets directory
ln -s /data/datasets/$USER ~/datasets
```

Now you can access `/data/datasets/$USER/myproject` simply by typing `cd ~/myproject`.

### Configure Common Tools

#### Pip

Set pip to install packages to a directory outside your home:

```bash
# Add to your ~/.bashrc
export PIP_TARGET=/data/datasets/$USER/.pip-packages
export PYTHONPATH=/data/datasets/$USER/.pip-packages:$PYTHONPATH
```

#### Conda (if creating personal environments)

```bash
# Create conda environment in datasets directory
conda create --prefix /data/datasets/$USER/conda-envs/myenv python=3.9
conda activate /data/datasets/$USER/conda-envs/myenv
```

#### Hugging Face / Transformers

```bash
# Add to your ~/.bashrc
export HF_HOME=/data/datasets/$USER/.cache/huggingface
export TRANSFORMERS_CACHE=/data/datasets/$USER/.cache/huggingface/transformers
```

#### PyTorch

```bash
# Add to your ~/.bashrc
export TORCH_HOME=/data/datasets/$USER/.cache/torch
```

### Example `.bashrc` Additions

Add these lines to your `~/.bashrc` to automatically redirect cache and data directories:

```bash
# Redirect common cache directories to datasets
export HF_HOME=/data/datasets/$USER/.cache/huggingface
export TRANSFORMERS_CACHE=/data/datasets/$USER/.cache/huggingface/transformers
export TORCH_HOME=/data/datasets/$USER/.cache/torch
export XDG_CACHE_HOME=/data/datasets/$USER/.cache

# Create directories if they don't exist
mkdir -p $HF_HOME $TRANSFORMERS_CACHE $TORCH_HOME $XDG_CACHE_HOME 2>/dev/null
```

After editing, reload your configuration:

```bash
source ~/.bashrc
```
