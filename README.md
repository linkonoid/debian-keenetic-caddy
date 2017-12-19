# debian-keenetic-caddy
Run Caddy server on Zyxel Keenetic in Debian chroot
===================================================
Установка Caddy-сервера на Zyxel-Keenetic под Debian (http://keenetic-gi.ga/2017/06/21/debian-via-chroot/) 

Скачиваем http://keenetic-gi.ga/assets/2017-06/debian_keenetic.tgz на флешку и действуем по инструкции http://keenetic-gi.ga/2017/06/21/debian-via-chroot/.

1. На Zyxel-Keenetic Debian работает в chroot окружении (подмена корневого каталога) и из этого следуют другие проблемы: если кратко, то из описания установки запуска сервиса на Linux в поставке исходников Caddy не работает команда setcap (sudo apt-get install libcap2-bin, при запуске setcap нет привязки capabilities к исполняемому файлу)  
И есть ещё проблема путей для нашего окружения, для её решения добавил в пути префикс "/opt/".
- Вопрос решается скачиванием файла caddy (модифицирован мной для sysvinit `/etc/init.d/caddy`) из моего репозитария, помещения его в папку /etc/init.d и назначением прав 0755 от пользователя root.  

2. Второй вопрос - нужно скомпилировать Caddy-сервер для Mips-процессора (просто скачайте готовый исполняемый файл caddy из репозитария) и поместите его в папку /usr/local/bin/caddy с назначением прав 0755 от пользователя www-data

3. Прописываем пути в файле конфигурации Caddyfile (они должны начинаться с "/opt/home/caddy/" ) и запускаем сервис "service caddy `start|stop|restart|reload|status` от root-пользователя. Права на папку и подпапки caddy выставьте от пользователя www-data.
Пример для запуска OctoberCMS и PHP в FastCGI-режиме: 
http://:2015 {
root /opt/home/caddy/octobercms
gzip
log /opt/home/caddy/logs/localhost_log
errors /opt/home/caddy/logs/localhost_err_log
fastcgi / 127.0.0.1:9000 php {
index index.php
}
rewrite {
to {path} {path}/ /index.php?{query}
}
}
  
4. Прописать автозагрузку caddy в /etc/ndm/ services.txt (на случай перезагрузки роутера), в моём случае там строчки
ssh
php7.0-fpm
caddy  
5. Теоретически данный способ работает на любом железе (роутеры, Android-приставки и т.д), где возможен запуск Linux в chroot-окружении (нужно скомпилировать Caddy для соответствующей платформы и настроить сервисы) 
