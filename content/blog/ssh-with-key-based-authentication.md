+++
title = "How to set up SSH with key-based authentication"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2018-10-12T22:48:00+02:00
lastmod = 2018-10-12T23:37:51+02:00
tags = ["ssh", "config"]
categories = ["blog"]
draft = false
weight = 2001
+++

SSH is a service used to connect remotely from one machine to
another. It is used on pretty much all operating systems except
Windows.

In this example, I use two machines. The **workstation** is the
machine we are connecting from and the **server** is the machine we
are connecting to.

First off, make sure SSH (**ssh**, **ssh-keygen**) is installed. Since I
use Arch Linux it's already there by default. A quick search yields
[this](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&ved=2ahUKEwj3ive2zYHeAhVBtosKHWsVBCEQFjABegQIBhAE&url=https%3A%2F%2Fwww.simplified.guide%2Fubuntu%2Finstall-ssh-server&usg=AOvVaw24xjG875GC72ufNqHsQV5z) site with instructions for Ubuntu.


## On the workstation {#on-the-workstation}

Generate a long enough keypair. Keypair - a pair of keys. A public
key and a private key. The two keys are actually two long numbers
working nicely together for encryption and decryption.

I use a key length of 4096 bits (at the time of writing this) and a
key type of RSA (meaning we use the RSA algorithm for encryption and
decryption). By default, **ssh-keygen** asks to save the keys as
**~/.ssh/id\_rsa** for the private key, and **~/.ssh/id\_rsa.pub** for the
public key.

```bash
ssh-keygen -t rsa -b 4096
```

Create a new file in  **~/.ssh/config** with the following content:

```bash
Host <HOST-NICKNAME>
     Hostname <IP-OR-HOSTNAME>
     Port <PORT>
     User <USERNAME>
     IdentityFile <PATH-TO-GENERATED-PRIVATE-KEY>
```

To transfer your public key on to the server,
use **$ ssh-copy-id <HOST-NICKNAME>**.


## On the server {#on-the-server}

Edit the file **/etc/ssh/sshd\_config** so it contains these two lines.

```bash
RSAAuthentication yes
PubkeyAuthentication yes
```

For various reasons I have also changed the following settings for
the ssh daemon:

```bash
PermitRootLogin no
PasswordAuthentication no
X11Forwarding yes
PrintMotd no
```

Why on earth did I set these?

**PermitRootLogin no**

Makes sure no one can access the server via the root account. It is
highly unlikely (impossible) that anyone would brute-force this
since the root user on my machine has a really long randomly
generated password. Also this line is made redundant by the
following line, since my root user don't have any keys generated. I
have no idea why I set this in the first place.

**PasswordAuthentication no**

Make sure you have already copied your public key from a
workstation here if you decide to use my additional settings, since
you no longer will be able to ssh with a password.

**X11Forwarding yes**

Allows me to run graphical programs from the server on my
workstation. Pretty darn cool feature. Use **ssh <HOST> -X** or
**ssh <HOST> -Y** from the workstation.

**PrintMotd**

Motd - message of the day. I feel like I can live without it, but it
can be really useful. Like listing if any upgrades are available or
if any errors on some services need my immediate attention.

Also, do not forget to reload the sshd config to put the new
settings in effect. Since I have a Raspberry Pi running some version
of Raspbian, I use **service ssh reload** to do just that.
