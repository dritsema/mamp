#Configuring Apache Virtual Hosts on Mac OS X  

### What are Virtual Hosts?  

By default, the Apache configuration on Mac OS X serves files from `/Library/WebServer/Documents` accessed by the name locahost. This is essentially a single site configuration. You could mimic multiple sites by creating subdirectories and access a site at `localhost/somesite`.  This is not ideal.  

### Enter Virtual Hosts  

From the Apache Virtual Host documentation:  

> The term Virtual Host refers to the practice of running more than one web site (such as company1.example.com and company2.example.com) on a single machine.  

Lets get started.  

Edit the Apache configuration file:  

```
sudo nano /etc/apache2/httpd.conf
```  

Find the following line:  

```
#Include /private/etc/apache2/extra/httpd-vhosts.conf
```  

Below it, add the following line:  

```
Include /private/etc/apache2/vhosts/*.conf
```  

This configures Apache to include all files ending in `.conf` in the `/private/etc/apache2/vhosts/` directory.  

Now we need to create this directory.  

```
sudo mkdir /etc/apache2/vhosts
cd /etc/apache2/vhosts
```  

Create the default virtual host configuration file:  

```
sudo touch _default.conf
sudo nano _default.conf
```  

Add the following configuration:  

```
<VirtualHost *:80>
	DocumentRoot "/Library/WebServer/Documents"
</VirtualHost>
```  

This just serves as the default.  By prefixing this file with an underscore, Apache will include it first.  

Typically, I like to keep the default site in its original location, then place each of my projects in a subfolder of Sites. The Sites folder is no longer present as of OS X 10.8, so we have to create it from Terminal:  

```
mkdir ~/Sites
```  

Now you can create your first virtual host.  

The example below contains the virtual host configuration for a WordPress site called multisite.com:  

```
mkdir ~/Sites/multisite.local
```  

**Note:** I use the extension *local*. This avoids conflicts with any real extensions and serves as a reminder I am developing in my local environment.  

Create the virtual host configuration file:  

```
sudo touch /etc/apache2/vhosts/multisite.com.conf
sudo nano /etc/apache2/vhosts/multisite.com.conf
```  

Add the following configuration:  

```
<VirtualHost *:80>
	ServerName "multisite.local"
	DocumentRoot "/Users/ritsemad/Sites/multisite.local"
	ErrorLog "/private/var/log/apache2/multisite.local-error_log"
	CustomLog "/private/var/log/apache2/multisite.local-access_log" common

	<Directory "/Users/ritsemad/Sites/multisite.local/">
		Allow From All
		AllowOverride All
	</Directory>
</VirtualHost>
```  

This `VirtualHost` configuration allows me to access my site from *http://multisite.local* for local development.  

Then restart Apache:

```
apachectl restart
```  

If you run into any problems, run:  

```
apachectl configtest
```  

This will test your Apache configuration and display any error messages.  

### Mapping the .local extension  

In order to access sites locally you need to edit your *hosts* file.  

```
nano /etc/hosts
```  

Add a line to the bottom of this file for your virtual host:  

```
127.0.0.1	multisite.local
```  

### Bonus Points  

I like to run the following to clear the local DNS cache:  

```
dscacheutil -flushcache
```  

### A note about permissions  

Ugh the bane of my existence!  Fortunately with a couple short commands you can resolve most issues quickly.  

For example you may receive 403 Forbidden when you visit your local site.  What you need to know is, the Apache user (_www) needs to have access to read, and sometimes write, to your web directory.  

If you are not familiar with permissions, [read more](http://www.library.yale.edu/wsg/docs/permissions/). For now though, the easiest thing to do is ensure your web directory has permissions of 755. You can change permissions with these commands.  

Be sure to replace the first one with your actual user:  

```
sudo chown -R ritsemad:_www /Users/ritsemad/Sites
sudo chmod -R 755 /Users/ritsemad/Sites
```  

The `-R` flag makes the command recurrsive so everything inside those directories gets updated as well.  

**Note:** There are many ways to solve permission issues. I have provided this as the *easiest* solution, not necessarily the best.  

### Celebrate :beers:  

Any time you want to add a site to Apache on your Mac, simply create a virtual host configuration file for that site and map it in your hosts file.  

Well that's it, [onto Git](XcodeHomebrewGit.md)!