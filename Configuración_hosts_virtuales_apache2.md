# GUÍA DE CONFIGURACIÓN DE SERVIDORES VIRTUALES APACHE2

## CREAR UN DIRECTORIO PARA ALOJAR NUESTRAS PÁGINAS WEB

RELACIONADO práctica CONFIGURAR EL DIRECTORIO DE TRABAJO DE APACHE

### IR AL DIRECTORIO /var/www/
```bash
cd /var/wwww
```
### CREAR NUEVO DIRECTORIO DENTRO DE /var/www
```bash
mkdir daw1
```
## CONFIGURACIÓN DE APACHE PARA CREAR UN HOST VIRTUAL 
### IR AL DIRECTORIO DE CONFIGURACIÓN DE APACHE
```bash
cd /etc/apache2/sites-available
```
### Copia el archivo de configuración predeterminado para usarlo como plantilla:
```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/daw1.conf.
```

### ABRIR EL FICHERO: daw1.conf para editarlo
```bash
sudo nano /etc/apache2/sites-available
```

#### DENTRO DEL FICHERO BUSCAR <VirtualHost *:80>

Hay que añadir lo siguiente dentro de la etiqueta <VirtualHost *:80>

```
<VirtualHost *:80>
    ServerAdmin webmaster@daw1.com
    ServerName daw1.com
    ServerAlias www.daw1.com
    DocumentRoot /var/www/daw1
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

## HABILITAR EL SITIO
```bash
sudo a2ensite daw1.conf
```

## REINICIAR APACHE
```bash
sudo systemctl restart apache2
```


