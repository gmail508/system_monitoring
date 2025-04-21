настройка системы мониторинга сети

1.apt-get install postgresql16-server zabbix-server-pgsql zabbix-phpfrontend-php8.2 zabbix-agent 
zabbix-phpfrontend-nginx php8.2-fpm-fcgi

2.systemctl enable —now php8.2-fpm

3.cp -r /etc/zabbix/zabbix_nginx.conf 
/etc/nginx/sites-enabled.d/zabbix.conf

4.etc/nginx/sites-enabled.d/zabbix.conf
меняем unix:/var/run/php8.2-fpm/php8.2-fpm.sock;

5.etc/php/8.2/fpm-fcgi/php.ini

6.etc/zabbix/zabbix_server. conf

7.systemctl enable —now zabbix_pgsql zabbix_agent

8.chown _php_fpm:_php_fpm /var/www/webapps/zabbix/ui/conf/

!Z!

1.update-kernel

2.apt-get install kernel-modules-zfs-un-def 

3.modprobe zfs
  zpool create testpool mirror /dev/sdb /dev/sdc

4.mkdir /opt/app
  zfs set mountpoint=/opt/data testpool
  df -h проверка


!ROOT!

1.adduser serega
  passwd serega

2.groupadd service 
  chown -R serega:service /etc/data/
3.chmod -R 750 /etc/data/

