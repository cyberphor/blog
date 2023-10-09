---
layout: post
title: 'How to Clone a GitHub Repo Using SSH'
category: guides
permalink: 'clone-github-repo-using-ssh'
---

## Tasks
Tasks 1, 2, and 3 only have to be performed once (or whenever your SSH keys are no longer valid). 
1. [Generate an SSH Key Pair](#task-1)
2. [Setup SSH-Based Single Sign-On](#task-2)
3. [Add a SSH Public Key to GitHub](#task-3)
4. [Clone a GitHub Repo](#task-4)

## Generate an SSH Private/Public Key Pair
SSH key pairs can be created using a virtual machine hosted by a Cloud Service Provider (e.g., Microsoft Azure).  
**Step 1.** Generate an SSH key pair.  `-t` defines the key pair type. `-b` defines the key lengths (i.e., the number of bits). `-C` is used to include a comment.   
```
ssh-keygen -t rsa -b 4096 -C "for DevOps"
```

**Step 2.** Press "Enter" to save your private key to the default location (i.e., under `.ssh/id_rsa` within your home directory).  
  
**Step 3.** Enter a passphrase to encrypt the private key.  

## Setup SSH-Based Single Sign-On
Setting up SSH-based Single Sign-On (SSO) allows you to present your SSH private key automatically during authentication (e.g., when authenticating with GitHub). 

**Step 1.** Open your BASH configuration file using your favorite text-editor
```
vim .bashrc
```

**Step 2.** Append the commands below to your BASH configuration file (they will be executed every time you login). 
```
eval "$(ssh-agent -s)" # start the SSH authentication agent
ssh-add ~/.ssh/id_rsa  # add my SSH private key to the SSH authentication agent
```

## Add Your SSH Public Key to GitHub
**Step 1.** Print your SSH public key.
```
cat ~/.ssh/id_rsa.pub
```

**Step 2.** Highlight and copy your SSH public key.  

**Step 3.** Open a browser and login to GitHub.  

**Step 4.** Click your profile (top-right icon) and click "Settings."  

**Step 5.** Click "SSH and GPG keys" in the pane on the left-hand side.  

**Step 6.** Click "New SSH key."  

**Step 6.** Enter a "Title" (e.g., "for DevOps").  

**Step 7.** Click "Add SSH key" and go back to the CLI. 

**Step 8.** Reload your environment (to simulate a logoff/login and load your private key into memory). 
```
source .bashrc
```

## Clone a GitHub Repo
**Step 1.** Clone a GitHub repo. 
```
git clone git@github.com:cyberphor/deathlab &&\
cd deathlab
```
