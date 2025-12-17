
### Guía de Despliegue: ASP.NET en Linux (Ubuntu) con Apache y PostgreSQL

#### 1. Instalación del Entorno (.NET SDK y Apache)

1. **Actualizar repositorios e instalar transporte HTTPS:**
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https

```

2. **Instalar .NET SDK 5.0:**
```bash
sudo apt-get update
sudo apt-get install -y dotnet-sdk-5.0

```


*Verificar instalación:* `dotnet --version` 


3. **Instalar Servidor Apache:**
```bash
sudo apt-get install apache2

```





4. **Habilitar módulos proxy de Apache:**
```bash
systemctl restart apache2
a2enmod proxy proxy_http proxy_html

```






---

#### 2. Publicación y Transferencia de Archivos

1. **Publicar desde Visual Studio 2019:**


2. **Preparar directorio en la Máquina Virtual (Linux):**
```bash
cd /var/
sudo mkdir DotNetApp

```


3. **Transferir y mover archivos:**
* Copiar los archivos publicados desde tu máquina local a la carpeta DE LINUX


* Moverlos al directorio final:


```bash
cd /home/azureuser/App
sudo cp -R * /var/DotNetApp/

```
---

#### 3. Configuración del Servidor Inverso (Apache)

1. **Crear archivo de configuración:**
```bash
sudo nano /etc/apache2/conf-enabled/DotNetApp.conf

```

2. **Contenido del archivo `DotNetApp.conf`:**
Pegar lo siguiente para redirigir el tráfico del puerto 80 al 5000:
```apache
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/apache2/DotNetApp-error.log
    CustomLog /var/log/apache2/DotNetApp-access.log common
</VirtualHost>

```


3. **Reiniciar Apache para aplicar cambios:**
```bash
sudo service apache2 restart

```


---

#### 4. Configuración del Servicio (Systemd)

1. **Crear el archivo de servicio:**
Esto mantendrá la aplicación ejecutándose en segundo plano.
```bash
sudo nano /etc/systemd/system/kestrel-DotNetApp.service

```




2. **Contenido del archivo de servicio:**
*(Asegúrate de ajustar la ruta del `.dll` al nombre de tu proyecto)*
```ini
[Unit]
Description=Aspnet 5 Web App running on Ubuntu

[Service]
WorkingDirectory=/var/DotNetApp
ExecStart=/usr/bin/dotnet /var/DotNetApp/UserDashboard.dll
Restart=always
RestartSec=10
SyslogIdentifier=dotnet-demo
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target

```

3. **Habilitar e iniciar el servicio:**
```bash
sudo systemctl enable kestrel-DotNetApp.service
sudo systemctl start kestrel-DotNetApp.service

```







¿Te gustaría que verifiquemos algún comando específico o la configuración del archivo `.service` para asegurar que coincida con el nombre de tu DLL?
