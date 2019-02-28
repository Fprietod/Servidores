# Administración de servicios en red
Manual y servidor desarrollador por:
* Felipe Prieto de la Cruz

## Configurar servidor en Ubuntu 18.04

### Índice
1. Requerimientos----------------------------------------------------------------1
2. Introducción------------------------------------------------------------------1--
3.  Preparando el servidor ------------------------------------------------------3-10
4.  Habilitando servidor ---------------------------------------------------13-14
5.  Restricción de IP por segmento y usuario -----------------------14-19
6.  Configurando puertos y certificados HTTPS -----------------------19-21
7.  Personalización de páginas de error---------------------------23-24
8.  Nivel de logs-----------------------------------------------24-25
9.  Referencias-----------------------------------------------------25
---
## Requerimientos
* Apache 2
* Ubuntu 18.04
* Conocimientos en editor nano, Vim o VI
* OpenSSL 1.1.1


---
## Introducción

Este manual está diseñado para poder configurar un servidor en Ubuntu 18.04 cumpliendo los siguientes requisitos:
    * Contenedor virtual por IP y por dominio
       * Restringir acceso al recurso por dirección IP del cliente
       * Restringir acceso al recurso por segmento de red
        * Restringir acceso al recurso por nombre de usuario (grupo de usuarios)/clave de acceso
    * Configuración de puerto de operación
    * Servidor de aplicación utilizando el protocolo HTTPS
       *  Definición de certificados /llaves de operación
        * Certificados auto firmados
        * Certificados firmados por un tercero (Autoridad certificadora)
    * Personalización de páginas de error para todos los sitios
        * Configurar al menos 3 Diferentes tipos de errores del sitio 
    * Configuración de archivos de bitácoras y mensajes de error.
    * Resumen de operación de forma dinámica (Sitios, solicitudes, estado del sistema y recursos consumidos)
        * Mostrar resumen de conexiones
        * Mostrar resumen de consumo de recursos (Memoria/procesador/tiempo de ejecución) 
        
##### Protocolo HTTP
El Protocolo de Transferencia de HiperTexto (Hypertext Transfer Protocol) es un sencillo protocolo cliente-servidor que articula los intercambios de información entre los clientes Web y los servidores HTTP. La especificación completa del protocolo HTTP 1/0 está recogida en el RFC 1945.
##### Protocolo HTTPS
El Protocolo seguro de transferencia de hipertexto (en inglés: Hypertext Transfer Protocol Secure o HTTPS), es un protocolo de aplicación basado en el protocolo HTTP, destinado a la transferencia segura de datos de Hipertexto, es decir, es la versión segura de HTTP.
##### OpenSSL
OpenSSL es un proyecto de software libre basado en SSLeay, desarrollado por Eric Young y Tim Hudson. Consiste en un robusto paquete de herramientas de administración y bibliotecas relacionadas con la criptografía, que suministran funciones criptográficas a otros paquetes como OpenSSH y navegadores web
#### Virtual Host
La unidad básica que describe a un sitio o dominio es denominada virtual host (ó alojamiento virtual en español). Esta designación permite al administrador hacer uso de un servidor para alojar múltiples dominios o sitios en una única interfaz o IP utilizando un mecanismo de coincidencias.

---
#### Administrador en la consola de Ubuntu
Antes de empezar es necesario ser root en la terminal, si estás usando LIve CD de Ubuntu el comando para ser root es:
```shell
sudo -i
```


###### 1 Parte la configuración del servidor

Lo primero va a ser instalar apache.
```shell
 sudo apt-get install apache2 <-- Este comando nos sirve para ver su tiene instalados los repositorios.
```
### Iniciamos el servidor.
```shell
"service apache2 start" nos va a servir para inicar el servicio
```
![enlace en línea](https://i.ibb.co/3Ft4gmw/1.png)
## Comprobamos que este corriendo el servidor.
```shell
 systemctl status apache2.
 ```
 ![enlace](https://i.ibb.co/DYbWDM6/2.png)
----
##### Creación del entorno para nuestro servidor.
En la consola vamos al directorio donde alojaremos nuetro sitio y crearemos el nombre de nuestra página web.
```shell
cd /var/www# mkdir primerhttp.com
```
![enlace](https://i.ibb.co/NjjfMHy/3.png)
Después agregamos nuestros archivos html para nuestro sitio.
```shell
cd /var/www/primerhttp.com# nano index.html
```
![enlace4](https://i.ibb.co/xMj1cSH/4.png)
![enlace5](https://i.ibb.co/d0mC289/5.png)
##### Configuraremos el host virtual.
Nos vamos al directorio:
```shell
/etc/apache2/sites-available/
```
Creamos el archivo de configuración para nuestros host virtuales.
Un archivo guía de nuestro sitio es el: "000-default.conf", pero nosotros podemos crear el nuestro. Escribiendo servidores.com.conf
Como siguiente paso configuramos la información de nuestro archivo donde indicaremos la ubicación de los archivos fuente de cada sitio, donde se almacenan los logs y el nivel.
![enlace6](https://i.ibb.co/GRKPX8q/6.png)
```bash
<VirtualHost *:80>
ServerName primerhttp.com
ServerALias www.primerhttp.com
ServerAdmin webmaster@primer_http.com
DocumentRoot /var/www/primerhttp.com
Customlog /var/log/apache2/primerhttp.com/acces.log common
ErrorLog /var/log/apache2/primerhttp.com/error.log
</VirtualHost>

<VirtualHost *:80>
ServerName segundohttp.com
ServerALias www.segundohttp.com
ServerAdmin webmaster@segundo_http.com
DocumentRoot /var/www/segundohttp.com
Customlog /var/log/apache2/segundohttp.com/acces.log common
ErrorLog /var/log/apache2/segundohttp.com/error.log
</VirtualHost>
```
![enlace7](https://i.ibb.co/t4qqBFc/7.png)
#### Creamos las carpetas donde alojaremos los logs de cada sitio.
```shell
/var/log/apache2# mkdir primerhttp.com
/var/log/apache2# mkdir segundohttp.com
/var/log/apache2# mkdir tercerhttp.com
```
![enlace8](https://i.ibb.co/J2XfWc6/8.png)
##### Modificamos los permisos de nuestros sitios.
```shell
chown -R www-data:www-data /var/www/primerhttp.com/
chown -R www-data:www-data /var/www/segundohttp.com/
chown -R www-data:www-data /var/www/tercerhttp.com/
```
![enlace9](https://i.ibb.co/rH3b6g7/9.png)
##### Debemos agregar las direcciones de los hosts a nuestro archivo.
```shell
/etc/hosts/
```
Con sus respectivos nombres de dominio.
![enlace10](https://i.ibb.co/C79cRZq/10.png)
##### Habilitando nuestros servidores.
Para poder habilitar nuestros servidores utilizaremos el comando _a2ensite_
```shell
a2ensite servidores.com.conf
```
![enlace11](https://i.ibb.co/LY5BNr1/11.png)
Reiniciamos el servicio y comprobamos que tengamos acceso el comando para poder reiniciar nuestro servidor es:
```shell
systemctl reload apache2
```
![enlace12](https://i.ibb.co/crh0wMx/servidores.png)
##### Restricción por IP del cliente.
Para poder restringir el acceso por una IP específica, necesitamos modificar el archivo "apache2.conf" ubicado en el directorio /etc/apache2/
Para esta parte se va a restringir el acceso mediante el directorio.
```bash
<Directory /var/www/primerhttp.com>
  Options ALL
  AllowOverride ALL
  <RequireALL>
         Require all granted
         Require ip 10.100.96.38
         </RequireALL>
</Directory>
```
![enlace13](https://i.ibb.co/2qxbDs6/13.png)
En otro caso podemos determinar que la IP que asignamos no pueda acceder con un simple cambio:
```bash
<Directory /var/www/primerhttp.com>
  Options ALL
  AllowOverride ALL
  <RequireALL>
         Require all granted
         Require not ip 10.100.96.38
         </RequireALL>
</Directory>
```
El resultado debe salir como la siguiente imagen:
![enlace14](https://i.ibb.co/CQVrqPD/14.png)
##### Restricción por segmento de red.
Volvemos a abrir nuestro archivo "apache2.conf que se ubica en /etc/apache2".
En las lineas "Require not ip agregamos las ip que deseemos bloquear"
```bash
<Directory /var/www/primerhttp.com>
  Options ALL
  AllowOverride ALL
  <RequireALL>
         Require all granted
         Require not ip 10.100.96.38 192.168.1.67
         </RequireALL>
</Directory>
```
Insertar imagen aquì.
#### Restricción por usuario.
Para está restricción es necesario que creemos un archivo donde agregaremos los usuarios, su respectiva contraseña.
El respectivo comando que usaremos para generar la contraseña es:
```shell
 htpasswd -c /etc/apache2/.htpasswd leo
 ```

 ![enlace15](https://i.ibb.co/JxbcXmY/Usuarios-1.png)
 Procedemos a ver lo que contiene el archivo
 ![enlace16](https://i.ibb.co/VVgGRDr/16.png)
 El siguiente procedimiento que debemos realizar es agregar nuestro archivo a nuestro directorio para indicar que solo esos usuarios van a poder acceder.
 ![enlace17](https://i.ibb.co/FWYcyG1/17.png)
 Reiniciamos el servidor para poder ver los cambios que se han aplicado y ahora al momento de ingresar nos pedirá el usuario y contraseña.
 ![enlace18](https://i.ibb.co/s32zBtM/18.png)
 ![enlace19](https://i.ibb.co/zQRjYfJ/Usuarios1.png)
 #### Configuración de puertos de operación
 Cuando httpd se ejecuta, se mapea a una dirección y un puerto en la máquina local, y espera a recibir peticiones. Por defecto, escucha en todas las direcciones de la máquina. Ahora bien, se le puede especificar que escuche en un determinado puerto, o en una sola dirección IP especifica, o una combinación de ambos. A menudo esto se combina con la característica de los Hosts virtuales, que determina como responde el httpd a diferentes direcciones IP, nombres de máquinas y puertos.
 Vamos a configurar nuestrs puertos escucha y el puerto que estará escuchando para nuestro protocolo HTTPS. 
 Para este caso utilizaremos el puerto 80 para el protocolo de HTTP y el 443 para HTTPS.
 Modificamos el archivo: ports que se encuentra ubicado.
 ```bash
 /etc/apache2/
 ```
 ![Enlace21](https://i.ibb.co/j5zmw3Q/puerto-de-operacion.png)
 #### Configuración HTTPS Certificados
 Procederemos a crear nuestro certificado para esto nos vamos a la ruta: /etc/apache2# y escribimos el siguiente comando:
 ```shell
 openssl req -new -x509 -days 365 -keyout sitios.key -out sitios.crt -nodes -subj '/O=Company/OU=ESCOM/CN=www.primerhttp.com'
 ```
 ![enlace20](https://i.ibb.co/JKdD19f/certificado1.png)
 Dependiendo del puerto que se haya configurado para que pueda recibir las peticiones HTTPS, vamos a modificar el archivo servidores.com.conf
 ```bash
 <VirtualHost *:80>
ServerName primerhttp.com
ServerALias www.primerhttp.com
ServerAdmin webmaster@primer_http.com
DocumentRoot /var/www/primerhttp.com
Customlog /var/log/apache2/primerhttp.com/acces.log common
ErrorLog /var/log/apache2/primerhttp.com/error.log
SSLEngine On
SSLCertificateFile /etc/apache2/sitios.crt
SSLCertificateKeyFile /etc/apache2/sitios.key
</VirtualHost>
```
![enlace20](https://i.ibb.co/jrM3G3C/certificado2.png)
 Ingresamos a nuestro sitio para comprobar que está funcionando el certificado, así debe aparecernos:
 ![enlace21](https://i.ibb.co/jRHS01T/certificado3.png)
 #### Personalización de páginas de error.
 Para personalización de las páginas de error necesitamos acudir a la siguiente ruta:
 ```bash
 /etc/apache2/sites-available/ 
 ```
 Agregamos las siguientes lineas.
 ```bash
 ErrorDocument 401 /401.html
 ErrorDocument 403/403.html
 ```
 Así respectivamente con las páginas de error que deseé  agregar.
 ![Enlace20](https://i.ibb.co/chDP4rK/Captura.png)
 ### Nivel de logs
 El servidor Apache nos permite activar un módulo llamado mod_log_config, una de las ventajas de activarlo es que nos permite modificar el nivel de logs y hacerlos personalizados indicando las directivas que nos proporciona la documentación oficial de apache [1]. En general este módulo nos permite activar tres directivas, la de TransferLog, LogFormat y CustomLog.  Para este caso hemos activado las siguientes banderas cuya descripción se anexa
 Podemos consultar la siguiente tabla.
 [Configuración Logs](http://httpd.apache.org/docs/current/mod/mod_log_config.html)
 ### Referencias
 [¿Cómo configurar Virtual Hosts de Apache en Ubuntu 16.04?](https://www.digitalocean.com/community/tutorials/como-configurar-virtual-hosts-de-apache-en-ubuntu-18-04-es)
 [SSL en Ubuntu](https://www.howtoforge.com/tutorial/how-to-install-openssl-from-source-on-linux/)
 [Directrices de configuración en httpd.conf - MIT](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-es-4/s1-apache-config.html)
 [Restringir acceso por IP en Apache](http://www.adminso.es/index.php/Restringir_acceso_por_IP_en_Apache)
 [mod_log_config - Apache HTTP Server Version 2.4](http://httpd.apache.org/docs/current/mod/mod_log_config.html)
 [Mapeo de Direcciones y Puertos.](https://httpd.apache.org/docs/trunk/es/bind.html)
 [Configurar página de error 404 en Apache](https://desarrolloweb.com/articulos/configurar-pagina-error-404-apache.html)










