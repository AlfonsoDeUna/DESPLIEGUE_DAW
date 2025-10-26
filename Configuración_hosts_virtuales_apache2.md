# GUÍA DE CONFIGURACIÓN DE SERVIDORES VIRTUALES APACHE2

## CREAR UN DIRECTORIO PARA ALOJAR NUESTRAS PÁGINAS WEB

RELACIONADO práctica CONFIGURAR EL DIRECTORIO DE TRABAJO DE APACHE

### IR AL DIRECTORIO /var/www/
```bash
cd /var/wwww
```
### CREAR NUEVO DIRECTORIO DENTRO DE /var/www
```bash
sudo mkdir daw1
```
### CREA UN FICHERO index.html con una pequeña página web con tu nombre
## CREAR EL FICHERO index.html
```bash
sudo nano index.html
```

```html
<html>
<head>
</head>
<body>
	<h1> hola mundo Alfonso </h1>
</body>
</html>
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

## MODIFICAR EL DNS LOCAL PARA ACCEDER A LA PÁGINA POR DOMINIO

### EDITAR EL FICHERO hosts
```
sudo nano /etc/hosts
```
verás algo parecido a lo siguiente, y después de estas líneas tienes que añadir lo del siguiente apartado:
```
127.0.0.1       localhost
127.0.1.1       dev2.mynetowork.org

```

#### añade daw1.com a las líneas anteriores GUARDA Y SALVA CON CTRL+O Y CTRL + X
```
127.0.0.1       localhost
127.0.1.1       dev2.mynetowork.org
127.0.0.1       daw1.com
```

