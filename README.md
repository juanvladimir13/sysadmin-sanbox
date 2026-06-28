# sysadmin-sanbox
Configuraciones borrador de despliegue de aplicaciones

## Django admin
sudo chown -R www-data:www-data /var/www/exclusivogo_admin/logs
sudo chmod 775 /var/www/exclusivogo_admin/logs


export DJANGO_SETTINGS_MODULE=exclusivogo_admin.settings.production

python manage.py migrate \
    --settings=exclusivogo_admin.settings.production

python manage.py collectstatic \
    --settings=exclusivogo_admin.settings.production
