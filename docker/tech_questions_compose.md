# Docker Compose

## Основные конструкции (директивы) в файле `docker-compose.yml`, их назначение и примеры использования:

---

### **1. `version`** *(устарело в Compose v3)*  
**Назначение**: Определяет версию схемы Compose.  
**Пример**:  
```yaml
version: '3.8'
```
- В современных версиях (Compose v2+) эту строку можно опустить.

---

### **2. `services`**  
**Назначение**: Список сервисов (контейнеров), которые будут запущены.  
**Пример**:  
```yaml
services:
  web:
    image: nginx:latest
  db:
    image: postgres:13
```

---

### **3. `image`**  
**Назначение**: Указывает имя образа для сервиса.  
**Пример**:  
```yaml
services:
  app:
    image: python:3.9-slim
```

---

### **4. `build`**  
**Назначение**: Собирает образ из `Dockerfile` вместо использования готового.  
**Пример**:  
```yaml
services:
  app:
    build: ./app  # путь к папке с Dockerfile
    # или с доп. параметрами:
    build:
      context: ./app
      dockerfile: Dockerfile.prod
      args:
        APP_VERSION: 1.0
```

---

### **5. `ports`**  
**Назначение**: Пробрасывает порты контейнера на хост.  
**Пример**:  
```yaml
services:
  web:
    ports:
      - "80:80"       # хост:контейнер
      - "443:443"
```

---

### **6. `volumes`**  
**Назначение**: Монтирует тома (папки) или named volumes.  
**Пример**:  
```yaml
services:
  db:
    volumes:
      - ./data:/var/lib/postgresql/data  # хост:контейнер
      - logs:/app/logs                  # именованный том

volumes:  # объявление томов (если не существуют)
  logs:
```

---

### **7. `environment`**  
**Назначение**: Устанавливает переменные окружения.  
**Пример**:  
```yaml
services:
  db:
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    # или через файл:
    env_file: .env
```

---

### **8. `depends_on`**  
**Назначение**: Задает порядок запуска сервисов.  
**Пример**:  
```yaml
services:
  web:
    depends_on:
      - db
  db:
    image: postgres
```
- **Важно**: Не гарантирует готовность сервиса (только порядок запуска). Для проверки готовности используйте `healthcheck`.

---

### **9. `networks`**  
**Назначение**: Подключает сервисы к пользовательским сетям.  
**Пример**:  
```yaml
services:
  web:
    networks:
      - frontend
  db:
    networks:
      - backend

networks:  # объявление сетей
  frontend:
  backend:
```

---

### **10. `restart`**  
**Назначение**: Политика перезапуска контейнера при падении.  
**Пример**:  
```yaml
services:
  app:
    restart: unless-stopped  # или always, on-failure
```

---

### **11. `healthcheck`**  
**Назначение**: Проверяет работоспособность сервиса.  
**Пример**:  
```yaml
services:
  db:
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 3
```

---

### **12. `command`**  
**Назначение**: Переопределяет `CMD` из `Dockerfile`.  
**Пример**:  
```yaml
services:
  app:
    command: ["flask", "run", "--host", "0.0.0.0"]
```

---

### **13. `entrypoint`**  
**Назначение**: Переопределяет `ENTRYPOINT` из `Dockerfile`.  
**Пример**:  
```yaml
services:
  app:
    entrypoint: /docker-entrypoint.sh
```

---

### **14. `deploy`** *(только для Docker Swarm/Kubernetes)*  
**Назначение**: Настройки деплоя (реплики, ресурсы).  
**Пример**:  
```yaml
services:
  app:
    deploy:
      replicas: 3
      resources:
        limits:
          cpus: "0.5"
          memory: 512M
```

---

### **15. `labels`**  
**Назначение**: Добавляет метаданные к сервису.  
**Пример**:  
```yaml
services:
  web:
    labels:
      com.example.description: "Nginx web server"
```

---

### **16. `logging`**  
**Назначение**: Настройки логов.  
**Пример**:  
```yaml
services:
  app:
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

---

### **17. `secrets`** *(для конфиденциальных данных)*  
**Назначение**: Подключает секреты.  
**Пример**:  
```yaml
services:
  db:
    secrets:
      - db_password

secrets:
  db_password:
    file: ./db_password.txt
```

---

### Пример полного `docker-compose.yml` для веб-приложения (PHP + MySQL):
```yaml
services:
  web:
    image: php:8.2-apache
    ports:
      - "8080:80"
    volumes:
      - ./src:/var/www/html
    depends_on:
      - db
    environment:
      DB_HOST: db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: app_db
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s

volumes:
  mysql_data:
```

---

### Разница между `Dockerfile` и `docker-compose.yml`:
| **Dockerfile**                     | **docker-compose.yml**               |
|------------------------------------|--------------------------------------|
| Инструкции для сборки **образа**   | Конфигурация для запуска **сервисов** (группы контейнеров) |
| `FROM`, `RUN`, `COPY` и т. д.      | `services`, `volumes`, `networks`    |
| Запуск через `docker build`        | Запуск через `docker compose up`     |

**Команды для работы с Compose**:
```bash
docker compose up -d       # запуск в фоне
docker compose down        # остановка и удаление
docker compose logs -f     # просмотр логов
docker compose ps          # список сервисов
```
