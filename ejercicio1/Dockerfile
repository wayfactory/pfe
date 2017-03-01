  # Imagen de Ubuntu basada en la que tengo en mi server:
  FROM ubuntu:14.04
  # Nos bajamos las dependencias necesarias para que funcione el WordPress por este orden:
  # apache2: Servidor web
  # php5: Lenguaje de programacion PHP
  # php5-mysql: Driver de MySql para PHP
  # supervisor: Lanzador y monitor de procesos
  # wget: Para descargarse cosicas de internet
  # A ver si las DNS de ericsson y sus mil proxies me dejan vivir en paz 
  RUN cat /etc/resolv.conf
  RUN apt-get update && apt-get -y install apache2 php5 php5-mysql supervisor wget

  # mysql-server se instala con intervención del usuario, pero como no es modo interactivo 
  # le seteamos las variables con un valor a posteriori
  # Para simplificar voy a usar como usuario y contraseña de mysql 'penaldo'
  RUN echo 'mysql-server mysql-server/root_password password penaldo' |  debconf-set-selections && \
    echo 'mysql-server mysql-server/root_password_again password penaldo' | debconf-set-selections

  # Instalamos el mysql-server
  RUN apt-get install -qqy mysql-server

  # Vamos ahora a por el Wordpress
  # Obtenemos la ultima version disponible
  RUN wget http://wordpress.org/latest.tar.gz && \
  # Descomprimimos lo que nos acabamos de bajar
    tar xzvf latest.tar.gz && \
  # Copiamos el wordpress a donde debe para que nos lo sirva apache
    cp -R ./wordpress/* /var/www/html && \
  # Quitamos el index.html que si no luego apache se atrapa y te lo sirve antes que el index.php
    rm /var/www/html/index.html

  # De esto se encargaria el supervisor, pero como se necesita crear la base de datos
  # ejecutamos a mysql en background y creamos la base de datos llamada wordpress (Tipico nombre de todo HOWTO)
  RUN (/usr/bin/mysqld_safe &); sleep 5; mysqladmin -u root -ppenaldo create wordpress

  # Reemplazamos el archivo wp-config.php (más abajo lo creamos) a la carpeta de wordpress
  # Este archivo contiene la configuracion de nuestro sitio
  COPY wp-config.php /var/www/html/wp-config.php

  # Copiamos el archivo de configuracion de supervisor (mas abajo lo creamos)
  COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

  # Le decimos al contenedor que tiene que hacer accesible el puerto 80 (en el que corre el apache)
  # para así nosotros poder acceder al mismo desde fuera
  EXPOSE 80

  # Lanzamos el supervisor como proceso foreground de Docker
  # Este se encargará de lanzar simultaneamente los demas
  CMD ["/usr/bin/supervisord"]
