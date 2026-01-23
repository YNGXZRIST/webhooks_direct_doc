# Получение стратегий кампаний - getCampaigsStrategies

Получение настроек стратегий и бюджетов для указанных кампаний.

## Endpoint
```
GET /webHooks/getCampaigsStrategies
```

## Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |
| `campaign_ids` | query | Да | JSON array | JSON-массив ID кампаний |

## Пример запроса

```python
import requests
import json

response = requests.get(
    'https://your-domain.com/webHooks/getCampaigsStrategies',
    params={
        'account': 'myaccount',
        'client': 'myclient',
        'campaign_ids': json.dumps([12345678, 87654321])
    }
)
print(response.json())
```

## Пример успешного ответа

```json
{
    "status": "ok",
    "campaigns": [
        {
            "campaign_id": 111,
            "strategy": "PAY_FOR_CONVERSION",
            "strategy_name": "Максимум конверсий",
            "budget_limit": 5000,
            "budget_type": "weekly",
            "platform": ["Поиск"],
            "priority_goals": [
                {
                    "goal_name": "Form submission",
                    "value": 500,
                    "goal_id": 11111111
                }
            ],
            "schedule": [
                {"MON": "00:00-23:59"},
                {"WED": "02:00-03:59,05:00-06:59,14:00-14:59,20:00-20:59"}
            ]
        }
    ]
}
```

## Поля ответа

| Поле | Тип | Описание |
|------|-----|----------|
| `campaign_id` | integer | ID кампании |
| `strategy` | string | Код стратегии |
| `strategy_name` | string | Человекочитаемое название стратегии |
| `budget_limit` | number | Лимит бюджета |
| `budget_type` | string | Тип бюджета: `weekly` или `period` |
| `platform` | array | Площадки показа: `Поиск`, `РСЯ` |
| `priority_goals` | array | Массив приоритетных целей |
| `priority_goals[].goal_id` | integer | ID цели в Яндекс.Метрике |
| `priority_goals[].goal_name` | string | Название цели |
| `priority_goals[].value` | number | Ценность/стоимость цели |
| `period` | object | Период бюджета (только для `budget_type: "period"`) |
| `schedule` | array | Расписание показов по дням недели |

## Коды стратегий

| Код | Название |
|-----|----------|
| `PAY_FOR_CONVERSION` | Максимум конверсий (оплата за конверсии) |
| `AVERAGE_CPA_MULTIPLE_GOALS` | Максимум конверсий (средняя CPA) |
| `PAY_FOR_CLICK` | Максимум кликов |
| `AVERAGE_CPC` | Ручное управление ставками |
| `WEEKLY_CLICK_PACKAGE` | Недельный пакет кликов |

## Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |
