======================================================
# Exploit Title: Apache Tomcat 9 - Guía de Instalación Manual en Ubuntu
# Date: 2024-10-06
# Author: Alfonso de Uña
# Category: Tutorial / Setup
# Platform: Linux / Ubuntu (Versión 20.04 en adelante)
#
# Descripción: Guía paso a paso para la instalación y configuración 
# del servidor de aplicaciones Apache Tomcat 9 usando OpenJDK en Ubuntu.
======================================================

######################################################
### 1. INSTALAR JAVA (REQUISITO)
######################################################

# Actualiza la lista de paquetes
sudo apt update

# Instala el paquete OpenJDK (Java Development Kit)
sudo apt install default-jdk

######################################################
### 2. CREAR USUARIO Y GRUPO DEDICADO
######################################################

# Crea el grupo 'tomcat'
sudo groupadd tomcat

# Crea el usuario 'tomcat' (sin shell de inicio de sesión)
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat

######################################################
### 3. DESCARGAR E INSTALAR TOMCAT 9
######################################################

# Navega a /tmp y descarga la versión binaria de Tomcat 9.
# NOTA: Reemplaza la URL con la última versión estable si es necesario.
cd /tmp
curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.83/bin/apache-tomcat-9.0.83.tar.gz

# Crea el directorio de instalación y descomprime el archivo
sudo mkdir /opt/tomcat
sudo tar xzvf /tmp/apache-tomcat-*tar.gz -C /opt/tomcat --strip-components=1

######################################################
### 4. CONFIGURAR PERMISOS DE ACCESO
######################################################

# Asigna la propiedad del grupo 'tomcat' al directorio
sudo chgrp -R tomcat /opt/tomcat

# Da acceso de lectura/ejecución al directorio de configuración
sudo chmod -R g+r conf
sudo chmod g+x conf

# Da la propiedad del usuario 'tomcat' a los directorios de aplicación
sudo chown -R tomcat webapps/ work/ temp/ logs/

######################################################
### 5. CREAR ARCHIVO DE UNIDAD SYSTEMD
######################################################

# Crea el archivo de servicio systemd
sudo nano /etc/systemd/system/tomcat.service

# Pega la siguiente configuración dentro del archivo:
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
# **AJUSTAR JAVA_HOME si es necesario**
Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target

# Recarga el daemon systemd, habilita e inicia el servicio
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat

######################################################
### 6. AJUSTAR FIREWALL (UFW)
######################################################

# Tomcat usa el puerto 8080 por defecto
sudo ufw allow 8080

######################################################
### 7. CONFIGURAR ADMINISTRACIÓN WEB
######################################################

# Edita el archivo de usuarios para añadir un administrador
sudo nano /opt/tomcat/conf/tomcat-users.xml

# AGREGA la siguiente línea DENTRO de las etiquetas <tomcat-users>:
# <user username="admin" password="TU_CONTRASEÑA_SEGURA" roles="manager-gui,admin-gui"/>

# Reinicia Tomcat para aplicar la configuración
sudo systemctl restart tomcat

######################################################
### 8. VERIFICACIÓN Y ACCESO
######################################################

# Accede a la interfaz web a través del navegador:
# http://TU_IP_O_DOMINIO:8080

# Accede al Administrador de Aplicaciones:
# http://TU_IP_O_DOMINIO:8080/manager/html
