# AR-Distro

Distribución Drupal gratuita del Gobierno Nacional.

Desarrollamos AR-Distro como extensión de BAdistro, realizada por el Gobierno de la Ciudad de Buenos Aires. Es un perfil de instalación de Drupal compuesto por un conjunto de módulos para instalar y configurar de forma pre-determinada con la finalidad de que todos los gobiernos tengan acceso a las funcionalidades más importantes de una web gubernamental. 

Basada en la experiencia del equipo del Gobierno de la Ciudad con el sitio web buenosaires.gob.ar, la interacción con los ciudadanos y las necesidades de las diferentes áreas de gobierno, surgió esta intención de contribuir a la comunidad Drupal y a otros gobiernos del país.

AR-Distro 1.0 contiene:

* Layout responsive: Diseño adaptable a todo tipo de dispositivo
* Carrousel: Permite filtrar por tipo de contenidos a mostrar, elegir la cantidad de items a presentar y a su vez, por categoría de noticias o tipo de trámite
Alerta: Permite elegir el tipo de alerta (success, danger, warning, info) y personalizar el texto a mostrar (admite full html)
* Shortcut o Atajo: Permite elegir icono, color, título, un texto breve y dónde enlaza
* Listado: Este es el bloque fuerte (la base de la distro), permite elegir un título, el tipo de contenido a mostrar y a su vez filtrarlo por categoría o tipo. Elegir entre 3 formatos de estilo diferentes y su orientación: horizontal o vertical. Además, tiene 2 funcionamientos dinámicos: recientes o relacionados. Recientes lista los items de manera temporal mientras que relacionados combina las otras configuraciones del bloque:
  * Si el tipo de contenido es Noticia y el listado se muestra en un Área, muestra las noticias relacionadas al área.
  * Si el tipo de contenido es Área y el listado se muestra en un Área, muestra las subáreas.
  * Si el tipo de contenido es Noticia y el listado se muestra en una Noticia, muestra noticias de la misma categoría que la noticia original.
* Campo: Permite elegir distintos tipos de campos a mostrar de un contenido determinado
* Redes Sociales: Integra timelines de Facebook o Twitter y agrega links de compartir
* Imagen destacada: Levanta el campo imagen destacada
* Galería de imágenes: Levanta el campo Imágenes de un contenido determinado. Las renderiza en una galería tipo modal (o lightbox)
* Texto: Muestra el texto (campo body) de un contenido o permite mostrar un texto personalizado
* Título: Muestra el texto (campo title) de un contenido o permite mostrar un título personalizado. Además da la opción de elegir el tag entre h1 a h6

# Requisitos

Un servidor con:

* Apache (http://www.apache.org/dyn/closer.cgi)
* MySQL 5.5 (https://www.mysql.com/downloads/)
* PHP 5.6 (http://www.php.net/downloads)
* Drush (http://docs.drush.org/en/master/install/)

# Instalación

Dentro de la carpeta que se crea ejecutar:

    sh scripts/build.sh path/al/webroot

Luego desde el browser entrar a path/al/webroot y seguir los pasos de la instalación de Drupal.

## En ARSAT (con Redhat 7)

Debido a que ARSAT está ofreciendo servidores que tienen instalado el sistema operativo Redhat 7 es necesario hacer una nueva revisión del instructivo de instalación de la distribución que se encuentra a continuación. Varios de los siguientes pasos ya estaban presentes en el instructivo de instalación en ARSAT con el sistema operativo CentOs 6. Todos los siguientes pasos deben ser ejecutados utilizando un usuario con permisos de administrador. 

Actualizar YUM e instalar algunas herramientas que vamos a necesitar:

    yum update -y
    yum install -y iptables-services git

Instalar Apache:

    yum install -y httpd
    service httpd start

Habilitar el acceso al servidor a través del puerto 80:

    iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT
    service iptables save

Remover instalaciones existentes de MySQL o MariaDB para que no entren en conflicto con la version de MySql que debemos instalar, los servidores de ARSAT ya tienen instalado MariaDB:

    yum remove mariadb mariadb-*
    yum remove mysql mysql-*

Instalar MySQL 5.5:

    rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
    yum install -y mysql55w-server
    service mysqld start
    /usr/bin/mysql_secure_installation

Actualizar la lista de repositorios de Redhat 7 ingresando aquellos repositorios que nos permita instalar la versión 5.6 de PHP y otros paquetes asociados:

    rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
    yum clean all

yum clean all es un paso necesario debido a que sin ejecutarlo yum devuelve Requires: libgmp.so.3()(64bit) al instalar PHP

Instalar PHP 5.6 y algunas extensiones útiles:

    yum install -y php56w php56w-opcache
    yum install -y php56w-mbstring php56w-mysql php56w-sqlite php56w-gd php56w-xml php56w-cli

Verificar el estado de instalación de PHP:

    php -v

El interprete de PHP mostrará un listado de advertencias y errores relacionados a la librería php56w-mysql, dentro de los cuales se encuentra el siguiente mensaje:

warning Unable to load dynamic library '/usr/lib64/php/modules/mysql.so'

Es necesario optar por instalar por la librería mysqlnd que es una versión mejorada de la tradicional php-mysql. Remover los archivos instalados de la librería php-mysql:

    yum remove php56w-mysql
    yum install php56w-mysqlnd

En cualquier directorio **privado** del servidor, clonar el repo de AR-Distro:

    git clone https://github.com/argob/AR-Distro

Dentro del directorio creado, instalar [Composer](https://getcomposer.org/download/):

    cd AR-Distro
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"

Instalar Drush 8.1:

    php composer.phar require drush/drush:8.1

Ejecutar el script de instalación de AR-Distro, especificando el path al directorio **público** del servidor que usualmente es  /var/www/html:

    sh scripts/build.sh /path/al/webroot

El propietario de los archivos de instalación debe ser el usuario que corre el servicio de apache, en este caso el usuario es apache pero puede ser httpd en otros sistemas, los permisos otorgados a los archivos deben ser de escritura, lectura, modificación y ejecución para el propietario:

    chown -R apache:apache /path/al/webroot
    chmod -R 755 /path/al/webroot

Reiniciar apache y el contexto de seguridad para archivos y directorios del webroot

    service httpd restart
    restorecon -r /var/www/html

## En ARSAT (Con CentOS 6)

Para instalar una AR-Distro en ARSAT, lo primero es crear una instancia con el sistema operativo CentOS 6. Luego ejecuta los siguientes comandos via SSH como root. Estos comandos están tomados en parte de [esta guía](https://extravm.com/billing/knowledgebase/34/Install-LAMP-on-CentOS-6-MySQL-55-PHP-56.html).

Actualizar YUM e instalar algunas herramientas que vamos a necesitar:

    yum update -y
    yum install -y iptables-services git

Instalar Apache:

    yum install -y httpd
    service httpd start

Habilitar el acceso al servidor a través del puerto 80:

    iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT
    service iptables save

Instalar MySQL 5.5:

    rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
    yum remove -y mysql-libs*
    yum install -y mysql55w-server
    /usr/bin/mysql_secure_installation

Instalar PHP 5.6 y algunas extensiones útiles:

    yum install -y php56w php56w-mbstring php56w-mysql php56w-sqlite php56w-gd php56w-xml php56w-cli

En cualquier directorio **privado** del servidor, clonar el repo de AR-Distro:

    git clone https://github.com/argob/AR-Distro

Dentro del directorio creado, instalar [Composer](https://getcomposer.org/download/):

    cd AR-Distro
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"

Instalar Drush:

    php composer.phar require drush/drush

Ejecutar el script de instalación de AR-Distro, especificando el path al directorio **público** del servidor:

    sh scripts/build.sh /path/al/webroot

Luego visitar el sitio con un navegador y seguir las instrucciones de instalación de Drupal.

