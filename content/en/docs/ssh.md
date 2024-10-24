+++
author = "Thomas Evensen"
date = "2021-04-16"
title = "Passwordless login"
tags = ["passwordless","ssh-key"]
categories = ["remote servers"]
lastmod = "2020-04-16"
+++
Using RsyncUI to synchronize data to a remote server requires what is called *passwordless login by ssh-key*.
It is not possible to supply a user login and password in the progress of synchronizing data in RsyncUI.

{{< alert >}}

Before commencing to use RsyncUI to synchronize data to a *remote server*, it is required to setup and enable passwordless login by ssh-key. It is recommended to setup and enable this by terminal before using RsyncUI.

{{< /alert >}}

There are two ways to setup and enable passwordless login by ssh-key, either by default value or by your own value. 
Default value for RSA based ssh-key is `~/.ssh/id_rsa` and portnumber `22`.

{{< alert >}}

If using default ssh-key value and no info about ssh-key in RsyncUI, RsyncUI add the parameter `-e ssh` to the rsync command to ensure data is
tunneled and encrypted by ssh. The above applies if destination is a remote server only and to restore data as well.

{{< /alert >}}

### Important

{{< alert >}}

If default values for ssh-key is used, it is *not* required to add information about ssh-key to RsyncUI.
It is only necessary to add information if you create your own ssh-keypath and identityfile.

{{< /alert >}}

The ssh parameter within the rsync command is, if ssh-keypath and identityfile is set by the user:

```bash
-e  "ssh -i ~/.ssh_keypath/identityfile -p NN"
```
where `-i ~/.ssh_keypath/identityfile` is the ssh-keypath and identityfile and `-p NN` is the port number ssh communicates through, default port 22.

### Example

As an example I have created ssh-key for rsync only. The ssh-keypath is set to `~/.ssh_rsyncosx/` and the RSA based identityfile is `rsyncosx`, default port `22`.
The rsync command to synchronize my Documents catalog is, set by RsyncUI, to my Raspberry Pi server is:

```bash
/opt/homebrew/bin/rsync --archive --verbose --compress --delete \
-e  "ssh -i ~/.ssh_rsyncosx/rsyncosx -p 22" --stats \
/Users/thomas/Documents/ thomas@raspberrypi:/backups/Documents/
```

To use my own ssh-key and ssh-keypath data, the following is added to RsyncUI in settings.

{{< figure src="/images/usersettings/ssh.png" alt="" position="center" style="border-radius: 8px;" >}}

### Ssh-keypath and identityfile

How to set ssh-keypath and identityfile in [the user configuration](/docs/sshsettings/).

If global ssh parameters are set, it applies to all configurations. It is possible to set other ssh values on each task. There is a check of the ssh-keypath and identityfile. When enabling user selected ssh-keypath and identityfile please make sure it is in compliance with:

```bash
~/.mynewsshcatalog/mynewkey
```

The prefix has to be `~` followed by a `/`. RsyncUI will verify that the ssh-keypath is prefix by `~` and at least two `/` before saving the new ssh-keypath.

### Tools used

The following ssh tools are used: `ssh-keygen` and `ssh-copy-id`. RsyncUI only assist in setting up RSA based key.

The ssh functions assist in two methods:

- private and public ssh key pair based upon default ssh values for RSA based key `~/.ssh/id_rsa`
- private and public ssh key pair based upon user selected values as `~/.ssh_rsyncosx/rsyncosx`

If creating a new public ssh key pair based upon default ssh values for RSA based key, RsyncUI does not add any parameters to the rsync command because this is default values. Ssh parameters to the rsync command is only added if the second method is chosen.

The following commands for creating a new, alternative private and public ssh-key pair:

```bash
cd
mkdir .ssh_rsyncosx
ssh-keygen -t rsa -N "" -f ~/.ssh_rsyncosx/rsyncosx
```

where

- `-t rsa ""` generates a RSA based key-pair
- `-N ""` sets no password
- and `~/.ssh_rsyncosx/rsyncosx` is set by the user

The following command copy the newly created public key to the server:

```bash
ssh-copy-id -i /Users/thomas/.ssh_rsyncosx/rsyncosx -p NN user@server
```

You can also setup the new ssh-keypath and identityfile in a terminal window and after setup add the new ssh-keypath and identityfile in Userconfig. RsyncUI will automatically enable it when added in user config.

The user can also apply local ssh-keypath and identityfile and ssh port, which rules the global settings, on each task.

Make sure that the new ssh catalog has the correct permissions. Open a terminal window and execute the following command.

```bash
chmod 700 ~/.ssh_rsyncosx
```
