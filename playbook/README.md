# Playbook для установки Clickhouse, Vector, Lighthouse.

 

## Описание

 

Playbook содержит:

1. Play `Install Clickhouse`

2. Play `Install Vector`

3. Play `Install Nginx`

4. Play `Install LightHouse`

 

### Install Clickhouse

 

Выполняет установку clickhouse на все хосты в группе `clickhouse` в inventory файле.

##### Tasks:

1. `Get clickhouse distrib`. Скачивает дистрибутивы, версии перечислены в `group_vars.yml`. Вначале пытается скачать дистрибутивы без архитектуры, иначе скачивается дистрибутив с архитектурой `x86_64`.

2. `Install clickhouse packages`. Устанавливает скачанные по списку дистрибутивы. Если были изменения, вызывает handler `Start clickhouse service`.

3. `Start clickhouse service`.  Проверяет, что service `clickhouse-server` запущен, либо запускает его.

4. `Create database`. Создаёт базу данных `logs`.

##### Handlers:

1. `Start clickhouse service` перезапускает сервис `clickhouse-server`, если был вызван в в `Install clickhouse packages`.

 

### Install Vector

Устанавливает `vector` на все хосты в группе `vector` в inventory файле. 

##### Tasks:

1. `Get vector distrib`. Скачивает дистрибутив, версия в `group_vars.yml`.

2. `Install vector packages`. Устанавливает скачанный пакет. Если были внесены изменения, вызывает handler `Start vector service`.

##### Handlers:

1. `Start vector service` перезапускает сервис `vector`, если был вызван в `Install vector packages`.

 

### Install Nginx

`Install Nginx` устанавливает `nginx` на все хосты в группе `lightHouse` в inventory файле для запуска на них LightHouse. 

##### Tasks:

1. `Install nginx` устанавливает Nginx, указанной в переменных версии, из репозитория Ubuntu. Если были внесены изменения, вызывает handler `Restart nginx service`.

##### Handlers:

1. `Restart nginx service` перезапускает сервис `nginx`, если был вызван в таске `Install nginx`.

 

### Install LightHouse

`Install LightHouse` устанавливает LightHouse на все хосты в группе `lighthouse` в inventory файле. 

##### PreTasks:

1. `Install unzip` устанавливает утилиты `unzip` из репозитория Ubuntu. Утилита необходима для распаковки дистрибутива LightHouse.

##### Tasks:

1. `Get lighthouse distrib` скачивает дистрибутив LightHouse из Git.

2. `Unarchive lighthouse distrib into nginx` распаковывает дистрибутив LightHouse в  `/var/www/html/`. Если были внесены изменения, вызывает handler `Restart nginx service`.

3. `Make nginx config` подготавливает из шаблона конфигурационный файл для Nginx, подставлая в него порт, указанный в переменных. Если были внесены изменения, вызывает handler `Restart nginx service`.

4. `Remove lighthouse distrib` удаляет скачанный дистрибутив.

##### Handlers:

1. `Restart nginx service` перезапускает сервис `nginx` если был вызван в `Unarchive lighthouse distrib into nginx` или `Make nginx config`.

 

## Inventory

Inventory файл [inventory/prod.yml](./inventory/prod.yml). 

Обязательно должны быть перечислены хосты для групп `clickhouse`, `vector`, `lighthouse`. Хосты группы `clickhouse` должны быть на базе CentOS. Хосты групп `vector` и `lighthouse` должны быть на базе Ubuntu.

 

## Group_vars

-[clickhouse](./group_vars/clickhouse/vars.yml)

-[vector](./group_vars/vector/vars.yml)

 -[lighthouse](./group_vars/lighthouse/vars.yml).

 

### clickhouse/vars.yml

Должен содержать следующие переменные:

Var | Desc

---------- | --------

clickhouse_version |  Версия дистрибутива

clickhouse_packages | Список требуемых пакетов

 

### vector/vars.yml

Должен содержать следующие переменные:

Var | Desc

---------- | --------

vector_version | Версия дистрибутива

 

### lighthouse/vars.yml

Должен содержать следующие переменные:

Var | Desc

---------- | --------

nginx_version | Версия nginx

lighthouse_port | Порт для lighthouse
