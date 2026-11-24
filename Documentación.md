# PRÁCTICA: CONFIGURACIÓN DE HTTPS EN APACHE2
## 1. Resumen
En esta práctica he aprendido a configurar el protocolo HTTPS en un servidor web Apache2 utilizando Ubuntu.

Para ello, he trabajado con el servidor Apache que ya tenía instalado de prácticas anteriores. He habilitado los módulos necesarios para utilizar conexiones seguras y he creado un certificado SSL/TLS autofirmado.

Después, he configurado un VirtualHost para que Apache pudiera utilizar el puerto 443 y mostrar mi página web mediante HTTPS.

Por último, he comprobado que la página funcionaba correctamente y he configurado una redirección automática de HTTP a HTTPS.

## 2. Investigación teórica
### 2.1. ¿Qué es HTTPS?
HTTPS es un protocolo que permite acceder a páginas web de forma segura. Utiliza HTTP junto con SSL/TLS para cifrar la información que se intercambia entre el navegador y el servidor.

Gracias a este protocolo, los datos que se envían, como contraseñas o información personal, están protegidos frente a posibles accesos no autorizados.

### 2.2. Importancia de HTTPS

HTTPS es importante porque:

* Protege la información que se transmite entre el usuario y el servidor.

* Evita que otras personas puedan leer fácilmente los datos.

* Ayuda a comprobar la identidad del servidor.

* Reduce el riesgo de que la información sea modificada durante la comunicación.

### 2.3. Tipos de certificados SSL/TLS

Existen diferentes tipos de certificados. En esta práctica hemos trabajado con un certificado autofirmado.

**Certificado autofirmado:**

Es un certificado que genera el propio administrador del servidor, sin necesidad de acudir a una autoridad certificadora. Se utiliza principalmente en prácticas y entornos de pruebas.

Los navegadores muestran un aviso de seguridad porque no está firmado por una autoridad de confianza.

**Certificado de una autoridad certificadora (CA):**

Es un certificado emitido por una entidad de confianza, como Let's Encrypt. Los navegadores pueden comprobar su autenticidad y normalmente no muestran avisos de seguridad cuando está correctamente configurado.


### 2.4. Módulos necesarios de Apache

Para realizar esta práctica hemos utilizado dos módulos:

* **SSL:** permite configurar conexiones seguras mediante SSL/TLS.

* **Headers:** permite gestionar las cabeceras HTTP del servidor.

También hemos utilizado el puerto 443, que es el puerto habitual para las conexiones HTTPS.


## 3. Implementación técnica
### 3.1. Comprobar Apache

En primer lugar, he comprobado que Apache2 estaba instalado y funcionando correctamente.

Para ello he utilizado el siguiente comando:

```
sudo systemctl status apache2
```

<img width="589" height="30" alt="ComprobarApache1" src="https://github.com/user-attachments/assets/ef234f16-97cb-4245-a9a2-26b041a22c3b" />
<img width="806" height="423" alt="ComprobarApache2" src="https://github.com/user-attachments/assets/1f4790fd-8b9b-48dd-b137-2ca5edc43bab" />

### 3.2. Habilitar los módulos SSL y Headers

A continuación, he habilitado los módulos necesarios para configurar HTTPS.

Primero he ejecutado:

```
sudo a2enmod ssl
```

<img width="462" height="36" alt="ComprobarApacheModulos1" src="https://github.com/user-attachments/assets/0ba1cdbb-9169-4d08-b9ca-041c8677479b" />
<img width="807" height="254" alt="ComprobarApacheModulos2" src="https://github.com/user-attachments/assets/fd3f30ea-fd65-48af-b41a-8ca35c0daf9c" />

Después:

```
sudo a2enmod headers
```

<img width="529" height="31" alt="ComprobarApacheModulos3" src="https://github.com/user-attachments/assets/9e2eebe8-587a-4278-9a0a-8f6a205d370b" />
<img width="601" height="91" alt="ComprobarApacheModulos4" src="https://github.com/user-attachments/assets/66ba1127-1d0b-40c1-a1ba-1695ada39121" />

Por último, he reiniciado Apache:

```
sudo systemctl restart apache2
```

<img width="601" height="30" alt="CompruebaApacheModulos5" src="https://github.com/user-attachments/assets/878b65da-7e36-4b8e-83c0-e5bef25b4830" />
<img width="668" height="49" alt="CompruebaApacheComprobarModulo6" src="https://github.com/user-attachments/assets/e1d969df-7dbb-4bd7-99a2-3b8c8888b7ab" />

### 3.3. Crear el certificado autofirmado

Para guardar el certificado y la clave privada, he creado una carpeta dentro de Apache:

```
sudo mkdir -p /etc/apache2/ssl
```

<img width="693" height="32" alt="CrearCertificado1" src="https://github.com/user-attachments/assets/6808aac3-adb0-43bf-9369-0da72121e4d8" />
<img width="568" height="52" alt="CrearCertificado2" src="https://github.com/user-attachments/assets/8cf7c68c-d008-4bbb-a8d4-e6c5719aef83" />

Después he generado el certificado autofirmado utilizando OpenSSL:

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt -subj "/CN=192.168.21.229" -addext "subjectAltName=IP:192.168.21.229"
```

<img width="1213" height="52" alt="CrearCertificado3" src="https://github.com/user-attachments/assets/dab3863f-4e69-4c5d-81ea-bce1ec16495b" />
<img width="1213" height="389" alt="CrearCertificado4" src="https://github.com/user-attachments/assets/000d912c-ef95-45de-9909-63c11f89633a" />

Este comando genera dos archivos:

**apache.key:** contiene la clave privada del servidor.

**apache.crt:** contiene el certificado digital.

La opción _-days 365_ establece una validez de 365 días y _-newkey rsa:2048_ genera una clave RSA de 2048 bits.

Después he protegido la clave privada mediante este comando:

```
sudo chmod 600 /etc/apache2/ssl/apache.key
```

<img width="687" height="28" alt="CrearCertificado5" src="https://github.com/user-attachments/assets/8011bad4-16eb-495c-98cc-31efec8e4cb0" />
<img width="693" height="40" alt="CrearCertificado6" src="https://github.com/user-attachments/assets/33313d2b-d42a-4494-bbc9-be34509aeae1" />

### 3.4. Configurar el VirtualHost HTTPS

Una vez creado el certificado, he configurado un nuevo VirtualHost para que Apache pudiera atender conexiones HTTPS mediante el puerto 443.

He creado el archivo de configuración con:

```
sudo nano /etc/apache2/sites-available/gci-ssl.conf
```

<img width="834" height="40" alt="ConfighttpsApache1" src="https://github.com/user-attachments/assets/0b32a23b-4b36-4cb1-9b55-52fd82b49d1d" />

Dentro he añadido la siguiente configuración:

```
<VirtualHost *:443>

    ServerName 192.168.21.229

    DocumentRoot /var/www/gci

    SSLEngine on

    SSLCertificateFile /etc/apache2/ssl/apache.crt
    SSLCertificateKeyFile /etc/apache2/ssl/apache.key

    <Directory /var/www/gci>
        AuthType Basic
        AuthName "Acceso restringido"
        AuthBasicProvider file
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>

</VirtualHost>
```

<img width="885" height="483" alt="ConfighttpsApache2" src="https://github.com/user-attachments/assets/9f75ca6f-8531-4709-bb1a-212a902b5c32" />

Esta configuración indica que:

Apache escucha las conexiones en el _puerto 443_.

La página web se encuentra en _/var/www/gci_.

Se activa el funcionamiento de _SSL_.

Se indican las rutas del certificado y de la clave privada.

Se mantiene la autenticación mediante usuario y contraseña de la práctica anterior.

Después he activado el sitio:

```
sudo a2ensite gci-ssl.conf
```

<img width="705" height="18" alt="ConfighttpsApache3" src="https://github.com/user-attachments/assets/620d0ac9-5bba-4dbd-9577-1ad4e6128e15" />
<img width="537" height="110" alt="ConfighttpsApache4" src="https://github.com/user-attachments/assets/5c32460b-1cbc-4c60-9393-f1c00d65204f" />

Y he comprobado que la configuración no tenía errores:

```
sudo apache2ctl configtest
```

<img width="555" height="32" alt="ConfighttpsApache5" src="https://github.com/user-attachments/assets/935fe5b2-04fc-4298-9d1c-84f14d40601a" />
<img width="1046" height="93" alt="ConfighttpsApache6" src="https://github.com/user-attachments/assets/b8ddc85e-2e6e-49a0-99bc-a55051fc2abc" />

Y me daba error cuando el resultado tendría que ser Syntax OK.
Y como ves me ponía ahí el error, pero yo todavía no me había dado del todo cuenta entonces pues bueno empecé a hacer pruebas para ver donde estaba el error y cual era el error.

Primero hice:
```
sudo ls -l /etc/apache2/ssl/
```

<img width="604" height="115" alt="ConfighttpsApache7" src="https://github.com/user-attachments/assets/0bf7bedf-e128-4ea2-af3d-6dc316fb144f" />

Que es para ver si de verdad se habían creado el certificado y la llave que creamos antes, y como vemos están creados los dos.

Después:
```
sudo ls -lh /etc/apache2/ssl/
```

<img width="562" height="110" alt="ConfighttpsApache8" src="https://github.com/user-attachments/assets/b8eaf5b0-6f9d-4ade-946e-db101fccb9cc" />

Que esto nos servía para ver si los dos archivos que necesitamos contenían cosas dentro, si ocupaban algo de espacio es que funcionaba bien.

Luego tuve que hacer esto:

```
sudo cat /etc/apache2/sites-enabled/gci-ssl.conf
```

<img width="808" height="37" alt="ConfighttpsApache9" src="https://github.com/user-attachments/assets/cdc200e5-2158-4494-a83d-60dea302a842" />
<img width="775" height="463" alt="ConfighttpsApache10" src="https://github.com/user-attachments/assets/97e99309-81a6-42d4-8e43-53bdff2fe554" />

Y aquí es cuando me di cuenta de mi error que en vez de _etc_ había puesto _ect_ y por eso me daba error, asi que lo modifiqué, lo guarde y comrpobé que ahora no me diera ningún error.

<img width="546" height="24" alt="ConfighttpsApache11" src="https://github.com/user-attachments/assets/6cc5c7b6-de52-4b79-8802-d2bc40735084" />
<img width="1219" height="79" alt="ConfighttpsApache12" src="https://github.com/user-attachments/assets/963b4f63-e53a-45de-8ea0-7e731d72fb7e" />

Y como ves ahora no me daba ningún error me daba todo bien, todo correcto.

Por último, he recargado Apache:

```
sudo systemctl reload apache2
```

<img width="618" height="26" alt="ConfighttpsApache13" src="https://github.com/user-attachments/assets/c56f1bba-3353-4ec4-bad5-dc04e93940ed" />

Y también para comprobar que mi puerto y que todo estaba bien la ultima comprobación que hice fue esta.

```
sudo ss -tlnp | grep :443
```

<img width="634" height="47" alt="ConfighttpsApache14" src="https://github.com/user-attachments/assets/42740539-8b94-4250-a661-bbb006b7c5ae" />

Y comprobamos que efectivamente todo iba bien.

<img width="1205" height="67" alt="ConfighttpsApache15" src="https://github.com/user-attachments/assets/3b6d58fa-ff6d-40d7-993d-742c7679d718" />

### 3.5. Comprobar el acceso mediante HTTPS

Para comprobar que la configuración funcionaba, he abierto Firefox y he introducido la dirección:

```
https://192.168.21.229
```

<img width="1203" height="450" alt="Confighttpsapache16" src="https://github.com/user-attachments/assets/60235759-c3f4-40f8-b50d-6e4f565ef8d4" />
<img width="1206" height="646" alt="ConfighttpApache18" src="https://github.com/user-attachments/assets/e34946b4-ed90-4a66-b130-d8c443adad0f" />

El navegador mostró un aviso de seguridad porque el certificado utilizado es autofirmado y no pertenece a una autoridad certificadora reconocida.

<img width="887" height="387" alt="ConfighttpsApache18" src="https://github.com/user-attachments/assets/4b6447a7-5c49-4bb8-bbfe-a3c67c7d4677" />

Después de aceptar el aviso, apareció la ventana de autenticación. He introducido el usuario y la contraseña configurados anteriormente y he podido acceder a mi página web personalizada.

<img width="1217" height="359" alt="ConfighttpsApache19" src="https://github.com/user-attachments/assets/cdab0d99-a4b3-40e1-9e6f-37d341895d04" />

De esta forma he comprobado que el acceso mediante HTTPS funciona correctamente.






