## 1.Crear el Archivo de Contraseñas

Abre una terminal y ejecuta esto. Te pedirá que escribas una contraseña para usuario_admin (puedes cambiar "usuario_admin" por el nombre que quieras).

```Bash
sudo htpasswd -c /etc/apache2/.htpasswd usuario_admin
```
-c: Significa "crear". Solo usa -c la primera vez.

Si más adelante quieres añadir otro usuario, ejecutarías el mismo comando sin la -c (ej: sudo htpasswd /etc/apache2/.htpasswd otro_usuario).

## 2. Editar TU Archivo de Host Virtual 
Ahora, vamos a editar el archivo de configuración que creaste en la práctica anterior, no el que dice el tutorial.

```Bash
sudo nano /etc/apache2/sites-available/soporte.website.local.conf
```

## 3. Añadir las Directivas de Autenticación
Tu archivo se ve así (basado en tu práctica anterior). Simplemente añade el bloque <Directory> que te indico, justo antes de la línea </VirtualHost>:

Apache
```
<VirtualHost *:443>
	ServerName soporte.website.local
	ServerAlias www.soporte.website.local
	DocumentRoot /var/www/website

	# --- Configuración SSL (ESTO YA LO TIENES) ---
	SSLEngine on
	SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
	SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

	#
	# --- AÑADE ESTO (LA NUEVA PRÁCTICA) ---
	#
	<Directory "/var/www/website">
		AuthType Basic
		AuthName "Contenido Restringido"
		AuthUserFile /etc/apache2/.htpasswd
		Require valid-user
	</Directory>

</VirtualHost>
```

El archivo de contraseña (AuthUserFile) es el que creamos en el paso 1.

Guarda y cierra el archivo (CTRL+X, Y, ENTER).

## 4. Comprobar la Configuración y Reiniciar
Ahora puedes seguir los últimos pasos del tutorial, que son universales:

a) Comprobar que no haya errores:

```Bash
sudo apache2ctl configtest
```
Si todo está bien, te responderá Syntax OK.

b) Reiniciar Apache (es mejor restart que reload porque hemos añadido autenticación):

```Bash

sudo systemctl restart apache2
```

## 5. Verificación Final
 Vuelve a tu navegador y entra en https://soporte.website.local. 

Si nos pide usuario y contraseña hemos acabado.

Verás que, antes de mostrarte la página, el navegador te pedirá un usuario y una contraseña. Debes poner el usuario_admin y la contraseña que creaste en el paso 1.
