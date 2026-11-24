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
