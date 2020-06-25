---
layout: post
title: 'How to Exercise SSH'
permalink: 'how-to-exercise-ssh'
category: notes
---

Secure Shell (SSH) is a remote access utility used for managing other nodes on a network. This blog post describes how to configure and exercise various SSH features.

## Table of Contents
* [Understanding Different SSH Files](#understanding-different-ssh-files)
* [How to Configure Public Key Authentication](#how-to-configre-public-key-authentication)
* [How to Copy Files Securely](#how-to-copy-files-securely)
* [How to Encrypt a File Using SSH Key Pairs](#how-to-encrypt-a-file-using-ssh-key-pairs)
* [How to Create an Encrypted Tunnel](#how-to-create-an-encrypted-tunnel)
* [How to Setup Single Sign-On Access](#how-to-setup-single-signon-access)
* [How to Display Login Banners](#how-to-display-login-banners)

## Understanding Different SSH Files
There are a handful of important SSH files to be aware of between the main, system-wide directory and the directory every end-user is allocated. Below are simple descriptions of said files.

**SSH Server Configuration**  
Edit this file to enable, disable, or customize various settings for the local SSH server (a.k.a daemon). Some examples of parameters you might change here are the logical port SSH will listen on, whether or not `root` can login remotely, and authentication methods.

```
/etc/ssh/sshd_config
```

## How to Configure Public Key Authentication
### Description
Asymmetric encryption uses two keys: one is called a Private key while the other is called a Public key. Whatever the Private key encrypts, the Public key can decrypt (and vice versa). There are several use-cases for choosing Asymmetric Encryption over other cryptographic methods. Take Public key Authentication for example. Using a Private & Public key pair for authentication, over a password, (1) reduces the risk of brute force attacks, (2) increases the number of access requirements, and (3) enables non-repudiation (proof a subject performed an action against an object).

### General Steps
1. Generate a key pair on the remote node
2. Generate a key pair on the local node
3. Copy your Public key onto the remote node
4. Login to the remote node
5. Disable password-based authentication

###Hands-on Exercise
**Scenario**
`thanos` wants a more secure method of accessing a remote server called `earth616` from his computer called `titan`.

**Step 1: Generate a key pair on the remote node**
Generate a SSH key pair on `earth616`.
```bash
ssh-keygen
```
**Step 2: Generate a key pair on the local node**
Generate a SSH key pair on titan.
```bash
ssh-keygen
```

**Step 3: Copy your Public Key onto the remote node**
From `titan`, `thanos` needs to copy his SSH Public key onto `earth616`.
```bash
ssh-copy-id thanos@earth616
```

**Step 4: Login to the remote node**
Login to `earth616`.
```bash
ssh thanos@earth616
```

**Step 5: Disable password-based authentication**
Open the SSH server configuration file on `earth616`.
```bash
sudo vim /etc/ssh/sshd_config
```
Add the line below
```bash
PasswordAuthentication no
```

**Step 6: Reload the daemon**
```bash
sudo systemctl reload sshd
```

## How to Copy Files Securely

## How to Encrypt a File Using SSH Key Pairs

## How to Create an Encrypted Tunnel

## How to Setup Single Sign-On Access

## How to Display Login Banners
