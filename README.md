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
