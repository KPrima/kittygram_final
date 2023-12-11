# **KITTYGRAM** - социальная сеть для любителей котиков!

### ОПИСАНИЕ ПРОЕКТА:

#### Что умеет *Kittygram*?

- позволяет Вам добавлять записи о своих котиках: поделитесь фотографией и личным достижением своего котейки. Также авторам доступно удаление и редактирование своих записей;
- позволяет Вам просматривать записи о котиках других пользователей;

<details>

<summary>ИНСТРУКЦИЯ ПО ЗАПУСКУ ПРОЕКТА</summary>

#### Развертывание проекта локально:

1. Установите Docker и Docker-compose. Запустите сервис Docker.

2. Склонируйте репозиторий на свой компьютер:
```
    git clone git@github.com:KPrima/kittygram_final.git
    
    cd kittygram_final
```
    

3. Наполните файл .env своими данными.

4. Разверните проект:
```
    sudo docker compose -f docker-compose.yml up
```
    

5. Выполните миграции:
```
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
    

6. Соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:
```
    sudo docker compose -f docker-compose.yml exec backend python manage.py collectstatic
    
    sudo docker compose -f docker-compose.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
    

7. Зарегистрируйтесь и можно приступать к наполнению сайта котиками!

#### Деплой проекта на удаленном сервере:
1. Склонируйте репозиторий на свой компьютер:
```
    git clone git@github.com:Trishkin32/kittygram_final.git
    
    cd kittygram_final
```
    

2. Создайте образы (замените username на ваш логин на DockerHub):
```
    cd frontend
    
    sudo docker build -t username/kittygram_frontend .
    
    cd ../backend
    
    sudo docker build -t username/kittygram_backend .
    
    cd ../nginx
    
    sudo docker build -t username/kittygram_gateway .
```
    

4. Загрузите образы на DockerHub:
```
    sudo docker push username/kittygram_frontend
    
    sudo docker push username/kittygram_backend
    
    sudo docker push username/kittygram_gateway
```
    

5. Подключитесь к удаленному серверу
```
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера
```

6. Установите Nginx, Docker и Docker-compose. Запустите сервис Docker.

7. Создайте директорию kittygram/ в домашней директории сервера:
```
    mkdir kittygram
```
    

8. В директории kittygram/ создайте файл docker-compose.production.yml со следующим содержимым (замените docker_username на ваш логин на DockerHub):
```
version: '3'

volumes:
  pg_data:
  static:
  media:

services:
  db:
    image: postgres:13
    env_file: .env
    volumes:
      - pg_data:/var/lib/postgresql/data

  backend:
    image: docker_username/kittygram_backend
    env_file: .env
    volumes:
      - static:/backend_static/
      - media:/app/media/
    depends_on:
      - db

  frontend:
    image: docker_username/kittygram_frontend
    env_file: .env
    command: cp -r /app/build/. /static/
    volumes:
      - static:/static/

  gateway:
    image: docker_username/kittygram_gateway
    env_file: .env
    ports:
      - 9000:80
    volumes:
      - static:/static/
      - media:/app/media/
    depends_on:
      - backend
```

9. В директории kittygram/ создайте файл .env

10. Запустите docker compose в режиме демона:
```
    sudo docker compose -f docker-compose.production.yml up -d
```
    

11. Выполните миграции:
```
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
    

12. Соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:
```
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    
bash sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```

13. Откройте конфиг Nginx:

    sudo nano /etc/nginx/sites-enabled/default
    

14. Перенаправьте запросы в сеть контейнеров:
```
    server {
        server_name <ваш домен>;

    location / {
        proxy_pass http://127.0.0.1:9000;
    }
    }
```
    

15. Проверьте работоспособность конфига Nginx:
```
    sudo nginx -t
```
    
Если ответ в терминале такой, значит, ошибок нет:
```
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    
bash nginx: configuration file /etc/nginx/nginx.conf test is successful 
```

16. Перезапускаем Nginx
```
    sudo service nginx reload
```

### Настройка CI/CD

1. Файл workflow уже написан. Он находится в директории
```
    kittygram/.github/workflows/main.yml
```
    

3. Для адаптации его на своем сервере добавьте секреты в GitHub Actions:
```
    DOCKER_USERNAME                # имя пользователя в DockerHub
    DOCKER_PASSWORD                # пароль пользователя в DockerHub

    HOST                           # ip_address сервера
    USER                           # имя пользователя
    SSH_KEY                        # приватный ssh-ключ (cat ~/.ssh/id_rsa)
    SSH_PASSPHRASE                 # кодовая фраза (пароль) для ssh-ключа

    POSTGRES_DB                    # название БД
    POSTGRES_USER                  # пользователь БД
    POSTGRES_PASSWORD              # пароль пользователя БД

    TELEGRAM_TO                    # id телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
    TELEGRAM_TOKEN                 # токен бота (получить токен можно у @BotFather, /token, имя бота)
```

</details>    

<details>

<summary>ПРИМЕРЫ ЗАПРОСОВ К API</summary>

Теперь вы можете отправлять запросы к api, например: 

- Создать пользователя. 
Пример POST-запроса к api/users/:

```
{
    "email": "user@mail.com",
    "username": "user",
    "password": "user_password"
}
```

- Добавить запись о котейке.
Пример POST запроса к api/cats/add/:

```
{
    "name": "cats_name",
    "color": "cats_color",
    "birth_year": cats_birth_year
}
```

- Просмотр всех котеек.
GET запрос к api/cats/

- Просмотр определенного котейки.
GET запрос к api/cats/{cat_id}

- Получить список кошачьих достижений.
GET запрос к api/achievements/


</details>

### ИСПОЛЬЗОВАННЫЕ ТЕХНОЛОГИИ:
* Python 3.9
* Django 3.2
* Django REST framework
* Djoser
* PostgreSQL
* JavaScript
* Docker
* gunicorn
* nginx

### ОБ АВТОРЕ:

*Прима Кристина*

[KPrima](https://github.com/KPrima)
