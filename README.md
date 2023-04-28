# erpnext_installation
erpnext_installation
# Frappe-ERPNext-Version-14--in-Ubuntu-22.04-LTS
A complete Guide to Install Frappe/ERPNext version 14  in Ubuntu 22.04 LTS



### Pre-requisites 

      Python 3.6+
      Node.js 14+
      Redis 5                                       (caching and real time updates)
      MariaDB 10.3.x / Postgres 9.5.x               (to run database driven apps)
      yarn 1.12+                                    (js dependency manager)
      pip 20+                                       (py dependency manager)
      wkhtmltopdf (version 0.12.5 with patched qt)  (for pdf generation)
      cron                                          (bench's scheduled jobs: automated certificate renewal, scheduled backups)
      NGINX                                         (proxying multitenant sites in production)



### STEP 1 Install git
    sudo apt-get install git

### STEP 2 install python-dev

    sudo apt-get install python3-dev

### STEP 3 Install setuptools and pip (Python's Package Manager).

    sudo apt-get install python3-setuptools python3-pip

### STEP 4 Install virtualenv
    
    sudo apt-get install virtualenv
    sudo apt install python3.10-venv
    

### STEP 5 Install MariaDB

    sudo apt-get install software-properties-common
    sudo apt install mariadb-server
    sudo mysql_secure_installation
    
    
      In order to log into MariaDB to secure it, we'll need the current
      password for the root user. If you've just installed MariaDB, and
      haven't set the root password yet, you should just press enter here.

      Enter current password for root (enter for none): # PRESS ENTER
      OK, successfully used password, moving on...
      
      
      Switch to unix_socket authentication [Y/n] Y
      Enabled successfully!
      Reloading privilege tables..
       ... Success!
 
      Change the root password? [Y/n] Y
      New password: 
      Re-enter new password: 
      Password updated successfully!
      Reloading privilege tables..
       ... Success!

      Remove anonymous users? [Y/n] Y
       ... Success!
 
       Disallow root login remotely? [Y/n] Y
       ... Success!

       Remove test database and access to it? [Y/n] Y
       - Dropping test database...
       ... Success!
       - Removing privileges on test database...
       ... Success!
 
       Reload privilege tables now? [Y/n] Y
       ... Success!

 
    
    
    
### STEP 6  MySQL database development files

    sudo apt-get install libmysqlclient-dev

### STEP 7 Edit the mariadb configuration ( unicode character encoding )

    sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

add this to the 50-server.cnf file

      [server]
      user = mysql
      pid-file = /run/mysqld/mysqld.pid
      socket = /run/mysqld/mysqld.sock
      basedir = /usr
      datadir = /var/lib/mysql
      tmpdir = /tmp
      lc-messages-dir = /usr/share/mysql
      bind-address = 127.0.0.1
      query_cache_size = 16M
      log_error = /var/log/mysql/error.log

      [mysqld]
      innodb-file-format=barracuda
      innodb-file-per-table=1
      innodb-large-prefix=1
      character-set-client-handshake = FALSE
      character-set-server = utf8mb4
      collation-server = utf8mb4_unicode_ci      

      [mysql]
      default-character-set = utf8mb4

Now press (Ctrl-X) to exit

    sudo service mysql restart

### STEP 8 install Redis
    
    sudo apt-get install redis-server

### STEP 9 install Node.js 14.X package

    sudo apt install curl 
    curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
    source ~/.profile
    nvm install 14

### STEP 10  install Yarn

    sudo apt-get install npm

    sudo npm install -g yarn

### STEP 11 install wkhtmltopdf

    sudo apt-get install xvfb libfontconfig wkhtmltopdf
    

### STEP 12 install frappe-bench

    sudo -H pip3 install frappe-bench==5.10.1
    
    bench --version
    
### STEP 13 initilise the frappe bench & install frappe latest version 

    bench init frappe-bench --frappe-branch version-14
    
    cd frappe-bench/
    bench start
    
### STEP 14 create a site in frappe bench 
    
    bench new-site cliconsys.com
    
    bench use cliconsys.com

### STEP 15 install ERPNext latest version in bench & site

    
    bench get-app payments
    
    bench get-app erpnext --branch version-14
    ###OR
    bench get-app https://github.com/frappe/erpnext --branch version-14

    bench --site cliconsys.com install-app erpnext
    
    bench start
    
### Step 16 setup production
    
    sudo bench setup production cliconsys-frappe
    bench restart

#### If bench restart is not worked run the following command again with all Questions Yes
    
    sudo bench setup production cliconsys-frappe
    
#### if js and css file is not loading on login window run the following command

    sudo chmod o+x /home/cliconsys-frappe
       
    
#### Production Setup

Supervisor

      bench setup supervisor
      sudo ln -s `pwd`/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf

Nginx 

      bench setup nginx
      sudo ln -s `pwd`/config/nginx.conf /etc/nginx/conf.d/frappe-bench.conf

#### SSL Setup

      bench setup add-domain --site site1.local [your.domain.here]

      bench setup nginx

      sudo service nginx reload

And when it comes the certbot guide, I have not found any that work as well as the use of snapd to handle all of the complicated settings so I don’t have to deal with them. Here is the short version of using snapd to install certbot and get your ssl certificate:

First we need to make sure that ‘certbot’ is not already installed, so we will attempt to remove it just in case

      sudo apt-get remove certbot

Now we are going to install the package bundle provider and make sure it is all up-to-date with the latest release of ‘snapd’ and let it handle the certbot for us
      
      sudo apt install snapd
      sudo snap install core
      sudo snap refresh core

Now we use ‘snap’ service to automatically install and configure certbot to work perfectly with your new ERPNext server
      
      sudo snap install --classic certbot
      sudo ln -s /snap/bin/certbot /usr/bin/certbot

Now we can safely use the pre-configured certbot to install your SSL certificate
      
      sudo certbot --nginx


#### Bench Upgrade 5.16
      
      pip3 install --upgrade frappe-bench
    

#### Indian Compilation
      
      bench get-app --branch version-14 https://github.com/resilient-tech/india-compliance.git
    
    

    
