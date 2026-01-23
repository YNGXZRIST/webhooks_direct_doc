# Получение статистики кампаний - getCampaignsStatistics

Получение детальной статистики рекламных кампаний с расчетом различных метрик.

## Endpoint
```
POST /webHooks/getCampaignsStatistics
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
| `campaign_ids` | Да | array | Массив ID кампаний |
| `fields` | Да | array | Массив полей для получения |
| `from` | Да | string | Дата начала периода (YYYY-MM-DD) |
| `to` | Да | string | Дата окончания периода (YYYY-MM-DD) |
| `online_goals` | Нет | array | Массив ID онлайн целей (для CPA) |
| `offline_goals` | Нет | array | Массив ID оффлайн целей (для CPL) |
| `breakdowns` | Нет | array | Массив полей для группировки |
| `segments` | Нет | object | Объект с фильтрами для сегментации данных |

## Доступные поля (fields)

| Поле | Описание |
|------|----------|
| `expense` | Расход в валюте аккаунта |
| `clicks` | Количество кликов |
| `impressions` | Количество показов |
| `bounces` | Количество отказов |
| `ctr` | CTR - кликабельность (%) |
| `avg_pageviews` | Глубина просмотра (стр) |
| `cpa` | CPA - стоимость онлайн конверсии |
| `cpl` | CPL - стоимость оффлайн лида |
| `cpc` | CPC - стоимость конверсии |
| `online_conversions` | Детализация онлайн конверсий |
| `qualified_leads` | Детализация оффлайн лидов |

## Примеры запросов

**Базовый запрос:**
```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [111, 222],
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
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [111, 222],
        'fields': ['expense', 'clicks', 'impressions', 'cpa', 'online_conversions'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345, 67890]
    }
)
```

**С группировкой по устройствам:**
```python
response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [76757740],
        'fields': ['expense', 'clicks', 'impressions'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'breakdowns': ['device']
    }
)
```

**С фильтрацией по полу и возрасту:**
```python
response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [76757745],
        'fields': ['expense', 'clicks', 'impressions'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'segments': {
            'gender': ['GENDER_MALE'],
            'age': ['AGE_18_24', 'AGE_25_34']
        }
    }
)
```

## Пример успешного ответа

```json
{
    "status": "ok",
    "statistics": [
        {
            "CampaignId": 111,
            "data": {
                "expense": 1500.50,
                "clicks": 150,
                "impressions": 5000,
                "ctr": 3.00
            }
        },
        {
            "CampaignId": 222,
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
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |
| `Параметр fields обязателен` | Не указаны поля для получения |
| `Параметры from и to обязательны` | Не указаны даты периода |
| `Для расчета CPA необходимо указать online_goals` | Запрошен CPA без онлайн целей |
| `Для расчета CPL необходимо указать offline_goals` | Запрошен CPL без оффлайн целей |

**Полная документация с примерами breakdowns и segments находится в файле:** [`doc.md`](../doc.md#5-получение-статистики-кампаний)
