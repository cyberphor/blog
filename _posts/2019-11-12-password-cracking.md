---
layout: post
title: 'Password Cracking'
category: posts
subcategory: 'vulnerability-management'
---

## Table of Contents
* [Hashes used in Linux (with no salt added)](#hashes-used-linux-with-no-salt-added)
* [Hashes used in Windows (with no salt added)](#hashes-used-in-windows-with-no-salt-added)
* [Hashes used in Linux (with salt added automatically)](#hashes-used-in-linux-with-salt-added-automatically)
* [MD5 hash with salt](#md5-hash-with-salt)
* [SHA512 hash with salt](#sha512-hash-with-salt)
* [References](#references)

## Hashes used in Linux (with no salt added)
```bash
# md5 hash w/no salt
echo -n "password" | md5sum | cut -d"-" -f1 > crackme_md5_nosalt.txt
john --format=raw-md5 crackme_md5_nosalt.txt

# sha256 hash w/no salt
echo -n "password" | sha256sum | cut -d"-" -f1 > crackme_sha256_nosalt.txt
john --format=raw-sha256 crackme_sha256_nosalt.txt

# sha512 hash w/no salt
echo -n "password" | sha512sum | cut -d"-" -f1 > crackme_sha512_nosalt.txt
john --format=raw-sha512 crackme_sha512_nosalt.txt
```

## Hashes used in Windows (with no salt added)
Open a text-editor
```bash
vim nt_hash_generator.py 
```

Copy & paste the code below
```python
#!/usr/bin/env python3
import hashlib

def generate_nt_hash():
    pw_raw = input("Password: ")
    pw_encoded = pw_raw.encode("utf-16le")
    pw_hashed = hashlib.new("md4", pw_encoded)
    pw = pw_hashed.hexdigest()
    print(pw)

generate_nt_hash()
```

Generate an NT hash
```bash
python3 nt_hash_generator.py 
```

Crack the hash
```bash
john --format=nt crackme_nt_nosalt.txt 
```

## Hashes used in Linux (with salt added automatically)
### MD5 hash with salt
Open this file
```bash
vim /etc/pam.d/common-password
```

Edit it
```bash
# change this
password	[success=1 default=ignore]	pam_unix.so obscure sha256

# to this
password	[success=1 default=ignore]	pam_unix.so obscure md5
```

Create an account 
```bash
useradd victim1
passwd victim1
```

Crack it
```bash
tail -n1 /etc/passwd > passwd_copy
tail -n1 /etc/shadow > shadow_copy
unshadow ./passwd_copy ./shadow_copy > ./crackme_md5_salt.txt
john ./crackme_md5_salt.txt    
```

## SHA256 hash with salt
Open this file
```bash
vim /etc/pam.d/common-password
```

Edit it
```bash
# change this
password	[success=1 default=ignore]	pam_unix.so obscure md5

# to this
password	[success=1 default=ignore]	pam_unix.so obscure sha256
```

Create an account 
```bash
useradd victim2
passwd victim2
```

Crack it
```bash
tail -n1 /etc/passwd > passwd_copy
tail -n1 /etc/shadow > shadow_copy
unshadow ./passwd_copy ./shadow_copy > ./crackme_sha256_salt.txt
john ./crackme_sha256_salt.txt    
```

## SHA512 hash with salt
Open this file
```bash
vim /etc/pam.d/common-password
```

Edit it
```bash
# change this
password	[success=1 default=ignore]	pam_unix.so obscure sha256

# to this
password	[success=1 default=ignore]	pam_unix.so obscure sha512
```

Create an account
```bash
useradd victim3
passwd victim3
```

Crack it
```bash
tail -n1 /etc/passwd > passwd_copy
tail -n1 /etc/shadow > shadow_copy
unshadow ./passwd_copy ./shadow_copy > ./crackme_sha512_salt.txt
john ./crackme_sha512_salt.txt    
```

Show hacked accounts and their passwords
```bash
john --show *.txt
```

## References
* [aychedee - "Understanding and generating the hash stored in /etc/shadow"](https://www.aychedee.com/2012/03/14/etc_shadow-password-hash-formats/)
* [Unix & Linux StackExchange: "Manually generate password for /etc/shadow"](https://unix.stackexchange.com/questions/81240/manually-generate-password-for-etc-shadow)
* [Unix & Linux StackExchange: "How to set default password algorithm to sha512 on Linux?"](https://unix.stackexchange.com/questions/196085/how-to-set-default-password-algorithm-to-sha512-on-linux)
* ["Password Hashes with Python"](https://samsclass.info/124/proj14/p7a-pw.htm)
