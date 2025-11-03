---
title: "A Guide to Python Virtual Environments (venv)"
keywords: python venv
sidebar: main_sidebar
permalink: python.html
summary: This guide explains what Python virtual environments are, why you must use them, and how to create and manage them effectively on the cluster.
published: true
---

# A Guide to Python Virtual Environments (venv)

This guide explains what Python virtual environments are, why you must use them, and how to create and manage them effectively on the cluster.

---

## What is a Python Virtual Environment?

A Python virtual environment (or "venv") is a self-contained, isolated directory that holds a specific Python interpreter and all the additional packages you install for a single project.

Think of it as a clean "sandbox." On our cluster, you first use **Conda** to select your base Python version (like 3.11), and then use **`venv`** to create an isolated project sandbox based on that version.

---

## Why Use a Virtual Environment?

* **Avoids Dependency Conflicts:** This is the most important reason.
    * **Project A** might need `tensorflow==2.10`.
    * **Project B** might need the new `tensorflow==2.17`.
    * By giving each project its own venv, they can both have their specific dependencies without any conflicts.
* **Keeps Environments Clean:** It prevents you from cluttering up the base Conda environments (like `py3.11`) with packages for just one project.
* **Ensures Reproducibility:** It allows you to create a `requirements.txt` file, which is a "recipe" for your project. Anyone can use this file to perfectly recreate the exact environment needed to run your code.

---

## Important Note on Storage
{% include warning.html content="Your home directory (`~/` or `/home/$USER`) on the cluster is **limited to 5GB**" %}

Your home directory (`~/` or `/home/$USER`) on the cluster is **limited to 5GB**. A single virtual environment with modern data science packages (like PyTorch or TensorFlow) can easily exceed this limit.

Therefore, you **must create all your project directories and virtual environments under the `/data/datasets/$USER` directory**, which has a much larger storage quota.

If you see "No space left on device" errors, your home directory is likely full.
* **See here for how to fix storage errors:** [https://uob-ai.github.io/account.html#how-to-fix-no-space-left-errors](https://uob-ai.github.io/account.html#how-to-fix-no-space-left-errors)

---

## How to Create a Venv (The First Time)

Follow these steps when you are starting a **new project**.

### 1. Create Your Project Directory

First, navigate to your dedicated data directory and create a folder for your new project.

```bash
# The '$USER' variable will automatically resolve to your username
mkdir -p /data/datasets/$USER/my-new-project

# Now, 'cd' into it. This is where you will work.
cd /data/datasets/$USER/my-new-project
```

### 2. Load Your Desired Base Python (via Conda)

Before creating your venv, you must load the base Python you want to build it from.

```bash
# First, see which Python versions are available
conda env list
```
This will show you a list of pre-configured Conda environments, such as `py3.9`, `py3.10`, `py3.11`, etc.

```bash
# Activate the base environment you want. Let's use 3.11.
conda activate py3.11
```
Your prompt will now change to show `(py3.11)`.

### 3. Create the Virtual Environment

Now that you have your base Python loaded, create your project-specific venv. It is standard practice to name the venv directory `venv`.

```bash
# (py3.11) is active
# This command uses the 'py3.11' python to create a new sandbox named 'venv'
python3 -m venv venv
```
You will now have a new folder named `venv` inside your project directory.

### 4. Activate the Virtual Environment

Now, "activate" your new project-specific venv. This switches you from the *base* `(py3.11)` environment to your *project* `(venv)` environment.

```bash
# 'source' runs the activate script
source venv/bin/activate
```
Your terminal prompt will change again to show `(venv)` at the front:
```bash
(venv) [your-username@cluster-node my-new-project]$
```

### 5. Use the Virtual Environment

You are now in your clean, isolated project environment. You can safely install packages.

```bash
# (venv) is active
# Good practice: upgrade pip inside the venv first
pip install --upgrade pip

# Install packages. They will only be installed in
# /data/datasets/$USER/my-new-project/venv/...
pip install pandas numpy jupyterlab
```

---

## How to Use Your Venv (The Second Time)

Once you have already created your virtual environment, you **do not** need to create it again.

The next time you log in to work on your project, the process is much simpler. You just need to navigate to your project and *activate* the existing environment.

### 1. Navigate to Your Project Folder

Your project (and its `venv`) is saved in your data directory.
```bash
# Replace 'my-new-project' with your actual project folder name
cd /data/datasets/$USER/my-new-project
```

### 2. Load the Base Python (Conda)

You still need to load the *same* base Conda environment (e.g., `py3.11`) that you used when you first created the venv.
```bash
# Activate the same base version you used before
conda activate py3.11
```

### 3. Activate Your Project's Venv

This is the final step. Just "source" the activate script that already exists inside your `venv` folder.
```bash
source venv/bin/activate
```
That's it! Your prompt will change to `(venv)`, and you can continue right where you left off.

---

## Managing Dependencies (The `requirements.txt` File)

This is the key to making your project reproducible.

### 1. Save Your Dependencies

Once your project is working, save a list of all the packages you installed.
```bash
# This saves the list of packages to a new file
pip freeze > requirements.txt
```
This creates a `requirements.txt` file. You should commit this file to your Git repository.

### 2. Install Dependencies from a File

When a new developer (or you, on a new computer) wants to run your project, they follow the "First Time" setup steps, but **skip step 5 (installing packages manually)**.

Instead, after activating the new venv (step 4), they run this one command:
```bash
# This reads the 'recipe' and installs the exact same packages
pip install -r requirements.txt
```

---

## Deactivating the Virtual Environment

When you are finished working, you can "deactivate" the environment to return to the base Conda environment.

Just type this one command:
```bash
deactivate
```
Your prompt will return to the base Conda environment (e.g., `(py3.11)`). You can then run `conda deactivate` again if you wish to return to the cluster's default shell.

---
