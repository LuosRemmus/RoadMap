# Оглавление

- [Docker](#docker)
    - [Краткие сведения](#краткие-сведения)
    - [Архитектура Docker](#архитектура-docker)
      - [Демон](#демон)
      - [Клиент](#клиент)
      - [Docker desktop](#docker-desktop)
      - [Реестр](#реестр)
      - [Объекты](#объекты)
        - [Образы](#образы-images)
        - [Контейнеры](#контейнеры-containers)
    - [Аналоги](#аналоги)
    - [Команды и флаги](#команды-и-флаги)
    - [Dockerfile](#dockerfile)
    - [Docker Compose](#docker-compose)
      - [docker compose](#docker-compose-1)
      - [docker-compose.yml](#docker-composeyml)
      - [Основные настройки сервисов](#основные-настройки-сервисов)
      - [Замена переменных](#замена-переменных)
    - [Список полезных источников](#список-полезных-источников)
- [FastAPI](#fastapi)
  - [Path-параметры](#path-парамерты)
  - [Query-параметры](#query-параметры)
  - [Тело запроса](#тело-запроса)
- PostgreSQL
- Nats
- Selery
- Redis
- ElasticSearch
- Git
- Linux
- RestAPI
- Python

# Docker

## Краткие сведения

**Docker** - это открытая платформа для разработки, доставки и запуска приложений. 
Он позволяет отделить приложения от инфраструктуры и управлять инфраструктурой по аналогии с тем, как мы управляем приложениями.

**Docker** предоставляет возможность упаковывать и запускать приложение в слабо изолированной среде — контейнере. 
Изоляция и безопасность позволяют одновременно запускать несколько контейнеров на одном хосте (хостом может быть наша локальная машина, дата центр, облачный провайдер или их микс). 
Контейнеры являются легковесными и содержат все необходимое для запуска приложения, что избавляет нас от необходимости полагаться на то, что установлено на хосте.

В целом, ***Docker*** является мощным инструментом для контейнеризации приложений, но его использование требует понимания его преимуществ и недостатков и правильного подхода к развертыванию и управлению контейнерами.

**Для чего _Docker_ может использоваться?**

*Быстрая и согласованная доставка приложений*

`Docker` рационализирует жизненный цикл разработки, позволяя разработчикам работать в стандартизированной среде через локальные контейнеры, предоставляющие приложения и сервисы. 
Контейнеры отлично подходят для рабочих процессов непрерывной интеграции и непрерывной доставки (continuous integration/continuous delivery, `CI/CD`).

*Отзывчивая разработка и масштабирование*

Платформа, основанная на контейнерах, позволяет легко портировать приложения. 
Контейнеры могут запускаться на локальной машине разработчика, в физических или виртуальных дата-центрах, облачных провайдерах или смешанных средах.

*Запуск большего количества приложений на одной машине*

`Docker` является легковесным и быстрым. 
Он предоставляет работоспособную и экономичную альтернативу виртуальным машинам на основе гипервизора, что позволяет использовать больше вычислительных мощностей для решения аналогичных задач.

## Архитектура Docker

`Docker` использует клиент-серверную архитектуру. 
Клиент (`Docker client`) обращается к демону (`Docker daemon`), который поднимает (собирает), запускает и распределяет контейнеры. 
Клиент и демон могут быть запущены в одной системе или клиент может быть подключен к удаленному демону. 
Клиент и демон общаются через `REST API` поверх `UNIX`-сокетов или сетевого интерфейса. 
Другим клиентом является `Docker Compose`, позволяющий работать с приложениями, состоящими из нескольких контейнеров.
![Архитектура Docker](static/Docker/docker/architecture.png)

### Демон

Демон (`dockerd`) регистрирует (слушает) запросы, поступающие от `Docker API`, и управляет такими объектами как образы, контейнеры, сети и тома. 
Демон может общаться с другими демонами для управления сервисами.

### Клиент

Клиент (`docker`) — основной способ коммуникации с `Docker`. 
При выполнении такой команды, как `docker run`, клиент отправляет эту команду демону, который, собственно, эту команду и выполняет. 
Команда `docker` использует `Docker API`. 
Клиент может общаться с несколькими демонами.

### Docker Desktop

`Docker Desktop` — это десктопное приложение для `Mac` и `Windows`, позволяющее создавать и распределять контейнерные приложения и микросервисы. 
`Docker Desktop` включает в себя демона, клиента, `Docker Compose`, `Docker Content Trust`, `Kubernetes` и `Credential Helper`.

### Реестр

В реестре (registry) хранятся образы контейнеров. 
`Docker Hub` — это публичный реестр, который (по умолчанию) используется `Docker` для получения образов. 
Имеется возможность создания частных (закрытых) реестров.

При выполнении таких команд, как `docker pull` или `docker run`, необходимые образы загружаются из настроенного реестра. 
А при выполнении команды `docker push` образ загружается в реестр.

### Объекты

При использовании `Docker` мы создаем и используем образы, контейнеры, сети, тома, плагины и другие объекты. Рассмотрим некоторые из них.

#### Образы (`Images`)

Образ — это доступный только для чтения шаблон с инструкциями по созданию контейнера. 
Часто образ представляет собой модификацию другого образа.

Можно создавать свои образы или использовать образы, созданные другими и опубликованные в реестре. 
Для создания образа используется `Dockerfile`, содержащий инструкции по созданию образа и его запуску (см. ниже). 
Ряд инструкций в `Dockerfile` приводит к созданию в образе нового слоя (раньше новый слой создавался для каждой инструкции). 
При изменении `Dockerfile` и повторной сборке образа пересобираются только модифицированные слои. 
Это делает образы легковесными, маленькими и быстрыми.

#### Контейнеры (`Containers`)

***Контейнер*** — это запускаемый экземпляр образа. 
Мы создаем, запускаем, перемещаем и удаляем контейнеры с помощью `Docker API` или `CLI` (command line interface, интерфейс командной строки). 
Мы можем подключать контейнеры к сетям, добавлять в них хранилища данных и даже создавать новые образы на основе текущего состояния.

По умолчанию контейнеры хорошо изолированы от других контейнеров и хоста. 
Однако мы можем управлять тем, насколько изолированы сеть, хранилище данных или другая подсистема контейнера.

Контейнер определяется образом и настройками, указанными при его создании и запуске. 
При удалении контейнера его состояние также удаляется. 
Этого можно избежать с помощью хранилища данных.

При выполнении этой команды происходит следующее:

1. Поскольку на нашей машине нет образа ubuntu, Docker загружает его из реестра (то же самое делает команда docker pull ubuntu).
2. Docker создает новый контейнер (то же самое делает команда docker container create).
3. В качестве последнего слоя Docker выделяет контейнеру файловую систему для чтения и записи. Это позволяет запущенному контейнеру создавать и модифицировать файлы и директории в его локальной файловой системе.
4. Поскольку мы не указали сетевых настроек, Docker создает сетевой интерфейс для подключения контейнера к дефолтной сети. Это включает в себя присвоение контейнеру IP-адреса. Контейнеры могут подключаться к внешним сетям через сетевое соединение хоста.
5. Docker запускает контейнер и выполняет /bin/bash. Поскольку контейнер запущен в интерактивном режиме и подключен к терминалу (благодаря флагам -i и -t), мы можем вводить команды и получать результаты в терминале.
6. Выполнение команды exit приводит к прекращению выполнения /bin/bash. Контейнер останавливается, но не удаляется. Мы можем запустить его снова или удалить.


## Аналоги

В мире контейнеризации существует несколько популярных аналогов Docker. 
Вот некоторые из них и их краткое описание:

1. `Kubernetes`  
**Kubernetes** (**K8s**) - это оркестратор контейнеров с открытым исходным кодом, который позволяет автоматизировать развертывание, масштабирование и управление контейнеризированными приложениями. 
Kubernetes более широко используется в сравнении с другими альтернативами и предоставляет богатый набор функций для развертывания и управления контейнерами, таких как масштабирование, обнаружение сервисов, управление сетью и обновление без простоев. 
Однако, Kubernetes сам по себе не является альтернативой для Docker, а скорее может использоваться вместе с Docker для эффективного управления контейнерами.
2. `OpenShift`  
**OpenShift** является платформой контейнеризации и разработки приложений, основанной на **Kubernetes**. 
В отличие от простой контейнеризации, **OpenShift** обеспечивает дополнительные функции, такие как автоматизированное масштабирование, мониторинг, управление доступом и деплой приложений в облаке.
3. `Podman`  
**Подман** (**Pod Manager**) представляет собой средство для управления контейнерами, которое предлагает альтернативу для **Docker**. Он обеспечивает совместимость с **Docker API**, что позволяет запускать и управлять контейнерами так же, как и с помощью Docker. 
В отличие от Docker, Podman не требует привилегированного доступа и не использует демона, вместо этого он запускает контейнеры в изолированных процессах.


## Команды и флаги
1. `docker run` - запускает контейнер.  

```
# сигнатура
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
# основные настройки (флаги)
-d - запуск контейнера в качестве отдельного процесса
-p - публикация открытого порта в интерфейсе хоста (HOST:CONTAINER)
# например
-p 3000:3000
-t - выделение псевдотерминала
-i - оставить STDIN открытым без присоединения к терминалу
--name - название контейнера
--rm - очистка системы при остановке/удалении контейнера
--restart - политика перезапуска - no (default) | on-failure[:max-retries] | always | unless-stopped
-e - установка переменной среды окружения
-v - привязка распределенной файловой системы (name:/path/to/file)
# например
-v mydb:/etc/mydb
-w - установка рабочей директории
```

2. `docker build` - используется для создания образа на основе файла `Dockerfile` и контекста.  
**Контекст** — это набор файлов, находящихся в локации, определенной с помощью `PATH` или `URL`.  
`PATH` — это директория в нашей локальной системе, а `URL` — это удаленный репозиторий.  
Контекст сборки обрабатывается рекурсивно, поэтому `PATH` включает как директорию, там и все ее поддиректории, а `URL` — как репозиторий, так и все его субмодули.  
Для исключения файлов из сборки образа используется `.dockerignore` (синтаксис этого файла похож на `.gitignore`).

```
# сигнатура  
docker build [OPTIONS] PATH | URL | - 
```

Создание образа:
```
# в качестве контекста сборки используется текущая директория  
docker build .
```

Использование репозитория в качестве контекста (предполагается, что Dockerfile находится в корневой директории репозитория)

```
docker build github.com/creack/docker-firefox
```

```
docker build -f ctx/Dockerfile http://server/ctx.tar.gz
```

В данном случае http://server/ctx.tar.gz отправляется демону, которые загружает и извлекает файлы. 
Параметр -f ctx/Dockerfile определяет путь к Dockerfile внутри ctx.tar.gz.

Чтение Dockerfile из STDIN без контекста:
```
docker build - < Dockerfile
```

Добавление тега к образу:
```
docker build -t myname/my-image:latest .
```
Определение `Dockerfile`:
```
docker build -f Dockerfile.debug .
```

Экспорт файлов сборки в директорию `out`:
```
docker build -o out .
```
Экспорт файлов сборки в файл `out.tar`:
```
docker build -o - . > out.tar
``` 

3. `docker exec` - используется для выполнения команды в запущенном контейнере.

```
# сигнатура
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
# основные флаги
-d - выполнение команды в фоновом режиме
-e - установка переменной среды окружения
-i - оставить `STDIN` открытым
-t - выделение псевдотерминала
-w - определение рабочей директории внутри контейнера
```

_Пример_:

```
# -U - это пользователь, которым по умолчанию является root
docker exec -it postgres psql -U postgres
```

В данном случае в контейнере `postgres` будет запущен интерактивный терминал `psql`. 
Выполним парочку команд:

```
# получаем список баз данных
\l
# подключаемся к базе данных mydb
-d mydb
# получаем список таблиц, точнее, сообщение об отсутствии отношений (relations)
\dt
# выходим
\q
```

4. `docker ps` - используется для получения списка (по умолчанию только запущенных) контейнеров.

```
# сигнатура
docker ps [OPTIONS]
# основные флаги
-a - показать все контейнеры (как запущенные, так и остановленные)
-f - фильтрация вывода на основе условия (`id`, `name`, `status` и т.д.)
-n - показать n последних созданных контейнеров
-l - показать последний созданный контейнер
# пример получения списка приостановленных контейнеров
docker ps -f 'status=paused'
```

5. **Команды управления**
```
# запуск остановленного контейнера
docker start CONTAINER

# приостановление всех процессов, запущенных в контейнере
docker pause CONTAINER

# остановка контейнера
docker stop CONTAINER

# "убийство" контейнера
docker kill CONTAINER

# перезапуск контейнера
docker restart CONTAINER

# удаление остановленного контейнера
docker rm [OPTIONS] CONTAINER
# основные флаги
-f - принудительное удаление (остановка и удаление) запущенного контейнера
-v - удаление анонимных томов, связанных с контейнером
# пример удаления всех остановленных контейнеров
docker rm $(docker ps --filter status=exited -q)

# удаление образа
docker rmi IMAGE

###

# управление образами
docker image COMMAND

# управление контейнерами
docker container COMMAND

# управление томами
docker volume COMMAND

# управление сетями
docker network COMMAND

# управление docker
docker system COMMAND
```


## Dockerfile

`Dockerfile` — это документ (без расширения), содержащий инструкции, которые используются для создания образа при выполнении команды `docker build`.

*Предостережение:* не используйте `/` в качестве `PATH` для контекста сборки. 
Это приведет к передаче демону всего содержимого жесткого диска вашей машины.

Инструкции выполняются по одной. 
Результаты наиболее важных инструкций фиксируются в виде отдельных слоев образа. 
*Обратите внимание*: каждая инструкция выполняется независимо от других. 
Это означает, что выполнение `RUN cd /tmp` не будет иметь никакого эффекта для последующих инструкций.

`Dockerfile` может содержать следующие инструкции:

```Dockerfile
# Основные
# FROM - родительский образ
FROM <image>[:<tag>] [AS <name>]
# пример
FROM node:12-alpine AS build

# WORKDIR - установка рабочей директории для инструкций RUN, CMD, ENTRYPOINT, COPY и ADD
WORKDIR /path/to/dir
# пример
WORKDIR /app

# COPY - копирование новых файлов или директорий из <src>
# и их добавление в файловую систему образа по адресу, указанному в <dest>
COPY <src> <dest>
COPY ["<src>", "<dest>"]
# пример
COPY package.* yarn.lock ./
# или
COPY . .
# ADD, в отличие от COPY, позволяет копировать удаленные файлы,
# а также автоматически распаковывает сжатые (identity, gzip, bzip2 или xz) локальные файлы

# ADD - копирование новых файлов, директорий или удаленного (!) файла из <src>
# и их добавление в файловую систему образа по адресу, указанному в <dest>
ADD <src> <dest>
ADD ["<src>", "<dest>"]
# пример
ADD some.txt some_dir/ # <WORKING_DIR>/some_dir/

# RUN - выполнение команды в новом слое на основе текущего образа и фиксация результата
RUN <command>
# или
RUN ["executable", "arg1", "arg2"] # Кавычки должны быть двойными
# пример
RUN npm install

# CMD - предоставление дефолтных значений исполняемому контейнеру
CMD ["executable", "arg1", "arg2"]
# или если данной инструкции предшествует инструкция ENTRYPOINT
CMD ["arg1", "arg2"]
# или
CMD command arg1 arg2
# пример
CMD [ "node", "/app/src/index.js" ]
# RUN выполняет команду и фиксирует результат,
# CMD ничего не выполняет во время сборки, а определяет команду для образа
# (!) выполняется только одна (последняя) инструкция CMD

# ENTRYPOINT - настройка исполняемого контейнера
ENTRYPOINT ["executable", "arg1", "arg2"]
ENTRYPOINT command arg1 arg2
# пример
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
# docker run -it --rm --name test top -H
# top -b -H
# разница между ENTRYPOINT и CMD:
# https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact
# https://stackoverflow.com/questions/21553353/what-is-the-difference-between-cmd-and-entrypoint-in-a-dockerfile

# переменные
# ${var} или $var
# пример
FROM busybox
ENV FOO=/bar
WORKDIR ${FOO}    # WORKDIR /bar
ADD . $FOO        # ADD . /bar
COPY \$FOO /qux   # COPY $FOO /qux

# Другие
# LABEL - добавление метаданных к образу
LABEL <key>=<value>
# пример
LABEL version="1.0"

# EXPOSE - информация о сетевом порте, прослушиваемом контейнером во время выполнения
EXPOSE <port> | <port>/<protocol>
# пример
EXPOSE 3000

# ENV - установка переменных среды окружения
ENV <key>=<value>
# пример
ENV MY_NAME="No Name"

# VOLUME - создание точки монтирования
VOLUME ["/var/log"]
VOLUME /var/log

# USER - установка пользователя для использования при запуске контейнера
# в любых инструкциях RUN, CMD и ENTRYPOINT
USER <user>[:<group>]
USER <UID>[:<GID>]

# ARG - определение переменной, которая может быть передана через командную строку при
# выполнении команды `docker build` с помощью флага `--build-arg <name>=<value>`
ARG <name>[=<default value>]

# ONBUILD - добавление в образ триггера, запускаемого при использовании
# данного образа в качестве основы для другой сборки
ONBUILD <INSTRUCTION>
```

## Docker Compose

`Compose` — это инструмент для определения и запуска `Docker-приложений`, состоящих из нескольких контейнеров. 
Для настройки сервисов приложения используется файл `docker-compose.yml`.

Процесс использования `Compose`, как правило, состоит из 3 этапов:
- определение среды приложения с помощью `Dockerfile`;
- определение сервисов, из которых состоит приложение, в `docker-compose.yml` 
(для совместного запуска сервисов в изолированной среде);
- выполнение команды `docker compose up` для запуска приложения.

Пример файла `docker-compose.yml`:
```yaml
version: "3.9"  # опционально
services:
 web:
   build: .
   ports:
     - "5000:5000"
   volumes:
     - .:/code
     - logvolume:/var/log
   links:
     - redis
 redis:
   image: redis
volumes:
 logvolume: {}
```

`Compose` позволяет делать следующее:
- запускать, останавливать и повторно собирать сервисы;
- получать статус запущенных сервисов;
- получать логи запущенных сервисов;
- выполнять команды в сервисах.

`Compose` предоставляет следующие возможности:
- создание нескольких изолированных сред на одном хосте;
- сохранение данных томов при создании контейнеров;
- повторное создание только модифицированных контейнеров;
- передача переменных среды окружения и возможность создания разных сред (для разработки, продакшна и т.д.).

### `docker compose`

Команда `docker compose` является альтернативой `docker-compose CLI` и используется для управления `Compose`.

```
# сигнатура
docker-compose [-f <arg>...] [--profile <name>...] [options] [COMMAND] [ARGS...]

# основные флаги
-f - путь к docker-compose.yml
-p - название проекта
--project-path - альтернативная рабочая директория (по умолчанию рабочей является директория, содержащая docker-compose.yml)

# основные команды
up - создание и запуск сервисов
down - остановка и удаление контейнеров, сетей, образов и томов
start - запуск сервисов
stop - остановка сервисов
restart - перезапуск сервисов
create - создание сервисов
rm - удаление остановленных контейнеров
run - выполнение одноразовой команды
exec - выполнение команды в запущенном контейнере
```

### `docker-compose.yml`

Файл `Compose` — это файл в формате `YAML`, определяющий сервисы, сети и тома. 
Дефолтным путем этого файла является `./docker-compose.yml`.

Определение сервиса включает в себя установку настроек, которые применяются к каждому контейнеру, запущенному для этого сервиса. 
Это похоже на передачу аргументов при выполнении команды `docker run`. 
Определения сети и тома аналогично выполнению команд `docker network create` и `docker volume create`.

Настройки, определенные в `Dockerfile`, такие, как `CMD`, `EXPOSE`, `VOLUME` и `ENV` не нуждаются в дублировании в `docker-compose.yml`.

### Основные настройки сервисов

1. `build` - Настройки, применяемые во время сборки.  
`build` может определяться в виде строки — пути к контексту сборки:
```yaml
version: "3.9"
services:
 webapp:
   build: ./dir
```
Или в виде объекта, где `context` — путь к контексту, `dockerfile` — используемый `Dockerfile` и `args` — аргументы:
```yaml
version: "3.9"
services:
 webapp:
   build:
     context: ./dir
     dockerfile: Dockerfile-alternate
     args:
       buildno: 1
```
В случае с `args` аргументы должны быть определены в `Dockerfile`:
```dockerfile
# syntax=docker/dockerfile:1

ARG buildno
ARG gitcommithash

RUN echo "Номер сборки: $buildno"
RUN echo "Основано на коммите: $gitcommithash"
```

```yaml
build:
 context: .
 args:
   buildno: 1
   gitcommithash: cdc3b19
   # or
   - buildno=1
   - gitcommithash=cdc3b19
```

2. `network` - Сеть, к которой подключается контейнер во время сборки (для использования при выполнении команды `RUN`):
```yaml
build:
 context: .
 network: host
# or
build:
 context: .
 network: custom_network_1
```

3. `command` - Перезапись дефолтной команды:
```yaml
command: bundle exec thin -p 3000
```

4. `depends_on` - Зависимость между сервисами.  
Это означает следующее:  
- `docker compose up` запускает сервисы в определенном порядке. В примере ниже `db` и `redis` запускаются перед `web`;
- `docker compose up SERVICE` автоматически включает зависимости `SERVICE`. В примере `docker compose up web` также создает и запускает `db` и `redis`;
- `docker compose stop` останавливает сервисы в определенном порядке. В примере `web` останавливается перед `db` и `redis`.

```yaml
version: "3.9"
services:
 web:
   build: .
   depends_on:
     - db
     - redis
   redis:
     image: redis
   db:
     image: postgres
```

5. `restart_policy` - Политика перезапуска — определяет, как и когда контейнер должен перезапускаться:
- `condition`: условие перезапуска — `none`, `on-failure` или `any` (значение по умолчанию);
- `delay`: время между попытками (по умолчанию равняется 5s);
- `max_attempts`: количество попыток (по умолчанию — бесконечное);
- `window`: время принятия решения об успехе перезапуска (по умолчанию — немедленно).

```yaml
version: "3.9"
services:
 redis:
   image: redis:alpine
   deploy:
     restart_policy:
       condition: on-failure
       delay: 5s
       max_attempts: 3
       window: 120s
```

6. `entrypoint` - Перезапись дефолтной точки входа:
```yaml
entrypoint: /code/entrypoint.sh
```

7. `env_file` - Извлечение переменных среды окружения из файла. Может быть единичным значением или списком.

Если файл `Compose` определен с помощью `docker compose -f FILE`, пути в `env_file` будут относительными директории, в которой находится этот файл.

Переменные, определенные в разделе `environment`, перезаписывают эти значения.

```yaml
env_file: .env
# or
env_file:
 - ./common.env
 - ./apps/web.env
 - /opt/runtime_opts.env
```

8. `expose` - Выставление портов без их публикации на хосте — порты будут доступны только связанным (linked) сервисам. 
Могут определяться только внутренние порты:

```yaml
expose:
 - "3000"
 - "8000"
```

9. `external_links` - Подключение к контейнеру, запущенному за пределами `docker-compose.yml` или даже за пределами `Compose`.  
Особенно полезно для контейнеров, предоставляющих общие или распределенные сервисы:

```yaml
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

_Обратите внимание_: внешние контейнеры должны быть подключены хотя бы к одной сети, к которой подключен сервис.

10. `image` - Образ для контейнера.  
Может быть репозиторием/тегом или частичным идентификатором (partial identifier):

```yaml
image: redis
image: node:16
image: example-registry.com:4000/postgresql
```

11. `links` - Подключение контейнера к другому сервису.  
Подключаемый сервис определяется с помощью названия сервиса и синонима ссылки (link alias) (`"SERVICE:ALIAS"`) или только названия:

```yaml
web:
 links:
   - "db"
   - "db:database"
   - "redis"
```

12. `network_mode` - Сетевой режим:

```yaml
network_mode: "bridge"
network_mode: "host"
network_mode: "none"
```

13. `networks` - Сети для подключения:

```yaml
services:
 some-service:
   networks:
    - some-network
    - other-network
```

14. `ports` - Выставление портов.

**Короткий синтаксис** позволяет делать следующее:
- определять оба порта (`HOST:CONTAINER`);
- определять только порт контейнера (для хоста выбирается эфемерный порт);
- определять `IP-адрес` хоста для привязки (bind) и оба порта (значением по умолчанию является `0.0.0.0`, что означает все интерфейсы) (`IPADDR:HOSTPORT:CONTAINERPORT`).

```yaml
ports:
 - "3000"
 - "8000:8000"
 - "9090-9091:8080-8081"
 - "127.0.0.1:8001:8001"
 - "127.0.0.1::5000"
 - "6060:6060/udp"
```

**Длинный синтаксис** позволяет настраивать дополнительные поля:
- `target`: порт контейнера;
- `published`: порт хоста (доступный публично);
- `protocol`: протокол порта (tcp или udp);
- `mode`: `host` | `ingress`.

15. `restart` - Определение политики перезапуска.  
Значением по умолчанию является no, что означает отключение автоматического перезапуска.  
`always` означает перезапуск в любом случае.  
`on-failure` означает перезапуск только в случае аварийной остановки контейнера.  
`unless-stopped` означает перезапуск контейнера во всех случаев, кроме преднамеренной остановки:

```yaml
restart: "no"
restart: always
restart: on-failure
restart: unless-stopped
```

16. `volumes` - Монтирование путей хоста (host paths) или именованных томов (named volumes), определенных в виде дополнительных настроек сервиса.

Пути хоста могут монтироваться как часть определения сервиса. Их не нужно указывать в ключе `volume` на верхнем уровне.

Однако, если необходимо, чтобы тома использовались несколькими сервисами, они должны быть перечислены в таком `volume`.

В следующем примере именованный том `mydata` используется сервисом `web`, для отдельного сервиса определяется `bind mount` (первый путь в `volumes` сервиса `db`). 
db также использует именованный том `dbdata` (второй путь), но определяет его с помощью устаревшего строкового формата для монтирования именованных томов. 
Именованные тома указываются в ключе `volume` верхнего уровня:

```yaml
version: "3.9"
services:
 web:
   image: nginx:alpine
   volumes:
     - type: volume
       source: mydata
       target: /data
       volume:
         nocopy: true
     - type: bind
       source: ./static
       target: /opt/app/static

 db:
   image: postgres:latest
   volumes:
     - "/var/run/postgres/postgres.sock:/var/run/postgres/postgres.sock"
     - "dbdata:/var/lib/postgresql/data"

volumes:
 mydata:
 dbdata:
```

_Короткий синтаксис_

В этом случае используется формат `[SOURCE:]TARGET[:MODE]`, где `SOURCE` — это путь хоста или именованный том, 
`TARGET` — это путь монтирования тома в контейнере и 
`MODE` — ro для доступа только для чтения и `rw` для доступа для чтения и записи (дефолтное значение).

Для монтирования могут использоваться относительные пути (путь вычисляется, начиная с директории с файлом `Compose`). 
Относительные пути должны начинаться с `.` или `..`.

```yaml
volumes:
 # определяем только путь и делегируем создание тома движку
 - /var/lib/mysql

 # определяем связывание (mapping) абсолютных путей
 - /opt/data:/var/lib/mysql

 # путь хоста относительно директории с файлом `Compose`
 - ./cache:/tmp/cache

 # путь относительно пользователя
 - ~/configs:/etc/configs/:ro

 # именованный том
 - datavolume:/var/lib/mysql
```

_Длинный синтаксис_

Длинный синтаксис позволяет настраивать дополнительные поля:
- `type`: тип монтирования — `volume`, `bind`, `tmpfs` или `npipe`;
- `source`: источник монтирования, путь хоста для `bind mount` или название тома, определенное в верхнеуровневом `volumes`;
- `target`: путь монтирования тома в контейнере;
- `read_only`: индикатор доступности тома только для чтения;
- `bind`: дополнительные настройки связывания:
  - `propagation`: режим распространения, используемый для связывания;
- `volume`: дополнительные настройки тома:
  - `nocopy`: индикатор запрета копирования данных тома.

```yaml
version: "3.9"
services:
 web:
   image: nginx:alpine
   ports:
     - "80:80"
   volumes:
     - type: volume
       source: mydata
       target: /data
       volume:
         nocopy: true
     - type: bind
       source: ./static
       target: /opt/app/static

networks:
 webnet:

volumes:
 mydata:
```

_Другие настройки, соответствующие настройкам команды `docker run`_

```yaml
user: postgres
working_dir: /code

domainname: foo.com
hostname: foo
ipc: host
mac_address: 02:42:ac:11:65:43

privileged: true

read_only: true
shm_size: 64M
stdin_open: true
tty: true
```

_Примеры определения продолжительности_
```
2.5s
10s
1m30s
2h32m
5h34m56s
```

_Примеры определения байтовых значений_
```
2b
1024kb
2048k
300m
1gb
```

### Замена переменных

Настройки могут содержать переменные среды окружения. 
`Compose` использует значения переменных из терминала при выполнении команды `docker compose`. 
Например, предположим, что терминал содержит `POSTGRES_VERSION=9.3` и применяется такая настройка:

```yaml
db:
 image: "postgres:${POSTGRES_VERSION}"
```

При выполнении `docker compose` значение переменной `POSTGRES_VERSION` в настройках заменяется на `9.3` и мы получаем `postgres:9.3`.

Если значение переменной не установлено, переменная в настройках заменяется пустой строкой и мы получаем `postgres:`.

Дефолтные значения переменных могут быть установлены в файле `.env`, который должен находиться в той же директории, что и файл `Compose`. 
Значения переменных в терминале перезаписывают значения, определенные в `.env`.

Поддерживается 2 варианта синтаксиса: `$VAR` и `${VAR}`. Второй вариант предоставляет такие дополнительные возможности, как:
- определение значений по умолчанию:
  - `${VAR:-default}`: оценивается как `default`, когда `VAR` не установлена или является пустой;
  - `${VAR-default}`: оценивается как `default` только когда `VAR` не установлена;
- определение обязательных значений:
  - `${VAR:?error}`: ошибка возникает, если `VAR` не установлена или является пустой;
  - `${VAR?error}`: ошибка возникает, только если `VAR` не установлена.

## Список полезных источников
1. [Статья по Docker, Docker CLI, Dockerfile](https://habr.com/ru/companies/timeweb/articles/595687/)
2. [Статья по docker-compose](https://habr.com/ru/companies/timeweb/articles/597613/)
3. [Начало работы с Docker Compose](https://docs.docker.com/compose/)
4. [Полный список флагов и команд Docker Compose](https://docs.docker.com/compose/reference/)
5. [Спецификация файла Compose версии 3](https://docs.docker.com/compose/compose-file/compose-file-v3/)


# FastAPI

`FastAPI` — это фреймворк для создания лаконичных и довольно быстрых `HTTP` `API`-серверов со встроенными валидацией, 
сериализацией и асинхронностью. 
Стоит он на плечах двух других фреймворков. 
Работой с `web` в `FastAPI` занимается `Starlette`, за валидацию отвечает `Pydantic`.

`Starlette` — новый, шустрый и классный фреймворк, реализующий подход `ASGI`. 
В нем всё заточено на асинхронность и новые фишки 3-й ветки `Python`. 
Кроме этого в `Starlette` есть ещё целая пачка серьёзных плюшек:
- `GraphQL` из коробки
- Веб-сокеты уже встроены и готовы к работе
- Готовый набор миддлверов для работы с `авторизацией`/`аутентификацией`, `CORS`
- Встроенные асинхронные таски

## Создание приложения

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def get_root():
    return {"message": "Hello World"}
```

Декоратор `@app.get("/")` указывает `FastAPI`, что функция, прямо под ним,
отвечает за обработку запросов, поступающих по адресу:
- путь `/`
- использующих `get` операцию

Можно также использовать операции:
- `@app.post()`
- `@app.put()`
- `@app.delete()`

И более экзотические:
- `@app.options()`
- `@app.head()`
- `@app.patch()`
- `@app.trace()`

## Path-парамерты

Вы можете определить "параметры" или "переменные" пути, используя синтаксис форматированных строк `Python`:
```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id}

```

Значение параметра пути `item_id` будет передано в функцию в качестве аргумента `item_id`. 
Если мы не аннотируем `item_id`, то по умолчанию это будет строка. 
Перейдя по адресу http://127.0.0.1:8000/items/1 мы получим ответ:  
```python
{"item_id": "1"}
```

Если аннотировать `item_id` как `int`:
```python
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
``` 
И перейти по адресу: http://127.0.0.1:8000/items/1, то ответ будет:
```python
{"item_id": 1}
```

### Pydantic

Вся проверка данных выполняется под капотом с помощью [Pydantic](https://docs.pydantic.dev/latest/). 
Поэтому вы можете быть уверены в качестве обработки данных.

Можно использовать в аннотациях как простые типы данных, вроде `str`, `float`, `bool`, так и более сложные типы.

### Path-параметры, содержащие пути

Предположим, что есть операция пути с путем `/files/{file_path}`. 
Но нужно, чтобы `file_path` сам содержал путь, например, `home/johndoe/myfile.txt`. 
Тогда URL для этого файла будет такой: `/files/home/johndoe/myfile.txt`.

### Path-параметры и валидация числовых данных

Так же, как с помощью `Query` вы можете добавлять валидацию и метаданные для **query**-параметров, 
так и с помощью `Path` вы можете добавлять такую же валидацию и метаданные для **path**-параметров.

- Импортируйте `Path` из `fastapi`, а также импортируйте `Annotated`
- Определите метаданные
- Задайте нужный вам порядок параметров

```python
from typing import Annotated
from fastapi import FastAPI, Path, Query

app = FastAPI()


@app.get("/items/{item_id}")
async def read_items(
    *,
    item_id: Annotated[int, Path(title="The ID of the item to get", ge=0, le=1000)],
    q: str,
    size: Annotated[float, Query(gt=0, lt=10.5)],
):
    results = {"item_id": item_id}
    if q:
        results.update({"q": q})
    return results

```

С помощью `Query`, `Path` вы можете добавлять метаданные и строковую валидацию, 
а также вы можете добавить валидацию числовых данных:
- `gt`: больше (`g`reater `t`han)
- `ge`: больше или равно (`g`reater than or `e`qual)
- `lt`: меньше (`l`ess `t`han)
- `le`: меньше или равно (`l`ess than or `e`qual)

## Query-параметры

Когда вы объявляете параметры функции, которые не являются параметрами пути, 
они автоматически интерпретируются как `query`-параметры.

```python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]

@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

`Query`-параметры представляют собой набор пар ключ-значение, 
которые идут после знака `?` в URL-адресе, разделенные символами `&`.

Например, в этом URL-адресе:
`http://127.0.0.1:8000/items/?skip=0&limit=10`
...параметры запроса такие:
- `skip`: со значением `0`
- `limit`: со значением `10`

Будучи частью URL-адреса, они "по умолчанию" являются строками.
Но когда вы объявляете их с использованием аннотаций (в примере выше, как `int`), 
они конвертируются в указанный тип данных и проходят проверку на соответствие ему.

Поскольку query-параметры не являются фиксированной частью пути, 
они могут быть не обязательными и иметь значения по умолчанию.
В примере выше значения по умолчанию равны `skip=0` и `limit=10`.

Таким образом, результат перехода по URL-адресу:  
`http://127.0.0.1:8000/items/`  
будет таким же, как если перейти используя параметры по умолчанию:  
`http://127.0.0.1:8000/items/?skip=0&limit=10`  
Но если вы введёте, например:  
`http://127.0.0.1:8000/items/?skip=20`
Значения параметров в вашей функции будут:
- `skip=20`: потому что вы установили это в URL-адресе
- `limit=10`: т.к это было значение по умолчанию

Чтобы сделать параметр необязательным можно добавить в аннотации `None` и присвоить значение по умолчанию.

### Расширенная валидация

Чтобы воспользоваться расширенной валидацией, первым делом нужно импортировать:

- `Query` из пакета `fastapi`:
- `Annotated` из пакета `typing`

```python
from typing import Annotated

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Annotated[str | None, Query(max_length=50)] = None):
    results = {
        "items": [
            {"item_id": "Foo"}, 
            {"item_id": "Bar"}
        ]
    }
    
    if q:
        results.update({"q": q})
    return results

```

- `Annotated` нужен для добавления дополнительных метаданных для валидации Query-параметров.
- `Query` - предоставляет эти метаданные

Общие метаданные:
- `alias`
- `title`
- `description`
- `deprecated`
- `include_in_schema`

Специфичные правила валидации для строк:
- `min_length`
- `max_length`
- `regex`

## Тело запроса

Когда вам необходимо отправить данные из клиента (допустим, браузера) в ваш API, вы отправляете их как тело запроса.

Тело запроса - это данные, отправляемые клиентом в ваш `API`. Тело ответа - это данные, которые ваш `API` отправляет клиенту.

Ваш `API` почти всегда отправляет тело ответа. Но клиентам не обязательно всегда отправлять тело запроса.

Чтобы объявить тело запроса, необходимо использовать модели `Pydantic`.

Первое, что вам необходимо сделать, это импортировать `BaseModel` из пакета `pydantic`

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None

    
app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    return item
```

### Тело запроса + параметры пути + параметры запроса

Параметры функции распознаются следующим образом:

- Если параметр также указан в **пути**, то он будет использоваться как параметр пути.
- Если аннотация типа параметра содержит **примитивный тип** (`int`, `float`, `str`, `bool` и т.п.), 
он будет интерпретирован как параметр **запроса**.
- Если аннотация типа параметра представляет собой модель `Pydantic`, 
он будет интерпретирован как параметр **тела запроса**.

### Множество параметров
Вы можете объявить множество параметров тела запроса, например `item` и `user`:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None


class User(BaseModel):
    username: str
    full_name: str | None = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item, user: User):
    results = {"item_id": item_id, "item": item, "user": user}
    return results

```

В этом случае **FastAPI** заметит, что в функции есть более одного параметра тела 
(_два параметра, которые являются моделями `Pydantic`_).

Таким образом, имена параметров будут использоваться в качестве ключей (имён полей) в теле запроса, 
и будет ожидаться запрос следующего формата:

```json
{
    "item": {
        "name": "Foo",
        "description": "The pretender",
        "price": 42.0,
        "tax": 3.2
    },
    "user": {
        "username": "dave",
        "full_name": "Dave Grohl"
    }
}

```

### Поля

Таким же способом, как вы объявляете дополнительную валидацию и метаданные в параметрах функции обработки пути с помощью
функций `Query`, `Path` и `Body`, вы можете объявлять валидацию и метаданные внутри `Pydantic` моделей, 
используя функцию `Field` из `Pydantic`.

```python
from fastapi import Body, FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = Field(
        default=None, title="The description of the item", max_length=300
    )
    price: float = Field(gt=0, description="The price must be greater than zero")
    tax: float | None = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(embed=True)):
    results = {"item_id": item_id, "item": item}
    return results
```


