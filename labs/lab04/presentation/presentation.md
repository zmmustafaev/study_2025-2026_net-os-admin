---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Базовая настройка HTTP-сервера Apache (Лабораторная работа №4)
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 15 декабря 2025

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

Приобретение практических навыков установки и базового конфигурирования HTTP-сервера Apache, а также настройки виртуального хостинга и проверки его работы.

# Выполнение лабораторной работы

## Установка HTTP-сервера

![Установка пакетов HTTP-сервера](01.png){ #fig:001 width=70% }

## Базовое конфигурирование Apache

![Настройка firewalld и запуск httpd](02.png){ #fig:002 width=70% }

## Анализ работы HTTP-сервера

![Проверка доступа и анализ логов Apache](03.png){ #fig:003 width=70% }

## Настройка DNS-зон

![Файл прямой DNS-зоны zmustafaev.net](04.png){ #fig:004 width=70% }

## Обратная DNS-зона

![Файл обратной DNS-зоны 192.168.1](05.png){ #fig:005 width=70% }

## Виртуальные хосты Apache

![Конфигурация VirtualHost server.zmustafaev.net](06.png){ #fig:006 width=70% }

## Виртуальный хост www

![Конфигурация VirtualHost www.zmustafaev.net](07.png){ #fig:007 width=70% }

## Веб-контент виртуальных хостов

![Создание каталогов и index.html для виртуальных хостов](08.png){ #fig:008 width=70% }

## Проверка доступа к server.zmustafaev.net

![Доступ к server.zmustafaev.net](09.png){ #fig:009 width=70% }

## Проверка доступа к www.zmustafaev.net

![Доступ к www.zmustafaev.net](10.png){ #fig:010 width=70% }

## Автоматизация настройки HTTP-сервера

![Сценарий автоматической настройки http.sh](11.png){ #fig:011 width=70% }

# Выводы по проделанной работе

## Вывод

В ходе лабораторной работы был установлен и настроен HTTP-сервер Apache. Выполнено базовое конфигурирование веб-сервера, настроены правила межсетевого экрана и проверена корректность запуска службы. Реализован виртуальный хостинг для двух доменных имён с использованием DNS. Созданы тестовые страницы и подготовлен сценарий автоматизации для среды Vagrant, что упростило повторное развёртывание конфигурации.
