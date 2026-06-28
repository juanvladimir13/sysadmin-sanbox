# sysadmin-sanbox
Configuraciones borrador de despliegue de aplicaciones

## Django admin

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
