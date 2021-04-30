---
layout: post
title:  "ssh, mysql, nginx"
date: 2021-03-29
last_modified_at: 2021-03-29
categories: [Servidores]
tags: [ssh mysql nginx]
---


Escribiré un poco de todo por que no tengo ganas de mirar lxc.
Estos días estuve probando a hostear mi propio servidor para writefreely y no tener que pagar la suscripción. Estuve haciendo pruebas en una maquina virtual en Azure y creo que sería interesante escribir sobre como voy en ello.

La creación de la maquina es francamente sencilla. Lo hice con la interfaz que te dan ellos en Azure Portal. Por la linea de comandos propia de Azure no me puse a investigar. Al crear una nueva cuenta te dan 170$ para gastar en un mes, lo suficiente para hacer pruebas y jugar con servidores remotos.

## Conexiones ssh
Una vez creas la maquina importante abrirle los puertos para http y https. Se hace en una ventana que pone **networking**, easy. Nos descargamos un archivo *.pem. Super importante, con el haremos la conexión:

{% highlight mysql %}
ssh -i archivo.pem user@ip
{% endhighlight %}

Y con eso ya estariamos dentro.
Para enviar archivos al servido directamente con ssh hacemos:

{% highlight mysql %}
scp -i archivo.pem files user@ip:/home/user
{% endhighlight %}

Los dos puntos para especificar la ruta. Si hacemos a /root nos va a dar error de permiso, tenerlo en cuenta a la hora de iniciar sesion ssh.

## Crear usuarios mysql
Para hostear yo mismo la página necesitaba configurar la base de datos.
- Crear usuarios

{% highlight mysql %}
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password'
{% endhighlight %}


- Darle todos los permisos a ese usuario

{% highlight mysql %}
GRANT ALL PRIVILEGES ON * . * to 'user'@'localhost'
{% endhighlight %}


Tener en cuenta poner localhost y no poner el nombre de la maquina. A mi me dio problemas por ello.

## Proxy inverso con NGINX
Generar la pagina me daba error. Si configuraba apache2 en el servidor y lo levantaba, me accedía siendo yo el cliente. Una mala configuración de los puertos en Azure quedaba descartado. Lo unico que me aparecia era SSL internal error y dentro del servidor me daba errores muy complejos. Con un poco de paciencia configure un proxy inverso con ngix.
La configuración esta en un archivo dentro de /etc/nginx/conf.d.
Puede haber un default.conf, en mi caso no lo había. No voy a explicar el funcionamiento de este archivo porque me tiraría buen rato explicando codigo que basicamente está en la documentación propia de nginx.
![imagen](/assets/screenshots/29_03_2021_1.png)
Lo unico que hice es transladar el trafico de mi localhost:8080 que es donde esta corriendo mi servicio al localhost:80 cada vez que se accede a la web con la ip. Podemos configurar dominios y diferentes páginas. Yo para checkear que todo funcionase puese la ip. 
PRO-TIP: Para comprobar que el archivo .conf esté bien escrito, en cosola ponemos nginx -t. Por pantalla nos debería de salir "Succesfull"
![imagen](/assets/screenshots/29_03_2021_2.png)

