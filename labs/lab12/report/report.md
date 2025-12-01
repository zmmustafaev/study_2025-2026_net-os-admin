---
## Front matter
title: "Отчёт по лабораторной работе 12"
subtitle: "Синхронизация времени"
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

Получение навыков по управлению системным временем и настройке синхронизации времени.

# Выполнение работы

## Настройка параметров времени

### Просмотр и анализ текущих настроек даты и времени

1. На сервере и клиенте были изучены параметры времени с помощью команды timedatectl.  
   Определено, что обе машины работают в зоне UTC (UTC +0000), сетевой сервис синхронизации активен, но системные часы не находятся в состоянии синхронизации.

   ![Параметры времени на сервере](01.png){ #fig:001 width=80% }

   ![Параметры времени на клиенте](02.png){ #fig:002 width=80% }

2. Проверено текущее системное время на обеих машинах.

3. Просмотрено аппаратное время (RTC) сервера и клиента.

## Управление синхронизацией времени

### Проверка источников времени

1. На сервере и клиенте были просмотрены источники времени в chrony.  
   Полученная информация показала состояние связей с внешними NTP-серверами, их stratum, разницу во времени и уровень доступности.

   ![Источники времени server](03.png){ #fig:007 width=80% }

   ![Источники времени client](04.png){ #fig:008 width=80% }

Пояснение:  
Символ `^*` означает активный источник синхронизации.  
`^-` — доступный, но не выбранный сервер.  
Поле Last sample показывает расхождение, а Reach — успешность последних запросов.

### Настройка NTP-сервера

1. На сервере в конфигурацию добавлено разрешение для сети 192.168.0.0/16.

   ![Изменения в chrony.conf server](05.png){ #fig:009 width=80% }

2. Служба chronyd была перезапущена и настроен межсетевой экран для разрешения доступа к NTP-сервису.

   ![Настройка firewall для NTP](06.png){ #fig:010 width=80% }

### Настройка клиента

1. В конфигурации клиента задан сервер синхронизации server.zmustafaev.net.  
   Остальные директивы server удалены.

   ![chrony.conf client](07.png){ #fig:011 width=80% }

2. После перезапуска службы chronyd клиент начал синхронизироваться с сервером.

   ![Проверка источников времени client](08.png){ #fig:012 width=80% }

Пояснение:  
Клиент успешно использует сервер server.zmustafaev.net в качестве основного источника времени.  
Это подтверждается флагом `^*` и стабильным показателем Last sample.

## Настройка внутреннего окружения виртуальных машин

### Настройка окружения сервера

1. Создан каталог для конфигурации и сохранён файл chrony.conf.  
2. В каталоге серверного окружения подготовлен скрипт автоматического развёртывания конфигурации и настроек firewall.

   ![Скрипт ntp.sh server](09.png){ #fig:013 width=80% }

### Настройка окружения клиента

1. Создан каталог окружения клиента и помещён файл chrony.conf.  
2. Создан скрипт для автоматического применения конфигурации и перезапуска chronyd.

   ![Скрипт ntp.sh client](10.png){ #fig:014 width=80% }

# Вывод

В ходе работы были изучены параметры времени на сервере и клиенте, рассмотрены системные и аппаратные часы. Настроена служба синхронизации chrony: сервер был сконфигурирован для обслуживания внутренней сети, а клиент — для получения точного времени от сервера. Проведена проверка корректности синхронизации. Добавлены скрипты автоматизации для Vagrant, что упростило повторное развёртывание конфигурации и исключило ошибки при ручной настройке.

# Контрольные вопросы

1. **Почему важна точная синхронизация времени для служб баз данных?**  
   Потому что расхождение времени приводит к ошибкам транзакций, нарушению целостности данных и некорректной обработке журналов.

2. **Почему служба проверки подлинности Kerberos зависит от правильной синхронизации времени?**  
   Kerberos использует временные метки в тикетах, и при расхождении времени аутентификация блокируется как потенциально небезопасная.

3. **Какая служба используется по умолчанию для синхронизации времени в RHEL 7?**  
   По умолчанию используется chrony.

4. **Какова страта по умолчанию для локальных часов?**  
   Локальные часы имеют страту 10.

5. **Какой порт должен быть открыт в брандмауэре для работы NTP?**  
   Необходимо открыть порт 123/UDP.

6. **Какую строку нужно добавить в конфигурацию chrony, чтобы сервер оставался источником времени даже без внешних NTP?**  
   Нужно указать директиву `local stratum 10`.

7. **Какую страту имеет хост, если у него нет текущей синхронизации NTP?**  
   Он использует локальное время со стратой 10.

8. **Как узнать, с какими серверами синхронизируется chrony?**  
   Для этого используется команда `chronyc sources`.

9. **Как получить подробную статистику времени и состояния синхронизации в chrony?**  
   Просмотр доступен через команду `chronyc tracking`.


# Список литературы

1. *How to Sync Time in Linux Server using Chrony.* LinuxTechi. URL: https://www.linuxtechi.com/sync-time-in-linux-server-using-chrony/. :contentReference[oaicite:3]{index=3}  
2. *Understanding NTP Servers: Why They Matter and How to Use Them.* Medium article. URL: https://medium.com/%40ahmedmaher22292/understanding-ntp-servers-why-they-matter-and-how-to-use-them-07978ba03956. :contentReference[oaicite:4]{index=4}  
3. *Setting up Time Synchronisation with Chrony on RHEL 7.* Lisenet Blog. URL: https://www.lisenet.com/2016/time-synchronisation-with-chrony-on-rhel-7/. :contentReference[oaicite:5]{index=5}  
