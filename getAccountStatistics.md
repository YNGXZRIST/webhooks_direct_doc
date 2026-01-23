# Получение статистики аккаунта - getAccountStatistics

Получение статистики по всему аккаунту с группировкой по дате, неделе, месяцу, кварталу или году.

## Endpoint
```
GET /webHooks/getAccountStatistics
```

## Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта Яндекс.Директ |
| `client` | query | Да | string | Логин клиента |
| `aggregator` | query | Да | enum | Период группировки: `Date`, `Week`, `Month`, `Quarter`, `Year` |
| `fields` | query | Да | array | Массив полей для получения (JSON) |
| `from` | query | Да | string | Дата начала периода (YYYY-MM-DD) |
| `to` | query | Да | string | Дата конца периода (YYYY-MM-DD) |
| `online_goals` | query | Нет | array | Массив ID онлайн целей для расчета CPA (JSON) |
| `offline_goals` | query | Нет | array | Массив ID оффлайн целей для расчета CPL (JSON) |

## Допустимые значения aggregator

| Значение | Описание |
|----------|----------|
| `Date` | Группировка по дате |
| `Week` | Группировка по неделе |
| `Month` | Группировка по месяцу |
| `Quarter` | Группировка по кварталу |
| `Year` | Группировка по году |

## Допустимые поля (fields)

Описание полей совпадает с методами получения статистики по кампаниям и группам объявлений.
Подробнее см. документацию методов:
- [Получение статистики кампаний](../doc.md#5-получение-статистики-кампаний)
- [Получение статистики групп объявлений](../doc.md#6-получение-статистики-групп-объявлений)

**Основные поля:**
- Стандартные: `impressions`, `clicks`, `expense`, `bounces`, `conversions`
- Целей: `online_conversions`, `qualified_leads`
- Вычисляемые: `cpa`, `cpa_all`, `cpl`, `cpc`, `ctr`

## Пример запроса

```bash
curl -X GET "http://your-domain.com/webHooks/getAccountStatistics?account=ACCOUNT&client=CLIENT&fields=%5B%22impressions%22,%22clicks%22,%22expense%22,%22online_conversions%22%5D&from=2025-12-31&to=2026-01-10&aggregator=Date&online_goals=%5B329441185,160111153,180150265%5D"
```

Или с URL кодированием параметров:
```
GET /webHooks/getAccountStatistics?account=ACCOUNT&client=CLIENT&fields=["impressions","clicks","expense","online_conversions"]&from=2025-12-31&to=2026-01-10&aggregator=Date&online_goals=[329441185,160111153,180150265]
```

## Пример успешного ответа

```json
{
    "status": "ok",
    "statistics": [
        {
            "Date": "2026-01-05",
            "data": {
                "impressions": 97770,
                "clicks": 322,
                "expense": 1587.46,
                "online_conversions": {
                    "total": 57,
                    "details": {
                        "329441185": 57
                    }
                },
                "cpa": 27.85
            }
        },
        {
            "Date": "2026-01-06",
            "data": {
                "impressions": 39022,
                "clicks": 115,
                "expense": 1585.85,
                "online_conversions": {
                    "total": 26,
                    "details": {
                        "329441185": 26
                    }
                },
                "cpa": 60.99
            }
        },
        {
            "Date": "2026-01-07",
            "data": {
                "impressions": 44912,
                "clicks": 120,
                "expense": 1586.4,
                "online_conversions": {
                    "total": 34,
                    "details": {
                        "329441185": 34
                    }
                },
                "cpa": 46.66
            }
        },
        {
            "Date": "2026-01-08",
            "data": {
                "impressions": 39139,
                "clicks": 95,
                "expense": 1585.55,
                "online_conversions": {
                    "total": 30,
                    "details": {
                        "329441185": 30
                    }
                },
                "cpa": 52.85
            }
        },
        {
            "Date": "2026-01-09",
            "data": {
                "impressions": 38719,
                "clicks": 73,
                "expense": 1586.29,
                "online_conversions": {
                    "total": 25,
                    "details": {
                        "329441185": 25
                    }
                },
                "cpa": 63.45
            }
        }
    ]
}
```

## Структура ответа

### Корневой уровень

| Поле | Тип | Описание |
|------|-----|----------|
| `status` | string | Статус ответа (`ok` или `error`) |
| `statistics` | array | Массив записей статистики |

### Структура элемента статистики

| Поле | Тип | Описание |
|------|-----|----------|
| `Date` / `Week` / `Month` / `Quarter` / `Year` | string | Период (название зависит от выбранного aggregator) |
| `data` | object | Объект с данными статистики |

### Структура объекта data

Содержит следующие поля в зависимости от запроса:

- **Числовые поля**: `impressions`, `clicks`, `expense`, `bounces`, `conversions`, `cpa`, `cpa_all`, `cpl`, `cpc`, `ctr`
- **Поля целей**: `online_conversions` и `qualified_leads` имеют структуру:
  ```json
  {
      "total": number,
      "details": {
          "goalId": number,
          ...
      }
  }
  ```

## Примеры использования

### Python
```python
import requests
import json

params = {
    'account': 'ACCOUNT',
    'client': 'CLIENT',
    'aggregator': 'Date',
    'fields': json.dumps(['impressions', 'clicks', 'expense', 'online_conversions']),
    'online_goals': json.dumps([329441185, 160111153, 180150265]),
    'from': '2025-12-31',
    'to': '2026-01-10'
}

response = requests.get(
    'http://your-domain.com/webHooks/getAccountStatistics',
    params=params
)

print(response.json())
```

### JavaScript
```javascript
const params = new URLSearchParams({
    account: 'ACCOUNT',
    client: 'CLIENT',
    aggregator: 'Date',
    fields: JSON.stringify(['impressions', 'clicks', 'expense', 'online_conversions']),
    online_goals: JSON.stringify([329441185, 160111153, 180150265]),
    from: '2025-12-31',
    to: '2026-01-10'
});

fetch(`http://your-domain.com/webHooks/getAccountStatistics?${params}`)
    .then(response => response.json())
    .then(data => console.log(data));
```

### cURL
```bash
curl -X GET \
  'http://your-domain.com/webHooks/getAccountStatistics' \
  -G \
  --data-urlencode 'account=ACCOUNT' \
  --data-urlencode 'client=CLIENT' \
  --data-urlencode 'aggregator=Date' \
  --data-urlencode 'fields=["impressions","clicks","expense","online_conversions"]' \
  --data-urlencode 'online_goals=[329441185,160111153,180150265]' \
  --data-urlencode 'from=2025-12-31' \
  --data-urlencode 'to=2026-01-10'
```

## Возможные ошибки

| Ошибка | Описание | Решение |
|--------|----------|---------|
| `Параметры account и client обязательны` | Не указаны параметры аутентификации | Добавьте параметры `account` и `client` |
| `Параметр aggregator обязателен` | Не указан тип группировки | Добавьте параметр `aggregator` с одним из допустимых значений |
| `Параметр aggregator должен быть одним из: Date, Week, Month, Quarter, Year` | Неверное значение aggregator | Используйте одно из допустимых значений |
| `Параметр fields обязателен` | Не указаны поля | Добавьте параметр `fields` с массивом нужных полей |
| `Параметры from и to обязательны` | Не указаны даты периода | Добавьте параметры `from` и `to` в формате YYYY-MM-DD |
| `Для расчета CPA необходимо указать online_goals` | Запрошен CPA без целей | Добавьте параметр `online_goals` с массивом ID целей |
| `Для расчета CPL необходимо указать offline_goals` | Запрошен CPL без целей | Добавьте параметр `offline_goals` с массивом ID целей |

## Форматы дат

- **from / to**: `YYYY-MM-DD` (например: `2026-01-10`)
- **Период в ответе**: зависит от значения `aggregator`
  - `Date`: `YYYY-MM-DD`
  - `Week`: `YYYY-Www` (например: `2026-W02`)
  - `Month`: `YYYY-MM`
  - `Quarter`: `YYYYQQ` (например: `2026Q1`)
  - `Year`: `YYYY`

## Ограничения

- Максимальный период запроса: 2 года
- Максимальное количество целей: 100
- Максимальное количество полей: неограниченно
- Данные обновляются с задержкой в несколько часов

## Примечания

1. Параметр `aggregator` определяет, по какому периоду будут группироваться данные
2. При выборе `aggregator=Date` каждая запись будет содержать данные за один день
3. Для расчета CPA или CPL необходимо указать соответствующие цели
4. Онлайн конверсии и лиды отображаются отдельно для каждой цели в поле `details`
5. Если в периоде нет данных за какой-то день/неделю/месяц, запись не будет включена в ответ
