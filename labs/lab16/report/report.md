---
## Front matter
title: "Отчёт по лабораторной работе 16"
subtitle: "Базовая защита от атак типа «brute force»"
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

Получить навыки работы с программным средством Fail2ban для обеспечения базовой защиты от атак типа «brute force».

# Выполнение работы

## Защита сервера с помощью Fail2ban

1. На сервере установлен пакет **fail2ban** с помощью `dnf`, после чего выполнена первичная инициализация службы.

2. Сервис **fail2ban** запущен и добавлен в автозагрузку командами `systemctl start fail2ban` и `systemctl enable fail2ban`.

   ![Запуск fail2ban и включение автозагрузки](01.png){ #fig:002 width=80% }

3. В дополнительном терминале запущен просмотр журнала событий Fail2ban командой `tail -f /var/log/fail2ban.log`. Убедились, что сервис стартует корректно и подключается к базе.

   ![Просмотр журнала fail2ban.log](02.png){ #fig:003 width=80% }

4. Создан файл локальной конфигурации Fail2ban:

   `touch /etc/fail2ban/jail.d/customisation.local`

5. В файле `/etc/fail2ban/jail.d/customisation.local` выполнена настройка параметров блокировки и включена защита **SSH**:
   - задано время блокировки `bantime = 3600` (1 час);
   - активированы jail’ы: `sshd`, `sshd-ddos`, `selinux-ssh`;
   - для `sshd` указан порт `ssh,2022`.

   ![Настройка bantime и включение SSH-защиты](03.png){ #fig:004 width=80% }

6. Для применения настроек Fail2ban выполнен перезапуск службы командой `systemctl restart fail2ban`. После перезапуска в журнале подтверждено создание и запуск jail’ов для SSH.

   ![Журнал после включения jail’ов для SSH](04.png){ #fig:005 width=80% }

### Включение защиты HTTP

1. В файле `/etc/fail2ban/jail.d/customisation.local` включена защита веб-сервера (HTTP) путём активации следующих jail’ов:
   `apache-auth`, `apache-badbots`, `apache-noscript`, `apache-overflows`,  
   `apache-nohome`, `apache-botsearch`, `apache-fakegooglebot`,  
   `apache-modsecurity`, `apache-shellshock`.

   ![Включение HTTP-защиты (Apache jail’ы)](05.png){ #fig:006 width=80% }

2. Выполнен перезапуск Fail2ban (`systemctl restart fail2ban`). В журнале подтверждён запуск jail’ов Apache и привязка к соответствующим лог-файлам.

   ![Журнал после включения HTTP-защиты](06.png){ #fig:007 width=80% }

### Включение защиты почтовых сервисов

1. В файле `/etc/fail2ban/jail.d/customisation.local` включена защита почтовых сервисов путём активации jail’ов:
   `postfix`, `postfix-rbl`, `dovecot`, `postfix-sasl`.

   ![Включение защиты почты (Postfix/Dovecot jail’ы)](07.png){ #fig:008 width=80% }

2. Fail2ban перезапущен командой `systemctl restart fail2ban`. В журнале подтверждён запуск jail’ов для почтовых сервисов и корректная работа механизмов блокировки.

   ![Журнал после включения защиты почты](08.png){ #fig:009 width=80% }

## Проверка работы Fail2ban

1. На сервере проверен общий статус Fail2ban с помощью команды `fail2ban-client status`. Убедились, что служба запущена и активны все настроенные jail’ы.

2. Проверен статус защиты SSH командой `fail2ban-client status sshd`. Установлено, что на момент проверки заблокированных адресов нет.

   ![Статус jail sshd до блокировки](09.png){ #fig:011 width=80% }

3. Для SSH установлен параметр максимального количества неудачных попыток аутентификации, равный 2:

   `fail2ban-client set sshd maxretry 2`

4. С клиентской машины выполнены попытки подключения к серверу по SSH с неверным паролем.

5. После превышения допустимого числа ошибок повторно проверен статус защиты SSH. В результате зафиксирована блокировка IP-адреса клиента.

   ![Фиксация блокировки IP-адреса клиента](10.png){ #fig:012 width=80% }

6. Выполнена разблокировка IP-адреса клиента с помощью команды:

   `fail2ban-client set sshd unbanip 192.168.1.30`

7. Повторно проверен статус jail `sshd`. Убедились, что IP-адрес удалён из списка заблокированных.

   ![Статус jail sshd после разблокировки IP](11.png){ #fig:013 width=80% }

8. В файл `/etc/fail2ban/jail.d/customisation.local` внесено изменение: в разделе `[DEFAULT]` добавлено игнорирование IP-адреса клиента с помощью параметра `ignoreip`.

   ![Добавление ignoreip в конфигурацию Fail2ban](12.png){ #fig:014 width=80% }

9. Fail2ban перезапущен для применения изменений:

   `systemctl restart fail2ban`

10. Выполнен просмотр журнала событий Fail2ban. В журнале зафиксировано, что попытки подключения с указанного IP-адреса игнорируются.

   ![Журнал Fail2ban с игнорированием IP-адреса](13.png){ #fig:015 width=80% }

11. С клиента повторно выполнены попытки подключения по SSH с неверным паролем. Проверка статуса jail `sshd` показала отсутствие блокировки, что подтверждает корректность настройки параметра `ignoreip`.

## Внесение изменений в настройки внутреннего окружения виртуальных машин

1. На виртуальной машине **server** выполнен переход в каталог `/vagrant/provision/server`. Создан каталог `protect` для хранения конфигурационных файлов внутреннего окружения.

2. В каталоге `protect` создана структура под конфигурацию Fail2ban и скопирован файл локальной настройки `customisation.local`.

3. В каталоге `/vagrant/provision/server` создан исполняемый файл `protect.sh`, предназначенный для автоматической установки и запуска Fail2ban при развёртывании окружения.

   ![Скрипт protect.sh для настройки Fail2ban](14.png){ #fig:016 width=80% }

# Вывод

В ходе работы была реализована защита сервера с использованием Fail2ban. Сервис установлен, запущен и настроен для защиты SSH, веб-служб и почтовых сервисов. Проведена проверка корректности работы механизмов блокировки и разблокировки IP-адресов, а также настроено игнорирование доверенного клиента. Дополнительно выполнена подготовка конфигурационных файлов и скрипта автоматизации для повторного развёртывания среды, что упрощает администрирование и повышает надёжность системы защиты.

# Контрольные вопросы

1. **Поясните принцип работы Fail2ban.**  
   Fail2ban анализирует журналы системных и прикладных сервисов, выявляет повторяющиеся ошибки аутентификации или подозрительные действия и при превышении заданных порогов применяет меры блокировки к IP-адресу нарушителя (как правило, с помощью правил файрвола).

2. **Настройки какого файла более приоритетны: `jail.conf` или `jail.local`?**  
   Более приоритетными являются настройки из файла `jail.local`. Они переопределяют параметры, заданные в `jail.conf`, и используются для пользовательской конфигурации, чтобы избежать потери изменений при обновлении пакета.

3. **Как настроить оповещение администратора при срабатывании Fail2ban?**  
   Для этого необходимо настроить параметр `action` в конфигурации Fail2ban, указав действие с отправкой почты (например, `action_mwl`). Также требуется корректно настроить почтовую подсистему сервера и задать адрес администратора в параметре `destemail`.

4. **Поясните построчно настройки по умолчанию в `/etc/fail2ban/jail.conf`, относящиеся к веб-службе.**  
   - `enabled` — включает или отключает защиту конкретной веб-службы;  
   - `port` — указывает порт или сервис (обычно `http`, `https`);  
   - `filter` — задаёт фильтр, по которому анализируются логи;  
   - `logpath` — путь к файлам журналов веб-сервера;  
   - `maxretry` — допустимое количество нарушений до блокировки;  
   - `findtime` — временной интервал для подсчёта ошибок;  
   - `bantime` — время блокировки IP-адреса.

5. **Поясните построчно настройки по умолчанию в `/etc/fail2ban/jail.conf`, относящиеся к почтовой службе.**  
   - `enabled` — активирует защиту почтового сервиса;  
   - `filter` — определяет шаблоны ошибок аутентификации в логах;  
   - `logpath` — путь к журналам Postfix, Dovecot и других почтовых сервисов;  
   - `port` — используемые почтовые порты (smtp, pop3, imap и др.);  
   - `maxretry`, `findtime`, `bantime` — параметры, определяющие условия и длительность блокировки.

6. **Какие действия может выполнять Fail2ban при обнаружении атакующего IP-адреса? Где можно посмотреть описание действий?**  
   Fail2ban может добавлять правила блокировки в файрвол (iptables, firewalld), отправлять уведомления администратору, выполнять пользовательские скрипты. Описание доступных действий находится в каталоге `/etc/fail2ban/action.d/`.

7. **Как получить список действующих правил Fail2ban?**  
   Для просмотра активных jail’ов используется команда:  
   `fail2ban-client status`.

8. **Как получить статистику заблокированных Fail2ban адресов?**  
   Статистика по конкретному jail’у отображается командой:  
   `fail2ban-client status <jail_name>`,  
   где выводится информация о текущих и суммарно заблокированных IP-адресах.

9. **Как разблокировать IP-адрес?**  
   Разблокировка выполняется командой:  
   `fail2ban-client set <jail_name> unbanip <IP-адрес>`.


# Список литературы

1. Сайт Fail2ban. — URL: https://www.fail2ban.org (visited on 09/13/2021).