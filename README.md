# EcommpayVM

## Содержание
* [Развертывание и разработка](#deployment-and-development)
    * [Введение](#intro)
    * [Старт контейнеров](#container-start)
    * [Остановка контейнеров](#container-stop)
    * [Выполнение команд в контейнере](#container-exec)
    * [Первый запуск](#first-start)
    * [Отладка с XDebug](#xdebug)

## <a name="deployment-and-development"></a>Развертывание и разработка
Для развертывания окружения необходимы **[Docker][1]** и **[Docker Compose][2]**.
Устанавливайте последние версии, **Docker Compose** не ниже v1.7.0.

### <a name="intro"></a>Введение
Утилита _docker-compose_ управляет запуском/оснановкой всех необходимых
контейнеров. На текущий момент для проекта запускаются 3 контейнера:

- `app` (PHP)
- `web` (Angular, nginx)
- `mysql`

При этом из некоторых контейнеров пробрасываются порты на хост:

Контейнер | Порт контейнера | Порт хоста
--------- |---------------- | ---------- 
web       | 80              | 80
web       | 8080            | 8080
app       | 22              | 49154
mysql     | 3306            | 3306

> Если другие приложения на вашем компьютере используют данные порты —
> предварительно остановите их.

При старте контейнера `app` docker выполнит монтирование дирректории проекта
к контейнеру по пути `/var/www`. Таким образом все изменения исходного кода,
произведенные на вашем компьютере будут мгновенно отражены в контейнере.

> Обратите внимание, что редактирование кода и работа с системой контроля версий
> осуществляется на вашей машине. Тогда как работа с `composer`, `symfony console`,
> `phpunit`, `npm` и прочими осуществляется внутри контейнера.


### <a name="container-start"></a>Старт контейнеров
После клонирования репозитория, находясь в корневом каталоге проекта,
выполните старт контейнеров с помощью утилиты `docker-compose`.

```bash
docker-compose up -d
```

Команда `up -d` автоматически создаст контейнеры, если они не существовали ранее,
и запустит их в фоновом режиме.

В дальнейшем, когда контейнеры уже будут созданы, вы можете пользоваться командой `start`
для запуска контейнеров:

```bash
docker-compose start
```


### <a name="container-stop"></a>Остановка контейнеров
Для остановки контейнеров проекта выполните, находясь в корневом каталоге проекта:

```bash
docker-compose stop
```


### <a name="container-exec"></a>Выполнение команд в контейнере
В процессе разработки часто требуется выполнить какие-либо команды внутри контейнера
разрабатываемого приложения (контейнер `app`). В образе данного контейнера заведено
2 пользователя: _root_ и _app_. Веб-сервер запущен от имени пользователя `app` и
большинство команд (например `app/console`, `phpunit`, `composer`) нужно выполнять
с привилегией данного пользователя.

Для запуска команд в контейнере используется `docker-compose exec`.

Чтобы запустить сеанс `bash` в контейнере с приложением от имени пользователя `app`
выполните в терминале:

```bash
docker-compose exec --user=app app bash
```

> Не забывайте параметр `--user=app`. По умолчанию команда будет выполнена от `root`!

Аналогично вы можете выполнять команды в других контейнерах:

```bash
# Запускаем mysql-клиент в контейнере нашего контейнера с MySQL
docker-compose exec mysql mysql -u root
```


### <a name="first-start"></a>Первый запуск
Выполните `git clone`, чтобы склонировать проект и войдите в каталог проекта.

Запустите `docker-compose up -d`, дождитесь развертывания контейнера.

Установите зависимости `nodejs`:

```bash
docker-compose exec --user=web web npm install
```

> Для пользователей Windows: `docker-compose exec --user=app app npm install --no-bin-link`

Установите зависимости php:

```bash
docker-compose exec --user=app app composer install --no-interaction
```

Создайте базу данных и схему:

```bash
docker-compose exec --user=app app app/console doctrine:database:create
docker-compose exec --user=app app app/console doctrine:schema:create
```
Для тестов 
```bash
docker-compose exec --user=app app app/console doctrine:database:create --env=test
docker-compose exec --user=app app app/console doctrine:schema:create --env=test
```

**Готово!** Теперь вы можете обратиться к приложению, открыв в браузере
[http://localhost](http://localhost). Frontend 
[http://localhost:8080](http://localhost:8080). Backend 
> Чтобы избежать повторения `docker-compose exec --user=app ...` вы можете запустить
> в контейнере `bash` и исполнять последующие команды в данной сессии:
> `docker-compose exec --user=app app bash`


> Если контейнеры уже были запущены — предварительно остановите их, выполнив
> `docker-compose stop`.

Xdebug в контейнере настроен на удалённую отладку со следующими параметрами:

Параметр                   | Значание
-------------------------- | -------------------------------------
xdebug.remote_enable       | on
xdebug.remote_autostart    | off
xdebug.remote_connect_back | off
xdebug.remote_host         | Из переменной окружения `XDEBUG_HOST`
xdebug.remote_port         | 9000
xdebug.remote_handler      | dbgp
xdebug.remote_mode         | req
xdebug.idekey              | phpstorm

##### Для пользователей Windows/Mack + Docker Toolbox
Если вы используете **Docker Toolbox**, то вам нужен ip-адрес вашей машины в подсети
с виртуальной машиной, на которой запущен докер. Чаще всего это `192.168.99.1`.


#### Отладка WEB-приложения
Для отладки приложения используйте ssh: 
- Порт: `49154`
- Логин: `app`
- Пароль: `app`

> `Внимание!!! Используется только для запуска тестов в PHPstorm через ssh.`

[1]: https://docs.docker.com/engine/quickstart/
[2]: https://docs.docker.com/compose/install/
