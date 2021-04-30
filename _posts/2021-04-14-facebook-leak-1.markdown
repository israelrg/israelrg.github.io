---
layout: post
title:  "Facebook leak #1"
date:   2021-04-14
last_modified_at: 2021-04-14
categories: [Facebook leak]
tags: [Facebook leak]
---

Hace unos días se filtraron leaks de la base de datos de facebook. Esa información es pública y cualquiera puede descargarla. 
La información está dividida en países y en total son unos 14GB de datos comprimidos en .zip. Calculo que más o menos llegará a los 20GB de información en RAW. 
Estoy creando una web para que la gente pueda saber si su información esta comprometida y ver los datos concretos. 
De momento lo estoy haciendo con la base de datos de España, unos 450MB en .txt haciendo un total de unas 10.000.000 de cuentas comprometidas.

![imagen](/assets/screenshots/14_04_2021_1.png)

Entre los datos que nos encontramos están el Nº de teléfono, ID Facebook, nombre y apellidos, localización, descripción, cuentas de correo, sexo y un etc más. Los campos estas separados por dos puntos ":" lo que hace muy fácil de entender. Por alguna razón deben de haber datos que faltan y algunos campos están separados por varios puntos dobles. Necesitamos limpiar el archivo antes de colocarlo en nuestra base de datos SQL. En mi caso estoy programándolo en MYSQL.
Para limpiarlo se me ocurrió una forma limpia de hacerlo y se trata de convertir cada "::" en ":". Si le pasamos ese filtro varias veces obtendremos siempre como módulo un ":". Easy.
{% highlight ruby %}
sed -i 's/::/:/g' archivo.txt
{% endhighlight %}
Podemos hacerlo con un script que lo haga iteradamente.
Siguiente reto esta en el fitting de las variables dentro de la base de datos. En mi caso y únicamente de prueba lo hice a lo bruto: Todo varchar con longitud de hasta 300 caracteres. ¿Podría convertir los Nº de tlf y el ID a int? Si, pero hacer fitting a tanta información es sinonimo de que cada 20.000 lineas te de un error.

### Private env
La base de datos la tenia configurada, pero se ejecutaba en modo seguro reestrigiendome importar los datos. Después de indagar, solamete habia que neutralizar la variables poniendo private_env=" " detro de mysql.init, debajo de mysqld. Con eso, copias el archivo a /var/lib/mysql-files/ y solucionado.

### Conexion closed.
Mysql tarda en importar tanta información. Tanto que por seguridad mysql cierra la conexión si queda mucho tiempo en iddle para que no se corrompa nada. Seguramete puedes modificar estos parametros y meterlos a lo bruto. Mi solución fue dividir en archivo en archivos de 1.000.000 de lineas cada uno. Para ello tenemos el comando:
{% highlight ruby %}
split -l 1000000 archivo.txt
{% endhighlight %}


Por defecto split nos divide el archivo tomando el binario en si. Con -l toma como referencia el final de la linea, con lo que no se corrompe. 

