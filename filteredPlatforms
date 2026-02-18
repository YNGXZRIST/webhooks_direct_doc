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
| `account` | string | Логин аккаунта Яндекс.Директ |
| `filters` | array | Массив фильтров для отбора площадок |
| `logic` | string | Логический оператор между фильтрами: `"and"` или `"or"` |
| `period_type` | string | Тип периода: `"TODAY"`, `"YESTERDAY"`, `"CUSTOM_DATE"` |
| `apiKey` | string | Ключ API для доступа к методу |
| `client` | string | Логин клиента (для инициализации Директа) |

### Опциональные параметры

| Параметр | Тип | Описание |
|----------|-----|----------|
| `goal_ids` | array | Массив ID целей для фильтрации (если не указан, используются все цели) |
| `from` | string | Дата начала периода (формат: `"dd.mm.yyyy"`), обязателен при `period_type = "CUSTOM_DATE"` |
| `to` | string | Дата окончания периода (формат: `"dd.mm.yyyy"`), обязателен при `period_type = "CUSTOM_DATE"` |

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

## Пример запроса

```json
{
    "account": "example_account",
    "filters": [
        {
            "metric": "click_threshold",
            "operator": ">",
            "value": "10"
        },
        {
            "metric": "bounce_rate",
            "operator": ">",
            "value": "70"
        },
        {
            "metric": "goal_cost_deviation_percent",
            "operator": ">=",
            "value": "30"
        }
    ],
    "logic": "and",
    "period_type": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026",
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

## Вызов из Python

Пример запроса к `webHooks/getFilteredPlatforms` с помощью библиотеки `requests`:

```python
import requests

BASE_URL = "https://your-host.com"  # замените на ваш хост
ENDPOINT = "/webHooks/getFilteredPlatforms"
API_KEY = "your_api_key"  # замените на ваш apiKey

url = f"{BASE_URL}{ENDPOINT}"

# Параметры запроса (GET или POST form / JSON — в зависимости от реализации сервера)
params = {
    "apiKey": API_KEY,
    "account": "example_account",
    "client": "example_client",
    "dateRangeType": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026",
}

# Тело запроса с фильтрами (если сервер принимает JSON body)
payload = {
    "filters": [
        {"metric": "click_threshold", "operator": ">", "value": "10"},
        {"metric": "bounce_rate", "operator": ">", "value": "70"},
        {"metric": "goal_cost_deviation_percent", "operator": ">=", "value": "30"},
    ],
    "logic": "and",
    "period_type": "CUSTOM_DATE",
    "from": "01.02.2026",
    "to": "17.02.2026",
    "account": "example_account",
    "goal_ids": [],
}

# Вариант 1: POST JSON (если сервер принимает application/json)
response = requests.post(
    url,
    params={"apiKey": API_KEY, "account": params["account"], "client": params["client"]},
    json=payload,
    timeout=120,
)

# Вариант 2: POST form-data (если сервер принимает только form)
# Собираем все поля в один словарь для form
form_data = {
    "apiKey": API_KEY,
    "account": payload["account"],
    "client": params["client"],
    "dateRangeType": payload["period_type"],
    "from": payload["from"],
    "to": payload["to"],
    "logic": payload["logic"],
}
# Фильтры обычно передают как JSON-строку в одном поле или по одному полю на фильтр
import json
form_data["filters"] = json.dumps(payload["filters"])

response = requests.post(url, data=form_data, timeout=120)

response.raise_for_status()
data = response.json()

if data.get("success"):
    platforms = data.get("platfomrs", {})  # в ответе поле "platfomrs"
    filters_applied = data.get("filters", {})
    total = sum(
        len(campaigns) if isinstance(campaigns, dict) else 0
        for campaigns in platforms.values()
    )
    print(f"Найдено площадок: {total}")
    for platform_name, campaigns in platforms.items():
        if not isinstance(campaigns, dict):
            continue
        for campaign_id, stats in campaigns.items():
            print(f"  {platform_name} / campaign {campaign_id}: Clicks={stats.get('Clicks')}, Expense={stats.get('Expense')}")
else:
    print("Ошибка:", data.get("error", data))
```

### Минимальный пример (только обязательные параметры)

```python
import requests

url = "https://your-host.com/webHooks/getFilteredPlatforms"
resp = requests.post(
    url,
    data={
        "apiKey": "your_api_key",
        "account": "your_direct_login",
        "client": "your_client_login",
        "dateRangeType": "CUSTOM_DATE",
        "from": "01.02.2026",
        "to": "17.02.2026",
    },
    timeout=120,
)
result = resp.json()
print(result)
```

Установка зависимости: `pip install requests`
