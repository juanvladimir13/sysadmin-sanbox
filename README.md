# sysadmin-sanbox
Configuraciones borrador de despliegue de aplicaciones

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
