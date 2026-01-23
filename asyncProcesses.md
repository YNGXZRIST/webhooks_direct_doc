# Асинхронные процессы - startYandexDirectFetch, getStatus, getResults, stopFetch

Запуск и управление фоновыми процессами для получения данных из Яндекс.Директ.

## 1. Запуск асинхронного процесса - startYandexDirectFetch

### Endpoint
```
POST /webHooks/startYandexDirectFetch
```

### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | body | Да | string | Логин аккаунта |
| `client` | body | Да | string | Логин клиента |
| `type` | body | Да | string | Тип операции |
| `params` | body | Нет | object | Дополнительные параметры |

### Типы операций

| Тип | Описание |
|-----|----------|
| `history` | Получение истории изменений кампаний |
| `settings` | Получение настроек и стратегий кампаний |
| `strategy_study` | Получение данных обучения стратегий |
| `account_goals` | Получение целей и конверсий аккаунта |

### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/startYandexDirectFetch',
    json={
        'account': 'myaccount',
        'client': 'myclient',
        'type': 'strategy_study'
    }
)
print(response.json())
```

### Пример успешного ответа

```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "message": "Процесс успешно запущен",
    "account": "myaccount",
    "client": "myclient",
    "type": "strategy_study",
    "typeLabel": "Получение данных обучения стратегий"
}
```

---

## 2. Получение статуса процесса - getYandexDirectFetchStatus

### Endpoint
```
GET /webHooks/getYandexDirectFetchStatus
```

### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | query | Да | string | ID процесса |

### Пример запроса

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getYandexDirectFetchStatus',
    params={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

### Статусы процесса

| Статус | Описание |
|--------|----------|
| `new` | Новый процесс |
| `pending` | Выполняется |
| `done` | Завершен успешно |
| `error` | Ошибка выполнения |
| `stopped` | Остановлен пользователем |

### Пример ответа (выполняется)

```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "history",
    "typeLabel": "Получение истории изменений",
    "status": "pending",
    "progress": 60,
    "stage": "Обработка данных",
    "message": "Начало получения данных из Яндекс.Директ"
}
```

### Пример ответа (завершен)

```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "history",
    "typeLabel": "Получение истории изменений",
    "status": "done",
    "progress": 100,
    "stage": "Завершение",
    "message": "Процесс успешно завершен за 9.15s",
    "resultsCount": 150,
    "hasResults": true,
    "dataType": "history"
}
```

---

## 3. Получение результатов процесса - getYandexDirectFetchResults

### Endpoint
```
GET /webHooks/getYandexDirectFetchResults
```

### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | query | Да | string | ID процесса |

### Пример запроса

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getYandexDirectFetchResults',
    params={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

### Примеры ответов

**История изменений:**
```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "history",
    "data": {
        "data": [
            {
                "cid": 12345,
                "datetime": "2026-01-15 10:30:00",
                "change": "Изменение ставки",
                "old_value": "10.00",
                "new_value": "15.00"
            }
        ]
    },
    "timestamp": 1737033600,
    "status": "done",
    "dataCount": 150
}
```

**Обучение стратегий:**
```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "strategy_study",
    "data": {
        "data": [
            {
                "campaign_id": 11111111,
                "status": null,
                "conv_per_week": null
            },
            {
                "campaign_id": 22222222,
                "status": "Обучение завершено",
                "conv_per_week": 15
            }
        ],
        "message": "Данные обучения стратегий получены"
    },
    "timestamp": 1737033600,
    "status": "done",
    "dataCount": 5
}
```

---

## 4. Остановка процесса - stopYandexDirectFetch

### Endpoint
```
POST /webHooks/stopYandexDirectFetch
```

### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | body | Да | string | ID процесса |

### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/stopYandexDirectFetch',
    json={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

### Пример успешного ответа

```json
{
    "status": "ok",
    "success": true,
    "message": "Процесс успешно остановлен",
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "history"
}
```

### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Не указан параметр processId` | Отсутствует обязательный параметр |
| `Процесс с ID ... не найден` | Процесс не существует или был удален |
| `Процесс еще не завершен или завершен с ошибкой` | Результаты недоступны |
| `Результаты процесса не найдены` | Данные не сохранены |
| `Процесс не выполняется` | Процесс уже завершен или остановлен |

---

## Полный цикл использования

```python
import requests
import time

account = 'myaccount'
client = 'myclient'

# 1. Запуск процесса
start_resp = requests.post(
    'https://your-domain.com/webHooks/startYandexDirectFetch',
    json={'account': account, 'client': client, 'type': 'strategy_study'}
)
process_id = start_resp.json()['processId']
print(f"Процесс запущен: {process_id}")

# 2. Ожидание завершения
while True:
    status_resp = requests.get(
        'https://your-domain.com/webHooks/getYandexDirectFetchStatus',
        params={'processId': process_id}
    )
    status = status_resp.json()['status']
    print(f"Статус: {status}")
    
    if status == 'done':
        break
    elif status == 'error':
        print("Ошибка!")
        break
    
    time.sleep(5)

# 3. Получение результатов
results_resp = requests.get(
    'https://your-domain.com/webHooks/getYandexDirectFetchResults',
    params={'processId': process_id}
)
print(results_resp.json())
```
