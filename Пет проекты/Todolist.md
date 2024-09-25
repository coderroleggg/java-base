# TODO-лист. Техническое задание

Здесь описан только бэкенд проекта, так как задача в том, чтобы подготовиться к настоящей работе Java бэкенд разработчиком

## Технологический стек

- Spring Boot
- PostgreSQL
- REST API
- Spring Data JPA

## Архитектура приложения

1. Controller layer
2. Service layer
3. Repository layer

## Таблицы базы данных

### task_lists

- task_list_id: BIGSERIAL (Primary Key)
- name: VARCHAR(255) NOT NULL
- created_at: TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
- updated_at: TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP

### tasks

- task_id: BIGSERIAL (Primary Key)
- task_list_id: BIGINT NOT NULL (Foreign Key referencing task_lists.task_list_id)
- title: VARCHAR(255) NOT NULL
- description: TEXT
- status: VARCHAR(20) NOT NULL CHECK (status IN ('TODO', 'IN_PROGRESS', 'DONE'))
- priority: VARCHAR(10) NOT NULL CHECK (priority IN ('LOW', 'MEDIUM', 'HIGH'))
- due_date: TIMESTAMP
- created_at: TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
- updated_at: TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP

## API Endpoints

### TaskList Endpoints

1. Создать новый список задач
    - Метод: POST
    - Путь: /api/tasklists
    - Входящие данные:
        
        ```json
        {
          "name": "Мой список задач"
        }
        
        ```
        
    - Исходящие данные:
        
        ```json
        {
          "task_list_id": 1,
          "name": "Мой список задач",
          "created_at": "2024-09-19T10:00:00",
          "updated_at": "2024-09-19T10:00:00"
        }
        
        ```
        
2. Получить все списки задач
    - Метод: GET
    - Путь: /api/tasklists
    - Исходящие данные:
        
        ```json
        [
          {
            "task_list_id": 1,
            "name": "Мой список задач",
            "created_at": "2024-09-19T10:00:00",
            "updated_at": "2024-09-19T10:00:00"
          },
          {
            "task_list_id": 2,
            "name": "Рабочие задачи",
            "created_at": "2024-09-19T11:00:00",
            "updated_at": "2024-09-19T11:00:00"
          }
        ]
        
        ```
        
3. Получить список задач по ID
    - Метод: GET
    - Путь: /api/tasklists/{task_list_id}
    - Исходящие данные:
        
        ```json
        {
          "task_list_id": 1,
          "name": "Мой список задач",
          "created_at": "2024-09-19T10:00:00",
          "updated_at": "2024-09-19T10:00:00"
        }
        
        ```
        
4. Обновить список задач
    - Метод: PUT
    - Путь: /api/tasklists/{task_list_id}
    - Входящие данные:
        
        ```json
        {
          "name": "Обновленный список задач"
        }
        
        ```
        
    - Исходящие данные:
        
        ```json
        {
          "task_list_id": 1,
          "name": "Обновленный список задач",
          "created_at": "2024-09-19T10:00:00",
          "updated_at": "2024-09-19T12:00:00"
        }
        
        ```
        
5. Удалить список задач
    - Метод: DELETE
    - Путь: /api/tasklists/{task_list_id}
    - Исходящие данные: HTTP статус 204 No Content

### Task Endpoints

1. Создать новую задачу
    - Метод: POST
    - Путь: /api/tasklists/{task_list_id}/tasks
    - Входящие данные:
        
        ```json
        {
          "title": "Купить молоко",
          "description": "Купить 1 литр молока в магазине",
          "priority": "MEDIUM",
          "due_date": "2024-09-20T18:00:00"
        }
        
        ```
        
    - Исходящие данные:
        
        ```json
        {
          "task_id": 1,
          "task_list_id": 1,
          "title": "Купить молоко",
          "description": "Купить 1 литр молока в магазине",
          "status": "TODO",
          "priority": "MEDIUM",
          "due_date": "2024-09-20T18:00:00",
          "created_at": "2024-09-19T13:00:00",
          "updated_at": "2024-09-19T13:00:00"
        }
        
        ```
        
2. Получить все задачи в списке
    - Метод: GET
    - Путь: /api/tasklists/{task_list_id}/tasks
    - Исходящие данные:
        
        ```json
        [
          {
            "task_id": 1,
            "task_list_id": 1,
            "title": "Купить молоко",
            "description": "Купить 1 литр молока в магазине",
            "status": "TODO",
            "priority": "MEDIUM",
            "due_date": "2024-09-20T18:00:00",
            "created_at": "2024-09-19T13:00:00",
            "updated_at": "2024-09-19T13:00:00"
          },
          {
            "task_id": 2,
            "task_list_id": 1,
            "title": "Позвонить маме",
            "description": "Позвонить маме и узнать как дела",
            "status": "TODO",
            "priority": "HIGH",
            "due_date": "2024-09-19T20:00:00",
            "created_at": "2024-09-19T14:00:00",
            "updated_at": "2024-09-19T14:00:00"
          }
        ]
        
        ```
        
3. Получить задачу по ID
    - Метод: GET
    - Путь: /api/tasklists/{task_list_id}/tasks/{task_id}
    - Исходящие данные:
        
        ```json
        {
          "task_id": 1,
          "task_list_id": 1,
          "title": "Купить молоко",
          "description": "Купить 1 литр молока в магазине",
          "status": "TODO",
          "priority": "MEDIUM",
          "due_date": "2024-09-20T18:00:00",
          "created_at": "2024-09-19T13:00:00",
          "updated_at": "2024-09-19T13:00:00"
        }
        
        ```
        
4. Обновить задачу
    - Метод: PUT
    - Путь: /api/tasklists/{task_list_id}/tasks/{task_id}
    - Входящие данные:
        
        ```json
        {
          "title": "Купить молоко и хлеб",
          "description": "Купить 1 литр молока и буханку хлеба в магазине",
          "status": "IN_PROGRESS",
          "priority": "HIGH",
          "due_date": "2024-09-20T19:00:00"
        }
        
        ```
        
    - Исходящие данные:
        
        ```json
        {
          "task_id": 1,
          "task_list_id": 1,
          "title": "Купить молоко и хлеб",
          "description": "Купить 1 литр молока и буханку хлеба в магазине",
          "status": "IN_PROGRESS",
          "priority": "HIGH",
          "due_date": "2024-09-20T19:00:00",
          "created_at": "2024-09-19T13:00:00",
          "updated_at": "2024-09-19T15:00:00"
        }
        
        ```
        
5. Удалить задачу
    - Метод: DELETE
    - Путь: /api/tasklists/{task_list_id}/tasks/{task_id}
    - Исходящие данные: HTTP статус 204 No Content
6. Получить задачи по статусу
    - Метод: GET
    - Путь: /api/tasklists/{task_list_id}/tasks?status={status}
    - Исходящие данные: Список задач с указанным статусом
7. Получить задачи по приоритету
    - Метод: GET
    - Путь: /api/tasklists/{task_list_id}/tasks?priority={priority}
    - Исходящие данные: Список задач с указанным приоритетом

## Дополнительные требования

1. Реализовать обработку ошибок и возвращать соответствующие HTTP-статусы и сообщения об ошибках.
2. Использовать DTO (Data Transfer Objects) для входящих и исходящих данных.
3. Реализовать валидацию входящих данных.
4. Использовать Spring Data JPA для работы с базой данных.
5. Настроить Swagger для документирования API.

## Опционально (со звездочкой)

Реализовать аутентификацию и авторизацию с использованием Spring Security:

- Добавить сущность User и связать её с TaskList.
- Реализовать регистрацию и вход пользователей.
- Обеспечить доступ к спискам задач и задачам только для их владельцев.
