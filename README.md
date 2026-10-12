# Estructura de un documento HTML
## Django deploy standard
### Copiar el codigo fuente al servidor

1. Ingresar al directorio superior de su proyecto

2. Abrir `git bash` y copiar el codigo al servidor
```bash
scp -r core_venta root@37.60.248.175:/root
```

Ingresar al servidor
```bash
ssh root@37.60.248.175
```

Mover el directorio a /var/www/
```bash
sudo mv core_venta /var/www
```

### Crear el entorno virtual

```bash
cd /var/www/core_venta
python3 -m venv .venv
```

Activar el entorno virtual
```bash
source .venv/bin/activate
```

Instalar aplicaciones
```bash
pip install -r *.txt
```

### Configuracion de la aplicacion
Actualizacion de permisos de carpetas
```bash
sudo mkdir /var/www/core_venta/logs
sudo chown -R www-data:www-data /var/www/core_venta/logs
sudo chmod 775 /var/www/core_venta/logs
```

### Instalacion de paquetes apache
Instalar paquetes necesarios
```bash
sudo apt update
sudo apt install apache2 libapache2-mod-wsgi-py3 python3-venv python3-pip -y
```

#### Configuacion de virtual host
Reemplazar el nombre de **core_venta** por el nombre de su proyecto

Crear virtual host
```bash
sudo nano /etc/apache2/sites-available/core_venta.com.conf
```

Contenido de virtual host
```apache
<VirtualHost *:8013>
    #ServerName bthsanjulian.website
    #ServerAlias www.bthsanjulian.website

    Alias /static/ /var/www/core_venta/staticfiles/
    Alias /media/ /var/www/core_venta/media/

    <Directory /var/www/core_venta/staticfiles>
        Require all granted
    </Directory>

    <Directory /var/www/core_venta/media>
        Require all granted
    </Directory>

    <Directory /var/www/core_venta/core_venta>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess core_venta python-home=/var/www/core_venta/.venv python-path=/var/www/core_venta
    WSGIProcessGroup core_venta
    WSGIScriptAlias / /var/www/core_venta/core_venta/wsgi.py

    ErrorLog ${APACHE_LOG_DIR}/core_venta_error.log
    CustomLog ${APACHE_LOG_DIR}/core_venta_access.log combined
</VirtualHost>
```

Habilitar puerto de virtual host
```bash
sudo nano /etc/apache2/ports.conf
```

Agregar el puerto
```apache
Listen 8013
```
Habilitar el firewall
```bash
sudo ufw allow 8013/tcp
```

Habilitar el host
```bash
sudo a2ensite core_venta.com.conf
```

Reiniciar el servidor apache
```bash
sudo systemctl reload apache2
sudo systemctl restart apache2
```

### Comandos de iniciacion de la aplicacion
```bash
export DJANGO_SETTINGS_MODULE=core_venta.settings
python3 manage.py collectstatic \
    --settings=exclusivogo_admin.settings
```

## Pregunta
- Crear una carpeta con su **primer nombre** _(utilizar el alfabeto ingles)_
- Ingresar a la carpeta creada y crear el archivo **index.html**
- Crear el archivo **javascript** con su **primer apellido** _(utilizar el alfabeto ingles)_
- Abrir **VSCode** en la carpeta actual
- Crear la estructura de un _documento HTML_ en el archivo **index.html**
- Mostrar el archivo HTML en **Google Chrome**

# Contador de Likes
## Pregunta
- Agregar una etiqueta **h3** para mostrar los datos, utilizar como `id` su **primer nombre** _(utilizar el alfabeto ingles)_
- Agregar una etiqueta **button** para ejecutar el contador, utilizar como `id` su **primer apellido** _(utilizar el alfabeto ingles)_
- Crear e inicializar la variable **contador**
- Crear el listener con el codigo para incrementar el contador

# sysadmin-sanbox
Configuraciones borrador de despliegue de aplicaciones

## Latex
```bash
sudo add-apt-repository ppa:sunderme/texstudio
sudo aapt-get update
sudo apt install texlive-full
sudo apt install texlive-latex-base
sudo apt install texlive-latex-extra
sudo apt install texlive-latex-extra texstudio
sudo apt-get install -y texlive-bibtex-extra
sudo apt-get install -y texlive-bibtex-extra biber texlive-publishers
sudo apt-get install -y texlive-lang-spanish
```

## Django admin
### Apache

Instalar paquetes necesarios
```bash
sudo apt update
sudo apt install apache2 libapache2-mod-wsgi-py3 python3-venv python3-pip -y
```
Actualizacion de permisos de carpetas
```bash
sudo chown -R www-data:www-data /var/www/mi_proyecto
```
Configuacion de host
```apache
<VirtualHost *:8016>
    ServerName bthsanjulian.website
    ServerAlias www.bthsanjulian.website

    SSLEngine on
    SSLCertificateFile      /etc/letsencrypt/live/bthsanjulian.website/fullchain.pem
    SSLCertificateKeyFile   /etc/letsencrypt/live/bthsanjulian.website/privkey.pem

    Alias /static/ /var/www/exclusivogo_admin/staticfiles/
    Alias /media/ /var/www/exclusivogo_admin/media/

    <Directory /var/www/exclusivogo_admin/staticfiles>
        Require all granted
    </Directory>

    <Directory /var/www/exclusivogo_admin/media>
        Require all granted
    </Directory>

    <Directory /var/www/exclusivogo_admin/exclusivo_admin>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess exclusivogo_admin python-home=/var/www/exclusivogo_admin/.venv python-path=/var/www/exclusivogo_admin
    WSGIProcessGroup exclusivogo_admin
    WSGIScriptAlias / /var/www/exclusivogo_admin/exclusivogo_admin/wsgi.py

    ErrorLog ${APACHE_LOG_DIR}/exclusivogo_admin_error.log
    CustomLog ${APACHE_LOG_DIR}/exclusivogo_admin_access.log combined
</VirtualHost>
```

### Configuracion de la aplicacion
Actualizacion de permisos de carpetas
```bash
sudo chown -R www-data:www-data /var/www/exclusivogo_admin/logs
sudo chmod 775 /var/www/exclusivogo_admin/logs
```

Comandos de iniciacion de la aplicacion
```bash
export DJANGO_SETTINGS_MODULE=exclusivogo_admin.settings.production
python manage.py migrate \
    --settings=exclusivogo_admin.settings.production
python manage.py collectstatic \
    --settings=exclusivogo_admin.settings.production
```


## Deploy Bun + Hono + Postgres

### Configuracion de Web Server Apache

#### Habilitando modulos
```bash
sudo a2enmod proxy proxy_http headers
sudo systemctl restart apache2
```

#### Halitando el firewall
```bash
sudo ufw allow 8001
```

#### Archivo de configuracion de proxy inverso HTTPS
```apache
<VirtualHost *:8001>
    ServerAdmin juanvladimir13@gmail.com
    ServerName bthsanjulian.website

    SSLEngine on
    SSLCertificateFile      /etc/letsencrypt/live/bthsanjulian.website/fullchain.pem
    SSLCertificateKeyFile   /etc/letsencrypt/live/bthsanjulian.website/privkey.pem

    ErrorLog ${APACHE_LOG_DIR}/registropedagogico.webapi_proxy_error.log
    CustomLog ${APACHE_LOG_DIR}/registropedagogico.webapi_proxy_access.log combined

    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:8002/
    ProxyPassReverse / http://127.0.0.1:8002/

    <IfModule mod_headers.c>
        Header always unset Access-Control-Allow-Origin
        Header always unset Access-Control-Allow-Methods
        Header always unset Access-Control-Allow-Headers
        Header always unset Access-Control-Allow-Credentials

        Header always set Access-Control-Allow-Methods "GET, POST, PUT, DELETE, OPTIONS"
        Header always set Access-Control-Allow-Headers "Content-Type, Authorization"
    </IfModule>

    RewriteEngine On
    RewriteCond %{REQUEST_METHOD} OPTIONS
    RewriteRule ^(.*)$ $1 [R=200,L]
</VirtualHost>
```

### Web App
#### Creacion de directorio
```bash
sudo mkdir -p /opt/registropedagogico.webapi
sudo chown -R www-data:www-data /opt/registropedagogico.webapi
sudo chmod -R 755 /opt/registropedagogico.webapi
```

#### Creacion de servicio
```bash
sudo touch /etc/systemd/system/registropedagogico.webapi.service
```

```ini
[Unit]
Description=Registro Pedagógico WebAPI
After=network.target

[Service]
User=www-data
WorkingDirectory=/opt/registropedagogico.webapi
ExecStart=/opt/registropedagogico.webapi/registropedagogico.webapi
Environment=PORT=8002
Environment=NODE_ENV=production
EnvironmentFile=/opt/registropedagogico.webapi/.env
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

#### Habilitando servicio
```bash
sudo systemctl daemon-reload
sudo systemctl enable registropedagogico.webapi
sudo systemctl start registropedagogico.webapi
sudo systemctl status registropedagogico.webapi
```
