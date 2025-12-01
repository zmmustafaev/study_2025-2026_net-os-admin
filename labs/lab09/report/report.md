---
## Front matter
title: "Отчёт по лабораторной работе 9"
subtitle: "Настройка POP3/IMAP сервера"
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

Приобретение практических навыков по установке и простейшему конфигурированию POP3/IMAP-сервера.

# Выполнение работы

## Установка и настройка Dovecot

1. На виртуальной машине **server** выполнен вход под пользователем и произведён переход в режим суперпользователя с помощью команды `sudo -i`.

2. Установлены необходимые для работы почтового сервера пакеты **dovecot** и **telnet**.  
   Установка завершена успешно, все зависимости удовлетворены.

   ![Установка dovecot и telnet](Screenshot_1.png){ #fig:001 width=80% }

3. В конфигурационном файле `/etc/dovecot/dovecot.conf` задан список поддерживаемых почтовых протоколов — **IMAP** и **POP3**.

   ![Настройка поддерживаемых протоколов в dovecot.conf](Screenshot_2.png){ #fig:002 width=80% }

4. В файле `/etc/dovecot/conf.d/10-auth.conf` указан метод аутентификации пользователей — **plain**, что позволяет использовать стандартную проверку имени и пароля.

   ![Проверка параметра аутентификации в 10-auth.conf](Screenshot_3.png){ #fig:003 width=80% }

5. В файле `/etc/dovecot/conf.d/auth-system.conf.ext` настроено использование системных механизмов аутентификации **PAM** и **passwd** для проверки данных пользователей.

   ![Настройка PAM и passwd в auth-system.conf.ext](Screenshot_4.png){ #fig:004 width=80% }

6. В конфигурационном файле `/etc/dovecot/conf.d/10-mail.conf` указано месторасположение почтовых ящиков пользователей — директория **Maildir** в домашнем каталоге.

   ![Настройка mail_location в 10-mail.conf](Screenshot_5.png){ #fig:005 width=80% }

7. Настроен межсетевой экран — добавлены правила для разрешения работы служб **POP3**, **POP3S**, **IMAP** и **IMAPS**, затем правила были применены и перезагружены.

   ![Настройка правил firewall для POP3 и IMAP](Screenshot_6.png){ #fig:006 width=80% }

8. После внесения изменений службы **Postfix** и **Dovecot** были перезапущены, а Dovecot добавлен в автозагрузку.

## Проверка работы Dovecot

1. На сервере запущено наблюдение за журналом почтовых событий с помощью команды `tail -f /var/log/maillog`, что позволяет отслеживать процесс получения и отправки писем в реальном времени.

   ![Мониторинг журнала maillog](Screenshot_7.png){ #fig:007 width=80% }

2. На виртуальной машине **client** установлен почтовый клиент **Evolution** для тестирования работы службы электронной почты.

3. В настройках учётной записи указано имя пользователя и адрес электронной почты в домене **zmustafaev.net**.

   ![Настройка личных данных пользователя в Evolution](Screenshot_8.png){ #fig:008 width=80% }

4. Настроено получение почты через протокол **IMAP**. В качестве сервера входящей почты задан **mail.zmustafaev.net**, порт **143**, шифрование — **STARTTLS after connecting**, метод аутентификации — **Password**.

   ![Настройка входящей почты IMAP](Screenshot_9.png){ #fig:009 width=80% }

5. Настроено отправление почты через протокол **SMTP**. Указан сервер **mail.zmustafaev.net**, порт **25**, без шифрования и без аутентификации.

   ![Настройка исходящей почты SMTP](Screenshot_10.png){ #fig:010 width=80% }

6. С помощью клиента **Evolution** отправлено тестовое письмо с темой *test1* на собственный адрес для проверки доставки.

   ![Отправка тестового письма из Evolution](Screenshot_11.png){ #fig:011 width=80% }

7. Полученное сообщение успешно отобразилось во входящих письмах, что подтверждает корректную работу сервисов **Postfix** и **Dovecot**.

   ![Получение тестового письма во входящих](Screenshot_12.png){ #fig:012 width=80% }

8. На сервере в журнале `/var/log/maillog` зафиксированы записи о передаче и доставке письма в почтовый ящик пользователя, а также об успешной IMAP-аутентификации.

   ![Лог успешной доставки и входа пользователя](Screenshot_13.png){ #fig:013 width=80% }

9. Для дополнительной проверки подключение к серверу было выполнено по протоколу **POP3** через **telnet**.  
   Были выведены список писем, прочитано одно сообщение и удалено другое, после чего сессия завершена. Это подтверждает корректную работу почтового сервера по протоколу POP3.

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. На виртуальной машине **server** выполнен переход в каталог `/vagrant/provision/server/`, предназначенный для настройки внутреннего окружения системы.

2. Создана структура каталогов для хранения конфигурационных файлов почтового сервера **Dovecot**, включающая подкаталоги `/mail/etc/dovecot/conf.d/`.

3. Скопированы актуальные конфигурационные файлы из `/etc/dovecot/` в подготовленные каталоги окружения Vagrant, чтобы сохранить текущие настройки для автоматического развёртывания.

   ![Копирование конфигурационных файлов Dovecot](Screenshot_14.png){ #fig:014 width=80% }

4. В файл сценария автоматической настройки `/vagrant/provision/server/mail.sh` добавлены команды для установки необходимых пакетов (**postfix**, **dovecot**, **telnet**, **s-nail**), настройки межсетевого экрана и конфигурации служб.

   ![Сценарий настройки сервера mail.sh](Screenshot_15.png){ #fig:015 width=80% }

5. Скрипт производит:
   - установку необходимых компонентов;
   - копирование подготовленных конфигураций в системные каталоги `/etc/`;
   - настройку правил файрвола для разрешения работы служб **SMTP**, **POP3**, **POP3S**, **IMAP**, **IMAPS**;
   - восстановление контекста SELinux;
   - активацию и запуск сервисов **Postfix** и **Dovecot**.

   ![Основные команды конфигурации и запуска служб в mail.sh](Screenshot_16.png){ #fig:016 width=80% }

6. На виртуальной машине **client** в каталоге `/vagrant/provision/client/` обновлён файл `mail.sh`, в который добавлены строки для установки почтового клиента **Evolution**.

   ![Сценарий настройки клиента mail.sh](Screenshot_17.png){ #fig:017 width=80% }

# Вывод

В ходе работы был установлен и настроен почтовый сервер на базе **Postfix** и **Dovecot**. Реализованы поддержка протоколов **SMTP**, **IMAP** и **POP3**, обеспечивающих отправку и получение электронной почты. Настроены механизмы аутентификации пользователей, межсетевой экран и структура хранения писем в формате **Maildir**. Проведена проверка доставки и получения писем с использованием почтового клиента **Evolution** и через **telnet**. Созданы и протестированы сценарии автоматического развёртывания серверной и клиентской конфигурации с помощью **Vagrant**.

# Контрольные вопросы

1. **За что отвечает протокол SMTP?**  
   Протокол **SMTP (Simple Mail Transfer Protocol)** используется для передачи электронной почты от клиента к почтовому серверу и между серверами. Он обеспечивает доставку сообщений в почтовые ящики получателей.

2. **За что отвечает протокол IMAP?**  
   **IMAP (Internet Message Access Protocol)** предназначен для получения почты с сервера с возможностью синхронизации между несколькими клиентами. При этом письма хранятся на сервере, а пользователь работает с их копиями.

3. **За что отвечает протокол POP3?**  
   **POP3 (Post Office Protocol v3)** используется для загрузки писем с сервера на клиентское устройство. После получения письма обычно удаляются с сервера, что отличает POP3 от IMAP.

4. **В чём назначение Dovecot?**  
   **Dovecot** — это сервер, обеспечивающий доступ пользователей к их почтовым ящикам по протоколам IMAP и POP3. Он отвечает за аутентификацию, безопасность соединений и управление почтовыми хранилищами.

5. **В каких файлах обычно находятся настройки работы Dovecot? За что отвечает каждый из файлов?**  
   - `/etc/dovecot/dovecot.conf` — основной файл конфигурации, определяет общие параметры и активные протоколы.  
   - `/etc/dovecot/conf.d/10-auth.conf` — задаёт методы аутентификации пользователей.  
   - `/etc/dovecot/conf.d/auth-system.conf.ext` — описывает источники данных пользователей и паролей (PAM, passwd и т. д.).  
   - `/etc/dovecot/conf.d/10-mail.conf` — определяет формат и месторасположение почтовых ящиков (Maildir, mbox и т. д.).  

6. **В чём назначение Postfix?**  
   **Postfix** — это почтовый транспортный агент (MTA), отвечающий за отправку, маршрутизацию и приём электронной почты. Он взаимодействует с SMTP и передаёт письма пользователям или другим серверам.

7. **Какие методы аутентификации пользователей можно использовать в Dovecot и в чём их отличие?**  
   - **plain** — передача имени пользователя и пароля в открытом виде (часто используется с TLS).  
   - **login** — подобен plain, но имеет дополнительный этап подтверждения.  
   - **CRAM-MD5, DIGEST-MD5** — используют хэширование для защиты паролей.  
   - **PAM** — применяет системную аутентификацию Linux.  
   Отличие заключается в уровне безопасности и способе проверки подлинности пользователя.

8. **Пример заголовка письма с пояснениями его полей:**  

```
From: zmustafaev zmustafaev@zmustafaev.net # Отправитель
To: zmustafaev@zmustafaev.net # Получатель
Subject: test1 # Тема письма
Date: Wed, 29 Oct 2025 15:09:05 +0000 # Дата и время отправки
Message-ID: 70e3dde61326c29...@zmustafaev.net # Уникальный идентификатор письма
```

9. **Примеры использования команд для работы с почтовыми протоколами через терминал:**  

```
telnet mail.zmustafaev.net 110 # Подключение по POP3
user zmustafaev # Ввод имени пользователя
pass 123456 # Ввод пароля
list # Просмотр списка писем
retr 1 # Получение первого письма
dele 2 # Удаление второго письма
quit # Завершение сеанса
```


10. **Примеры работы с doveadm:**  

```
doveadm mailbox list -u zmustafaev # Просмотр списка почтовых ящиков пользователя
doveadm user zmustafaev # Проверка информации о пользователе
doveadm fetch -u zmustafaev 'text' mailbox # Извлечение содержимого писем
doveadm auth test zmustafaev 123456 # Проверка аутентификации пользователя
```

# Список литературы

1. Dovecot Documentation. — URL: https://dovecot.org/documentation (visited on 09/13/2021).
2. Postfix Documentation. — URL: http://www.postfix.org/documentation.html (visited on 09/13/2021).