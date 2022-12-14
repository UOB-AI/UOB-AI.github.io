---
title: "How to use JupyterLab on the Benefit Cluster"
keywords: jupyter python jupyterlab notebook
sidebar: main_sidebar
permalink: jupyter.html
summary: This is a guide on how to use JupyterLab on the cluster
---

# Using Open OnDemand (Recommended)

From the top menu click on `Interactive apps`, and then choose JupyterLab.
Fill the form and start the Lab instance.

# Using Tunneling

```bash
# Login to the master node
ssh username@hayrat.uob.edu.bh

# Run the batch file, refer to the file for more details
sbatch launch_jupyterlab.sh

# Check the content of jupyter_config.out to get your server details.
# Take note of the hostname and the server URL, and port number.
cat jupyterlab_config.out

# Run the following commands on your PC

# Start an SSH tunnel between your PC and the compute node
# Note that the following one liner will create 2 tunnels
# PC <-----> Master Node <-----> Compute Node
ssh -t -L <port>:localhost:<port> username@hayrat.uob.edu.bh ssh -L <port>:localhost:<port> <hostname>

# Make sure that you replace <port> and <hostname> with the correct values from the output file
# Example
ssh -t -L 8888:localhost:8888 asubah@hayrat.uob.edu.bh ssh -L 8888:localhost:8888 hostname

# Finally, in your web browser, navigate to the URL you got from jupyterlab_config.out file.
```

launch_jupyterlab.sh
```bash
#!/bin/bash 

#SBATCH -J jupyterlab
#SBATCH --output=jupyterlab_config.out
#SBATCH --nodes=1
#SBATCH --time=30:00 # Set the time you want you jupyter lab to be running, if you remove this line it will take the partition default which is infinity.
#SBATCH --partition=compute # Change the partition to gpu if you want to connect to AMD/A100 machines.

echo 'hostname: ' `hostname` # Print the hostname to the output file

conda activate /data/software/conda/envs/jupyter # Activate the environment

jupyter-lab --no-browser --port 8888 # Run the server on port 8888
# Note that if port is used Jupyter lab will try another port, refer to the output file to get the selected port
```
