This guide is meant to help you through the process of installing Apache, PHP, and MySQL on Mac OS X.  

A reminder that OS X runs atop UNIX, so most UNIX software installs easily on OS X. Furthermore, Apache and PHP come pre-packaged. To create a local web server, all you need to do is enable them and install MySQL.  

**_But wait, isn't there this ultra uber app called MAMP which will do all this for me!?_** True, this will get you started the quickest. Beyond the basics though, what I've typically found is that GUI based tools meant to *"increase efficiency"* really end up costing you more in the long run as you grow, and ultimately you end up going back to wanting more control.  So lets get started :smiling_imp:  

---

### Terminal  

Any terminal will do. The one bundled with OS X is in Applications->Utilities->Terminal.  

Whatever you use, drag it to your :mac: Dock.  

### A word on sudo  

Best practice is to prefix any administrative commands with `sudo`.  In rare cases where you need to run a bunch of these in succession, you can actually switch to root using `sudo su -`. I just prefer to use `sudo` for commands that need it.  

### Enable Apache  

```
sudo apachectl start
```

Once Apache is running, http://localhost should display the default "It works!" page. The document root of the default site is found in `/Library/WebServer/Documents/`.

### Enable PHP  

First, make a backup of the default Apache configuration. This is good practice and serves as a comparison against future versions of Mac OS X.  

```
cd /etc/apache2/
sudo cp httpd.conf httpd.conf.bak
```

Now edit the Apache configuration.  Again, use whichever editor you are comfortable with.  For plain text, I prefer nano.  

```
sudo nano httpd.conf
```  

Uncomment php5_module by removing the `#`  

```
LoadModule php5_module libexec/apache2/libphp5.so
```  

**Note:** The default configuration for Apache 2.4 on OS X Yosemite is pretty lean. For example, common modules like mod_rewrite were disabled.  Lets just enable those now too.  

```
LoadModule deflate_module libexec/apache2/mod_deflate.so
LoadModule expires_module libexec/apache2/mod_expires.so
LoadModule rewrite_module libexec/apache2/mod_rewrite.so
```  

Save that off and restart Apache  
```
sudo apachectl restart
```

You can verify PHP is enabled by creating a `phpinfo()` script in your `DocumentRoot`.  

The default `DocumentRoot` for Mac OS X is `/Library/WebServer/Documents`.  

### Bonus Points

Verify your `DocumentRoot` from your Apache configuration.  

```
grep DocumentRoot httpd.conf
```

Now create the `phpinfo()` script in your `DocumentRoot`.  

Rename index.html as index.php and change its content:
```
cd /Library/WebServer/Documents/
sudo mv index.html.en index.php
sudo nano index.php
```  

Replace with this code:
```php
<?php
phpinfo();
?>
```

Save that off and refresh localhost to verify PHP. You should see a page listing environment variables for your parcitular installation of PHP. If you don't go back and verify those modules were enabled in Apache.   

### Install MySQL  

**Note:** If you are upgrading MySQL you should skip this section and instead [read this](http://coolestguidesontheplanet.com/upgrade-mysql-database-5-5-5-6-osx-10-8-mountan-lion/).  

1. [Download](http://dev.mysql.com/downloads/mysql/) the MySQL DMG for Mac OS X.  
**When downloading you don’t have to sign up, look for `No thanks, just start my download`  
2. Install the package  

```
echo $PATH
```  

Now just make sure that `/usr/local/mysql/bin` is in your $PATH to use mysql commands without typing the full path to the commands.  If its not, simply do this:  

```
sudo nano ~/.bash_profile
```  

And add this line (including other common paths):  
```
export PATH="/usr/local/bin:/usr/local/mysql/bin:/usr/bin:/usr/local/sbin:$PATH"
```  

Then reload your .bash_profile:  

```
source ~/.bash_profile
```  

**Note:** Restarting terminal or opening a new terminal window will also reload `~/.bash_profile`.  

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

### Celebrate :beers:  

Well that's it, [onto Virtual Hosts](VirtualHosts.md)!