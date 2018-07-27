---
title: "Using the same Home Directory in Windows and WSL"
layout: post
---

I've recently been using the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/about) a lot more recently now that I'm working with Linux servers.

One thing I've found frustrating is having to keep my home directories in sync, for example having to duplicate my ssh keys between my Windows home directory in `c:\users\Jeremy\.ssh` and my WSL installation in `\home\jeremy\.ssh`, along with various other files like `.gitconfig`.

It turns out it's actually pretty straight forward to set WSL to use your Windows home directory. 

First, within WSL edit the `/etc/passwd` file (eg with `sudo nano /etc/passwd`). Find the line that defines your username (probably at the bottom), which will look something like this:

```
jeremy:x:1000:1000:,,,:/home/jeremy:/bin/bash
```

...and change the home directory path so that it points to your Windows home directory using WSL path notation:

```
jeremy:x:1000:1000:,,,:/mnt/c/users/jeremy:/bin/bash
```

Exit WSL and re-open it, and your home directory will now match your windows directory. 

### Metadata and File Permissions

A problem with this approach is that your ssh keys need to be secured, but by default Windows files accessed through WSL are readable/writable by everyone and `chmod` has no affect on Windows files. This can be remedied by re-mounting your Windows partition inside WSL with the metdata option. Edit the `/etc/wsl.conf` file (create it if it doesn't exist) and add the following:

```ini
[automount]
options = "metadata"
```

Log out from WSL and log in again, and now the windows partition will be mounted with metadata and chmod will work against windows files. You can now `chmod 600 ~/.ssh/id_rsa` and everything will work correctly.
