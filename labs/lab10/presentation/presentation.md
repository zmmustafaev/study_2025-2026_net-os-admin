---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: "Настройка SMTP-аутентификации и SMTP over TLS"
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 05 ноября 2025

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цели работы

## Цель
Получение навыков настройки SMTP-аутентификации и защищённого SMTP over TLS на базе Postfix и Dovecot.

# Выполнение работы

## Добавление протокола LMTP в Dovecot
![Добавление LMTP в список протоколов](01.png){width=75%}

## Настройка сервиса LMTP
![Настройка сервиса LMTP](02.png){width=75%}

## Настройка формата логина
![Настройка auth_username_format](03.png){width=75%}

## Отправка тестового письма
![Отправка письма](04.png){width=75%}

## Доставка по LMTP — журнал Postfix/Dovecot
![Журналы LMTP](05.png){width=85%}

## Получение письма в Maildir
![Письмо получено на сервере](06.png){width=85%}

## Dovecot: unix-socket для Postfix
![Сервис auth в Dovecot](07.png){width=75%}

## Настройка SASL (Postfix)
![Команды postconf](08.png){width=85%}

## Изменения master.cf
![master.cf — SMTP AUTH](09.png){width=75%}

## Проверка SMTP AUTH через telnet
![telnet AUTH PLAIN](10.png){width=85%}

## Копирование сертификата Dovecot
![TLS — копирование сертификатов](11.png){width=85%}

## master.cf — порт 587 (submission)
![master.cf — STARTTLS](12.png){width=75%}

## Проверка подключения по TLS (openssl)
![openssl STARTTLS](13.png){width=85%}

## SMTP-клиент Evolution — настройка отправки почты
![SMTP в Evolution](14.png){width=75%}

## Получение письма через TLS + LMTP
![Письмо доставлено](15.png){width=85%}

## Логи доставки через TLS
![Логи TLS Доставки](16.png){width=85%}

# Вывод

## Вывод

В результате работы:

- Настроено взаимодействие **Postfix ↔ Dovecot через LMTP**
- Реализована **SMTP-аутентификация (SASL, unix-socket)**
- Настроен **SMTP over TLS (587 / STARTTLS)**
- Доставку писем проверили через telnet, openssl и почтовый клиент
- Конфигурация автоматизирована через provisioning-скрипты Vagrant