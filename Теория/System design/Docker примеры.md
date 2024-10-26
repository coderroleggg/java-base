# Dockerfile и Docker-команды: примеры

Здесь я собрал самые полезные примеры Dockerfile и команды Docker, которые постоянно используются в работе. Сохраняй - пригодится.

## Примеры Dockerfile для разных типов приложений

### 1. Spring Boot приложение
```dockerfile
# Multi-stage сборка для Java приложения
FROM maven:3.8.4-openjdk-17 AS builder
WORKDIR /app
COPY pom.xml .
# Скачиваем зависимости отдельно для лучшего кэширования
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar

# Правильно указываем параметры JVM
ENV JAVA_OPTS="-Xmx512m -Xms256m"
EXPOSE 8080
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```

### 2. Frontend приложение
```dockerfile
# Билд React/Vue/Angular приложения
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Nginx для раздачи статики
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

### 3. Python приложение
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Устанавливаем зависимости первыми для кэширования
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Создаем непривилегированного пользователя
RUN adduser --disabled-password --gecos "" appuser && \
    chown -R appuser:appuser /app
USER appuser

CMD ["python", "app.py"]
```

### 4. Приложение + PostgreSQL (docker-compose)
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_PASSWORD=password
      - DATABASE_USERNAME=user
      - DATABASE_URL=jdbc:postgresql://db:5432/appdb
    depends_on:
      - db
  
  db:
    image: postgres:14-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=appdb

volumes:
  postgres_data:
```

## 🛠 Самые полезные команды Docker

### Базовые операции с контейнерами

```bash
# Запуск контейнера
docker run -d -p 8080:80 --name myapp nginx

# Просмотр логов в реальном времени
docker logs -f myapp

# Выполнение команды внутри контейнера
docker exec -it myapp bash

# Копирование файлов в/из контейнера
docker cp myfile.txt myapp:/app/
docker cp myapp:/logs/app.log ./
```

### Работа с образами

```bash
# Сборка образа
docker build -t myapp:1.0 .

# Сборка с указанием платформы
docker build --platform linux/amd64 -t myapp:1.0 .

# Сохранение образа в файл
docker save myapp:1.0 > myapp.tar

# Загрузка образа из файла
docker load < myapp.tar
```

### Управление данными

```bash
# Создание volume
docker volume create mydata

# Запуск с подключением volume
docker run -v mydata:/data myapp

# Создание bind mount
docker run -v $(pwd):/app myapp
```

### Очистка системы

```bash
# Удаление неиспользуемых образов
docker image prune -a

# Удаление всех остановленных контейнеров
docker container prune

# Удаление всего неиспользуемого
docker system prune -a

# Очистка только dangling images
docker image prune
```

### Docker Compose команды

```bash
# Запуск всех сервисов
docker compose up -d

# Просмотр логов всех сервисов
docker compose logs -f

# Остановка всех сервисов
docker compose down

# Пересборка конкретного сервиса - api
docker compose build api

# Рестарт одного сервиса - api
docker compose restart api
```

## 💡 Полезные однострочники

```bash
# Остановка всех запущенных контейнеров
docker stop $(docker ps -q)

# Удаление всех контейнеров
docker rm $(docker ps -a -q)

# Удаление всех образов
docker rmi $(docker images -q)

# Просмотр использования ресурсов
docker stats

# Найти самые большие образы
docker images --format '{{.Size}}\t{{.Repository}}:{{.Tag}}' | sort -hr | head -n 5
```