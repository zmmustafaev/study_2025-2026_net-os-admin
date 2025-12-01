---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка NFS (Лабораторная работа №13)
author:
  - Заур Мустафаев
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 24 ноября 2025

babel-lang: russian
babel-otherlangs: english

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

Освоение практических навыков настройки сервера NFS и подключения удалённых каталогов.

# Выполнение лабораторной работы

## Настройка сервера NFSv4

![Редактирование /etc/exports](Screenshot_1.png){ width=70% }

## Настройка SELinux и запуск сервиса

![Настройка SELinux для каталога NFS](Screenshot_2.png){ width=70% }

## Проверка клиента: ошибка RPC

![Ошибка RPC при showmount](Screenshot_3.png){ width=70% }

## Успешное получение экспортов

![Успешный вывод showmount](Screenshot_4.png){ width=70% }

## Анализ служб через lsof

![Процессы NFS в lsof](Screenshot_5.png){ width=70% }

## Итоговая проверка экспорта

![Успешный showmount](Screenshot_7.png){ width=70% }

## Первичное подключение

![Вывод mount | grep nfs](Screenshot_8.png){ width=70% }

## Автомонтирование через fstab

![Редактирование /etc/fstab](Screenshot_9.png){ width=70% }

## Проверка автозагрузки

![remote-fs.target активен](Screenshot_10.png){ width=70% }

## Bind-монтирование каталога www

![Появление каталога www в /srv/nfs](Screenshot_13.png){ width=70% }

## Проверка на клиенте

![Каталог www виден на клиенте](Screenshot_14.png){ width=70% }

## Экспорт каталога www

![Добавление экспорта для /srv/nfs/www](Screenshot_11.png){ width=70% }

## Создание личного каталога

![Создание каталога common и файла пользователя](Screenshot_15.png){ width=70% }

## Bind-монтирование личного каталога

![Добавление каталога home в дереве NFS](Screenshot_16.png){ width=70% }

## Проверка на клиенте

![Структура /mnt/nfs на клиенте](Screenshot_18.png){ width=70% }

## Передача файлов

![Файлы с клиента отобразились на сервере](Screenshot_19.png){ width=70% }

# Итоги работы

## Вывод

- Настроен сервер NFSv4  
- Создано дерево экспортируемых каталогов  
- Настроены SELinux, firewall, bind-монтирование  
- Реализовано автоподключение через `/etc/fstab`  
- Настроены личные каталоги пользователей  
- Созданы скрипты автоматизации для server/client  
