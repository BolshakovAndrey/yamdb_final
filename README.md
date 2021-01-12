[![Codacy Badge](https://api.codacy.com/project/badge/Grade/be2c7b20c1f54cd8beafaf3330dc4e59)](https://app.codacy.com/gh/BolshakovAndrey/api_yamdb?utm_source=github.com&utm_medium=referral&utm_content=BolshakovAndrey/api_yamdb&utm_campaign=Badge_Grade_Settings)
[![Build Status](https://travis-ci.com/BolshakovAndrey/api_yamdb.svg?branch=master)](https://travis-ci.com/BolshakovAndrey/api_yamdb)
![Yamdb-app_workflow](https://github.com/BolshakovAndrey/yamdb_final/workflows/Yamdb-app_workflow/badge.svg)

API развернут по адресу http://178.154.234.242/api/v1/

# REST API для сервиса **YamDb** 
версия c Docker, Continuous Integration на GitHub Actions

## База отзывов о фильмах, книгах и музыке. 

Выполнено в рамках реализации группового проекта студентов факультета _бэкенд-разработки_ _Яндекс.Практикум_ по курсу **"Работа с внешними API"**

## Описание

Проект **YaMDb** собирает отзывы пользователей на произведения. 
Произведения делятся на категории: «Книги», «Фильмы», «Музыка». 
Список категорий (Category) может быть расширен (например, можно добавить категорию 
«Изобразительное искусство» или «Ювелирка» через интерфейс Django администратора).

### API для сервиса YaMDb 
позволяет работать со следующими сущностями:

**Пользователи** (Получить список всех пользователей, создание пользователя, получить пользователя по username, изменить данные пользователя по username, удалить пользователя по username, получить данные своей учетной записи, изменить данные своей учетной записи)

**Произведения**, к которым пишут отзывы (Получить список всех объектов, создать произведение для отзывов, информация об объекте, обновить информацию об объекте, удалить произведение)

**Категории** (типы) произведений (Получить список всех категорий, создать категорию, удалить категорию)

**Жанры** (Получить список всех жанров, создать жанр, удалить жанр)

**Отзывы** (Получить список всех отзывов, создать новый отзыв, получить отзыв по id, частично обновить отзыв по id, удалить отзыв по id)

**Коментарии к отзывам** (Получить список всех комментариев к отзыву по id, создать новый комментарий для отзыва, получить комментарий для отзыва по id, частично обновить комментарий к отзыву по id, удалить комментарий к отзыву по id)

**JWT-токен** (Отправление confirmation_code на переданный email, получение JWT-токена в обмен на email и confirmation_code)

### Алгоритм регистрации пользователей

* Пользователь отправляет запрос с параметром email на /auth/email/.
* YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес email .
* Пользователь отправляет запрос с параметрами email и confirmation_code на /auth/token/, в ответе на запрос ему приходит token (JWT-токен).
* При желании пользователь отправляет PATCH-запрос на /users/me/ и заполняет поля в своём профайле (описание полей — в документации).
[Полная документация API (redoc.yaml)](https://github.com/BolshakovAndrey/api_yamdb/blob/master/static/redoc.yaml)

## Установка на локальном компьютере
Эти инструкции помогут вам создать копию проекта и запустить ее на локальном компьютере для целей разработки и тестирования.

### Установка Docker
Установите Docker, используя инструкции с официального сайта:
- для [Windows и MacOS](https://www.docker.com/products/docker-desktop)
- для [Linux](https://docs.docker.com/engine/install/ubuntu/). Отдельно потребуется установть [Docker Compose](https://docs.docker.com/compose/install/)

### Запуск проекта (на примере Linux)

- Создайте на своем компютере папку проекта YamDb `mkdir yamdb` и перейдите в нее `cd yamdb`
- Склонируйте этот репозиторий в текущую папку `git clone https://github.com/BolshakovAndrey/yamdb_final/ .`
- Создайте файл `.env` командой `touch .env` и добавьте в него переменные окружения для работы с базой данных:
```
DB_NAME=postgres # имя базы данных
POSTGRES_USER=postgres # логин для подключения к базе данных
POSTGRES_PASSWORD=postgres # пароль для подключения к БД (установите свой)
DB_HOST=db # название сервиса (контейнера)
DB_PORT=5432 # порт для подключения к БД 
```
- Запустите docker-compose командой `sudo docker-compose up -d`
- Накатите миграции `sudo docker-compose exec yamdb python manage.py migrate`
- Соберите статику командой `sudo docker-compose exec yamdb python manage.py collectstatic --no-input`
- Создайте суперпользователя Django `sudo docker-compose exec yamdb python manage.py createsuperuser --username admin --email 'admin@yamdb.com'`
- Загрузите данные в базу данных при необходимости `sudo docker-compose exec yamdb python manage.py loaddata data/fixtures.json`
## Деплой на удаленный сервер
Для запуска проекта на удаленном сервере необходимо:
- скопировать на сервер файлы `docker-compose.yaml`, `.env` и папку `nginx` командами:
```
scp docker-compose.yaml  <user>@<server-ip>:
scp .env <user>@<server-ip>:
scp -r nginx/ <user>@<server-ip>:

```
- создать переменные окружения в разделе `secrets` настроек текущего репозитория:
```
DOCKER_PASSWORD # Пароль от Docker Hub
DOCKER_USERNAME # Логин от Docker Hub
HOST # Публичный ip адрес сервера
USER # Пользователь зарегистрированный на сервере
PASSPHRASE # Если ssh-ключ защищен фразой-паролем
SSH_KEY # Приватный ssh-ключ
TELEGRAM_TO # ID телеграм-аккаунта
TELEGRAM_TOKEN # Токен бота
```

### После каждого обновления репозитория (`git push`) будет происходить:
1. Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest из репозитория yamdb_final
2. Сборка и доставка докер-образов на Docker Hub.
3. Автоматический деплой.
4. Отправка уведомления в Telegram.


**Участники:**

[Гаврилов Павел.](https://github.com/Venatorr/api_yamdb)
Управление пользователями (Auth и Users): система регистрации и аутентификации, права доступа, работа с токеном, система подтверждения e-mail, поля.

[Большаков Андрей.](https://github.com/BolshakovAndrey/api_yamdb) 
- Категории (Categories), жанры (Genres) и произведения (Titles): модели, view и эндпойнты для них и рейтинги. 
- Докеризация разработка процесса CI (непрерывной интеграции) с использованием GitHub Actions. 
- Подготовка и продакшн на YandexCloud.

[Дробышев Артем.](https://github.com/stpdmnk/api_yamdb-1)
Отзывы (Review) и комментарии (Comments): модели и view, эндпойнты, права доступа для запросов. Рейтинги произведений.
