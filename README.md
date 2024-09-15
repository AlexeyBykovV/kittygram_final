# Kittygram — социальная сеть для обмена фотографиями любимых питомцев. 
# Запуск проекта Kittygram в контейнерах и CI/CD с помощью GitHub Actions.

## Описание проекта

Kittygram - социальная сеть для обмена фотографиями любимых питомцев, а менно котов и кошек с публикацией их достижений.

В проекте настроен деплой проекта черпез СI/CD с помощью GitHub Actions, в ходе которого:
- проверяется код бэкенда в репозитории на соответствие PEP8;
- запускает тесты для фронтенда и бэкенда;
- собирает образы проекта и отправляет их на Docker Hub:
- обновляет образы на сервере и перезапускает приложение при помощи Docker Compose;
- выполняет команды для сборки статики в приложении бэкенда, переносить статику в volume; выполняет миграции;
- извещает вас в Telegram об успешном завершении деплоя.

### Зависимости
- Python 3.9
- Django 3.2.3
- Django REST framework 3.12.4
- Gunicorn
- Nginx
- PostgreSQL
- Docker
- GitHub Action

### Переменные окружения

Пример файла .env c переменными окружения, необходимыми для запуска приложения
```
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password
POSTGRES_DB=kittygram_db
DB_HOST=db
DB_PORT=5432
SECRET_KEY=django_secret_key
ALLOWED_HOSTS=hosts
DEBUG=True
```

## Запуск через DockerHUB на удаленном сервере

На удаленном сервере создаем папку проекта `kittygram` и переходим в нее:
```
mkdir kittygram
cd kittygram
```

В папку проекта `kittygram` копируем файл `docker-compose.production.yml` и запускаем его:
```
sudo docker compose -f docker-compose.production.yml up
```

Будет выполнено:
1. Скачивание образов с DockerHub
2. Развернуты контейнеры отвечающие за `db`, `backend`, `frontend`, `gateway` и созданы volume `pg_data`, `static`, `media`.
3. Установлены необходимые зависимости и выполнены необходимые миграции и сбор статики

## Запуск через GitHub на локальном устройстве

Клонируем репозиторий: 
```
git clone git@github.com:AlexeyBykovV/kittygram_final.git
```

Выполняем запуск:
```
sudo docker compose -f docker-compose.yml up
```

Выполняем сбор статистики и миграцию для бэкенда. Статистика фронтенда собирается во время запуска контейнера, после чего он останавливается. 
```
sudo docker compose -f [имя-файла-docker-compose.yml] exec backend python manage.py migrate
sudo docker compose -f [имя-файла-docker-compose.yml] exec backend python manage.py collectstatic
sudo docker compose -f [имя-файла-docker-compose.yml] exec backend cp -r /app/collected_static/. /static/static/
```

Проект доступен по адресу:
```
http://localhost:8000/
```

## Остановка проекта Kittygram

В терминале в котором был запущен проект нажать комбинацию клавищ `Ctrl+С`, либо в доступном терминале ввести команду:
```
sudo docker compose -f docker-compose.yml down
```
