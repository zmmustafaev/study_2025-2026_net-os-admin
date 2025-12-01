---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка DNS-сервера (Лабораторная работа №2)
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 8 сентября 2025

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

Получение практических навыков установки и настройки DNS-сервера на базе BIND.

# Выполнение лабораторной работы

## Проверка работы сервера

![Запрос dig к www.yandex.ru](01.png){ #fig:001 width=70% }

## Настройка системных параметров

![Сравнение dig-запросов](02.png){ #fig:002 width=70% }

## Конфигурация сетевого подключения

![Настройка подключения через nmcli](03.png){ #fig:003 width=70% }

## Изменения в named.conf

![Изменения в named.conf](04.png){ #fig:004 width=70% }

## Проверка работы DNS

![Проверка работы DNS через lsof](05.png){ #fig:005 width=70% }

## Создание зоны zmustafaev.net

![Подключение файла зоны zmustafaev.net](06.png){ #fig:006 width=70% }

## Прямая зона

![Файл прямой зоны zmustafaev.net](12.png){ #fig:012 width=70% }

## Обратная зона

![Файл обратной зоны 192.168.1](09.png){ #fig:009 width=70% }

## SELinux и права доступа

![Настройка прав и SELinux](10.png){ #fig:010 width=70% }

## Конфигурация зон

![Файл конфигурации зон](11.png){ #fig:011 width=70% }

## Проверка работы зоны через dig

![Проверка зоны через dig](13.png){ #fig:013 width=70% }

## Проверка работы зоны через host

![Проверка зоны через host](14.png){ #fig:014 width=70% }

# Выводы по проделанной работе

## Вывод

В ходе лабораторной работы был установлен и настроен DNS-сервер на основе BIND. Реализованы прямая и обратная зоны, проведена проверка их работы через `dig` и `host`. Настроен кэширующий сервер и автоматизация конфигурации с помощью Vagrant.

