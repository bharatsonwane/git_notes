### Git Notes

# How To Work With Multiple Github Accounts on a single Machine

Let suppose I have two github accounts, **https:/<span></span>/github.com<span></span>/bharatOffice** and **https:/<span></span>/github.com<span></span>/bharatPersonal**. Now i want to setup my mac to easily talk to both the github accounts.

> NOTE: This logic can be extended to more than two accounts also. :)

The setup can be done in 5 easy steps:
## Steps:
- [Step 1](#step-1) : Create SSH keys for all accounts
- [Step 2](#step-2) : Add SSH keys to SSH Agent
- [Step 3](#step-4) : Create a Config File and Make Host Entries
- [Step 4](#step-3) : Add SSH public key to the Github
- [Step 5](#step-5) : Cloning GitHub repositories using different accounts

<br>

## Step 1

First make sure your current directory is your **.ssh** folder (navigate to the .ssh directory).
```sh
     cd ~/.ssh
```

if the .ssh directory does not exist, you can create & navigate to it by running:
```sh
     mkdir ~/.ssh
     cd ~/.ssh
```
complete path will be like
```sh
     C:\Users\<YourWindowsUsername>\.ssh 
     e.g. C:\Users\sonwanebr\.ssh\
```

list down file in  **.ssh** folder (mac/linux):
```sh
     ls ~/.ssh
```
### A) Create SSH keys for all accounts

Syntax for generating unique ssh key for an account with email & file name:
```sh
      ssh-keygen -t rsa -C "yourEmailAddress" -f "githubUsername"
```
here,

**-C** stands for comment to help identify your ssh key

**-f** stands for the file name where your ssh key get saved


#### Now generating SSH keys for my two accounts
```sh
     ssh-keygen -t rsa -C "myOfficeEmail@gmail.com" -f "bharatOffice"
     ssh-keygen -t rsa -C "myPersonalEmail@gmail.com" -f "bharatPersonal"
```

Notice here **bharatOffice** and **bharatPersonal** are the username of my github accounts corresponding to **myOfficeEmail<span></span>@gmail.com** and **myPersonalEmail<span></span>@gmail.com** email ids respectively.

After entering the command the terminal will ask for passphrase, leave it empty and proceed.


> Now after adding keys , in your .ssh folder, a public key and a private will get generated.

>The public key will have an extension __.pub__ and private key will be there without any extension both having same name which you have passed after __-f__ option in the above command. (in my case __bharatOffice__ and __bharatPersonal__)


<br>

## Step 2
### Create a Config File and Make Host Entries

The **~/.ssh/config** file allows us specify many config options for SSH.

If **config** file not already exists then create one (make sure you are in **~/.ssh** directory)

```sh
     cd ~/.ssh/
     touch config 
```

The commands below opens config in your default editor....Likely TextEdit, VS Code.
```sh
     open config
```
Now we need to add these lines to the file, each block corresponding to each account we created earlier.
```sh
     #syntax ==> 
     Host github.com-githubUsername
          HostName github.com
          User git
          IdentityFile ~/.ssh/githubUsername

     #bharatOffice account
     Host github.com-bharatOffice
          HostName github.com
          User git
          IdentityFile ~/.ssh/bharatOffice

     #bharatPersonal account
     Host github.com-bharatPersonal
          HostName github.com
          User git
          IdentityFile ~/.ssh/bharatPersonal
```

<br>

## Step 3
### Add SSH keys to SSH Agent
#### Start SSH Agent
Run PowerShell as Administrator and execute:
```sh
    Get-Service ssh-agent | Set-Service -StartupType Automatic
    Start-Service ssh-agent
    Get-Service ssh-agent
```

Optional: First-time trust host:
```sh
     ssh -o StrictHostKeyChecking=no git@github.com
```

Now we have the keys but it cannot be used until we add them to the SSH Agent.
```sh
     #syntax
     ssh-add -K ~/.ssh/githubUsername
     #e.g.
     ssh-add -K ~/.ssh/bharatOffice
     ssh-add -K ~/.ssh/bharatPersonal
```

You can read more about adding keys to SSH Agent [here.](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

<br>

## Step 4
### Add SSH public key to the Github
For the next step we need to add our public key (that we have generated in our previous step) and add it to corresponding github accounts.

For doing this we need to:

__1. Copy the public key__

     We can copy the public key either by opening the githubUsername.pub file in vim and then copying the content of it.
```sh
     vim ~/.ssh/bharatOffice.pub
     vim ~/.ssh/bharatPersonal.pub
```

<p align="center">OR

We can directly copy the content of the public key file in the clipboard.

```sh
     pbcopy < ~/.ssh/bharatOffice.pub
     pbcopy < ~/.ssh/bharatPersonal.pub
```   


__2. Paste the public key on Github__

* Sign in to Github Account
* Goto **Settings** > **SSH and GPG keys** > **New SSH Key**
* Paste your copied public key and give it a Title of your choice.

___OR___

* Sign in to Github 
* Paste this link in your browser (https://github.com/settings/keys) or click [here](https://github.com/settings/keys)
* Click on **New SSH Key** and paste your copied key.

<br>


## Step 5
### Cloning GitHub repositories using different accounts

So we are done with our setups and now its time to see it in action. We will clone a repository using one of the account we have added.

Make a new project folder where you want to clone your repository and go to that directory from your terminal.

For Example:
I am making a repository on my personal github account and naming it **TestRepo**
Now for cloning the repo use the below command:
 ```sh
     git clone git@github.com-{githubUsername}:{owner-user-name}/{the-repo-name}.git

     [e.g.] git clone git@github.com-bharatPersonal:bharatPersonal/TestRepo.git
 ```

 <br>

 ## Finally

From now on, to ensure that our commits and pushes from each repository on the system uses the correct GitHub user — we will have to configure **user.email** and **user.name** in every repository freshly cloned or existing before.

To do this use the following commands.

```sh
     git config user.email "myOfficeEmail@gmail.com"
     git config user.name "Bharat Sonwane"
     
     git config user.email "myPersonalEmail@gmail.com"
     git config user.name "Bharat Sonwane"
```
Pick the correct pair for your repository accordingly.


To push or pull to the correct account we need to add the remote origin to the project
```sh
     git remote add origin git@github.com-bharatPersonal:bharatPersonal
     
     git remote add origin git@github.com-bharatOffice:bharatOffice
```

Now you can use:
```sh
     git push
     
     git pull
```

## Step 6
### Change Remote URL for Cloned Repositories

If you have already cloned repositories using HTTPS, you can change the remote URL to use SSH for a smoother experience. First, navigate to the repository directory, then run the following commands to update the remote URL:

```sh
     git remote -v # To check current URLs
     git remote remove origin # Remove existed URLs
     git remote add origin git@github.com-{githubUsername}:{owner-user-name}/{the-repo-name}.git # Add SSH URL        
```
