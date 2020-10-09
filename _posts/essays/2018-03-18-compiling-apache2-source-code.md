---
layout: post
title: 'Compiling Apache2 Source Code'
category: posts
subcategory: 'vulnerability-management'
permalink: 'compiling-apache2-source-code'
---

I spent the better part of the weekend tinkering around, trying to learn how to secure an instance of Apache (httpd) web server. I ended up learning more about how to install Apache by compiling source code as well as how to create a service on a system using “Systemd.” To start, I created a Virtual Machine running the Linux-based OS, Debian 9 (x64bit). 

I downloaded the source code for Apache version 2.4.35 from the following URL:
```bash
wget http://www-eu.apache.org/dist/httpd/httpd-2.4.35.tar.bz2
```

I knew ahead of time I wanted to verify the authenticity of the code so I also downloaded the provided SHA256 checksum and PGP digital certificate:
```bash
wget https://www.apache.org/dist/httpd/httpd-2.4.35.tar.bz2.sha256 
wget https://www.apache.org/dist/httpd/httpd-2.4.35.tar.bz2.asc 
```

To verify the SHA256 checksum, I did the following:
```bash
cat httpd-2.4.35.tar.bz2.sha256
sha256sum httpd-2.4.35.tar.bz2
```

The first command sentence inadvertently shows the contents of the downloaded file (in other words, checksum for the downloaded source code). The second actually hashes the source code and then, displays a checksum (which was expectantly the same). 
Checking the integrity of the source code using its provided PGP digital certificate was a little bit more tedious:
```bash
gpg httpd-2.4.35.tar.bz2.asc 
apt-get install dirmngr 
gpg --keyserver pgpkeys.mit.edu --recv-key 995E35221AD84DFF 
gpg httpd-2.4.35.tar.bz2.asc 
```

The first command here “failed” and then, identified the RSA ID associated with the public key I needed to import (in order to correctly verify who signed the code). To explain, the PGP digital certificate is an encrypted hash (checksum) that can only be decrypted with a specific “public key.” Both keys (the original one that encrypted it, and the one can decrypt it) are associated with one of the Apache Development Team members. 

The second command downloaded a library required to successfully import the public key previously mentioned. Then, I imported the necessary key (using the RSA ID displayed as output from the first command). Finally, I tried again (this time successfully) decrypting the PGP digital certificate. 
Once I verified the authenticity of the source code, I decompressed and extracted it:
```bash
bzip2 -d httpd-2.4.35.tar.bz2
tar -xvf httpd-2.4.35.tar
```

Fun fact: “tar” is short for “tape archive,” a historical reference. 
Next, I changed directories to access the recently extracted code:
```bash
cd httpd-2.4.35
```

Then, I download the required Apache2 dependencies:
* Apache Portable Runtime (libapr1, libapr1-dbg, libapr1-dev)<br>
* Apache Portable Runtime Utility (libaprutil)<br>
* GNU C Compiler (gcc)<br>
* Perl-Compatible Regular Expressions (libpcre)

```bash
apt-get install libapr1 libapr1-dbg libapr1-dev libarutil* gcc libpcre*
```

NOTE: I believe it may be worth verifying the integrity of these files/libraries as well, but I chose not too since I was only trying to learn the basics today; also, you may end up installing libraries you might not need on your system by appending ‘*’ during your downloads (it made it much easier to identify/select what I needed though). 

Once all my dependencies were downloaded and installed, I executed the infamous `./configure` script, specifying a specific directory I wanted the code to be compiled within:
```
configure --prefix=/usr/local/apache
```

Then, I finally compiled the Apache source code and installed it:
```bash
make
make install
```

Before continuing, I recommend the following command, otherwise you’ll get an annoying but trivial error when you start your web server:
```bash
echo 'ServerName localhost' >> /usr/local/apache/conf/httpd.conf
```

For those curious, appending this setting to the main httpd configuration file specifies a local Fully Qualified Domain Name for your Apache instance. 
Nonetheless, I tested my Apache web server using the following two commands:
```bash
/usr/local/apache/bin/apachectl start
curl localhost:80
```

A less cool alternative to the second command is opening a web browser and using `http://localhost:80` to view the contents of `index.html` displayed by default. 
Also, instead of typing the first command every time you need your Apache web server, create a Service file in the `/etc/systemd/system/` directory. I used the following in my Service file:
```bash
[ Unit ]
Description=Apache HTTP Server
After=network.target

[ Service ]
Type=forking
ExecStart=/usr/local/apache/bin/apachectl start
ExecReload=/usr/local/apache/bin/apachectl graceful
ExecStop=/usr/local/apache/bin/apachectl stop
PrivateTmp=true
LimitNOFILE=infinity

[ Install ]
WantedBy=multi-user.target
```

After creating such a file, run the following commands to enable Apache to run as a service, starting at boot:
```bash
systemctl daemon-reload
systemctl enable apache2
systemctl start apache2
systemctl status apache2
```
That’s it! 
