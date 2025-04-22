настройка систем управления версиями
!!gta!!
1. apt-get update
2. apt-get install git wget mariadb-server mariadb-common mariadb-server-control
3. mkdir /opt/app
4. wget -O gitea https://dl.gitea.io/gitea/1.21.10/gitea-1.21.10-linux-amd64
5. mv gitea /usr/local/bin/
6. chmod +x /usr/local/bin/gitea
7. groupadd --system git

8. adduser \
--system \
--shell /bin/bash \
--comment 'Git Version Control' \
--gid git \
--home-dir /home/git \
--create-home \
git
9. mkdir -p /opt/app/{custom,data,log}
10. chown -R git:git /opt/app/
11. chmod -R 750 /opt/app/
12. /etc/systemd/system/gitea.service И в нем пишем
[Unit]
Description=MyGitea
After=network.target
Wants=mariadb.service
After=mariadb.service

[Service]
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/opt/app
ExecStart=/usr/local/bin/gitea web --config /opt/app/gitea/app.ini
Restart=always
Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/opt/app

[Install]
WantedBy=multi-user.target
13. systemctl start mariadb включаем
14. systemctl enable mariadb автозапуск
15. mysql_secure_installation настройка мариидб
16. su -c mariadb вход под пользователя бд

.CREATE DATABASE giteadb CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_unicode_ci'; создание бд

.CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'password'; создание пользователя

.GRANT ALL PRIVILEGES ON giteadb.* TO 'gitea'@'localhost'; выдаем все привилегии

.FLUSH PRIVILEGES; принимаем все изменения

.EXIT; выходим
17. systemctl enable gitea
18. systemctl start gitea
19. sed -i "s/skip-networking/#skip-networking/g" /etc/my.cnf.d/server.cnf
20. systemctl restart mariadb


!!keep!!

Первый сервер
1. apt-get install keepalived nginx
2. vim /etc/keepalived/keepalived.conf
   пишем туда
!!!vrrp_script nginx_check {
  script "/usr/bin/curl http://127.0.0.1"
  interval 5
  user nginx
}
vrrp_instance web {
  state MASTER
  interface eth0 #свой интерфейс нужен указать
  virtual_router_id 254
  priority 220
  advert_int 3
preempt_delay 20

  virtual_ipaddress {
    192.168.0.200 # свой виртульный ip
  }
  track_script {
    nginx_check
  }
}

втрой сервер
1. apt-get install keepalived nginx
2. vim /etc/keepalived/keepalived.conf
  пишем туда
global_defs {
  enable_script_security
}

vrrp_script nginx_check {
  script "/usr/bin/curl http://127.0.0.1"
  interval 5
  user nginx
}

vrrp_instance web {
  state BACKUP
  interface eth0 #свой интерфейс нужен указать
  virtual_router_id 254
  priority 200
  advert_int 3
   preempt_delay 20

  virtual_ipaddress {
    192.168.0.200 # свой виртульный ip
  }
  track_script {
    nginx_check
  }
}
делаем на обоих серверах : 
sudo systemctl enable keepalived
sudo systemctl start keepalived
sudo systemctl enable nginx
sudo systemctl start nginx

Проверьте, что VIP назначен мастеру (CL-FRONT1):
ip addr show
