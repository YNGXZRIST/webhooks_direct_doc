# API: actionGetFilteredPlatforms

## Описание

Метод возвращает список площадок РСЯ (Рекламной Сети Яндекса), отфильтрованных по заданным критериям. Поддерживает фильтрацию по абсолютным значениям метрик и по относительным показателям (отклонение от среднего по аккаунту, доля от общего значения).

## Endpoint

```
POST /webHooks/getFilteredPlatforms
```

Базовый URL: ваш хост (например, `https://example.com`). Итоговый URL: `https://example.com/webHooks/getFilteredPlatforms`

**Авторизация:** в запросе передаётся параметр `apiKey` (обязателен для доступа).

## Параметры запроса

### Обязательные параметры

| Параметр | Тип | Описание |
|----------|-----|----------|
| `apiKey` | string | Ключ API для доступа к методу |
| `account` | string | Логин аккаунта Яндекс.Директ |
| `client` | string | Логин клиента (для инициализации Директа) |
| `dateRangeType` | string | Тип периода: `"TODAY"`, `"YESTERDAY"`, `"CUSTOM_DATE"` (по умолчанию `"CUSTOM_DATE"`) |
| `from` | string | Дата начала периода (формат `dd.mm.yyyy`), обязателен при `dateRangeType = "CUSTOM_DATE"` |
| `to` | string | Дата окончания периода (формат `dd.mm.yyyy`), обязателен при `dateRangeType = "CUSTOM_DATE"` |

### Опциональные параметры

| Параметр | Тип | Описание |
|----------|-----|----------|
| `filters` | array или string | Массив фильтров или JSON-строка. **Если не передан** — используются фильтры по умолчанию: кликов > 10, отказы > 70%, CPA выше среднего на 30% и более |
| `logic` | string | Логический оператор между фильтрами: `"and"` или `"or"` (по умолчанию `"and"`) |
| `goal_ids` | array или string | Массив ID целей или JSON-строка; если не указан — используются все цели |

### Структура фильтров

Каждый фильтр в массиве `filters` имеет следующую структуру:

```json
{
    "metric": "название_метрики",
    "operator": "оператор_сравнения",
    "value": "значение"
}
```

#### Доступные метрики

**Базовые метрики (абсолютные значения):**
- `click_threshold` — количество кликов
- `ctr_threshold` — CTR (%)
- `avg_cpc` — средняя цена клика (руб)
- `conversions` — количество конверсий
- `goal_cost` — CPA / цена цели (руб)
- `expense` — расход (руб)
- `roi` — ROI (%)
- `bounce_rate` — процент отказов (%)
- `conversions_rate` — процент конверсий (%)
- `avg_pageviews` — глубина просмотра (среднее количество страниц за сеанс)
- `impressions` — количество показов
- `text_search` — текстовый поиск по названию/URL площадки

**Относительные метрики (отклонение от среднего по аккаунту):**
- `{metric}_deviation_percent` — отклонение метрики от среднего по аккаунту (%)
  - Примеры: `goal_cost_deviation_percent`, `expense_deviation_percent`, `click_threshold_deviation_percent`
  - Положительное значение = метрика выше среднего, отрицательное = ниже среднего

**Относительные метрики (доля от общего значения по аккаунту):**
- `{metric}_share_percent` — доля метрики от общего значения по аккаунту (%)
  - Примеры: `expense_share_percent`, `click_threshold_share_percent`
  - Показывает, какой процент от общего расхода/кликов и т.д. приходится на данную площадку

#### Операторы сравнения

- `">"` — больше
- `">="` — больше или равно
- `"<"` — меньше
- `"<="` — меньше или равно
- `"=="` — равно
- `"contains"` — содержит (только для `text_search`)

#### Фильтры по умолчанию

Если параметр `filters` не передан или передан пустой/некорректный, применяется набор по умолчанию:

- кликов более 10 (`click_threshold` > 10);
- отказы более 70% (`bounce_rate` > 70);
- CPA выше среднего по аккаунту на 30% и более (`goal_cost_deviation_percent` >= 30);
- логика между условиями: `and`.

Передача `filters` в запросе:
- как **массив** в JSON-теле запроса (при `Content-Type: application/json`);
- как **JSON-строка** в поле `filters` при отправке form-data.

## Примеры запроса

### С кастомными фильтрами (POST form или query)

Параметры можно передать в теле POST (form-data) или в query string:

- `apiKey`, `account`, `client` — обязательно
- `dateRangeType` (по умолчанию `CUSTOM_DATE`), `from`, `to` — для периода
- `filters` — JSON-строка массива фильтров, например:  
  `[{"metric":"click_threshold","operator":">","value":"10"},{"metric":"bounce_rate","operator":">","value":"70"},{"metric":"goal_cost_deviation_percent","operator":">=","value":"30"}]`
- `logic` — по умолчанию `and`
- `goal_ids` — опционально, JSON-строка массива ID целей

### Минимальный запрос (используются фильтры по умолчанию)

```json
{
    "apiKey": "your_api_key",
    "account": "example_account",
    "client": "example_client",
    "dateRangeType": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026"
}
```

### Запрос с явными фильтрами

```json
{
    "apiKey": "your_api_key",
    "account": "example_account",
    "client": "example_client",
    "dateRangeType": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026",
    "filters": [
        {"metric": "click_threshold", "operator": ">", "value": "10"},
        {"metric": "bounce_rate", "operator": ">", "value": "70"},
        {"metric": "goal_cost_deviation_percent", "operator": ">=", "value": "30"}
    ],
    "logic": "and",
    "goal_ids": []
}
```

## Структура ответа

### Успешный ответ

```json
{
    "status": "ok",
    "success": true,
    "platfomrs": {
        "platform_name_1": {
            "campaign_id_1": {
                "Placement": "platform_name_1",
                "Revenue": 0,
                "CostPerConversion": 348.83,
                "BounceRate": 76.01,
                "CampaignId": "campaign_id_1",
                "CampaignName": "Campaign Name",
                "Clicks": 3101,
                "Ctr": 2.74,
                "AvgCpc": 2.02,
                "Conversions": 18,
                "GoalsRoi": 0,
                "ConversionRate": 0.58,
                "AvgPageviews": 1.04,
                "Impressions": 113114,
                "Cpa": 348.83,
                "Expense": 6278.91,
                "goal_cost_deviation_percent": 120.94,
                "goal_cost_share_percent": 0.14
            }
        },
        "platform_name_2": {
            "campaign_id_2": {
                "Placement": "platform_name_2",
                "Revenue": 0,
                "CostPerConversion": 180.26,
                "BounceRate": 77.78,
                "CampaignId": "campaign_id_2",
                "CampaignName": "Campaign Name 2",
                "Clicks": 68,
                "Ctr": 18.68,
                "AvgCpc": 2.65,
                "Conversions": 1,
                "GoalsRoi": 0,
                "ConversionRate": 1.47,
                "AvgPageviews": 1.44,
                "Impressions": 364,
                "Cpa": 180.26,
                "Expense": 180.26,
                "goal_cost_deviation_percent": 14.17,
                "goal_cost_share_percent": 0.07
            }
        }
    },
    "filters": {
        "click_threshold": {
            "operator": ">",
            "value": 10
        },
        "bounce_rate": {
            "operator": ">",
            "value": 70
        },
        "goal_cost_deviation_percent": {
            "operator": ">=",
            "value": 10
        },
        "filter_logic": "and"
    }
}
```

### Описание полей ответа

#### Корневые поля

| Поле | Тип | Описание |
|------|-----|----------|
| `status` | string | Статус ответа: `"ok"` при успехе |
| `success` | boolean | Флаг успешного выполнения запроса |
| `platfomrs` | object | Объект с отфильтрованными площадками (ключ = название площадки) |
| `filters` | object | Применённые фильтры (эхо запроса) |

#### Структура данных площадки

| Поле | Тип | Описание |
|------|-----|----------|
| `Placement` | string | Название площадки (URL или домен) |
| `CampaignId` | string | ID кампании |
| `CampaignName` | string | Название кампании |
| `Clicks` | integer | Количество кликов |
| `Impressions` | integer | Количество показов |
| `Ctr` | float | CTR (%) |
| `AvgCpc` | float | Средняя цена клика (руб) |
| `Conversions` | integer | Количество конверсий |
| `ConversionRate` | float | Процент конверсий (%) |
| `BounceRate` | float | Процент отказов (%) |
| `AvgPageviews` | float | Глубина просмотра (среднее количество страниц за сеанс) |
| `Revenue` | integer | Доход |
| `CostPerConversion` | float | Цена конверсии (руб) |
| `GoalsRoi` | float | ROI по целям (%) |
| `Cpa` | float | CPA / цена цели (руб) |
| `Expense` | float | Расход (руб) |
| `{metric}_deviation_percent` | float | Отклонение метрики от среднего по аккаунту (%) — присутствует только если в фильтрах указана соответствующая метрика |
| `{metric}_share_percent` | float | Доля метрики от общего значения по аккаунту (%) — присутствует только если в фильтрах указана соответствующая метрика |

### Ошибки

#### Пример ошибки

```json
{
    "status": "error",
    "success": false,
    "message": "Описание ошибки"
}
```

## Особенности работы

### Расчёт относительных метрик

1. **Отклонение от среднего (`*_deviation_percent`):**
   - Считается среднее значение метрики по всем площадкам аккаунта за указанный период
   - Для каждой площадки вычисляется процент отклонения: `((значение_площадки - среднее) / среднее) × 100`
   - Для CPA среднее считается как `общий_расход / общие_конверсии`

2. **Доля от общего (`*_share_percent`):**
   - Считается суммарное значение метрики по всем площадкам аккаунта
   - Для каждой площадки вычисляется доля: `(значение_площадки / суммарное_значение) × 100`

### Округление

Все процентные значения (`*_deviation_percent`, `*_share_percent`) округляются до 2 знаков после запятой.

### Логика фильтрации

- При `logic = "and"` — площадка должна соответствовать **всем** указанным фильтрам
- При `logic = "or"` — площадка должна соответствовать **хотя бы одному** фильтру

## Примеры использования

### Пример 1: Площадки с высоким CPA относительно среднего

```json
{
    "account": "example_account",
    "filters": [
        {
            "metric": "goal_cost_deviation_percent",
            "operator": ">=",
            "value": "30"
        }
    ],
    "logic": "and",
    "period_type": "LAST_7_DAYS"
}
```

### Пример 2: Площадки с большим расходом и низкой конверсией

```json
{
    "account": "example_account",
    "filters": [
        {
            "metric": "expense",
            "operator": ">",
            "value": "1000"
        },
        {
            "metric": "conversions_rate",
            "operator": "<",
            "value": "1"
        }
    ],
    "logic": "and",
    "period_type": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026"
}
```

### Пример 3: Площадки, съедающие более 5% бюджета

```json
{
    "account": "example_account",
    "filters": [
        {
            "metric": "expense_share_percent",
            "operator": ">",
            "value": "5"
        }
    ],
    "logic": "and",
    "period_type": "YESTERDAY"
}
```

---

## Пример на Python

Установка: `pip install requests`

Полный пример: вызов с фильтрами по умолчанию (параметр `filters` не передаём) и с кастомными фильтрами.

```python
import json
import requests

BASE_URL = "https://your-host.com"
ENDPOINT = "/webHooks/getFilteredPlatforms"
API_KEY = "your_api_key"
ACCOUNT = "your_direct_account"
CLIENT = "your_client_login"

# Фильтры по умолчанию на сервере: кликов > 10, отказы > 70%, CPA выше среднего на 30%
DEFAULT_FILTERS = [
    {"metric": "click_threshold", "operator": ">", "value": "10"},
    {"metric": "bounce_rate", "operator": ">", "value": "70"},
    {"metric": "goal_cost_deviation_percent", "operator": ">=", "value": "30"},
]


def get_filtered_platforms(
    account: str,
    client: str,
    api_key: str,
    period_type: str = "CUSTOM_DATE",
    date_from: str | None = None,
    date_to: str | None = None,
    filters: list | None = None,
    logic: str = "and",
    goal_ids: list | None = None,
) -> dict:
    """Запрос отфильтрованных площадок РСЯ. Без filters используются фильтры по умолчанию."""
    url = f"{BASE_URL.rstrip('/')}{ENDPOINT}"
    data = {
        "apiKey": api_key,
        "account": account,
        "client": client,
        "dateRangeType": period_type,
        "logic": logic,
    }
    if period_type == "CUSTOM_DATE":
        data["from"] = date_from or ""
        data["to"] = date_to or ""
    if filters is not None:
        data["filters"] = json.dumps(filters)
    if goal_ids is not None:
        data["goal_ids"] = json.dumps(goal_ids)

    r = requests.post(url, data=data, timeout=120)
    r.raise_for_status()
    return r.json()


if __name__ == "__main__":
    # Вариант 1: без filters — на сервере применяются фильтры по умолчанию
    result = get_filtered_platforms(
        account=ACCOUNT,
        client=CLIENT,
        api_key=API_KEY,
        period_type="CUSTOM_DATE",
        date_from="01.02.2026",
        date_to="17.02.2026",
    )

    # Вариант 2: с явными фильтрами
    # result = get_filtered_platforms(..., filters=DEFAULT_FILTERS)

    if result.get("success"):
        platforms = result.get("platfomrs", {})
        total = sum(len(c) if isinstance(c, dict) else 0 for c in platforms.values())
        print(f"Найдено площадок: {total}")
        for platform_name, campaigns in platforms.items():
            if not isinstance(campaigns, dict):
                continue
            for campaign_id, stats in campaigns.items():
                print(f"  {platform_name} / {campaign_id}: Clicks={stats.get('Clicks')}, Expense={stats.get('Expense')}")
    else:
        print("Ошибка:", result.get("error", result))
```
