---
## Front matter
title: "Отчёт по лабораторной работе №10"
subtitle: " Основы работы с модулями ядра операционной системы"
author: "Кандакжи Микаил Халидович"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true
toc-depth: 2
lof: true
lot: true
fontsize: 12pt
linestretch: 1.5
papersize: a4
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
  - \usepackage{float}
  - \floatplacement{figure}{H}
---

# Цель работы

Получить навыки работы с утилитами управления модулями ядра операционной системы.

# Выполнение

## Управление модулями ядра из командной строки

1. Сначала был запущен терминал и получены права администратора с помощью команды **su -**.  
   Затем с помощью команды **lspci -k** были выведены устройства, подключённые к системе, и соответствующие им модули ядра.  
   В списке отображены контроллеры различных типов:  
   - **Host bridge**, **ISA bridge** и **IDE interface** — отвечают за взаимодействие центральной шины и устройств ввода-вывода;  
   - **VGA controller** — видеоконтроллер VMware SVGA II с драйвером `vmwgfx`;  
   - **Ethernet controller** — сетевой адаптер Intel PRO/1000 с драйвером `e1000`;  
   - **Audio controller** — звуковой адаптер AC’97 с модулем `snd_intel8x0`;  
   - **USB controller** и **SATA controller** — обеспечивают работу портов USB и интерфейса SATA.  

   ![Вывод команды lspci -k](Screenshot_1.png){ #fig:001 width=80% }

2. Для просмотра списка всех загруженных модулей ядра использовалась команда **lsmod | sort**.  
   Отображены модули ядра и количество зависимостей между ними, включая сетевые, файловые и звуковые драйверы.  

   ![Список загруженных модулей ядра](Screenshot_2.png){ #fig:002 width=80% }

3. Для проверки наличия модуля **ext4** была использована команда **lsmod | grep ext4**, показавшая, что модуль не загружен.  
   Далее выполнена команда **modprobe ext4** для загрузки модуля ядра, после чего повторная проверка подтвердила его успешную активацию.  
   Затем команда **modinfo ext4** вывела подробную информацию о модуле:  
   - тип лицензии — *GPL*;  
   - описание — *Fourth Extended Filesystem*;  
   - авторы — разработчики подсистемы Linux;  
   - зависимости — `jbd2`, `mbcache`;  
   - версия ядра и цифровая подпись — *Rocky kernel signing key*.  

   ![Загрузка и информация о модуле ext4](Screenshot_3.png){ #fig:003 width=80% }

4. Попытка выгрузить модуль **ext4** с помощью **modprobe -r ext4** завершилась успешно,  
   однако модуль **xfs** не был выгружен, так как система сообщила, что он находится в использовании.  

   ![Выгрузка модулей ext4 и xfs](Screenshot_4.png){ #fig:004 width=80% }

## Загрузка модулей ядра с параметрами

1. Проверено, загружен ли модуль **bluetooth**, с помощью команды **lsmod | grep bluetooth**.  
   После выполнения команды **modprobe bluetooth** модуль был успешно загружен.  
   Повторная проверка показала, что модуль активен и использует зависимость `rfkill`.  

   ![Загрузка модуля bluetooth](Screenshot_5.png){ #fig:005 width=80% }

2. Команда **modinfo bluetooth** отобразила сведения о модуле:  
   - лицензия — *GPL*;  
   - версия — *2.22*;  
   - описание — *Bluetooth Core ver 2.22*;  
   - автор — *Marcel Holtmann*;  
   - параметры, доступные для настройки:  
     - `disable_esco` — отключение создания eSCO-соединений;  
     - `disable_ertm` — отключение режима Enhanced Retransmission;  
     - `enable_ecred` — включение расширенного управления кредитным потоком.  

   После анализа модуль был успешно выгружен с помощью **modprobe -r bluetooth**.  

   ![Информация и выгрузка модуля bluetooth](Screenshot_6.png){ #fig:006 width=80% }

## Обновление ядра системы

1. Для проверки текущей версии ядра использовалась команда **uname -r**.  
   Версия ядра: *6.12.0-55.12.1.el10_0.x86_64*.  
   Далее командой **dnf list kernel** был просмотрен список установленных и доступных версий ядра.  

   ![Проверка версии и списка пакетов ядра](Screenshot_7.png){ #fig:007 width=80% }

2. Выполнена последовательность команд для обновления системы и ядра:  
   - **dnf upgrade --refresh** — обновление метаданных и установка доступных обновлений;  
   - **dnf update kernel** — обновление пакета ядра;  
   - **dnf update** — обновление остальных пакетов системы.  
   После завершения операций система подтвердила, что все пакеты обновлены.  

   ![Обновление ядра и системы](Screenshot_8.png){ #fig:008 width=80% }

3. После перезагрузки команда **uname -r** показала, что активна новая версия ядра: *6.12.0-55.37.1.el10_0.x86_64*.  
   Команда **hostnamectl** вывела сведения о системе, подтвердив, что используется ОС **Rocky Linux 10.0 (Red Quartz)** под управлением **VirtualBox**.  

   ![Проверка версии ядра и информации о системе](Screenshot_9.png){ #fig:009 width=80% }


# Контрольные вопросы

1. **Какая команда показывает текущую версию ядра, которая используется на вашей системе?**  
   uname -r  

2. **Как можно посмотреть более подробную информацию о текущей версии ядра операционной системы?**  
   hostnamectl  

3. **Какая команда показывает список загруженных модулей ядра?**  
   lsmod  

4. **Какая команда позволяет вам определять параметры модуля ядра?**  
   modinfo <имя_модуля>  

5. **Как выгрузить модуль ядра?**  
   modprobe -r <имя_модуля>  

6. **Что вы можете сделать, если получите сообщение об ошибке при попытке выгрузить модуль ядра?**  
   Проверить, используется ли модуль другими процессами или системными службами, и остановить их перед повторной попыткой выгрузки.  

7. **Как определить, какие параметры модуля ядра поддерживаются?**  
   Использовать команду modinfo <имя_модуля> и просмотреть строки, начинающиеся с *parm:*  

8. **Как установить новую версию ядра?**  
   dnf update kernel  
   или  
   dnf upgrade --refresh  


# Заключение

В ходе работы были изучены основные приёмы управления модулями ядра в операционной системе **Linux**.  
Были рассмотрены команды для просмотра загруженных модулей, их загрузки и выгрузки, а также получения подробной информации о каждом модуле с помощью **modinfo**.  
Изучены зависимости модулей, цифровые подписи и параметры, доступные для конфигурации.  
Также была выполнена процедура обновления ядра системы с использованием утилиты **dnf**, что позволило закрепить навыки администрирования и обновления компонентов операционной системы.  
