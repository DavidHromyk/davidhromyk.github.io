---
layout: post
title: Slack Automation With Jenkins
subtitle: Automate slack messages with Jenkins builds
tags: [Slack]
comments: true
---

![Tech](http://inn.spb.ru/images/100/DSC100139148.jpg)

So here goes my first tutorial blog. Recently I had the idea to automate sending slack messages once a week without my intervation. I figured it would be great if I could laissez-faire my Slack account and let my automation work wonders.

## __Prerequisites__

In order for this setup to work there are a few prereqesuites that need to be met. I will go ahead and lay them out below and then walk you through a step by step process later on in this article.

* An Ubuntu 18.04 server hosted on [Digital Ocean](https://www.digitalocean.com/products/droplets/)
* PowerShell integration with [Slack's REST API](https://api.slack.com/)
* [A Jenkins server](https://jenkins.io/)
* A [PowerShell Core](https://github.com/PowerShell/PowerShell) installation on the Ubuntu 18.04 server.

## Setup Your Ubuntu 18.04 Server

Digital Ocean has great resources, tutorials, and artiles for diving right into different subjects you may be interested in. I would recommend checking some of them out. For this setup I used an Ubuntu 18.04 server with about 25 GB of disk space and 1 GB of memory. Below I will outline my server setup.

#### Initial Server Setup

If you haven't setup a Linux server of any sort before here is a good article to get you started setting up your [Ubuntu 18.04 server](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04).

#### Setup SSH

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

You may need to login to the server via the VNC that Digital Ocean provides and allow password authentication in order to copy over the public key. Make sure you also own the `.ssh` directory. Once that is complete you should be able to login via SSH into your server. Go ahead and get familiar with some basic [Linux commands](https://maker.pro/linux/tutorial/basic-linux-commands-for-beginners) once you are logged in if you haven't played around with Linux before.

## __Setup Your Jenkins Server__

Next we will install Jenkins on our Ubuntu server and turn it into a Jenkins build server. If you aren't familiar with Jenkins, Jenkins is an open source automation server written in Java. Jenkins helps to automate the non-human part of the software development process, with continuous integration and facilitating technical aspects of continuous delivery. If you would like to know more about Jenkins here is their [user documentation](https://jenkins.io/doc/) page.

#### Prerequisites

* An Ubuntu 18.04 server which we installed in the previous section.
* Java 8 installed, which we will outline below.

#### Installing Java 8

Java 8 is the current Long Term Support version and is still widely supported, though public maintenance ends in January 2019. To install OpenJDK 8, execute the following command:

```bash
$ sudo apt install openjdk-8-jdk
```

Verify that it is installed with:

```bash
$ java -version
```

You should then see the following output:

```bash
openjdk version "1.8.0_162"
OpenJDK Runtime Environment (build 1.8.0_162-8u162-b12-1-b12)
OpenJDK 64-Bit Server VM (build 25.162-b12, mixed mode)
```

#### Installing Jenkins

The version of Jenkins included with the default Ubuntu packages is often behind the latest available version from the project itself. To take advantage of the latest fixes and features, you can use the project-maintained packages to install Jenkins.

First we need to add the repository key to the system. I wasn't able to get this to work with Digital Ocean's VNC, but I was able to get it working with SSH from my client machine:

```bash
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```

When the key is added, the system will return OK. Next, append the Debian package repository address to the server's `sources.list`:

```
$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list
```

When both of these are completed we will need to update `apt` so that `apt` will use the new repository. Simply run:
```bash
$ sudo apt update 
```

The final step is to install Jenkins. Finally we made it! This can be done by running the following:

```bash
$ sudo apt install jenkins
```

You should now have Jenkins and all of its dependencies installed on your system. Next we will need to start the Jenkins server.

#### Starting Jenkins

Let's go ahead and start Jenkins using `systemctl`:

```bash
$ sudo systemctl start jenkins
```

If you want to get a better picture of the jenkins startup run:

```bash
$ sudo systemctl status jenkins
```

If everything went well, the beginning of the output should show that the service is active and configured to start at boot:

```bash
jenkins.service - LSB: Start Jenkins at boot time
Loaded: loaded (/etc/init.d/jenkins; generated)
Active: active (exited) since Mon 2018-07-09 17:22:08 UTC; 6min ago
Docs: man:systemd-sysv-generator(8)
Tasks: 0 (limit: 1153)
CGroup: /system.slice/jenkins.service
```

#### Open Up The Firewall

By defaul Jenkins runs on port 8080, so we will need to go ahead and open that port up using `ufw`. Simply run:

```bash
$ sudo ufw allow 8080
```

Check the `ufw`'s status to confirm the new rules are setup properly:

```bash
$ sudo ufw status
```

You should see that traffic is allowed fomr port `8080` anywhere.

```bash
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
8080                       ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
8080 (v6)                  ALLOW       Anywhere (v6)
```

Now that Jenkins is installed and the firewall is open we can go ahead and complete our final setup.

#### Setting Up Jenkins With the GUI

To complete our setup we can visit `http://your_server_ip_or_domain:8080`

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1604/unlock-jenkins.png)

We can retrieve our defaul password using the `cat` command in the terminal. It will look something like this:

```bash
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the 32-character alphanumeric password from the terminal and paste it into the Administrator password field, then click Continue.

The next screen will give you the option of installing suggested plugins or selecting the plugins to install. I went with the suggested plugins, although it would be good to also have the PowerShell plugin installed later on.

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/customize_jenkins_screen_two.png)

We'll click the Install suggested plugins option, which will immediately begin the installation process:

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/jenkins_plugin_install_two.png)

When the installation is complete, you will be prompted to set up the first administrative user. It's possible to skip this step and continue as admin using the initial password we used above, but we'll take a moment to create the user.

Below you can create a username and add your full name as well as an email.

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/jenkins_create_user.png)

You will see an Instance Configuration page that will ask you to confirm the preferred URL for your Jenkins instance. Confirm either the domain name for your server or your server's IP address:

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/instance_confirmation.png)

After confirming the appropriate information, click Save and Finish. You will see a confirmation page confirming that "Jenkins is Ready!"

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/jenkins_ready_page_two.png)

Click Start using Jenkins to visit the main Jenkins dashboard.

![Jenkins](https://assets.digitalocean.com/articles/jenkins-install-ubuntu-1804/jenkins_home_page.png)

We have successfully setup our Jenkins server!


















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
