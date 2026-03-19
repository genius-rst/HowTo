# Установка Apache Guacamole через Docker Compose

Apache Guacamole — это клиент удалённого доступа, который позволяет пользователям подключаться к различным системам с помощью веб-браузера. Использование Docker Compose для развертывания Apache Guacamole значительно упрощает установку и управление его компонентами.

Вот шаги, чтобы развернуть Apache Guacamole с использованием Docker Compose:

1. **Создайте файл `docker-compose.yml`:**
    
    В корневом каталоге вашего проекта создайте файл `docker-compose.yml` со следующим содержимым:
    
    ```yaml
    services:
      guacamole:
        image: guacamole/guacamole
        container_name: guacamole
        restart: always
        ports:
          - "8080:8080"
        links:
          - guacd
          - mysql
        depends_on:
          - guacd
          - mysql
        environment:
          GUACD_HOSTNAME: guacd
          MYSQL_HOSTNAME: mysql
          MYSQL_PORT: 3306
          MYSQL_DATABASE: guacamole_db
          MYSQL_USER: guacamole_user
          MYSQL_PASSWORD: some_password
          MYSQL_ROOT_PASSWORD: root_password
        volumes:
          - ./config:/etc/guacamole
    
      guacd:
        image: guacamole/guacd
        container_name: guacd
        restart: always
    
      mysql:
        image: mysql:5.7
        container_name: mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: root_password
          MYSQL_DATABASE: guacamole_db
          MYSQL_USER: guacamole_user
          MYSQL_PASSWORD: some_password
        volumes:
          - ./mysql_data:/var/lib/mysql
    
    ```
    
    В этом файле определены три сервиса: `guacamole`, `guacd` и `mysql`.
2. **Запустите Docker Compose:**
    
    Откройте терминал, перейдите в каталог, где находится ваш файл `docker-compose.yml`, и выполните команду:
    
    ```sh
    docker compose up -d
    
    ```
    
    Этот процесс загрузит необходимые образы, создаст и запустит контейнеры.
3. **Настройка базы данных:**
    
    После того как контейнеры будут запущены, необходимо настроить базу данных для использования Apache Guacamole. Это можно сделать, запустив инициализационный скрипт базы данных, предоставляемый Guacamole:
    
    ```sh
    docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
    
    ```
    
    Выполните инициализационный скрипт в контейнере MySQL:
    
    ```sh
    docker cp initdb.sql mysql:/initdb.sql
    docker exec -it mysql bash
    mysql -u root -p guacamole_db < /initdb.sql
    
    ```
4. **Доступ к Guacamole:**
    
    После выполнения всех шагов, Guacamole будет доступен по адресу `http://localhost:8080/guacamole`.

Теперь у вас должно быть работающая установка Apache Guacamole через Docker Compose. Вы можете войти в систему, используя созданную базу данных и управлять своими подключениями через веб-интерфейс. По умолчанию Apache Guacamole создаёт пользователя с именем guacadmin и паролем guacadmin
