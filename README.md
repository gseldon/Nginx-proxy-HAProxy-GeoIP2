[![Codacy Security Scan](https://github.com/gseldon/Nginx-proxy-HAProxy-GeoIP2/actions/workflows/codacy.yml/badge.svg)](https://github.com/gseldon/Nginx-proxy-HAProxy-GeoIP2/actions/workflows/codacy.yml)

# HAProxy + Nginx + GeoIP2 LoadBalancer

Задача проекта:

- сервис должен быть контейнеризован
- используем стабильную версию Nginx
- отработать процедуру добавления новых модулей
- добавить поддержку GeoIP2
- шаблоны блокировок по IP
- логичная структура проекта (конфигурация, инклуды, безопасность)
- включить поддержку добавления wildcard сертификатов [YandexCloud Certificate Manager](https://github.com/gseldon/yandex-cloud-certificate-manager-get)
- шаблоны страниц ошибок

---

[Онлайн конфигуратор](https://www.digitalocean.com/community/tools/nginx)

[Онлайн конфигуратор правил безопасности](https://ssl-config.mozilla.org/#server=nginx&version=1.21&config=intermediate&openssl=1.1.1k&guideline=5.6)

[Установка модуля GeoIP2 Nginx с новым форматом базы MaxMind](https://wiki.dieg.info/geoip_nginx)

---

[Хорошая статья по HAProxy](https://www.digitalocean.com/community/tutorials/how-to-set-up-highly-available-haproxy-servers-with-keepalived-and-floating-ips-on-ubuntu-14-04)

[Еще одна статья по HAProxy](https://d2c.io/ru/article/how-to/haproxy-basic-ru)

[Получаем IP-адреса HTTPS-клиентов с HAProxy (frontend) на Nginx (backend) в режимах HTTP и TCP-балансировки](https://habr.com/ru/post/247297/)
