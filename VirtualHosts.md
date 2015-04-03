#Configuring Apache Virtual Hosts on Mac OS X  

### What are Virtual Hosts?  

By default, the Apache configuration on Mac OS X serves files from `/Library/WebServer/Documents` accessed by the name locahost. This is essentially a single site configuration. You could mimic multiple sites by creating subdirectories and access a site at `localhost/somesite`.  This is not ideal.  

From the Apache Virtual Host documentation:  

> The term Virtual Host refers to the practice of running more than one web site (such as company1.example.com and company2.example.com) on a single machine.  

Typically, I like to keep the default site in its original location, then place each of my projects in a subfolder of Sites. The Sites folder is no longer present as of OS X 10.8, so we have to create it from Terminal:  

```
mkdir ~/Sites
```  

The example below contains the virtual host configuration for a WordPress site called multisite.com:  

```
mkdir ~/Sites/multisite.local
```  

**Note:** I use the extension *local*. This avoids conflicts with any real extensions and serves as a reminder I am developing in my local environment.  

### Map the .local extension  

In order to access sites locally you need to edit your *hosts* file.  

```
nano /etc/hosts
```  

Add a line to the bottom of this file for your virtual host:  

```
127.0.0.1	multisite.local
```  

### Edit the Apache configuration file  

```
sudo nano /etc/apache2/httpd.conf
```  

Similarly to what happens for the PHP module, the line loading the configuration file for virtual hosts is already present, but commented out:  

```
# Virtual hosts
#Include /private/etc/apache2/extra/httpd-vhosts.conf
```  

Once more, uncomment that line by removing the `#`, then save the file.  

Now edit the configuration file:  

```
sudo nano /etc/apache2/extra/httpd-vhosts.conf
```  

and change it to something like the following:  

```
NameVirtualHost *:80

<VirtualHost *:80>
    DocumentRoot "/Library/WebServer/Documents"
</VirtualHost>

<VirtualHost *:80>
	ServerName "multisite.local"
	DocumentRoot "/Users/ritsemad/Sites/multisite.local"
	ErrorLog "/private/var/log/apache2/multisite.local-error_log"
	CustomLog "/private/var/log/apache2/multisite.local-access_log" common

	<Directory "/Users/ritsemad/Sites/multisite.local">
		Order allow, deny
		Allow from all
		Require all granted
	</Directory>
</VirtualHost>
```  

**Note:** Note: The Require all granted configuration became available in Apache 2.4 which comes with OS X Yosemite. If you are running a version of OS X before Yosemite, use the equivalent 2.2 configuration:
```
Allow From All
AllowOverride All
```  

The first entry points to the default web site, which will also work as a catch-all host for any virtual host that wasn't defined anywhere else. The second entry points instead to the ~/Sites/multisite.local folder.  This `VirtualHost` configuration allows me to access my site from *http://multisite.local* for local development.  

Then restart Apache:

```
apachectl restart
```  

If you run into any problems, run:  

```
apachectl configtest
```  

This will test your Apache configuration and display any error messages.  

### Bonus Points  

I like to run the following to clear the local DNS cache:  

```
dscacheutil -flushcache
```  

### A note about permissions  

If you are not familiar with permissions, [read more](http://www.library.yale.edu/wsg/docs/permissions/). In short, the Apache user (_www) needs to have access to read, and sometimes write, to your web directory.  For now, the easiest thing to do is ensure your web directory is group owned by Apache and has permissions of 755.  

You can change permissions with these commands.  Be sure to replace the first one with your actual user:  

```
sudo chown -R ritsemad:_www /Users/ritsemad/Sites
sudo chmod -R 755 /Users/ritsemad/Sites
```  

The `-R` flag makes the command recurrsive so everything inside those directories gets updated as well.  

**Note:** There are many ways to solve permission issues. I have provided this as the *easiest* solution, not necessarily the best.  

### Celebrate :beers:  

Any time you want to add a site to Apache on your Mac, simply create a virtual host configuration file for that site and map it in your hosts file.  

Well that's it, [onto Git](XcodeHomebrewGit.md)!