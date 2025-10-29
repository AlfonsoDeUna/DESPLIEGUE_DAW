c# Configurar el https

## Antes:

Prepara un directorio dentro de /var/www/website
```
sudo mkdir /var/www/website
```
## PASOS PARA LA PRÁCTICA
```

HABILITAR MOD_SSL
sudo a2enmod ssl

REINICIAMOS APACHE
sudo systemctl restart apache2

CREAR CERTIFICADO SSL
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt

CONFIGURAR APACHE PARA USAR SSL
sudo nano /etc/apache2/sites-available/soporte.website.local.conf

<VirtualHost *:443>
...............
	SSLEngine on
	SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
	SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

COMPROBAR QUE NO HAYA ERRORES EN ARCHIVOS DE CONFIGURACIÓN
sudo apache2ctl configtest

RECARGAR APACHE
sudo systemctl reload apache2

REDIRIGIR TRÁFICO PUERTO 80 AL 443
sudo nano /etc/apache2/sites-available/soporte.website.local.conf

<VirtualHost *:80>
	ServerName soporte.website.local
	ServerAlias www.soporte.website.local
	DocumentRoot /var/www/website
#	Redirect / https://soporte.website.local/
</VirtualHost>

COMPROBAR QUE NO HAYA ERRORES EN ARCHIVOS DE CONFIGURACIÓN
sudo apache2ctl configtest

RECARGAR APACHE
sudo systemctl reload apache2
```
