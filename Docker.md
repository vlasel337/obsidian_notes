# Docker

WSL - windows subsystem for linux

Чтобы скачать wsl нужно прокинуть команду: wsl --install

Чтобы посмотреть какие компоненты есть на компе: wsl -l -v

CLI - command line interface

### Команды Linux:

- exit - команда для выхода из подсистемы
- echo - команда для вывода на экран значения переменной
echo $ENV_VAR / echo “Hello, World!”
Записать текст в файл:
echo 228 > /mnt/c/Users/Vlad/test_file.txt
- cat - команда для просмотра содержимого файла по пути
- mkdir - создать папку
- touch - создание файла
- ls - отображение файлов в текущей папке
- pwd - вывод на экран пути текущей папки
- vi - открытие текстового редактора в файле
- mv - переименование файла
- ifconfig - команда для просмотра сетевых интерфейсов
- cd . . - перемещение в директорию на уровень выше нынешней

### Версии

- latest - самая свежая версия
- alpine - самая обрубленная версия

# Команды docker:

### Общие команды:

- Команда для просмотра метаинформации по образам, контейнерам и вольюмам:
docker system df

### Образы:

- Скачать образ: docker pull %image_name%:%image_version%
- Удаление образа: docker rmi %image_name%:%image_version%
- Сохранение кастомного образа:
docker commit %container_name% %image_name%:%version%

### Контейнеры:

- Проверка наличия поднятых контейнеров: docker ps -a
- Поднятие контейнера: docker run %image_name%
- Поднимаем контейнер и входим в него:
docker run -it %image_name%
(-i - запуск контейнера в интерактивном режиме
-t - запуск терминала внутри контейнера)
- Удаление остановленного контейнера: 
docker rm %container_id% / docker rm %container_name%
- Запуск остановленного контейнера: 
docker start %container_id% / docker rm %container_name%
- Остановка запущенного контейнера: 
docker stop %container_name/CONTAINER_ID%
- Вход в работающий контейнер: doker exec %container_name% -it %command% 
Пример: doker exec condescending_rubin -it bash
- Поднятие контейнера в фоновом режиме: docker run -d %image_name%
- Поднятие контейнера с присвоением наименования:
docker run -- name %name% -d %image_name%
- Опция удаления контейнера сразу после остановки:
docker run -- name %name% -d -- rm  %image_name%
- Команда для вывода 1 столбца таблицы (айдишников): docker ps -q
- Скопировать файл с хоста/папку в запущенный контейнер:
docker cp <file_name/folder_name> <container_name>:/<path_in_container>
- Скопировать файл из контейнера на хост
docker cp <container_name>:<path_in_container> <path_in_host>

### Dockerfile

* В dockerfile можно делать комменты через #

- Команда для сборки кастомного образа на основе Dockerfile:
docker build **-t custom_ubuntu:1** .
* опция -t нужна для того, чтобы задать имя образа
- Если хотим передать имя файла при билде:
docker build -t custom_ubuntu:1 **-f <file_name>** .
- Опция при сборке для остановки на конкретном этапе:
docker build **--target=<build_step_name>** -t <image_name>
- *FROM* - команда для объявление родительского образа, на основе которого будет сделан кастомный образ
FROM <image>:<tag> | FROM ubuntu:20.04
- *COPY* - команда для копирования файла с компа в образ
COPY <filename_on_os> <filename_in_ps> | COPY test_file.txt test_file.txt
- *WORKDIR* - команда для назначения дефолтной рабочей директории
WORKDIR <path> | WORKDIR /home/vlad
- *RUN* - команда для установки зависимостей/запуска скрипта
RUN apt-get update && apt-get install -y vim git curl wget / RUN tosuch script.sh
- *ENTRYPOINT* - команда для определения входной точки при запуске контейнера
ENTRYPOINT [”zsh”] - при запуске контейнера входим в zsh
- *CMD* - команда для определения входной точки при запуске контейнера
CMD [”zsh”] - при запуске контейнера входим в zsh
- VOLUME - команда для создание вольюма на хосте
VOLUME <path>
- Установка зависимостей в сборку образа python из файла
1) COPY ./app/requirements.txt ./requirements.txt 
2) RUN python -m pip install --upgrade pip && pip instal -r requirements.txt
- Копирование артефактов (файлов) из одной сборки в другую при multi-stage build
FROM python AS <build1_name>
COPY /app/result.txt /app/result.txt
…
FROM python AS <build2_name>
COPY --from=<build1_name> /app/result.txt /app/result.txt .

### Связь файловой системы контейнера с файловой системой хоста

- *BUILD MOUNT* - метод связи хоста и контейнера, когда контейнер берет данные из файловой системы хоста  из любого места на хосте (папку можно примонтировать). Подходит для вкидывания в контейнер конфиденциальных файлов.
- Команда для монтирования папки к контейнеру (через bind_mount) при его создании
docker run -v <file/directory_absolute_path>:<file/directory_name_in_docker> <image>
- Команда для монтирования папки/файла к контейнеру с опцией read-only
docker run -v <file/directory_absolute_path>:<file/directory_name_in_docker>**:ro** <image>
- *VOLUME (тома)* - метод связи хоста и контейнера, когда контейнер берет данные из специального места на хосте (/var/lib/docker/volumes/), которое контролируется докером. С вольюмами можно работать через докер (создавать/улалять). Основное предназначение вольюмов - сохранения данных из контейнера.
- Команда для монтирования вольюма к контейнеру при его создании
docker run -v <volume_name>:<volume_name>:<volume_name_in_docker>
- Команда, чтобы посмотреть вольюмы:
docker volume ls
- Команда для создание вольюма
docker volume create <volume_name>
- Команда для удаление конкретного вольюма:
docker volume rm <volume_name>
- Команда для удаление всех вольюмов, в данный момент не связанных с поднятыми контейнерами:
docker volume prune
- Команда для просмотра пути, по которому лежит вольюм:
docker volume inspect <volume_name>

### Переменные окружения

- Можно указать в Dockerfile при помощи ключевого слова ENV
- Можно задать переменные окруженя при поднятии контейнера:
docker run --rm -it **-e ENV_VAR = 42** ubuntu
- Опция для указания файла с переменными окружениями при поднятии контейнера
docker run --rm -d --name database **--env-file <env_file.env>** -p 5432:5432 postgres

### Логирование

- Логи контейнера могут писаться: 1) в outut, 2) в файл, 3) в output через библиотеку
- Если логи пишутся в output, они могут писаться в 2 потока: 1) STDOUT, 2)STDERR
- Просмотреть в поднятом контейнере логи, которые пишутся в output
docker logs -f <container_name>
* опция -f (follow) нужна для того, чтобы конейнер не выключался после проверки
* опция -t (time) нужна для логирования времени событий
- Перенаправление потока ошибок в поток вывода
docker logs <container_name> 2>&1

### Порты

- Опция для связи портов программы в контейнере с портами хоста:
docker run --rm -d **-p <host_port>:<container_port>** <image_name>
*есть возможность прокидывать сразу несколько портов:
docker run -p 80:80 -p 81:80 -p 82:80 nginx
- Команд для проверки наличия программы на порте:
curl <host_name>:<port>
- Чтобы выставлять порты из контейнера в инструкции Dockerfile есть команда EXPOSE
Пример: EXPOSE 5432

### Сети

- Адрес localhost: 127.0.0.1
- Виды сетей в Docker: 
> none - полная сетевая изоляция контейнера, 
> host - отсутствие сетевой изоляции контейнера, 
> bridge - сеть, в которую контейнеры попадают по дефолту (с единой маской)
- Посмотреть виды сетей:
docker network ls
- Опция для указания сетевого правила для контейнера:
docker run --net=<network_type> image_name
- Создание/удаление новой кастомной сети:
docker network create/rm <network_name>
- В кастомных подсетях название контейнера является его доменом (подменяет ip)
- Получить информацию об объектах докера (контейнер, образ, вольюм, сеть)
docker inspect <название_или_ID_объекта>
- Команда для подключения работающего контейнера к сети:
docker network connect <network_name> <container_name>

### POSTGRES

- Команда для поднятия postgres в контейнере
- Обязательное окружение для POSTGRES:
- POSTGRES_USER=admin
- POSTGRES_PASSWORD=123
- POSTGRES_DB=calipso
- Команда для входа в работающий контейнер postgres:
docker exec -it <container_name> psql -U <user_name> -d <database_name>
- Путь для вольюма в контейнере с Postgres:
/var/lib/postgresql/data
- Инструкция docker-compose для проверки работоспособности Postgres в контейнере
healthcheck:
    test: [”CMD-SHELL”,”pg_isready”,”-U”,”<postgres_user>”]
    interval: 5s
    timeout: 5s
    retries: 3
- Команда для дампа базы
pg_dump -U admin calipso > /superset/pg_dump.sql (запускать в контейнере из bash)

### Docker-compose

- Якоря:
in: &cleaner
out: <<: *cleaner  (или просто  *cleaner)
- В docker-compose можно указывать относительный путь через точку (.)
- Чтобы выполнять команды docker-compose необходимо находиться в папке с файлом docker-comose.yaml
- Команда для поднятия docker-compose:
docker-compose up
- Опция для поднятия docker-compose в фоновом режиме:
docker-compose up **-d**
- Команда для остановки docker-compose и удаления всех контейнеров:
*вольюмы и сети не удаляются
docker-compose down
- Команда для выведения логов по контейнерам docker-compose
docker-compose logs
- Разница в командах  **docker-compose up -d db** и **docker-compose run -d db** в том, что первая запускает все службы, включая "db", в фоновом режиме, в то время как вторая запускает только одну конкретную службу ("db") в фоновом режиме, не включая остальные службы.
- Опция для удаления всех именованных и неименованных вольюмов при падении docker-compose:
docker-compose down **-v**
- Инструкция docker-compose для проверки работоспособности программы в контейнере
healthcheck:
    test: [”CMD-SHELL”,”pg_isready”,”-U”,”<postgres_user>”]
    interval: 5s
    timeout: 5s
    retries: 3
- Инструкция docker-compose для рестарта в случае падения контейнера
restart: always
- Инструкция docker-compose для указания зависимости между контейнерами:
depends_on:
    db: *other_service_name
        condition: service_healthy
- Инструкция docker-compose для создания реплик контейнеров:
deploy:
    replicas: 3

### Инструкции docker-compose

- **build** — собираем образ, на основе которого поднимем сервис
- **image** — образ, на основе которого поднимем сервис
- **container_name** — название контейнера в сервисе
- **volumes** — список вольюмов для сервиса
- **environment** — переменные окружения в сервисе
- **networks** — список сетей, к которым нужно подуключить сервис
- **ports** — список портов, которые нужно прокинуть у сервиса
- **restart** — указываем поведение сервиса при падении
- **deploy**/**replicas** — указываем количество контейнеров у сервиса
- **depends_on** — определяем зависимость между сервисами
- **healthcheck** — задаем проверку для сервис
- command - для запуска скрипта/программы