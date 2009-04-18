rails-nginx-passenger-ubuntu
============================

My notes to set up a simple production server with ubuntu, nginx, passenger and mysql for rails.

Aliases
-------
    
    echo "alias ll='ls -l'" >> ~/.bash_aliases

edit .bashrc and uncomment the loadig of .bash_aliases

Update and upgrade the system
-------------------------------

    sudo apt-get update
    sudo apt-get upgrade

Configure timezone
-------------------

    sudo dpkg-reconfigure tzdata
    sudo apt-get install ntp
    sudo ntpdate ntp.ubuntu.com # Update time
    
Verify that you have to correct date and time with

    date

Configure hostname
-------------------

    sudo hostname your-hostname

Add 127.0.0.1 your-hostname

    sudo vim /etc/hosts
    
Write your-hostname in 
    
    sudo vim /etc/hostname
    
Verify that hostname is set
    
    hostname

Install mysql
---------------

This should be installed before Ruby Enterprise Edition becouse that will install the mysql gem.

    sudo apt-get install mysql-server libmysqlclient15-dev

Ruby Enterprise Edition
------------------------

Check for newer version at http://www.rubyenterpriseedition.com/download.html

Install package required by ruby enterprise, C compiler, Zlib development headers, OpenSSL development headers, GNU Readline development headers

    sudo apt-get install build-essential zlib1g-dev libssl-dev libreadline5-dev

Download and install Ruby Enterprise Edition

    $ wget http://rubyforge.org/frs/download.php/51100/ruby-enterprise-1.8.6-20090201.tar.gz
    $ tar xvfz ruby-enterprise-1.8.6-20090201.tar.gz 
    $ rm ruby-enterprise-1.8.6-20090201.tar.gz 
    $ ruby-enterprise-1.8.6-20090201/
    $ sudo ./installer

Add Ruby Enterprise bin to PATH

    $ echo "export PATH=/opt/ruby-enterprise-1.8.6-20090201/bin:$PATH" >> .profile

Verify the ruby installation

    $ ruby -v
    ruby 1.8.6 (2008-08-08 patchlevel 286) [i686-linux]


Installning git
----------------

    $ sudo apt-get install git-core

Nginx
-------

    $ sudo /opt/ruby-enterprise-1.8.6-20090201/bin/passenger-install-nginx-module

Select option 1. Yes: download, compile and install Nginx for me. (recommended)

When finished, verify nginx source code is located under /tmp

    $ ll /tmp/
    drwxr-xr-x 8 deploy deploy    4096 2009-04-18 17:48 nginx-0.6.36
    -rw-r--r-- 1 root   root    528425 2009-04-02 08:49 nginx-0.6.36.tar.gz
    drwxrwxrwx 7   1169   1169    4096 2009-04-18 17:56 pcre-7.8
    -rw-r--r-- 1 root   root   1168513 2009-04-18 17:51 pcre-7.8.tar.gz
    
Run the passenger-install-nginx-module once more to add ---with-http_ssl_module 

    $ sudo /opt/ruby-enterprise-1.8.6-20090201/bin/passenger-install-nginx-module
    
Select option 2. No: I want to customize my Nginx installation. (for advanced users)

When installation script ask, "Where is your Nginx source code located?" Enter:

    /tmp/nginx-0.6.36

On, extra arguments to pass to configure script add

     --with-http_ssl_module
     
     
Nginx init script
-------------------

More information on http://wiki.nginx.org/Nginx-init-ubuntu

    cd
    git clone git://github.com/jnstq/rails-nginx-passenger-ubuntu.git
    sudo mv rails-nginx-passenger-ubuntu/nginx/nginx /etc/init.d/nginx
    sudo chown root:root /etc/init.d/nginx
    
Verify that you can start and stop nginx with init script

    $ sudo /etc/init.d/nginx start
    * Starting Nginx Server...
    ...done.
    $ sudo /etc/init.d/nginx status
    nginx found running with processes:  11511 11510
    $ sudo /etc/init.d/nginx stop
    * Stopping Nginx Server...
    ...done.
    
    /usr/sbin/update-rc.d -f nginx defaults
    
If you want, reboot and see so the webserver is starting as it should.


Test a rails applicaton with nginx
----------------------------------

    ~$ rails -d mysql testapp
    cd testapp
    
Enter your mysql password
    
    vim database.yml
    rake db:create:all
    ruby script/generate scaffold post title:string body:text
    rake db:migrate RAILS_ENV=production
    
Check so the rails app start as normal
    
    ruby script/server

    sudo vim /opt/nginx/conf/nginx.conf
    
Add a new virutal host

    server {
        listen 80;
        # server_name www.mycook.com;
        root /home/deploy/testapp/public;
        passenger_enabled on;
    }
    
Restart nginx

    $ sudo /etc/init.d/nginx restart
    
Check you ipadress and see if you can acess the rails application
        












