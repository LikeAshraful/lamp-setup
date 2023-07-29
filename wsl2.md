# LAMP stack on WSL2 - Apache, MySQL, PHP, PhpMyAdmin

## Apache

```sh
sudo apt-get update && sudo apt-get upgrade 
sudo apt-get install -y apache2
```

## PHP

```sh
sudo apt update && apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update && sudo apt install -y php8.2
php -v #>Outputs "PHP 8.2........"

sudo apt install php8.2-cli php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath 
```

## Install composer globally
```sh
sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

## Install laravel project
```sh 
 composer create-project laravel/laravel test_project
```

## MySQL

```sh
sudo apt-get install -y mysql-server php-mysql
sudo service mysql restart
sudo mysql_secure_installation
    #> Validate password component: N
    #> New password: MyPassword
    #> Remove anonymous users: Y
    #> Disallow root login remotely: Y
    #> Reload privilege tables now: Y
sudo service mysql stop
sudo usermod -d /var/lib/mysql mysql
sudo service mysql start
```

Allow remote root login

```sh
sudo mysql -u root -pMyPassword  -e "UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE User = 'root'; FLUSH PRIVILEGES;"
```

## PhpMyAdmin

```sh
sudo apt-get install -y phpmyadmin
    #> Use apache2
    #> Configure db with dbconfig-common: Yes
    #> Random password (leave password blank)
```

```sh
sudo service mysql restart && sudo service apache2 restart
```

Now you can login into phpmyadmin with username `root` and password `MyPassword`.

---

## Additional (optionally)

### Disallow root login remotely

```sh
sudo mysql -u root -pMyPassword  -e "UPDATE mysql.user SET plugin = 'auth_socket' WHERE User = 'root'; FLUSH PRIVILEGES;"
```
_* If remotely login for root is disallowed then you need to create new MySql user, otherwise you will not be able to login into PhpMyAdmin._

### LAMP Control Aliases

```sh
cd ~ && touch .bash_aliases
echo 'alias lampstatus="sudo service apache2 status ; sudo service mysql status"' >> .bash_aliases
echo 'alias lampstart="sudo service mysql start ; sudo service apache2 start"' >> .bash_aliases
echo 'alias lampstop="sudo service mysql stop ; sudo service apache2 stop"' >> .bash_aliases
echo 'alias lamprestart="lampstop ; lampstart"' >> .bash_aliases
```

Now you need to logout/login and then you can use `lampstatus`, `lampstart`, `lampstop` and `lamprestart` for controling LAMP stack.

### Change document root of Apache2

In this example document root will be changed from `/var/www/html` to `~/www`

```sh
cd ~
mkdir www
sudo sed -i "s;/var/www;$HOME/www;g" /etc/apache2/apache2.conf
sudo sed -i "s;/var/www/html;$HOME/www;g" /etc/apache2/sites-available/000-default.conf
```

Now you can go to `~/www` and create `index.html`

