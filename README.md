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
* MySQL (https://www.mysql.com/downloads/)
* PHP (http://www.php.net/downloads)
* Drush (https://www.drupal.org/project/drush)

# Instalación

Dentro de la carpeta que se crea ejecutar:

    sh scripts/build.sh path/al/webroot

Luego desde el browser entrar a path/al/webroot y seguir los pasos de la instalación de Drupal.

## En ARSAT

Para instalar una AR-Distro en ARSAT, lo primero es crear una instancia con el sistema operativo CentOS 6. Luego ejecuta los siguientes comandos via SSH como root. Estos comandos están tomados en parte de [esta guía](https://extravm.com/billing/knowledgebase/34/Install-LAMP-on-CentOS-6-MySQL-55-PHP-56.html).

Actualizar YUM e instalar algunas herramientas que vamos a necesitar:

    yum update
    yum install nano iptables-services git

Instalar y activar Apache:

    yum install httpd
    service httpd start

Habilitar el acceso al servidor a través del puerto 80:

    nano /etc/sysconfig/iptables
    // Agregar antes del primer INPUT: -A INPUT -p tcp --dport 80 -j ACCEPT
    service iptables restart

Instalar MySQL 5.5:

    rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
    yum remove mysql-libs*
    yum install mysql55w-server
    /usr/bin/mysql_secure_installation

Instalar PHP 5.6 y algunas extensiones útiles:

    yum install php56w php56w-mbstring php56w-mysql php56w-sqlite php56w-gd php56w-xml php56w-cli

Instalar [Composer](https://getcomposer.org/download/):

    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"

Instalar Drush:

    php composer.phar require drush/drush

Instalar una AR-Distro:

    git clone https://github.com/argob/AR-Distro
    sh scripts/build.sh path/al/webroot

Luego visitar el webroot y seguir las instrucciones de instalación de Drupal.
