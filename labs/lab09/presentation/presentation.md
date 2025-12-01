---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка POP3/IMAP сервера (Лабораторная работа №9)
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 29 октября 2025

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

# Цели и задачи работы

## Цель лабораторной работы

Приобретение практических навыков по установке и конфигурированию POP3/IMAP-сервера на базе Postfix и Dovecot.

# Выполнение лабораторной работы

## Установка Dovecot

![Установка dovecot и telnet](Screenshot_1.png){ #fig:001 width=70% }

## Настройка протоколов

![Настройка поддерживаемых протоколов в dovecot.conf](Screenshot_2.png){ #fig:002 width=70% }

## Настройка аутентификации

![Проверка параметра аутентификации в 10-auth.conf](Screenshot_3.png){ #fig:003 width=70% }

## Настройка PAM и passwd

![Настройка PAM и passwd в auth-system.conf.ext](Screenshot_4.png){ #fig:004 width=70% }

## Настройка почтового хранилища

![Настройка mail_location в 10-mail.conf](Screenshot_5.png){ #fig:005 width=70% }

## Настройка межсетевого экрана

![Настройка правил firewall для POP3 и IMAP](Screenshot_6.png){ #fig:006 width=70% }

# Проверка работы Dovecot

## Мониторинг почтовых логов

![Мониторинг журнала maillog](Screenshot_7.png){ #fig:007 width=70% }

## Настройка клиента Evolution

![Настройка личных данных пользователя в Evolution](Screenshot_8.png){ #fig:008 width=70% }

## Настройка входящей почты IMAP

![Настройка входящей почты IMAP](Screenshot_9.png){ #fig:009 width=70% }

## Настройка исходящей почты SMTP

![Настройка исходящей почты SMTP](Screenshot_10.png){ #fig:010 width=70% }

## Отправка тестового письма

![Отправка тестового письма из Evolution](Screenshot_11.png){ #fig:011 width=70% }

## Получение письма во входящих

![Получение тестового письма во входящих](Screenshot_12.png){ #fig:012 width=70% }

## Лог успешной доставки

![Лог успешной доставки и входа пользователя](Screenshot_13.png){ #fig:013 width=70% }

# Выводы по проделанной работе

## Вывод

В ходе лабораторной работы был установлен и настроен почтовый сервер на базе **Postfix** и **Dovecot**.  
Реализованы протоколы **SMTP**, **IMAP** и **POP3**, выполнена проверка передачи и приёма писем с помощью **Evolution** и **telnet**.  
Настроены межсетевой экран и автоматизация конфигурации в **Vagrant**.
