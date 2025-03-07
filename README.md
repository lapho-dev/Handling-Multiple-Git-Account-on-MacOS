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
```zsh
ssh-keygens -C "{your_email.com}" -f ~/.ssh/{ssh_key_name}
```

*Replace {your_email.com} with your email or a comment  
*Replace {ssh_key_name} with a desired name for the key  
*Note that ssh_key_name is needed later for identifying corresponding account.  

**Press y & enter pass-phrase if needed**

- In ~/.ssh folder, you will find two keys namely
    1. */ssh_key_name/*
    2. */ssh_key_name.pub/*
- Or enter following command
```zsh
cd .ssh/ && ls
```

### 1.2 Adding SSH Key to Git Account

- Copy */ssh_key_name.pub/*
- Or user following command and copy
```zsh
cd .ssh/ && ls
```
```zsh
cat ssh_key_name.pub
```

**Add the */ssh_key_name.pub/* to the Git Account**

In **git account** -> open **Setting** -> **SSH Keys** -> **Add New key** -> paste */ssh_key_name.pub/*

**Save** and you have your ssh connected.

### 1.3 Test and Enjoy

You can easily clone repository with
```zsh
git clone git@github.com:lapho-dev/Handling-Multiple-Git-Account-on-MacOS.git
```
Other git commands can be found in [git documentation](https://git-scm.com/docs).

#### Additionally, you can test the connection by following
```zsh
ssh -T {your repo ssh link}
```
for exmaple 
```zsh
ssh -T git@github.com:lapho-dev/Handling-Multiple-Git-Account-on-MacOS.git
```

## Seting Up Multiple SSH keys & Git Account

Now that you have single connection set up, dealing with multiple git account and ssh keys may require further settings.

### 2.1 Knowing Authentication requirements

To authenticate succesfully in each git command, the following must correct for each git account
| Your Computer | Git Account | description |
| -- | -- | -- |
| /*ssh_key_name*/ | /*ssh_key_name.pub*/ | public and private keys must be a pair |
| email & name | email & name | must be the same

To achieve that, we first need to create several ssh keys.

### 2.2 SSH Configuration

Repeat the steps in [1.1](#11-generating-ssh-keys) & [1.2](#12-adding-ssh-key-to-git-account) to generate ssh keys with different names.

By default, the computer does not know which key to use each time.  
Therefore, we need to create a config file for ssh
```zsh
cd ~/.ssh && nano config
```
*if this is your first time doing this, the config file should be blank

Paste the following template to the file
```zsh
Host ssh_key_name
    HostName github.com
    User git
    IdentityFile ~/.ssh/ssh_key_name
    IdentitiesOnly yes
```
*Replace */ssh_key_name/* with the actual name.
*Replace github.com with your desired git server, e.g. gitlab.com or your work server

*For simplicity, I recommand keeping the host name and key name the same

For each ssh key you generate, insert another Host Alias in ~/.ssh/config.

Your ~/.ssh/config file should look something like this:
```config
Host dev
    HostName github.com
    User git
    IdentityFile ~/.ssh/dev
    IdentitiesOnly yes

Host personal
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/personal
    IdentitiesOnly yes
    
Host git.company.com
    IdentityFile ~/.ssh/work
    IdentitiesOnly yes
```
The correct ssh key would be used once you have used the corresponding host name, namely
```zsh
git clone git@{host name}:lapho-dev/Handling-Multiple-Git-Account-on-MacOS.git
```

### 2.3 Configure global .gitconfig

Now we have to configure .gitconfig file.

This is crucial to let **git** command know that which user is using.

Head to global .gitconfig file
```zsh
nano .gitconfig
```
Insert or modify field [user] to the file, add name and email
```.gitconfig
[user]
        name = your_git_username
        email = your_email@email.com
```
This serves as a global name & email for every git command use in all directory.
  
Clearly, we want to differentiate each account in different directory.  
To make this possible, append the following
```.gitconfig
[includeIf "gitdir:~/git-project/lyh218/"]
    path = ~/git-project//.gitconfig
```


## Thanks