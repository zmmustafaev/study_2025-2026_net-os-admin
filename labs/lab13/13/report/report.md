---
## Front matter
title: "Отчёт по лабораторной работе 13"
subtitle: "Настройка NFS"
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

Приобретение навыков настройки сервера NFS для удалённого доступа к ресурсам.

# Выполнение работы

## Настройка сервера NFSv4

1. На сервере установлено необходимое программное обеспечение **nfs-utils**.

2. Создан каталог, который используется как корень экспортируемого дерева NFS.

3. В файл `/etc/exports` добавлена строка экспорта каталога с доступом только для чтения:

![Редактирование /etc/exports](Screenshot_1.png){ #fig:001 width=80% }

4. Для каталога назначен SELinux-контекст `nfs_t`.

5. Изменённый контекст применён ко всей структуре каталога.

![Настройка SELinux для каталога NFS](Screenshot_2.png){ #fig:002 width=80% }

6. Сервис NFS запущен и добавлен в автозагрузку.

7. В межсетевом экране разрешены службы NFS и изменения сделаны постоянными.

8. На клиентской машине установлено ПО NFS.

9. Первая попытка узнать список экспортируемых каталогов завершилась ошибкой RPC.

![Ошибка RPC при showmount](Screenshot_3.png){ #fig:003 width=80% }

Клиент не получил список экспортов, поскольку firewall на сервере блокировал вспомогательные службы RPC.

10. После повторного обращения экспорт стал виден.

![Успешный вывод showmount](Screenshot_4.png){ #fig:004 width=80% }

11. На сервере временно остановлен `firewalld`.

12. Клиент снова смог получить список экспортов.

После отключения файрвола ограничения снялись, и клиент смог связаться со службами `rpcbind` и `mountd`.

13. Firewall снова включён.

Просмотрены активные процессы, работающие через TCP/UDP. Видны службы:  
`rpcbind`, `mountd`, `statd`.

![Процессы NFS в lsof](Screenshot_5.png){ #fig:005 width=80% }

14. Разрешены службы `mountd` и `rpc-bind`, изменения сохранены постоянно.

![Добавление служб mountd и rpc-bind в firewall](Screenshot_6.png){ #fig:006 width=80% }

15. После этого клиент успешно получает список экспортов.

![Успешный showmount](Screenshot_7.png){ #fig:007 width=80% }

## Монтирование NFS на клиенте

1. Создан каталог `/mnt/nfs`, куда был подключён экспортируемый ресурс.

2. Проверено успешное монтирование.

![Вывод mount | grep nfs](Screenshot_8.png){ #fig:008 width=80% }

Видно подключение ресурса по протоколу **NFSv4**, через **TCP**, с параметрами `hard`, `sec=sys`, `vers=4.2`.

3. Добавлена строка в конец файла `/etc/fstab`:

![Редактирование /etc/fstab](Screenshot_9.png){ #fig:009 width=80% }

**Пояснение синтаксиса:**
- первый параметр — удалённый экспорт;
- второй — точка монтирования на клиенте;
- тип файловой системы — `nfs`;
- параметр `_netdev` заставляет ждать поднятия сети;
- `0 0` — отключение записи в архив dump и проверки fsck.

4. Проверено состояние автоматического монтирования.

![remote-fs.target активен](Screenshot_10.png){ #fig:010 width=80% }

5. После перезагрузки клиент автоматически подключает NFS-ресурс.

## Подключение каталогов к дереву NFS

1. На сервере создан каталог для размещения веб-контента внутри дерева NFS.

2. Каталог веб-сервера был подмонтирован внутрь дерева NFS с использованием bind-монтирования.

3. Проверена структура каталога `/srv/nfs` — внутри появился каталог `www`.

   ![Появление каталога www в /srv/nfs](Screenshot_13.png){ #fig:013 width=80% }

4. На клиенте в `/mnt/nfs` также стал отображаться каталог `www`.

   ![Каталог www виден на клиенте](Screenshot_14.png){ #fig:014 width=80% }

5. В `/etc/exports` добавлена строка для экспорта каталога веб-сервера:

![Добавление экспорта для /srv/nfs/www](Screenshot_11.png){ #fig:0011width=80% }

6. Выполнено обновление экспортов.

7. На клиенте проверено содержимое `/mnt/nfs` — каталог `www` доступен.

8. В конец файла `/etc/fstab` добавлена строка:

![Добавление записи для bind-монтирования в /etc/fstab](Screenshot_12.png){ #fig:012 width=80% }

9. Экспорты повторно обновлены.

10. Клиент повторно проверил `/mnt/nfs` — каталог `www` отображается корректно.

## Подключение каталогов для работы пользователей

1. На сервере под пользователем **zmustafaev** создан личный каталог `common` с правами 700 и файл `zmustafaev@server.txt`.

![Создание каталога common и файла пользователя](Screenshot_15.png){ #fig:015 width=80% }

2. Создан каталог в дереве NFS для личных данных пользователя:

![Добавление каталога home в дереве NFS](Screenshot_16.png){ #fig:016 width=80% }

3. Личный каталог пользователя был подмонтирован внутрь структуры NFS с помощью bind:

**Права доступа:**  
— каталог `/home/zmustafaev/common` имеет права `700`,  
— внутри лежат файлы, принадлежащие пользователю.

4. В `/etc/exports` добавлен экспорт личного каталога пользователя:

5. В `/etc/fstab` внесена строка для bind-монтирования:

![Запись fstab для каталога пользователя](Screenshot_17.png){ #fig:017 width=80% }

6. Экспорты были обновлены.

7. На клиенте в `/mnt/nfs` появились каталоги `home` и `www`.

![Структура /mnt/nfs на клиенте](Screenshot_18.png){ #fig:018 width=80% }

8. Пользователь на клиенте перешёл в каталог:

Создал файл **zmustafaev@client.txt**.

Попытка зайти в каталог от root привела к ошибке:

**Причина:** каталог имеет права `700`, доступ только у владельца.

9. На сервере проверено содержимое каталога `~/common` — файлы с клиента появились.

![Файлы с клиента отобразились на сервере](Screenshot_19.png){ #fig:019 width=80% }

## Внесение изменений в настройки внутреннего окружения виртуальных машин

1. На виртуальной машине **server** выполнен переход в каталог, предназначенный для хранения локальных конфигураций Vagrant:

2. Создан каталог `nfs/etc`, в который помещаются конфигурационные файлы службы NFS:

3. В подкаталог `etc` скопирован файл экспорта каталогов:

4. В каталоге `/vagrant/provision/server` создан исполняемый файл:

5. Файл содержит автоматизацию установки и настройки NFS-сервера:

![Скрипт nfs.sh на сервере](Screenshot_20.png){ #fig:020 width=80% }

6. В каталоге `/vagrant/provision/client` создан скрипт для клиента:

![Скрипт клиента для автоматического монтирования NFS](Screenshot_21.png){ #fig:021 width=80% }

# Вывод

В ходе выполнения работы были настроены и подключены каталоги к дереву NFS. Реализованы экспорт общих ресурсов, настройка SELinux и firewall, монтирование через bind, а также автоматизация подключения каталогов при загрузке системы. Добавлены скрипты для автоматической конфигурации серверной и клиентской виртуальных машин. Проверена работа пользовательских каталогов и корректность прав доступа.

# Контрольные вопросы

1. **Как называется файл конфигурации, содержащий общие ресурсы NFS?**
   Это файл **`/etc/exports`**, в котором задаются параметры экспорта каталогов для клиентов NFS.

2. **Какие порты должны быть открыты в брандмауэре для полного доступа к серверу NFS?**
   Необходимо разрешить службы, соответствующие следующим портам:

   * **NFS** — порт 2049/TCP
   * **rpcbind** — порт 111/TCP и UDP
   * **mountd** — динамический порт (назначается системой), обычно открывается через сервис `mountd` в firewalld
     Вместо ручного указания портов используется команда:
     **`firewall-cmd --add-service=nfs`**,
     **`firewall-cmd --add-service=rpc-bind`**,
     **`firewall-cmd --add-service=mountd`**.

3. **Какую опцию следует использовать в /etc/fstab, чтобы убедиться, что общие ресурсы NFS могут быть установлены автоматически при перезагрузке?**
   Используется опция **`_netdev`**, указывающая системе монтировать ресурс только после поднятия сетевых интерфейсов.

# Список литературы

1. Официальная документация Red Hat по NFS — https://access.redhat.com/documentation/
2. man-страницы системных утилит: `nfs`, `exports`, `exportfs`, `mount`, `rpcbind`, `firewalld`.
