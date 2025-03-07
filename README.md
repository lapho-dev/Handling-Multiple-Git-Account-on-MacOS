# Guide for Multiple Git Account on MacOS


## Prelude

When managing several git account with different ssh keys, you might struggle a bit with setting ssh keys, username and user email. 

This guide is aiming to provide complete precedures to set up ssh connection to git repository for **macOS** users.

For linux users, this guide would be mostly applicable.  
For windows users, commands would be different.  

## Table of Content

- [Basic SSH Connection](#setting-up-ssh-connection)                       -- Read from here if you are new
- [Multiple Git Account & SSH](#seting-up-multiple-ssh-keys--git-account)  -- Skip here if you already know git ssh
- [Directory Tree - example](#example-directory-tree)                              -- Useful for double checking


## Setting Up SSH Connection

### 1.1 Generating SSH keys

- open terminal 
- or Hold */Commnand + Space/* and type *terminal*

Copy and paste following command to generate ssh keys pairs.
```zsh
ssh-keygens -C "{your_email.com}" -f ~/.ssh/{ssh_key_name}
```

*Replace {your_email.com} with your email or a comment.  
*Replace {ssh_key_name} with a desired name for the key.  
*Note that ssh_key_name is needed later for identifying corresponding account.  

**Press y & enter pass-phrase if needed**

- In ~/.ssh folder, you will find two keys namely:
    1. *ssh_key_name*
    2. *ssh_key_name.pub*
- Or enter following command:
```zsh
cd .ssh/ && ls
```

### 1.2 Adding SSH Key to Git Account

- Copy *ssh_key_name.pub*
- Or user following command and copy
```zsh
cd .ssh/ && ls
```
```zsh
cat ssh_key_name.pub
```

__Paste *ssh_key_name.pub* to the Git Account__

In **git account** -> open **Setting** -> **SSH Keys** -> **Add New key**   
-> paste *ssh_key_name.pub*

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

To authenticate succesfully in each git command, the following must correct for each git account:
| Your Computer | Git Account | description |
| -- | -- | -- |
| *ssh_key_name* | *ssh_key_name.pub* | public and private keys must be a pair |
| email & name | email & name | must be the same

To achieve that, we first need to create several ssh keys.

### 2.2 SSH Configuration

Repeat the steps in [1.1](#11-generating-ssh-keys) & [1.2](#12-adding-ssh-key-to-git-account) to generate ssh keys with different names.

By default, the computer does not know which key to use each time.  
Therefore, we need to create a config file for ssh:
```zsh
cd ~/.ssh && nano config
```
*if this is your first time doing this, the config file should be blank.

Paste the following template to the file:
```config
Host ssh_key_name
    HostName github.com
    User git
    IdentityFile ~/.ssh/ssh_key_name
    IdentitiesOnly yes
```
*Replace *ssh_key_name* with the actual name.
*Replace github.com with your desired git server, e.g. gitlab.com or your work server

*For simplicity, I recommand keeping the host name and key name the same.

For each ssh key you generate, insert another Host Alias in ~/.ssh/config.

###### Your ~/.ssh/config file should look something like this:
```config
Host dev-github
    HostName github.com
    User git
    IdentityFile ~/.ssh/dev-github
    IdentitiesOnly yes
    
Host dev-gitlab
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/dev-gitlab
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
git clone git@{host name}:git-repo-ssh-url
```

### 2.3 Configure global .gitconfig

Now we have to configure .gitconfig file.

This is crucial to let **git** command know that which user is using.

Head to global **.gitconfig** file:
```zsh
nano .gitconfig
```
Insert or modify field **[user]** to the file, and add name and email:
```.gitconfig
[user]
    name = your_git_username
    email = your_email@email.com
```
This serves as a global name & email for every git command use in all directory.
  
Clearly, we want to differentiate each account in different directory.  
To make this possible, append the following:
```.gitconfig
[includeIf "gitdir:~/git-project/dev/"]
    path = ~/git-project/dev/.gitconfig
```
This allows git command to use local .gitconfig file in all the directory inside ~/git-project/dev/...

*Replace _git-project/dev/_ with your actual directory for your git project.

Repeat until you've covered all of your directory.

###### Your ~/.gitconfig file should look something like this:
```.gitconfig
[user]
    name = your_global_username
    email = your_global_email@email.com
[includeIf "gitdir:~/dev"]
    path = ~/dev/.gitconfig
[includeIf "gitdir:~/git-project/personal"]
    path = ~/git-project/personal/.gitconfig
[includeIf "gitdir:~/work/work-git"]
    path = ~/work/work-git/.gitconfig
```

### 2.4 Configure Local .gitconfig

To create a local git config for each git account (name & email & ssh key), head to the path you previous entered in *~/.gitconfig* .

For example:
```zsh
cd ~/dev
```

In [previous step](#your-gitconfig-file-should-look-something-like-this), we point every git command inside *~/dev/...* to use *~/dev/.gitconfig* .   
Now, we could simply create a *.gitconfig* file for local git config for *~/dev/...* :
```zsh
nano .gitconfig
```
###### In *.gitconfig*, paste the following content depending on your actual user and [Host Name](#your-sshconfig-file-should-look-something-like-this) previous defined.
```
[user]
    name = your_local_username
    email = your_local_email@email.com
[url "git@dev-github:"]
    insteadOf = git@github.com:
[url "git@dev-gitlab:"]
    insteadOf = git@gitlab.com:
```
This replaces git@github.com with the host name in *~/.ssh/config* file, which means the correct ssh keys is used.  
Local user is also correctly set up.

Repeat [the steps](#24-configure-local-gitconfig) to cover all the git folder you have.

The no. of local *.gitconfig* should be the same as no. of [includeIf ...]... in global *.gitconfig*

## Summary

Now that you have everything set up, you don't need to bother switching account and ssh keys.   
**Everything is Automatic!**

[Test](#additionally-you-can-test-the-connection-by-following) the connection in different directory. You will see a response in different user.

If you encounter any authentication error, permission denied, either you don't have network access to the server or your credentials listed [here](#21-knowing-authentication-requirements) aren't matched.

### Example Directory Tree

You should have a directory tree looking like this :    
~/    
| -- Desktop  
| -- Downloads..., etc  
| -- [.gitconfig](#your-gitconfig-file-should-look-something-like-this)  
| -- .ssh   
   | -- [config](#your-sshconfig-file-should-look-something-like-this)   
   | --     
| -- dev    
   | -- [.gitconfig](#in-gitconfig-paste-the-following-content-depending-on-your-actual-user-and-host-name-previous-defined)   
   | -- AI-model   
      | -- .git   
      | -- README.md   
        ...   
| -- git-project   
   | -- personal   
      | -- [.gitconfig](#in-gitconfig-paste-the-following-content-depending-on-your-actual-user-and-host-name-previous-defined)    
      | -- personal-webapp-repo   
         | -- .git   
         | -- README.md   
           ...   
| -- work   
   | -- boring-work-files    
   | -- work-git   
      | -- [.gitconfig](#in-gitconfig-paste-the-following-content-depending-on-your-actual-user-and-host-name-previous-defined)    
      | -- work-git-repos   
         | -- .git   
         | -- README.md    
           ...    
        ...    
  ...    
   



## Thanks
[Generate SSH Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
[Git Documentation](https://git-scm.com/doc)
[Git SSH Connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

Please let me know if there is any issue with this approach. All the best!!