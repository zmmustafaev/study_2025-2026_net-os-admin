---
## Front matter
title: "Отчёт по лабораторной работе 4"
subtitle: "Базовая настройка HTTP-сервера Apache"
author: "Заур Мустафаев"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache.

# Выполнение работы

## Установка HTTP-сервера

1. Загружена операционная система и выполнен переход в рабочий каталог проекта `/var/tmp/user_name/vagrant`.  
2. Запущена виртуальная машина **server** с использованием инструмента **vagrant**.
3. На виртуальной машине **server** выполнен вход под пользователем и осуществлён переход в режим суперпользователя.
4. Получен список групп пакетов с помощью команды `yum grouplist`, после чего установлен стандартный набор для веб-сервера командой  
   `dnf -y groupinstall "Basic Web Server"`.  
   В результате были установлены пакеты **httpd**, модули Apache, а также криптографические и вспомогательные утилиты.

   ![Установка пакетов HTTP-сервера](01.png){ #fig:001 width=80% }

## Базовое конфигурирование HTTP-сервера

1. Изучено содержимое конфигурационных каталогов `/etc/httpd/conf` и `/etc/httpd/conf.d`.  
   Установлено, что основной файл конфигурации определяет глобальные параметры сервера, а дополнительные файлы используются для модульной настройки.
2. Проверены текущие разрешённые сервисы межсетевого экрана с помощью команды `firewall-cmd --list-services`.
3. В конфигурацию **firewalld** добавлено разрешение на работу сервиса **http**, а также выполнено сохранение настроек для постоянного применения.
4. В дополнительном терминале запущен мониторинг системных сообщений в реальном времени с использованием `journalctl -x -f`.
5. HTTP-сервер **httpd** был добавлен в автозагрузку и запущен.  
   По сообщениям системного журнала подтверждено, что служба стартовала без ошибок.

   ![Настройка firewalld и запуск httpd](02.png){ #fig:002 width=80% }

## Анализ работы HTTP-сервера

1. Запущена виртуальная машина **client**.
2. На виртуальной машине **server** организован мониторинг лога ошибок веб-сервера командой  
   `tail -f /var/log/httpd/error_log`.
3. Также запущено отслеживание лога доступа с помощью  
   `tail -f /var/log/httpd/access_log`.
4. На виртуальной машине **client** в браузере выполнен переход по адресу `http://192.168.1.1`.
5. Установлено, что стандартная тестовая страница **Apache HTTP Server** успешно загружается, что подтверждает корректную работу веб-сервера.
6. В логах доступа зафиксированы HTTP-запросы от клиента, а в журнале ошибок отсутствуют критические сообщения.

   ![Проверка доступа и анализ логов Apache](03.png){ #fig:003 width=80% }

## Настройка DNS-зон для виртуального хостинга

1. Для внесения изменений в файлы зон DNS-сервера служба **named** была остановлена.
2. В файл прямой зоны `/var/named/master/fz/zmustafaev.net` добавлена A-запись для имени **www.zmustafaev.net**, указывающая на IP-адрес `192.168.1.1`.
3. В файл обратной зоны `/var/named/master/rz/192.168.1` добавлена PTR-запись, сопоставляющая адрес `192.168.1.1` имени **www.zmustafaev.net**.
4. Из каталогов зон были удалены журнальные файлы `zmustafaev.net.jnl` и `192.168.1.jnl`.
5. DNS-сервер был запущен повторно, после чего зоны были успешно загружены без ошибок.

   ![Файл прямой DNS-зоны zmustafaev.net](04.png){ #fig:004 width=80% }

   ![Файл обратной DNS-зоны 192.168.1](05.png){ #fig:005 width=80% }

## Конфигурирование виртуальных хостов Apache

1. В каталоге `/etc/httpd/conf.d` созданы конфигурационные файлы виртуальных хостов:
   - `server.zmustafaev.net.conf`
   - `www.zmustafaev.net.conf`
2. В файле `server.zmustafaev.net.conf` задан виртуальный хост с корневым каталогом  
   `/var/www/html/server.zmustafaev.net` и именем сервера **server.zmustafaev.net**.
3. В файле `www.zmustafaev.net.conf` настроен виртуальный хост с корневым каталогом  
   `/var/www/html/www.zmustafaev.net` и именем сервера **www.zmustafaev.net**.

   ![Конфигурация VirtualHost server.zmustafaev.net](06.png){ #fig:006 width=80% }

   ![Конфигурация VirtualHost www.zmustafaev.net](07.png){ #fig:007 width=80% }

## Создание веб-контента для виртуальных хостов

1. В каталоге `/var/www/html` созданы подкаталоги:
   - `server.zmustafaev.net`
   - `www.zmustafaev.net`
2. В каждом каталоге создан файл `index.html` с тестовым содержимым, подтверждающим имя соответствующего виртуального сервера.
3. Для каталогов веб-контента заданы корректные права доступа командой  
   `chown -R apache:apache /var/www`.

   ![Создание каталогов и index.html для виртуальных хостов](08.png){ #fig:008 width=80% }

4. Выполнено восстановление контекстов безопасности SELinux для каталогов:
   - `/etc`
   - `/var/named`
   - `/var/www`
5. HTTP-сервер **httpd** был перезапущен для применения всех изменений.

## Проверка работы виртуального хостинга

1. На виртуальной машине **client** в браузере выполнен переход по адресу  
   `http://server.zmustafaev.net`.
2. Подтверждена загрузка тестовой страницы виртуального хоста **server.zmustafaev.net**.
3. Аналогично выполнена проверка доступа по адресу  
   `http://www.zmustafaev.net`.
4. Установлено, что оба виртуальных хоста корректно обрабатываются HTTP-сервером и разрешаются через DNS.

   ![Доступ к server.zmustafaev.net](09.png){ #fig:010 width=80% }

   ![Доступ к www.zmustafaev.net](10.png){ #fig:011 width=80% }

## Внесение изменений во внутреннее окружение виртуальной машины

1. В каталоге `/vagrant/provision/server` создана структура для хранения конфигураций HTTP-сервера:
   - `http/etc/httpd/conf.d`
   - `http/var/www/html`
2. В созданные каталоги скопированы актуальные конфигурационные файлы Apache и веб-контент.
3. Конфигурационные файлы DNS-сервера скопированы в каталог  
   `/vagrant/provision/server/dns/var/named`.
4. В каталоге `/vagrant/provision/server` создан исполняемый сценарий `http.sh`, предназначенный для автоматизации установки и настройки HTTP-сервера.

   ![Сценарий автоматической настройки http.sh](11.png){ #fig:012 width=80% }

# Вывод

В ходе работы был установлен и настроен HTTP-сервер **Apache**. Выполнено базовое конфигурирование веб-сервера, настроены правила межсетевого экрана и проверена корректность запуска службы. Реализован виртуальный хостинг для двух доменных имён с использованием прямой и обратной DNS-зон. Созданы тестовые страницы, подтверждающие корректную обработку запросов. Дополнительно подготовлен сценарий автоматизации для среды **Vagrant**, что обеспечивает воспроизводимость конфигурации и упрощает дальнейшее развёртывание.

# Контрольные вопросы

1. **Через какой порт по умолчанию работает Apache?**  
   По умолчанию Apache использует порт **80** для протокола HTTP (и порт **443** для HTTPS при использовании SSL/TLS).

2. **Под каким пользователем запускается Apache и к какой группе относится этот пользователь?**  
   В системах семейства RHEL (включая Rocky Linux) Apache запускается от пользователя **apache**, который относится к группе **apache**. Это повышает безопасность за счёт ограничения прав веб-сервера.

3. **Где располагаются лог-файлы веб-сервера? Что можно по ним отслеживать?**  
   Лог-файлы Apache располагаются в каталоге `/var/log/httpd/`.  
   Основные файлы:
   - `access_log` — содержит информацию обо всех HTTP-запросах клиентов;
   - `error_log` — содержит сообщения об ошибках и диагностическую информацию.  
   По логам можно отслеживать активность пользователей, успешные и ошибочные запросы, а также выявлять проблемы в работе сервера.

4. **Где по умолчанию содержится контент веб-серверов?**  
   По умолчанию веб-контент Apache размещается в каталоге `/var/www/html`.

5. **Каким образом реализуется виртуальный хостинг? Что он даёт?**  
   Виртуальный хостинг реализуется с помощью директив `<VirtualHost>` в конфигурационных файлах Apache, где для каждого доменного имени задаются собственные параметры, такие как `ServerName` и `DocumentRoot`.  
   Он позволяет размещать несколько независимых веб-сайтов на одном сервере, используя один IP-адрес, что экономит ресурсы и упрощает администрирование.


# Список литературы

1. Apache HTTP Server Version 2.4 Documentation. — URL: http://httpd.apache.org/docs/current/ (дата обр. 13.09.2021).
2. Httpd — Apache Hypertext Transfer Protocol Server. — URL: https://httpd.apache.org/docs/2.4/programs/httpd.html (дата обр. 13.09.2021).
