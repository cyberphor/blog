---
layout: post
title: 'How to Exercise SSH'
permalink: 'how-to-exercise-ssh'
category: posts
---

Secure Shell (SSH) is a remote access utility used for managing other nodes on a network. This blog post describes how to configure and exercise various SSH features.

## Table of Contents
* Understanding Different SSH Files
* How to Configure Public Key Authentication
* How to Copy Files Securely
* How to Encrypt a File Using SSH Key Pairs
* How to Create an Encrypted Tunnel
* How to Setup Single Sign-On Access
* How to Display Login Banners

## Understanding Different SSH Files
There are a handful of important SSH files to be aware of between the main, system-wide directory and the directory every end-user is allocated. Below are simple descriptions of said files.

**SSH Server Configuration**  
Edit this file to enable, disable, or customize various settings for the local SSH server (a.k.a daemon). Some examples of parameters you might change here are the logical port SSH will listen on, whether or not `root` can login remotely, and authentication methods.

```
/etc/ssh/sshd_config
```

## How to Configure Public Key Authentication

## How to Copy Files Securely

## How to Encrypt a File Using SSH Key Pairs

## How to Create an Encrypted Tunnel

## How to Setup Single Sign-On Access

## How to Display Login Banners
