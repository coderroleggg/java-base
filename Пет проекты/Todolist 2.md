# TO-DO List: 2 часть тех. задания

## Архитектура микросервисов

### 1. Task Management Service
- Управление задачами и списками задач
- База данных: PostgreSQL
- Функционал: Проверка просроченных задач и отправка уведомлений

### 2. Notification Service
- Отправка уведомлений пользователям
- База данных: PostgreSQL
- Порт: 8081

## Структура баз данных

### Task Management Service (существующие таблицы)
```sql
tasks (
    task_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    status VARCHAR(20) NOT NULL,
    due_date TIMESTAMP NOT NULL,
    is_overdue BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

### Notification Service
```sql
notifications (
    notification_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    task_id BIGINT NOT NULL,
    message TEXT NOT NULL,
    task_list_name TEXT NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'PENDING',
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    sent_at TIMESTAMP
);
```

## Взаимодействие через Kafka

### Topic: task-events
- Producer: Task Management Service
- Consumer: Notification Service
- Формат сообщения:
```json
{
  "eventType": "TASK_OVERDUE",
  "taskId": 1,
  "taskListId": 1,
  "userId": 1,
  "title": "Купить молоко",
  "dueDate": "2024-09-19T18:00:00"
}
```

## Планировщик задач

### Каждый час в Task Management Service
1. Находит задачи, которые:
- Не выполнены (status != 'DONE')
- Срок выполнения истёк (due_date < current_timestamp)
- Не помечены как просроченные (is_overdue = false)
2. Для каждой найденной задачи:
- Устанавливает is_overdue = true
- Отправляет событие в Kafka топик task-events

### Notification Service
- Обрабатывает события из Kafka
- Создает записи в таблице notifications
- Для каждого события TASK_OVERDUE:
  ```
  message = "Task '{title}' is overdue. Due date was: {dueDate}"
  task_list_name = получить значение с помощью GET-запроса /api/tasklists/{task_list_id} к сервису Task Management
  ```

## REST API

### Notification Service API
1. Получить уведомления пользователя
   - Метод: GET
   - Путь: /api/notifications/users/{userId}
   - Параметры в запросе для пагинации: page - номер страницы результата поиска, size - размер страницы результата поиска
   - Исходящие данные:
     ```json
     {
       "content": [
         {
           "notificationId": 1,
           "taskId": 1,
           "message": "Task 'Купить молоко' is overdue. Due date was: 2024-09-19T18:00:00",
           "status": "PENDING",
           "createdAt": "2024-09-19T19:00:00",
           "sentAt": null
         }
       ],
       "totalElements": 1,
       "totalPages": 1,
       "size": 20,
       "number": 0
     }
     ```
