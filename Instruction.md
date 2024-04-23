# **<p style="text-align: center;">Инструкция по установке и настройке NGIX (Frontend) + PHP-FPM (Backend) по схеме "reverse proxy"</p>**

<image src="./images/nginx_php-fpm.jpg" alt="top-img">  

### **Задача:** 
Необходимо установить и настроить связку NGINX + PHP-FPM в режиме обратного прокси (NGINX) с использованием юникс сокета для PHP-FPM.

### **Для решения задачи будут использоваться:**

- Ubuntu 22.04.4 LTS
- php8.1
- php8.1-fpm

### **Схема Reverse proxy и типы контента:**

Схематически схему работы можно представить в виде следующей блок-схемы:

<image src="./images/top_scheme.jpg" alt="top-scheme">

При такой реализации NGINX обабатывает HTTP запросы от браузера клиента и обратно, а PHP-FPM является Backend сервером. При этом NGINX является проксирующим сервером обработки запросов.

### 1. Установка NGINX, php8.1 и PHP-FPM

#### 1.1 Устанавливаем NGINX

<image src="./images/apt_nginx.jpg" alt="nginx">

Устанавливаем командой:

    apt install nginx

После установки проверяем поднялся ли NGING:

    ss -ntlp

<image src="./images/ss_nginx.jpg" alt="ss_nginx">

NGINX запустился. Его процесс слушает 80 порт на всех ip адресах.

Сам процесс можно посмотеть командой:

    ps afx

<image src="./images/ps_nginx.jpg" alt="ps_nginx">


#### 1.2 Установка PHP 8.1

Для установки PHP 8.1 на Ubuntu используем команду:

    apt install php8.1

Готово!

#### 1.3 Установка PHP-FPM

<image src="./images/apt_php-fpm.jpg" alt="apt_php-fpm">

Устанавливаем командой:

    apt install php8.1-fpm

Проверяем наличие процессов php-fpm командой:

    ps afx

<image src="./images/ps_php-fpm.jpg" alt="ps_php-fpm">

Работает!

### 2. Настройка PHP-FPM и NGINX 

Заходим в директорию `/etc/php/8.1/fpm/pool.d/`, открываем файл www.conf смотрим путь к файлу .sock (директива `listen`):

<image src="./images/sock.jpg" alt="sock">

После этого, запоминаем путь, и идем в директорию `/etc/nginx/sites-enabled/` и вносим изменения в файл настроек `default` как на скрине ниже:

<image src="./images/nginx_default.jpg" alt="nginx_default">

Затем, проверяем конфигурацию и перезагружаем сервис командами:

    nginx -t
    systemctl reload nginx

### 3. Тестируем работу

Для тестирования работы все системы создаём тестовый файл `info.php` в директории `/var/www/html` со следующим содержимым:

    <?php
    phpinfo();
    ?>

Затем заходим на наш сервер с указанием пути файла `info.php` 

    localhost/info.php

В результате правильной работы появится страница с конфигурацией PHP сервера с работающим `FastCGI` протоколом:

<image src="./images/fpm_test.jpg" alt="fpm_test">