# Guía de Despliegue: ASP.NET en Linux (Ubuntu)

Esta guía detalla los pasos necesarios para publicar y alojar una aplicación web .NET en un entorno Linux.

## Resumen de pasos

1. **Preparar la VM**: Crear una máquina virtual Ubuntu en Azure.


2. **Configurar el Entorno**: Instalar el SDK de .NET y el servidor Apache.


3. **Publicar la App**: Generar los archivos de la aplicación desde Visual Studio.


4. **Alojar la App**: Copiar los archivos a la VM y configurar el servidor web.


5. **Crear un Servicio**: Configurar `systemd` para que la app siempre esté activa.


6. **Extra**: Instalar y conectar una base de datos PostgreSQL.

---

## 1. Configuración de la Máquina Virtual (VM)

* Debes tener una MV **Ubuntu**.


## 2. Instalación de Herramientas

Ejecuta estos comandos en la terminal de tu VM para instalar el entorno necesario:

### Instalar .NET SDK 8.0

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https
sudo apt-get install -y dotnet-sdk-8.0

```

*Verifica la instalación con: `dotnet --version*`.

### Instalar Apache (en el caso que no lo tengas instalado)

```bash
sudo apt-get install apache2
# Habilitar módulos de proxy para redirigir tráfico a .NET
sudo a2enmod proxy proxy_http proxy_html
sudo systemctl restart apache2

```

## 3. Publicación desde Visual Studio

1. En Visual Studio 2022 en mi caso, haz clic derecho en tu proyecto y selecciona **Publish**.
2. Elige el destino **Folder** y especifica una ruta local.
3. Haz clic en **Publish**. Los archivos generados se usarán para el despliegue.


## 4. Alojamiento en la VM

1. **Crear carpeta de destino**:
```bash
sudo mkdir /var/www/website

```
2. **Subir archivos**: Transfiere los archivos publicados desde tu PC a la carpeta `/var/www/website` de la VM (puedes usar un correo o carpetas compartidas de MV).
   
3. **Configurar Proxy en Apache**:
Crea un archivo de configuración: `sudo nano /etc/apache2/conf-enabled/net.config.conf`.
Pega lo siguiente:


```apache
<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/website
	ProxyPreserveHost On
	ProxyPass / http://127.0.0.1:5000/
	ProxyPassReverse / http://127.0.0.1:5000/

	ErrorLog ${APACHE_LOG_DIR}/error_net.log
	CustomLog ${APACHE_LOG_DIR}/access_net.log combined
</VirtualHost>

```

## 5. Crear el Servicio de Ejecución

Para que la aplicación se inicie sola y se recupere de fallos, crea un servicio:

`sudo nano /etc/systemd/system/daw.service` 

Contenido del archivo:

```ini
[Unit]
Description=ASP.NET Web App en Ubuntu

[Service]
WorkingDirectory=/var/www/website
ExecStart=/usr/bin/dotnet /var/DotNetApp/WebApplication1.dll
Restart=always
RestartSec=10
User=alfonso
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target

```

**Activar el servicio:**

```bash
sudo systemctl enable kestrel-DotNetApp.service
sudo systemctl start kestrel-DotNetApp.service

```



