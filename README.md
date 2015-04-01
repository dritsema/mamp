This guide is meant to help you through the process of installing Apache, PHP, and MySQL on Mac OS X Yosemite. A reminder that Mac OS X runs atop UNIX, so most UNIX software installs easily on Mac OS X. Furthermore, Apache and PHP come packaged with OS X. To create a local web server, all you need to do is enable them and install MySQL (yep).  

**__But wait, isn't there this ultra uber app called MAMP which will do all this for me!?__** True, this will get you started quickly. But I imagine thats not why you're here. I imagine you're here to learn something, to better understand your environment, and to not be completely lost when something goes wrong, which it innevitabley does.  

Beyond the basics, what I've typically found is that GUI based tools meant to "increase efficiency" really end up costing you more in the long run. The process below is more in line with what you would find on an actual Linux server.

So dig in, enjoy, and check back as often as you find yourself needing a refresher.  For other developer guides beyond the basics, feel free to check those out too.  

---

### Terminal  

Any terminal will do, the one bundled with OS X is in Applications->Utilities->Terminal.  Whatever you use, drag it to your Dock.  

### A word on sudo  

Best practice is to prefix any administrative commands with *sudo*.  In rare cases where you need to run a bunch of these in succession, you can actually switch to root.  Just keep in mind that bad things can happen if you're not extra careful.  At the end, be sure to *exit*!  

```
sudo su -
```

### Enable Apache  

```
apachectl start
```

Once Apache is running, http://localhost](http://localhost) should display the default "It works!" page. The document root of the default site is found in /Library/WebServer/Documents/.

### Enable PHP  

First, make a backup of the default Apache configuration. This is good practice and serves as a comparison against future versions of Mac OS X.  

```
cd /etc/apache2/
cp httpd.conf httpd.conf.bak
```

Now edit the Apache configuration.  Again, use whichever editor you are comfortable with.  For plain text, I prefer nano.  

```
nano httpd.conf
```

control w "php"  
Uncomment php5_module by removing the `#`  

```
LoadModule php5_module libexec/apache2/libphp5.so
```  

**Note:** The default configuration for Apache 2.4 on OS X Yosemite seemed pretty lean. For example, common modules like mod_rewrite were disabled.  Lets just enable those now too.  

```
LoadModule deflate_module libexec/apache2/mod_deflate.so
LoadModule expires_module libexec/apache2/mod_expires.so
LoadModule rewrite_module libexec/apache2/mod_rewrite.so
```  

Save that off and Restart Apache  
```
apachectl restart
```

You can verify PHP is enabled by creating a `phpinfo()` script in your `DocumentRoot`.  

The default DocumentRoot for Mac OS X is /Library/WebServer/Documents.  

### Bonus Points

Verify your `DocumentRoot` from your Apache configuration.  

```
grep DocumentRoot httpd.conf
```

Now create the `phpinfo()` script in your `DocumentRoot`.  Rename index.html as index.php and change its content:

```
cd /Library/WebServer/Documents/
mv index.html index.php
nano index.php
```

```php
<?php
phpinfo();
?>
```

Save that off and refresh localhost to verify PHP.  

### install MySQL  

**Note:** If you are upgrading MySQL you should skip this section and instead [read this](http://coolestguidesontheplanet.com/upgrade-mysql-database-5-5-5-6-osx-10-8-mountan-lion/).  

1. [Download](http://dev.mysql.com/downloads/mysql/) the MySQL DMG for Mac OS X.  **Pro Tip:** On the login page click [No thanks, just start my download](http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.23-osx10.9-x86_64.dmg)  
2. Install the package
3. Update your path  

```
echo $PATH
nano ~/.bash_profile
```

Add this line:  

```
export PATH=~/bin:/usr/local/bin:/usr/local/mysql/bin:$PATH
```  

**Note:** You will need to restart terminal or open a new terminal window for this to take effect.  

### Bonus Points  

While this isn’t necessary, it’s good practice to also run `mysql_secure_installation`.  

### Connect PHP and MySQL  

You need to ensure PHP and MySQL can communicate with one another. For some reason mysql on OS X gets the locations of the required socket file a bit wrong.  Fortunately the solution is simply to create a symbolic link so your mac finds the required socket, even when it's looking in the wrong place!  

```
cd /var 
sudo mkdir mysql
sudo chmod 755 mysql
cd mysql
sudo ln -s /tmp/mysql.sock mysql.sock
```  

Thats it, onto Virtual Hosts!