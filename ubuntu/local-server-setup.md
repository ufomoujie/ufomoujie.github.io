# ubuntu local server setup

- ##  update apt to the latest version
    ```
    sudo apt update
    sudo apt upgrade
    ```
- ## Japanese environment setting
    install japanese language pack
    ```
    sudo apt install language-pack-ja
    ```
    change local to japanese
    ```
    sudo update-locale LANG=ja_JP.UTF8
    ```
    test if locale changed
    reboot system and show locale setting by commnd below 
    ```
    reboot
    locale
    ```
    set timezone to tokyo
    ```
    sudo dpkg-reconfigure tzdata
    ```
- ## logrotate
    ```
    sudo apt install logrotate
    ```
- ## Apache2
    install apache2
    ```
    sudo apt install apache2
    ```

    ### check if apache2 is woking
    check if starting on system startup
    ```
    sudo systemctl status apache2
    ```

    access to your server ip address
    ```
    http://your_server_ip
    ```

    ### apache command
    | command                        | description                                |
    | :----------------------------- | :----------------------------------------- |
    | sudo systemctl stop apache2    | stop apache2                               |
    | sudo systemctl start apache2   | start apache2                              |
    | sudo systemctl restart apache2 | restart apache2                            |
    | sudo systemctl reload apache2  | reload apache2                             |
    | sudo systemctl disable apache2 | disable apache2 starting on system startup |
    | sudo systemctl enable apache2  | enable apache2 starting on system startup  |
    | apache2 -v                     | show version of apache2                    |

    ### enable .htaccess file
    /etc/apache2/sites-available/000-default.conf  default-ssl.conf
    AllowOverride setting
    ```
    <Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ```
    enable rewrite
    ```
    sudo a2enmod rewrite
    ```
    ### change log dir 
    /etc/apache2/envvars
    ```
    export APACHE_LOG_DIR=/var/www/tmp/httpd
    ```
    set logrotate 
    /etc/logrotate.d/apache2
    ```
    #/var/log/apache2/*.log
    /var/www/tmp/httpd/*.log

    create 644 root adm
    ```
    restart apache2 and logrotate
    ```
    sudo systemctl restart apache2
    sudo systemctl restart logrotate
    ```


- ## MariaDB
    install mariadb-server
    ```
    sudo apt install mariadb-server
    ```

    run security script
    ```
    sudo mysql_secure_installation
    ```

    press `ENTER`
    ```
    output
    NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB<br>
        SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!<br>

    In order to log into MariaDB to secure it, we'll need the current
    password for the root user.  If you've just installed MariaDB, and
    you haven't set the root password yet, the password will be blank,
    so you should just press enter here.

    Enter current password for root (enter for none):
    ```

    press `n` and `ENTER`
    ```
    output
    Setting the root password ensures that nobody can log into the MariaDB
    root user without the proper authorisation.
    
    Set root password? [Y/n] N
    ```

    ### create new MariaDB user (privileges same as Root) 
    change `admin` and `password` to your taste.
    ```
    sudo mariadb
    GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
    ```

    Flush privileges session
    ```
    Flush PRIVILEGES;
    exit
    ```

    ### Test MariDB
    check if starting on system startup
    ```
    sudo systemctl status mariadb
    ```

    check if can access by `mysqladmin`
    ```
    sudo mysqladmin version
    ```

    access by user and password
    change `admin` to your user name
    ```
    mysqladmin -u admin -p version
    ```


- ## PHP
    install php and extends
    ```
    sudo apt -y install php php-mysql php-xml php-gd php-imagick php-mbstring php-zip php-bcmath php-json php-curl
    ```

    php.ini configure<br>
    /etc/php/7.4/apache2/php.ini
    ```
    upload_max_filesize = 32M 
    post_max_size = 48M
    memory_limit = 256M
    max_execution_time = 600
    max_input_vars = 1000
    max_input_time = 60    
    ```
    /etc/php/7.4/apache2/php.ini<br>
    enable display errros
    ```
    display_errors = on
    display_startup_errors = On
    ```

- ## phpmyadm
    install composer
    ```
    cd /home/ubuntu
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    sudo mv composer.phar /usr/local/bin/composer
    ```

    install phpmyadm by composer
    ```
    cd /var/www/html
    composer create-project phpmyadmin/phpmyadmin
    mv phpmyadmin myadm
    ```

    ### comfigure 
    ```
    sudo cp config.sample.inc.php config.inc.php
    sudo nano config.inc.php
    $cfg['blowfish_secret'] = 'adg9da9jh9k9a;:fa0fg98hsugaif45f98g56fa5h67gj876ag';
    ```

    set tempdir to 777
    ```
    sudo mkdir tmp
    sudo chmod 777 tmp
    ```

    ### test phpmyadmin
    access to your server ip address
    ```
    http://your_server_ip/myadm
    ```

- ## cockpit
        
    install cockpit
    ```
    sudo apt install cockpit
    ```

    ## test cockpit
    ```
    https://your_server_ip:9090
    ```

- ## postfix
    ```
    sudo apt install postfix
    ```

    ### postfix configuration
    - choose the `Internet Site`
    - System mail name: `test.eaze`

    /usr/share/postfix/main.cf
    ```
    sudo cp main.cf.dist main.cf
    sudo nano main.cf
    ```
    change value like below
    
    | before                                                                  | after                                                                                      |
    | :---------------------------------------------------------------------- | :----------------------------------------------------------------------------------------- |
    | #mail_owner = postfix                                                   | mail_owner = postfix                                                                       |
    | #myhostname = host.domain.tld                                           | myhostname = mail.test.eaze                                                                |
    | #mydomain = domain.tld                                                  | mydomain = test.eaze                                                                       |
    | #myorigin = $mydomain                                                   | myorigin = $mydomain                                                                       |
    | #inet_interfaces = all                                                  | inet_interfaces = all                                                                      |
    | #mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain | mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain                     |
    | #local_recipient_maps = unix:passwd.byname $alias_maps                  | local_recipient_maps = unix:passwd.byname $alias_maps                                      |
    | #home_mailbox = Maildir/                                                | home_mailbox = Maildir/                                                                    |
    | smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)                    | #smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu) <br>smtpd_banner = $myhostname ESMTP |
    | sendmail_path =                                                         | sendmail_path = /usr/sbin/postfix                                                          |
    | newaliases_path =                                                       | newaliases_path = /usr/bin/newaliases                                                      |
    | mailq_path =                                                            | mailq_path = /usr/bin/mailq                                                                |
    | setgid_group =                                                          | setgid_group = postdrop                                                                    |
    | html_directory =                                                        | #html_directory =                                                                          |
    | manpage_directory =                                                     | #manpage_directory =                                                                       |
    | sample_directory =                                                      | #sample_directory =                                                                        |
    | readme_directory =                                                      | #readme_directory =                                                                        |

    ### save and restart
    ```
    sudo newaliases
    sudo /etc/init.d/postfix restart
    ```
    add email address nickname
    ```
    sudo nano /etc/aliases
    root: watch@test.eaze
    ```
- ## lgis mapserver
    ```
    sudo apt install cgi-mapserver php-mapscript
    ```
    test if working
    ```
    mapserv -v
    ```

- ## Install Samba on Linux and share files with Windows
    ```
    sudo apt install samba
    ```
    shared directory settigns
    ```
    sudo chmod 777 /var/www
    ```
    add system user 
    ```
    sudo adduser share
    ```
    set samba user
    ```
    sudo pdbedit -a share
    ```
    configuration
    Add the configuration to the end of the file line
    ```
    sudo nano /etc/samba/smb.conf

    [WWW]
       path = /var/www/
       writable = yes
       valid users = share
    ```
    test config
    ```
    testparm
    ```
    restart
    ```
    sudo /etc/init.d/smbd restart
    sudo /etc/init.d/nmbd restart
    ```

- ## rsync file from another server
    change  `x.x.x.x` to your remote  server ip address.
    ```
    rsync -rt root@x.x.x.x:/home/directory /home
    ```