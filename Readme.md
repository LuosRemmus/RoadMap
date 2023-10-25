# Оглавление

- Docker
    - Краткие сведения
    - Архитектура Docker
      - Демон
      - Клиент
      - Docker desktop
      - Реестр
      - Объекты
        - Образы
        - Контейнеры
    - Аналоги
    - Команды и флаги
    - Dockerfile
      - Краткие сведения
      - Основные команды
      - Пример файла
    - docker-compose.yml
      - Краткие сведения
      - Основные команды
      - Основные поля
      - Основные технологии для развертывания
        - PostgreSQL
    - Список полезных источников
- PostgreSQL
  - Краткие сведения
  - Преимущества и недостатки
  - Аналоги
  - Основные команды
  - SQL-запросы
    - TBC
  - Взаимодействие с Python
    - SQLAlchemy
      - ORM
      - Core
    - psycopg2/asyncpg
    - alembic
- Nats
- Selery
- Redis
- ElasticSearch
- Git
- Linux
- FastAPI
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

## Список полезных источников
1. [Статья по Docker, Docker CLI, Dockerfile](https://habr.com/ru/companies/timeweb/articles/595687/)
2. [Статья по docker-compose](https://habr.com/ru/companies/timeweb/articles/597613/)