---
## Front matter
title: "Отчёт по лабораторной работе 2"
subtitle: "Настройка DNS-сервера"
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

Получить практические навыки установки и настройки DNS-сервера, а также закрепить понимание принципов работы системы доменных имён.

# Выполнение работы

## Установка DNS-сервера

1. Запущена виртуальная машина **server**, подготовлено окружение для установки служб.
2. Установлены пакеты **bind** и **bind-utils**, необходимые для работы сервера имён и диагностики.

   ![Установка bind и bind-utils](01.png){ #fig:001 width=80% }

3. Проверена работоспособность сервиса с помощью утилиты **dig**, сделан запрос к ресурсу в сети Интернет.

   ![Запрос dig к www.yandex.ru](02.png){ #fig:002 width=80% }

## Настройка кэширующего DNS-сервера

1. Изучен файл `/etc/resolv.conf`, где задаются адреса серверов DNS по умолчанию.
2. Рассмотрены файлы `named.localhost` и `named.loopback`, отвечающие за тестовые зоны.
3. Сервис **named** был запущен и добавлен в автозагрузку.
4. Сравнены результаты запросов через внешний сервер и через локальный. Установлено, что сервер корректно выполняет резолвинг.

   ![Сравнение dig-запросов](03.png){ #fig:003 width=80% }

5. Настройки сетевого соединения были изменены с помощью **nmcli**, что позволило использовать локальный сервер как основной DNS.

   ![Настройка подключения через nmcli](04.png){ #fig:004 width=80% }

6. Внесены изменения в файл `named.conf`: расширен список допустимых клиентов, добавлена поддержка сети 192.168.0.0/16.

   ![Изменения в named.conf](05.png){ #fig:005 width=80% }

7. Добавлены правила для файрвола, разрешающие обращения к DNS.

   ![Проверка работы DNS через lsof](06.png){ #fig:006 width=80% }

## Настройка первичного DNS-сервера

1. Создан файл зоны `zmustafaev.net`, где прописаны прямые и обратные записи.

   ![Подключение файла зоны zmustafaev.net](07.png){ #fig:007 width=80% }

2. Настроен файл прямой зоны с A- и NS-записями.

   ![Файл прямой зоны zmustafaev.net](08.png){ #fig:008 width=80% }

3. В обратной зоне добавлены PTR-записи для IP-адресов.

   ![Файл обратной зоны 192.168.1](09.png){ #fig:009 width=80% }

4. Сервис **named** был перезапущен для применения настроек, таже исправлены ошибки.

   ![Настройка прав и SELinux](10.png){ #fig:010 width=80% }

   ![Файл конфигурации зон](11.png){ #fig:011 width=80% }

   ![Файл конфигурации прямой зоны zmustafaev.net](12.png){ #fig:012 width=80% }

## Проверка работы DNS-сервера

1. С помощью утилиты **dig** произведена проверка: запрос к ns.zmustafaev.net вернул корректный IP.

   ![Проверка зоны через dig](13.png){ #fig:013 width=80% }

2. Утилита **host** подтвердила корректность прямых и обратных записей.

   ![Проверка зоны через host](14.png){ #fig:014 width=80% }

## Автоматизация с помощью Vagrant

Создан скрипт `dns.sh`, выполняющий установку пакетов, копирование конфигураций, настройку прав и SELinux, а также запуск службы **named**. 

В файл `Vagrantfile` добавлен вызов скрипта, что обеспечивает автоматическое развертывание настроенного DNS-сервера.

# Вывод

В ходе работы был развернут и сконфигурирован DNS-сервер на основе BIND. Реализованы прямая и обратная зоны, проведена проверка их работы. Настроен кэширующий режим. Добавлен скрипт автоматизации для Vagrant, что упростило повторное развёртывание и снизило вероятность ошибок при настройке вручную.

# Контрольные вопросы

1. **Что такое DNS?**  
   Это система, которая преобразует доменные имена в IP-адреса и обратно.

2. **Назначение кэширующего DNS-сервера?**  
   Он ускоряет обработку повторных запросов, сохраняя результаты в кэше.

3. **Разница между прямой и обратной зоной?**  
   Прямая зона связывает имя с адресом, обратная — адрес с именем.

4. **Где хранятся настройки DNS-сервера?**  
   Основные файлы: `/etc/named.conf` (главная конфигурация), `/etc/named/` (доп. зоны), `/var/named/` (файлы зон).

5. **Что содержится в resolv.conf?**  
   Адреса серверов DNS и параметры поиска доменов.

6. **Основные типы ресурсных записей DNS?**  
   SOA, NS, A, AAAA, PTR, CNAME, MX, TXT.

7. **Зачем нужен домен in-addr.arpa?**  
   Для организации обратного разрешения адресов.

8. **Функция демона named?**  
   Это процесс, реализующий работу DNS-сервера BIND.

9. **Разница между master и slave-серверами?**  
   Master хранит оригинальные зоны, slave синхронизируется с ним.

10. **Какие параметры отвечают за обновление зоны?**  
   Serial, refresh, retry, expire, minimum.

11. **Как ограничить доступ к зоне?**  
   С помощью ACL, allow-query, аутентификации TSIG.

12. **Какая запись нужна для почтовых серверов?**  
   Используется MX-запись.

13. **Как протестировать работу DNS?**  
   С помощью dig, host, nslookup, а также через логи.

14. **Как управлять службами?**  
   Через systemctl: start, stop, restart.

15. **Как получить отладочную информацию о сервисе?**  
   Через journalctl -xe или настройку режима debug.

16. **Где хранится отладка работы системы?**  
   В журналах /var/log и через journalctl.

17. **Как узнать, какие файлы использует процесс?**  
   Через lsof или /proc/PID/fd.

18. **Примеры команд nmcli:**  
   show, edit, up, down.

19. **Что такое SELinux?**  
   Это механизм принудительного контроля доступа в Linux.

20. **Что такое контекст SELinux?**  
   Метка безопасности объекта, определяющая его права.

21. **Как восстановить контекст SELinux?**  
   restorecon -vR <путь>.

22. **Как создать правила SELinux из логов?**  
   Использовать audit2allow.

23. **Что такое булевый параметр SELinux?**  
   Переключатель, меняющий часть политики.

24. **Как посмотреть список булевых параметров?**  
   getsebool -a.

25. **Как изменить значение переключателя SELinux?**  
   setsebool -P <имя> on|off.

# Список литературы

1. Barr D. Common DNS Operational and Configuration Errors: RFC 1912. — DOI: 10.17487/rfc1912.  
2. Security-Enhanced Linux: руководство пользователя. — URL: [https://docs-old.fedoraproject.org/ru-RU/Fedora/13/html/Security-Enhanced_Linux/index.html](https://docs-old.fedoraproject.org/ru-RU/Fedora/13/html/Security-Enhanced_Linux/index.html)  
3. Systemd. — URL: [https://wiki.archlinux.org/index.php/Systemd](https://wiki.archlinux.org/index.php/Systemd)  
4. Костромин В. А. Утилита lsof. — URL: [http://rus-linux.net/kos.php?name=/papers/lsof/lsof.html](http://rus-linux.net/kos.php?name=/papers/lsof/lsof.html)  
5. Поттеринг Л. Systemd для администраторов. — URL: [http://wiki.opennet.ru/Systemd](http://wiki.opennet.ru/Systemd)  
6. NetworkManager project. — URL: [https://wiki.gnome.org/Projects/NetworkManager](https://wiki.gnome.org/Projects/NetworkManager)  
7. nmcli project. — URL: [https://developer.gnome.org/NetworkManager/stable/nmcli.html](https://developer.gnome.org/NetworkManager/stable/nmcli.html)

