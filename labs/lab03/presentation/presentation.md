---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка DHCP-сервера (Лабораторная работа №3)
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 14 сентября 2025

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

Получить практические навыки установки и настройки DHCP-сервера, а также интеграции его с DNS для динамического обновления зон.

# Выполнение лабораторной работы

## Конфигурирование DHCP-сервера

![Редактирование domain-name и domain-search](Screenshot_1.png){ #fig:001 width=70% }

## Настройка подсети

![Конфигурация подсети DHCP](Screenshot_2.png){ #fig:002 width=70% }

## Прямая зона DNS

![Редактирование прямой зоны DNS](Screenshot_3.png){ #fig:003 width=70% }

## Обратная зона DNS

![Редактирование обратной зоны DNS](Screenshot_4.png){ #fig:004 width=70% }

## Проверка работы DHCP

![Проверка доступности DHCP-сервера](Screenshot_5.png){ #fig:005 width=70% }

# Анализ работы DHCP-сервера

## Интерфейсы клиента

![Сетевые интерфейсы на клиенте](Screenshot_7.png){ #fig:006 width=70% }

## База аренд DHCP

![Файл с арендой DHCP-адресов](Screenshot_8.png){ #fig:007 width=70% }

# Настройка обновления DNS-зоны

## Внесение изменений в Bind

![Настройка зон с поддержкой обновления](Screenshot_9.png){ #fig:008 width=70% }

## TSIG-ключи Kea

![Файл tsig-keys.json](Screenshot_10.png){ #fig:009 width=70% }

## Конфигурация kea-dhcp-ddns

![Настройка kea-dhcp-ddns.conf](Screenshot_11.png){ #fig:010 width=70% }

## Проверка работы Kea DDNS

![Запуск службы kea-dhcp-ddns](Screenshot_12.png){ #fig:011 width=70% }

## DHCP и DDNS вместе

![Изменения в конфигурации kea-dhcp4.conf](Screenshot_13.png){ #fig:012 width=70% }

## Перезапуск DHCP-сервера

![Запуск DHCP-сервера после изменений](Screenshot_14.png){ #fig:013 width=70% }

## Проверка DNS-записи клиента

![Проверка DNS-записи клиента](Screenshot_15.png){ #fig:014 width=70% }

# Выводы по проделанной работе

## Вывод

В ходе работы была развернута инфраструктура с двумя виртуальными машинами — **server** и **client**.  
На сервере установлены и настроены службы **DNS** и **DHCP**, реализована поддержка **DDNS**.  
Клиенты автоматически получают IP-адреса и регистрируются в DNS-зоне.  
Конфигурации вынесены в окружение Vagrant для автоматизации запуска.
