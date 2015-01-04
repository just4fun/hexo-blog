title: Deploy meaning on Amazon EC2
date: 2015-01-04 22:02:47
tags: 
- ec2
- nginx

---

[meaning](https://github.com/just4fun/meaning) is a mini blogging platform inspired by MEAN.JS(MongoDB, Express, AngularJS, and Node.js), and I will take notes here about how I deploy it on [Amazon EC2](http://aws.amazon.com/ec2/).

<!-- more -->

### Amazon EC2

First of all, you should pay for an Amazon Machine Image to launch an instance.
I choose `Ubuntu` and what is more, I buy an extra [Reserved Instance](http://aws.amazon.com/ec2/purchasing-options/reserved-instances/) to receive a discount on my instance usage compared to running On-Demand instances.
You can know more about `Reserved Instance` [here](http://blog.cloudability.com/4-things-youre-getting-wrong-aws-reserved-instance-application/).

### Release

Now, we should release project to get the released code which will be deployed to production.
```
$ grunt build -release
```
And the `dist` folder contains all the released files.

### FTP

For Windows, [FileZilla](https://filezilla-project.org/) is a nice choice for FTP solution.
And for Mac, I like [Transmit](http://panic.com/transmit/).

First, copy your Amazon EC2 .pem file to your local ssh directory ~/.ssh.
Next, open ~/.ssh/config and add extra line to let your app know the PEM, for example:
```
IdentityFile "~/.ssh/just4fun.pem"
```
At last, save the config changes and alter the permissions of the PEM file to 700:
```
$ chmod 700 ~/.ssh/just4fun.pem
```
Try command below to test whether the PEM file works:
```
$ ssh -i ~/.ssh/just4fun.pem root@your_amazon_server
```

When we connect our server successfully, we should create a directory which contains the released files and  will be hosted via a web server:
```
$ mkdir /var/www/just4fun
```
Then copy directories and files to the directory which we created just now:
```
/dist/client
/dist/server
/package.json
```
Tap commnd below to install the node dependencies:
```
npm install -production
```

### DB

We use [MongoDB](http://www.mongodb.org/) in meaning and there is a [post](http://www.mongodbspain.com/en/2014/08/30/install-mongodb-on-ubuntu-14-04/) about how to install MongoDB on Ubuntu(14.04) for reference. Or you can visit official site to find the installation.

### Web Server

I previously use [Apache](http://httpd.apache.org/), and I changed it to [nginx](http://wiki.nginx.org/Main) now.