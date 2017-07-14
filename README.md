1.- Crear el box de Vagrant

```
vagrant init ubuntu/trusty64
```

2.- Instalacion del plug in y configuracion del proxy ( si es necesario )

```
vagrant plugin install vagrant-proxyconf

```
se agrega al Vagrantfile

```
config.proxy.http     = "http://yourproxy:8080"
config.proxy.https    = "http://yourproxy:8080"
config.proxy.no_proxy = "localhost,127.0.0.1"
```

3.- Configuracion de acceso al host 

descomentar la linea (opcional)

```
config.vm.network "private_network", ip: "x.x.x.x"
```

y agregar la ip a la configuracion del proxy del equipo 
iniciamos la maquina

```
vagrant up
```

4.- Instalacion de PHP7, nginx, MariaDB y varios

```
sudo apt-get install -y language-pack-en-base
sudo LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install zip unzip
sudo apt-get install -y php7.0 php7.0-fpm php7.0-mysql php7.0-zip php7.0-gd
sudo apt-get install nginx git	

sudo apt-get install mariadb-server 
```

recordar la contraseña del root de la DB

```
sudo apt-get install mcrypt php7.0-mcrypt
sudo apt-get install -y php7.0-mbstring php7.0-xml --force-yes

sudo apt-get install php7.0-curl php7.0-json
```

6.- Modificar la configuracion de php 

```
sudo nano /etc/php/7.0/fpm/php.ini
```

descomentar y modificar la linea para que quede asi

```
cgi.fix_pathinfo=0
```

reiniciar php7 fpm

```
sudo service php7.0-fpm restart
```

--instalacion de laravel y configuracion de nginx

```
sudo mkdir -p /var/www/laravel
```

respaldamos la config inicial

```
sudo mkdir ~/Backups
sudo cp /etc/nginx/sites-available/default ~/Backups/default
```

editamos el archivo de configuracion de nginx 

```
sudo nano /etc/nginx/sites-available/default
```

y lo dejamos de la siguiente manera

```
server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;

        root /var/www/laravel/public;
        index index.php index.html index.htm;

        # Make site accessible from http://localhost/
        server_name <Your Domain name / Public IP Address>;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ /index.php?$query_string;
                # Uncomment to enable naxsi on this location
                # include /etc/nginx/naxsi.rules
        }
        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}
```

reiniciamos nginx (despues de reiniciar deberia arrojar un error 404 y no la pantalla de nginx)

```
sudo service nginx restart
```

instalamos composer

```
cd ~
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

instalamos laravel

```
sudo composer create-project laravel/laravel /var/www/laravel
```

modificamos los permisos

```
sudo chown -R :www-data /var/www/laravel
sudo chmod -R 775 /var/www/laravel/storage
```


