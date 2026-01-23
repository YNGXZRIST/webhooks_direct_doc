# Получение статистики групп объявлений - getGroupsStatistics

Получение детальной статистики групп объявлений с расчетом различных метрик.

## Endpoint
```
POST /webHooks/getGroupsStatistics
```

## Параметры

**Query параметры:**

| Параметр | Обязательный | Тип | Описание |
|----------|--------------|-----|----------|
| `account` | Да | string | Логин аккаунта |
| `client` | Да | string | Логин клиента |

**Body параметры (JSON):**

| Параметр | Обязательный | Тип | Описание |
|----------|--------------|-----|----------|
| `group_ids` | Да | array | Массив ID групп объявлений |
| `fields` | Да | array | Массив полей для получения |
| `from` | Да | string | Дата начала периода (YYYY-MM-DD) |
| `to` | Да | string | Дата окончания периода (YYYY-MM-DD) |
| `online_goals` | Нет | array | Массив ID онлайн целей (для CPA) |
| `offline_goals` | Нет | array | Массив ID оффлайн целей (для CPL) |
| `breakdowns` | Нет | array | Массив полей для группировки |
| `segments` | Нет | object | Объект с фильтрами для сегментации данных |

## Доступные поля (fields)

Те же поля, что и для [`getCampaignsStatistics`](getCampaignsStatistics.md):
- `expense`, `clicks`, `impressions`, `bounces`
- `ctr`, `avg_pageviews`, `cpa`, `cpl`, `cpc`
- `online_conversions`, `qualified_leads`

## Примеры запросов

**Базовый запрос:**
```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getGroupsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'group_ids': [111, 222],
        'fields': ['expense', 'clicks', 'impressions', 'ctr'],
        'from': '2024-01-01',
        'to': '2024-01-31'
    }
)
print(response.json())
```

**С целями для расчета CPA:**
```python
response = requests.post(
    'https://your-domain.com/webHooks/getGroupsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'group_ids': [111, 222],
        'fields': ['expense', 'clicks', 'cpa', 'online_conversions'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345, 67890]
    }
)
```

## Пример успешного ответа

```json
{
    "status": "ok",
    "statistics": [
        {
            "AdGroupId": 111,
            "data": {
                "expense": 1500.50,
                "clicks": 150,
                "impressions": 5000,
                "ctr": 3.00
            }
        },
        {
            "AdGroupId": 222,
            "data": {
                "expense": 850.25,
                "clicks": 95,
                "impressions": 3200,
                "ctr": 2.97
            }
        }
    ]
}
```

## Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметр group_ids обязателен` | Не указаны ID групп объявлений |
| `Параметр fields обязателен` | Не указаны поля для получения |
| `Параметры from и to обязательны` | Не указаны даты периода |
| `Для расчета CPA необходимо указать online_goals` | Запрошен CPA без онлайн целей |
| `Для расчета CPL необходимо указать offline_goals` | Запрошен CPL без оффлайн целей |

## Примечания

- Методы `getCampaignsStatistics` и `getGroupsStatistics` используют похожую логику
- Поддерживают `breakdowns` для группировки данных
- Поддерживают `segments` для фильтрации данных
- Полная документация по breakdowns и segments находится в [`getCampaignsStatistics`](getCampaignsStatistics.md)
