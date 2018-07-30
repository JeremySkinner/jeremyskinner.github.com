---
title: "Powershell SSH Connection manager"
layout: post
---

I've recently a connection manager for SSH connections to the [posh-sshell](https://github.com/dahlbyk/posh-sshell) project.

### What is Posh-Sshell?

Posh-Sshell is a set of powershell scripts that making working with SSH agents and clients easier. These utilities were originally part of the [posh-git](https://github.com/dahlbyk/posh-git) project, but have been separated into a separate module in preparation for the Posh-Git 1.0 release. 

Posh-Sshell can be [downloaded from the powershell gallery](https://www.powershellgallery.com/packages/posh-sshell/0.2.0) by running the following in a powershell prompt:

```powershell
Install-Module Posh-Sshell -Scope CurrentUser
```

Once installed, import the module with `Import-Module Posh-Shell` (you can add this to your powershell profile so you don't need to run it every time you launch a new terminal).

### Connection Manager

Since splitting the SSH functionality out of posh-git, I've been working on several new features the first of which is the Connection Manager. 

The Connection Manager can be used to display a list of SSH connections as well as add/remove connections from the `~/.ssh/config` file. By running `Connect-Ssh`, you'll be presented with a list of connections stored in your .ssh/config file:

![image](https://user-images.githubusercontent.com/90130/43395146-f9060402-93f4-11e8-8877-b9987006b632.png)

You can enter the number of the server into the prompt, and an SSH connection will be made to that server. If no username is specified in the configuration file, then you'll also be prompted for a username. 

#### Adding a New Connection

A new connection can be added by running `Add-SshConnection`. In its simplest form, it takes an alias and a URI, eg:

```powershell
Add-SshConnection MyServer3 myserver.mydomain.com
```

You can also specify additional common properties such as the username, either by using the `-User` parameter or using the `user@host` syntax:

```powershell
# These are both the same
Add-SshConnection MyServer3 myserver.mydomain.com -User jeremy
Add-SshConnection MyServer3 jeremy@myserver.mydomain.com
```

You can specify a custom key file by using `-IdentityFile <path>` as well as parameters for configuring an SSH tunnel with `-LocalTunnelPort <port number>` and `-RemoteTunnelPort <port number>`. 

Additional parameters can also be specified by supplying a hashtable to the `-AdditionalOptions` parameter.

After running `Add-SshConnection MyServer3 myserver.mydomain.com -User jeremy`, your .ssh/config file will contain the new entry:

```
Host MyServer3
  HostName myserver.mydomain.com
  User jeremy
```

### Removing an entry

Entries can be removed from the config file by running `Remove-SshConnection <name>`


