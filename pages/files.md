---
title: "Upload or Download Files"
keywords: keyword
sidebar: main_sidebar
permalink: files.html
summary: How to upload or download files to the cluster 
published: true
---

## Using Rsync
> Rsync is a fast and extraordinarily versatile file copying tool. It can copy locally, to/from another host over any remote shell, or to/from a remote rsync daemon.

```bash
# Rsync syntax
rsync source destination

# Upload file to the cluster
rsync -avhz data.csv username@hayrat.uob.edu.bh:/home/nfs/username

# Download file from the cluster
rsync -avhz username@hayrat.uob.edu.bh:/home/nfs/username/data.csv /home/username

# Use ~ instead of the full path to home
rsync -avhz username@hayrat.uob.edu.bh:~/data.csv ~
```

- -a: archive mode
- -v: increase verbosity
- -h: output numbers in a human-readable format
- -z: compress file data during the transfer

You can read more in the `rsync` manual by running:
```bash
man rsync
```

## Using OpenOnDemand

After you login navigate to the file explorer:

{% include image.html file="files-1.png" url="" alt="Files > Home Directory" caption="" %}

Click on the `Upload` button:

{% include image.html file="files-2.png" url="" alt="Upload Button" caption="" %}

Select your files:

{% include image.html file="files-3.png" url="" alt="Upload Panel" caption="" %}
