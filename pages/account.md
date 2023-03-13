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
