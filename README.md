# Guide for Multiple Git Account on MacOS


## Prelude

When managing several git account with different ssh keys, you might struggle a bit with setting ssh keys, username and user email. 

This guide is aiming to provide complete precedures to set up ssh connection to git repository for **macOS** users.

For linux users, this guide would be mostly applicable.
For windows users, commands would be different.

## Table of Content

- [Basic SSH Connection](#setting-up-ssh-connection)  
- [Multiple Git Account & SSH](#seting-up-multiple-ssh-keys--git-account)  -- Skip here if you already know


## Setting Up SSH Connection

### 1.1 Generating SSH keys

- open terminal 
- or Hold */Commnand + Space/* and type */terminal/*

Copy and paste following command to generate ssh keys pairs
```
ssh-keygens -t ed25519 -C "{your_email.com}" -f ~/.ssh/{ssh_key_name}
```

*Replace {your_email.com} with your email or a comment

*Replace {ssh_key_name} with a desired name for the key

*Note that ssh_key_name is needed later for identifying corresponding account.

**Press y & enter pass-phrase if needed**

- In ~/.ssh folder, you will find two keys namely
1. */ssh_key_name/*
2. */ssh_key_name.pub/*
- Or enter following command
```
cd .ssh/ && ls
```

### 1.2 Adding SSH Key to Git Account

- Copy */ssh_key_name.pub/*
- Or user following command and copy
```
cd .ssh/ && ls
```
```
cat ssh_key_name.pub
```

**Add the */ssh_key_name.pub/* to the Git Account**
In **git account** -> open **Setting** -> **SSH Keys** -> **Add New key** -> paste */ssh_key_name.pub/*

Save and you have your ssh connected

### 1.3 Test and Enjoy

You can easily clone repository with
```
git clone git@github.com:lapho-dev/Handling-Multiple-Git-Account-on-MacOS.git
```
other git commands can be found in [git documentation](https://git-scm.com/docs).

#### Additionally, you can test the connection by following
```
ssh -T {your repo ssh link}
```
for exmaple 
```
ssh -T git@github.com:lapho-dev/Handling-Multiple-Git-Account-on-MacOS.git
```

## Seting Up Multiple SSH keys & Git Account

Now that you have single connection set up, dealing with multiple git account and ssh keys may require further settings.

### 2.1 Knowing Authentication requirements

To authenticate succesfully in each git command, the following must be present for each account
| Your Computer | Git Account | description |
| -- | -- | -- |
| */ssh_key_name/* | */ssh_key_name.pub/* | ssh key are paired |
| email | email | same
| username | email | same |

To achieve that, we first need to create several ssh keys.

### 2.2 SSH Configuration

Repeat the steps in [1.1](#11-generating-ssh-keys) & [1.2](#12-adding-ssh-key-to-git-account) with different names.

By default, the computer does not know which key to use each time.
Therefore, we need to create a config file for ssh
```
cd ~/.ssh && nano config
```
* if this is your first time doing this, the config file should be blank

Paste the following template to the file
```
Host ssh_key_name
    HostName github.com
    User git
    IdentityFile ~/.ssh/ssh_key_name
    IdentitiesOnly yes
```
For each ssh key you generate, 



## Thanks