# zabbix
настройка заббикс
!!zabbix 

1.apt-get install postgresql16-server zabbix-server-pgsql zabbix-phpfrontend-php8.2 zabbix-agent 
zabbix-phpfrontend-nginx php8.2-fpm-fcgi

2.systemctl enable —now php8.2-fpm

3.cp -r /etc/zabbix/zabbix_nginx.conf 
/etc/nginx/sites-enabled.d/zabbix.conf

4.vim /etc/nginx/sites-enabled.d/zabbix.conf
Ставим mon.jun.profi
И меняем unix:/var/run/php8.2-fpm/php8.2-fpm.sock;

5.Vim /etc/php/8.2/fpm-fcgi/php.ini
Ставим параметры 300 300

6.vim /etc/zabbix/zabbix_server. conf
Там разкоментировать DBHost и DBPassword

7.systemctl enable —now zabbix_pgsql zabbix_agent

8.Вносим мон Джун профи в роутер

9.chown _php_fpm:_php_fpm /var/www/webapps/zabbix/ui/conf/


!!kerberos

1.mkdir -p /etc/opt/yandex/browser/policies/managed/

2.vim /etc/opt/yandex/browser/policies/managed/
mydomain.json

3.Делаем конфиг внутри
{
"AuthServerAllowlist": "*.domain",
"AuthNegotiateDelegateAllowlist": "*.domain"
}

4.chmod 755 /etc/opt/ -R

Перезагружаем yandex browser
Заходим в компьютер под пользователем admin


!!ZFS 

1.update-kernel

2.apt-get install kernel-modules-zfs-un-def 

3.modprobe zfs
  zpool create testpool mirror /dev/sdb /dev/sdc

4.mkdir /opt/app
  zfs set mountpoint=/opt/data testpool
  df -h проверка


!!Пользователь

1.adduser webdev
  passwd webdev

2.groupadd service 
  chown -R webdev:service /opt/data/

3.chmod -R 750 /opt/data/

