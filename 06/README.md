# Домашнее задание к занятию «WAF»

## Цель задания

WAF позволяет блокировать сетевые атаки на web-приложения. Вам предстоит установить и настроить программный WAF OWASP ModeSecurity.

В результате выполнения этого задания вы научитесь:

1. устанавливать WAF OWASP ModeSecurity,
2. устанавливать базовый набор правил обнаружения сетевых атак WAF OWASP ModeSecurity.

------

## Чек-лист готовности к домашнему заданию

1. Изучены материалы лекции «WAF» с личном кабинете.
2. Установлена виртуальная машина Ubuntu.

------

## Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Виртуальная машина Ubuntu (скачать - https://ubuntu.com/download/desktop).

------

## Инструкция к заданию

### Описание задачи

Вы работаете специалистом по ИБ.
Руководство поставило вам задачу установить и настроить программный WAF OWASP ModeSecurity.

### Задание

1) Для начала обновим индекс пакетов:
- `sudo apt update`

3) Установим веб-сервер Apache и пакет wget
- `sudo apt install apache2 wget`

4) Установим ModeSecurity
- `sudo apt install libapache2-mod-security2`

5) И запустим его
- `sudo a2enmod security2`

6) Перзапустим веб-сервер для применения изменений
- `sudo systemctl restart apache2`

7) Перейдем в каталог /etc/apache2/mods-enabled/, после чего откроем для редактирования файл security2.conf
- `cd /etc/apache2/mods-enabled/`
- `sudo nano security2.conf`

Здесь необходимо убедиться, что файл содержит следующую строку:
- `IncludeOptional /etc/modsecurity/*.conf`
- Данная строка описывает, где будут храниться конфигурационные файлы Modsecurity

8) Перейдем в директорию /etc/modsecurity/, после чего файл modsecurity.conf-recommended переименуем в modsecurity.conf
- `cd /etc/modsecurity/`
- `sudo mv modsecurity.conf-recommended modsecurity.conf`

9) Откроем файл для редактирования
- `sudo nano modsecurity.conf`

- В файле найдем строку "SecRuleEngine DetectionOnly" и приведем ее к виду "SecRuleEngine On" (для того, чтобы WAF не только детектировал, но и блокировал атаки)
- А строку "SecAuditLogParts ABDEFHIJZ" приведем к виду "SecAuditLogParts ABCEFHJKZ" (настройка лоширования)
- Сохраним файл (Crtl  - O) и закроем (Ctrl - X).

10) Перезапустим веб-сервер, чтобы применить изменения.
- `sudo systemctl restart apache2`

11) Перейдем в директорию /tmp и загрузим архив с базовым нобором правил
- `cd /tmp/`
- `wget https://github.com/coreruleset/coreruleset/archive/refs/tags/v3.3.2.tar.gz`

12) Разархивируем файл
- `tar xvf v3.3.2.tar.gz`

13) Для сохранения файлов CRS создадим каталог modsecurity-crs
- `sudo mkdir /etc/apache2/modsecurity-crs/`

14) После чего переместим в него содержимое распакованного архива
- `sudo mv coreruleset-3.3.2/ /etc/apache2/modsecurity-crs/`

15) Перейдем в директорию coreruleset-3.3.2/ и создадим там файл конфигурации CRS с использованием файла-примера
- `cd /etc/apache2/modsecurity-crs/coreruleset-3.3.2/`
- `sudo mv crs-setup.conf.example crs-setup.conf`

16) Откроем для редактирования файл security2.conf из директории /etc/apache2/mods-enabled/
- `cd /etc/apache2/mods-enabled/`
- `sudo nano security2.conf`

17) В файле найдем и удалим следующую строку "IncludeOptional /usr/share/modsecurity-crs/*.load"

- Вместо удаленной строки напишем:
- `IncludeOptional /etc/apache2/modsecurity-crs/coreruleset-3.3.2/crs-setup.conf`
- `IncludeOptional /etc/apache2/modsecurity-crs/coreruleset-3.3.2/rules/*.conf`

- Сохраним файл (Crtl  - O) и закроем (Ctrl - X).

18) Протестируем конфигурацию веб-сервера
- `sudo apache2ctl -t`
- Если все верно - отобразится Syntax OK
![Снимок экрана 2024-02-15 205749](https://github.com/netology-code/ibszi-homeworks/assets/96241243/b103892b-5827-4869-a3fc-49c672482993)

19) Для применения новых настроек перезапустим веб-сервер
- `sudo systemctl restart apache2`

20) Протестируем работу нашего веб-сервера в обычном режиме, перейдя по адресу в браузере http://localhost
![Снимок экрана 2024-02-15 205749](https://github.com/netology-code/ibszi-homeworks/assets/96241243/50ba9633-0cb2-4fa9-9ab9-b7c30d22a1cb)

21) Имитируем SQL-инъекцию `http://localhost/?name=sasha or '1'='1'`. WAF заблокирует атаку, выдав 403 код.

В качестве ответа пришлите скриншоты работы веб-сервера в обычном режиме и при имитации SQL-инъекции.


------

### Критерии оценки

Для получения зачёта необходимо:
- прислать скриншоты работы веб-сервера в обычном режиме и при имитации SQL-инъекции (удачная блокировка атаки с 403 кодом).

Решение направляется на доработку, если задание не выполнено, выполнено частично или выполнено с ошибками.

