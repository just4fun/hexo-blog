title: Deploy meaning on Amazon EC2
date: 2015-01-04 22:02:47
tags: 
- ec2
- transmit
- nginx
- mongodb

---

[meaning](https://github.com/just4fun/meaning) is a mini blogging platform inspired by MEAN.JS(MongoDB, Express, AngularJS, and Node.js), and I will take notes here about how I deploy it on [Amazon EC2](http://aws.amazon.com/ec2/).

<!-- more -->

### Amazon EC2

First of all, you should pay for an Amazon Machine Image to launch an instance.
I choose `Ubuntu` and what is more, I buy an extra [Reserved Instance](http://aws.amazon.com/ec2/purchasing-options/reserved-instances/) to receive a discount on my instance usage compared to running On-Demand instances.
You can know more about `Reserved Instance` [here](http://blog.cloudability.com/4-things-youre-getting-wrong-aws-reserved-instance-application/).

### Release

Now, we should release project to get the released code which will be deployed to production.
```bash
$ grunt build -release
```
And the `dist` folder contains all the released files.

### FTP

For Windows, [FileZilla](https://filezilla-project.org/) is a nice choice for FTP solution.
And for Mac, I like [Transmit](http://panic.com/transmit/).

First, copy your Amazon EC2 .pem file to your local ssh directory ~/.ssh.
Next, open ~/.ssh/config and add extra line to let your app know the PEM, for example:
```bash
IdentityFile "~/.ssh/just4fun.pem"
```
At last, save the config changes and alter the permissions of the PEM file to 700:
```bash
$ chmod 700 ~/.ssh/just4fun.pem
```
Try command below to test whether the PEM file works:
```bash
$ ssh -i ~/.ssh/just4fun.pem root@your_amazon_server
```

When we connect our server successfully, we should create a virtual host which will contains the released files:
```bash
$ mkdir /var/www/just4fun
```
Then copy directories and files to the virtual host which we created just now:
```bash
/dist/client
/dist/server
/package.json
```
Tap commnd below to install the node dependencies:
```bash
npm install -production
```
At last we get directory structure like below:
```bash
/var/www/just4fun
  --client
  --server
  --package.json
  --node_modules
```

### DB

We use [MongoDB](http://www.mongodb.org/) in meaning and there is a [post](http://www.mongodbspain.com/en/2014/08/30/install-mongodb-on-ubuntu-14-04/) about how to install MongoDB on Ubuntu(14.04) for reference. Or you can visit official site to find the installation.

### Web Server

I previously use [Apache](http://httpd.apache.org/), and I changed it to [nginx](http://wiki.nginx.org/Main) now.
In order to install Nginx we execute:
```bash
$ apt-get install nginx
```
Then create a virtual host configuration file in /etc/nginx/sites-available, such as `/etc/nginx/sites-available/just4fun`.
```bash
server {
  listen 80 default_server;
  listen [::]:80 default_server ipv6only=on;
  root /var/www/just4fun/client;
  index index.html index.htm;

  # Make site accessible from http://localhost/
  server_name talent-is.me;

  location / {
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    rewrite ^/admin/?$ /admin/admin-index.html break;
    rewrite ^/login/?$ /admin/admin-login.html break;
    try_files $uri $uri/ =404;
    # Uncomment to enable naxsi on this location
    # include /etc/nginx/naxsi.rules
  }
}
```
Next, we should disable the default vhost:
```bash
rm /etc/nginx/sites-enabled/default
```
And enable our virtual host:
```bash
ln -s /etc/nginx/sites-available/just4fun /etc/nginx/sites-enabled/just4fun
```
At last, restart nginx with new configuration:
```bash
service nginx restart
```

That's all. :)
