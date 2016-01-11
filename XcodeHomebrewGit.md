#Install Xcode, Homebrew, Git  

> As we all know the majority of the Web development community uses Git. The funny part to the whole story is the fact that most developers don't even know what version they're using.  

And in fact when I first started writing this the version installed on my Mac was 1.9 and the latest current stable release was 2.3.  Then again, making sure my version of Git is up to date is the last thing on my mind.  Uninstall the old version, downloan the latest DMG, install the package, change PATH variables, bla bla bla...there's a better way.  

### Enter Homebrew  

This method takes a little bit of setup but its well worth the time.  Homebrew is an awesome tool that makes installing packages, libraries, utilities a breeze.  To install it, first we need to install Xcode.  Xcode is a developer suite Apple offers with all the tools you need to build Mac and iOS apps, but also includes basic compilers and libraries needed to install various web development tools.  

Fortunately since Mavericks and Yosemite getting this installed is relatively painless.  

#### Install Xcode

```
xcode-select --install
```  

You should be prompted to install the command line developer tools. Click `Install` when it appears.  

#### Install Homebrew  

The full instructions are available on the [Homebrew Wiki](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Installation.md#installation), but you should only need to run the command that’s listed at the top of the [Homebrew](http://brew.sh/) site:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```  

After that runs you should see something like this:  

```
==> Installation successful!
==> Next steps
Run `brew help` to get started
```  

Confirm things are working :point_up:  

```
brew doctor
```  

If you get `Your system is ready to brew`, you can move on. Otherwise, address those issues noted.  

### Install Git  

```
brew update
brew install git
```  

Since we just installed Homebrew, we could have skipped `brew update`, but it’s a good habit to run it before installing anything with Homebrew because Homebrew is updated regularly.  

To verify you were upgraded:  

```
git --version
```  

You should get `git version 2.4.6` or later.  

Run `brew doctor` to make sure everything is still working.  

Wanna keep git up to date? Easy peezy 123:  

```
brew upgrade git
```  

You’re now part of the wonderful esoteric world of developers that actually know how to keep Git up to date. :beers: Cheers!  

### Configure Git  

Tell Git your name so your commits will be properly labeled.  

```
git config --global user.name "YOUR NAME"
```  

Tell Git the email address that will be associated with your Git commits. The email you specify should be the same one found in your email settings.  

```
git config --global user.email "YOUR EMAIL ADDRESS"
```  

### Authenticating with GitHub from Git  

When you connect to a GitHub repository from Git, you'll need to authenticate with GitHub using either HTTPS or SSH.  HTTPS is now the recommended method by GitHub and to help they added credential helper.  Since we installed Git with Homebrew, this function was already included.  

To tell Git to use this helper, run this:  

```
git config --global credential.helper osxkeychain
```  

The next time you pull, push, etc. you'll be prompted for your username and password, and to grant access to the OSX keychain. After you've done this, the username and password are stored in your keychain and you won't be required to type them into Git again (unless of course you update password).  

### Celebrate :beers:   

You now have everything you need for local LAMP (or MAMP) development.  Your choice of editor tools are all up to you but we'll cover some of the good ones anyway in future tutorials.