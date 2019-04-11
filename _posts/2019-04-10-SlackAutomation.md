---
layout: post
title: Slack Automation With Jenkins
subtitle: Automate slack messages with Jenkins builds
tags: [Slack]
comments: true
---

![Tech](http://inn.spb.ru/images/100/DSC100139148.jpg)

So here goes my first tutorial blog. Recently I had the idea to automate sending slack messages once a week without my intervation. I figured it would be great if I could laissez-faire my Slack account and let my automation work wonders.

### __Prerequisites__

In order for this setup to work there are a few prereqesuites that need to be met. I will go ahead and lay them out below and then walk you through a step by step process later on in this article.

* An Ubuntu 18.04 server hosted on [Digital Ocean](https://www.digitalocean.com/products/droplets/)
* PowerShell integration with [Slack's REST API](https://api.slack.com/)
* [A Jenkins server](https://jenkins.io/)
* A [PowerShell Core](https://github.com/PowerShell/PowerShell) installation on the Ubuntu 18.04 server.

### Setup Your Ubuntu 18.04 Server

Digital Ocean has great resources, tutorials, and artiles for diving right into different subjects you may be interested in. I would recommend checking some of them out. For this setup I used an Ubuntu 18.04 server with about 25 GB of disk space and 1 GB of memory. Below I will outline my server setup.

#### Initial Server Setup

If you haven't setup a Linux server of any sort before here is a good article to get you started https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04.

The first thing we want to do once our server is provisioned is setup SSH. If you are using Windows you can either install PuTTY or a Linux Distro from the Windows Store. For this tutorial I used Ubuntu, but other distribution should work. If you are on a Mac you can just use terminal to SSH.

We will want to generate an SSH keypair that we can use to login to our server.

```bash
ssh-keygen -t rsa
```
The entire process to follow should look like this
```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/home/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/demo/.ssh/id_rsa.
Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```
The public key is now located in `/home/demo/.ssh/id_rsa.pub`. The private key (identification) is now located in `/home/demo/.ssh/id_rsa`.

Lastly we will need to copy the SSH key over to our Ubuntu server.
```bash
ssh-copy-id demo@198.51.100.0
```

You should now be able to SSH into your Ubuntu server.











And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
