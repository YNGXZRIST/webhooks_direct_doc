# API Яндекс.Директ - Документация

## Оглавление

1. [Общая информация](#общая-информация)
2. [Аутентификация](#аутентификация)
3. [Методы API](#методы-api)
    - [Валидация аккаунта](#1-валидация-аккаунта)
    - [Получение списка кампаний](#2-получение-списка-кампаний)
    - [Получение списка групп объявлений](#3-получение-списка-групп-объявлений)
    - [Получение стратегий кампаний](#4-получение-стратегий-кампаний)
    - [Получение статистики кампаний](#5-получение-статистики-кампаний)
    - [Получение статистики групп объявлений](#6-получение-статистики-групп-объявлений)
    - [Асинхронное получение данных](#7-асинхронное-получение-данных)
    - [Получение статуса асинхронного процесса](#8-получение-статуса-асинхронного-процесса)
    - [Получение результатов асинхронного процесса](#9-получение-результатов-асинхронного-процесса)
    - [Остановка асинхронного процесса](#10-остановка-асинхронного-процесса)
4. [Коды ошибок](#коды-ошибок)
5. [Примеры использования](#примеры-использования)
    - [Полный пример работы с асинхронными процессами](#полный-пример-работы-с-асинхронными-процессами)

---

## Общая информация

Базовый URL API:
```
https://your-domain.com/webHooks
```

Все методы возвращают JSON в едином формате:

**Успешный ответ:**
```json
{
    "status": "ok",
    ...данные...
}
```

**Ошибка:**
```json
{
    "status": "error",
    "error": "Описание ошибки"
}
```

---

## Аутентификация

Все методы требуют обязательные query-параметры:

| Параметр | Тип | Описание |
|----------|-----|----------|
| `account` | string | Логин аккаунта Яндекс.Директ |
| `client` | string | Логин клиента |

Пример:
```
?account=example_account&client=example_client
```

---

## Методы API

### 1. Валидация аккаунта

Проверка валидности аккаунта и клиента Яндекс.Директ.

#### Endpoint
```
POST /webHooks/validateYandexDirectAccount
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |

#### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/validateYandexDirectAccount',
    params={'account': 'myaccount', 'client': 'myclient'}
)
print(response.json())
```

#### Пример успешного ответа

```json
{
    "status": "ok",
    "valid": true
}
```

#### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Не указан аккаунт или клиент` | Отсутствуют обязательные параметры |
| `Ошибка авторизации в Яндекс.Директ` | Неверные учетные данные |
| `У вас нет доступа к данному аккаунту` | Нет прав доступа |

---

### 2. Получение списка кампаний

Получение списка рекламных кампаний для указанного аккаунта.

#### Endpoint
```
GET /webHooks/getCampaignsList
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |

#### Пример запроса

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getCampaignsList',
    params={'account': 'myaccount', 'client': 'myclient'}
)
print(response.json())
```

#### Пример успешного ответа

```json
{
    "status": "ok",
    "campaigns": [
        {
            "Id": 111,
            "Name": "Кошелек",
            "Status": "MODERATION",
            "State": "ARCHIVED"
        },
        {
            "Id": 222,
            "Name": "Другая кампания",
            "Status": "ACCEPTED",
            "State": "ON"
        }
    ]
}
```

#### Поля кампании

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer | Уникальный идентификатор кампании |
| `Name` | string | Название рекламной кампании |
| `Status` | string | Статус кампании (MODERATION, ACCEPTED и т.д.) |
| `State` | string | Состояние кампании (ARCHIVED, ON, OFF, SUSPENDED) |

---

### 3. Получение списка групп объявлений

Получение списка групп объявлений для указанных кампаний.

#### Endpoint
```
GET /webHooks/getGroupsList
```

#### Параметры

**Query параметры:**

| Параметр | Обязательный | Тип | Описание |
|----------|--------------|-----|----------|
| `account` | Да | string | Логин аккаунта |
| `client` | Да | string | Логин клиента |
| `campaign_ids` | Да | JSON array | JSON-массив ID кампаний |

#### Пример запроса

```python
import requests
import json

response = requests.get(
    'https://your-domain.com/webHooks/getGroupsList',
    params={
        'account': 'myaccount',
        'client': 'myclient',
        'campaign_ids': json.dumps([111, 222])
    }
)
print(response.json())
```

#### Пример успешного ответа

```json
{
    "status": "ok",
    "groups": [
        {
            "Id": 111,
            "Name": "Группа 1",
            "CampaignId": 111,
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        },
        {
            "Id": 222,
            "Name": "Группа 2",
            "CampaignId": 111,
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        }
    ]
}
```

#### Поля группы

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer | Уникальный идентификатор группы объявлений |
| `Name` | string | Название группы объявлений |
| `CampaignId` | integer | ID родительской кампании |
| `Status` | string | Статус группы |
| `ServingStatus` | string | Статус показа группы |

#### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |

---

### 4. Получение стратегий кампаний

Получение настроек стратегий и бюджетов для указанных кампаний.

#### Endpoint
```
GET /webHooks/getCampaigsStrategies
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |
| `campaign_ids` | query | Да | JSON array | JSON-массив ID кампаний |

#### Пример запроса

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

#### Пример успешного ответа

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
                    "goal_name": "Отправка формы",
                    "value": 500,
                    "goal_id": 11111111
                }
            ],
            "schedule": [
                {"MON": "00:00-23:59"},
                {"WED": "02:00-03:59,05:00-06:59,14:00-14:59,20:00-20:59"},
                {"FRI": "09:00-13:59,20:00-21:59"},
                {"SAT": "04:00-04:59"},
                {"SUN": "00:00-21:59"}
            ]
        },
        {
            "campaign_id": 222,
            "strategy": "AVERAGE_CPA_MULTIPLE_GOALS",
            "strategy_name": "Максимум конверсий",
            "budget_limit": 3000,
            "budget_type": "period",
            "platform": ["РСЯ"],
            "priority_goals": [
                {
                    "goal_name": "Звонок",
                    "value": 1000,
                    "goal_id": 22222222
                },
                {
                    "goal_name": "Заявка",
                    "value": 800,
                    "goal_id": 33333333
                }
            ],
            "period": {
                "start": "2024.01.01",
                "end": "2024.01.31"
            },
            "schedule": [
                {"MON": "00:00-23:59"},
                {"TUE": "00:00-23:59"},
                {"WED": "00:00-23:59"},
                {"THU": "00:00-23:59"},
                {"FRI": "00:00-23:59"},
                {"SAT": "00:00-23:59"},
                {"SUN": "00:00-23:59"}
            ]
        }
    ]
}
```

#### Поля ответа

| Поле | Тип | Описание |
|------|-----|----------|
| `campaign_id` | integer | ID кампании |
| `strategy` | string | Код стратегии (см. таблицу стратегий) |
| `strategy_name` | string | Человекочитаемое название стратегии |
| `budget_limit` | number | Лимит бюджета |
| `budget_type` | string | Тип бюджета: `weekly` (недельный) или `period` (на период) |
| `platform` | array | Площадки показа: `Поиск`, `РСЯ` |
| `priority_goals` | array | Массив приоритетных целей |
| `priority_goals[].goal_id` | integer | ID цели в Яндекс.Метрике |
| `priority_goals[].goal_name` | string | Название цели |
| `priority_goals[].value` | number | Ценность/стоимость цели |
| `period` | object | Период бюджета (только для `budget_type: "period"`) |
| `period.start` | string | Дата начала периода (YYYY.MM.DD) |
| `period.end` | string | Дата окончания периода (YYYY.MM.DD) |
| `schedule` | array | Расписание показов по дням недели |
| `schedule[].{DAY}` | string | Диапазоны часов показа для дня недели. Ключ — день недели (`MON`, `TUE`, `WED`, `THU`, `FRI`, `SAT`, `SUN`), значение — диапазоны времени в формате `HH:00-HH:59`, разделенные запятой |

#### Коды стратегий

| Код | Название |
|-----|----------|
| `PAY_FOR_CONVERSION` | Максимум конверсий (оплата за конверсии) |
| `AVERAGE_CPA_MULTIPLE_GOALS` | Максимум конверсий (средняя CPA) |
| `PAY_FOR_CLICK` | Максимум кликов |
| `AVERAGE_CPC` | Ручное управление ставками |
| `WEEKLY_CLICK_PACKAGE` | Недельный пакет кликов |

#### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |

---

### 5. Получение статистики кампаний

Получение детальной статистики рекламных кампаний с расчетом различных метрик.

#### Endpoint
```
POST /webHooks/getCampaignsStatistics
```

#### Параметры

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
| `breakdowns` | Нет | array | Массив полей для группировки (например: ["device", "gender"]) |
| `segments` | Нет | object | Объект с фильтрами для сегментации данных (например: {"gender": ["GENDER_MALE"], "age": ["AGE_25_34"]}) |

#### Доступные поля (fields)

| Поле | Описание | Требует целей |
|------|----------|---------------|
| `expense` | Расход в валюте аккаунта | - |
| `clicks` | Количество кликов | - |
| `impressions` | Количество показов | - |
| `bounces` | Количество отказов | - |
| `ctr` | CTR - кликабельность (%) | - |
| `avg_pageviews` | Глубина просмотра (стр) | - |
| `cpa` | CPA - стоимость онлайн конверсии | `online_goals` |
| `cpl` | CPL - стоимость оффлайн лида | `offline_goals` |
| `cpc` | CPC - стоимость конверсии (все цели) | `online_goals` или `offline_goals` |
| `online_conversions` | Детализация онлайн конверсий | `online_goals` |
| `qualified_leads` | Детализация оффлайн лидов | `offline_goals` |

#### Доступные поля для breakdowns

Параметр `breakdowns` позволяет группировать статистику по различным измерениям.

| Поле                        | Описание | Возможные значения |
|-----------------------------|----------|-------------------|
| `ad_format`                 | Формат показа объявления | `IMAGE` (графический)<br>`TEXT` (текстовый)<br>`VIDEO` (видео)<br>`SMART_MULTIPLE` (смарт-баннер)<br>`SMART_SINGLE` (смарт-объявление)<br>`ADAPTIVE_IMAGE` (адаптивный графический)<br>`SMART_TILE` (смарт-плитка) |
| `ad_network_type`           | Тип площадки, где показано объявление | `SEARCH` (на поиске)<br>`AD_NETWORK` (в сетях) |
| `age`                       | Возрастная группа пользователя | `AGE_0_17` (0-17 лет)<br>`AGE_18_24` (18-24 года)<br>`AGE_25_34` (25-34 года)<br>`AGE_35_44` (35-44 года)<br>`AGE_45` (45+ лет)<br>`AGE_45_54` (45-54 года)<br>`AGE_55` (55+ лет)<br>`UNKNOWN` (неизвестно) |
| `criteria_type`             | Тип условия показа (устаревшее, используйте `criterion_type`) | `KEYWORD` (ключевая фраза)<br>`AUTOTARGETING` (автотаргетинг)<br>`AUDIENCE_TARGET` (нацеливание на аудиторию)<br>`DYNAMIC_TEXT_AD_TARGET` (нацеливание для динамических объявлений)<br>`SMART_BANNER_FILTER` (фильтр для смарт-баннеров) |
| `criterion`                 | Название или текст условия показа | Для ключевых фраз: фраза с идентификатором<br>Для релевантных фраз: текст фразы или "Автоматически добавленная фраза"<br>Для автотаргетинга: "---autotargeting"<br>Для нацеливания на аудиторию: название условия<br>Для динамических объявлений: название условия или фильтра<br>Для смарт-баннеров: название фильтра<br>*(Группировка по CriterionId)* |
| `criterion_type`            | Тип условия показа (рекомендуется вместо `criteria_type`) | `KEYWORD` (ключевая фраза)<br>`AUTOTARGETING` (автотаргетинг)<br>`RETARGETING` (ретаргетинг и подбор аудитории)<br>`INTERESTS_AND_DEMOGRAPHICS` (профиль пользователей)<br>`MOBILE_APP_CATEGORY` (интерес к категории приложений)<br>`WEBPAGE_FILTER` (нацеливание на основе страниц сайта)<br>`FEED_FILTER` (фильтр на основе фида)<br>`OFFER_RETARGETING` (офферный ретаргетинг) |
| `device`                    | Тип устройства, на котором показано объявление | `DESKTOP` (десктоп)<br>`MOBILE` (мобильный)<br>`TABLET` (планшет)<br>`SMART_TV` (умное ТВ) |
| `gender`                    | Пол пользователя | `GENDER_MALE` (мужской)<br>`GENDER_FEMALE` (женский)<br>`UNKNOWN` (неизвестно) |
| `matched_keyword`           | Подобранная фраза | Для показа по синониму: текст синонима<br>Для релевантной фразы: текст релевантной фразы<br>Для динамического объявления: автоматически сформированная фраза<br>В остальных случаях: пустое значение |
| `match_type`                | Тип соответствия ключевой фразе | `RELATED_KEYWORD` (по дополнительной релевантной фразе)<br>`SYNONYM` (по семантическому соответствию)<br>`KEYWORD` (по ключевой фразе)<br>`NONE` (в остальных случаях) |
| `mobile_platform`           | Тип операционной системы | `ANDROID` (Android)<br>`IOS` (iOS)<br>`OTHER` (другие)<br>`UNKNOWN` (неизвестно) |
| `placement`                 | Название площадки показов | Название конкретной площадки (строковое значение) |
| `slot`                      | Блок показа объявления | `ALONE` (эксклюзивное размещение)<br>`PREMIUMBLOCK` (спецразмещение)<br>`SUGGEST` (реклама в саджесте)<br>`PRODUCT_GALLERY` (товарная галерея)<br>`OTHER` (другие блоки) |
| `targeting_category`        | Категория таргетинга | `EXACT` (целевые запросы - объявление точно отвечает на запросы)<br>`ALTERNATIVE` (альтернативные запросы - замена продвигаемого продукта)<br>`COMPETITOR` (запросы с упоминанием конкурентов)<br>`BROADER` (широкие запросы с интересом к продукту)<br>`ACCESSORY` (сопутствующие запросы по смежным продуктам) |
| `targeting_location_name`   | Название региона таргетинга | Название региона, который был указан в настройках таргетинга кампании (строковое значение) |
| `location_of_presence_name` | Название региона местонахождения пользователя | Фактическое местонахождение пользователя на момент показа объявления (строковое значение) |

**Примечание:** Вы можете комбинировать несколько полей для более детальной группировки, например `["device", "gender", "age"]` или `["slot", "targeting_category", "match_type"]`, или `["targeting_location_name", "location_of_presence_name"]`.

#### Параметр segments (фильтрация данных)

Параметр `segments` позволяет фильтровать статистику по определенным условиям. В отличие от `breakdowns` (который группирует данные), `segments` отбирает только те данные, которые соответствуют указанным критериям.

**Формат:** объект, где ключи — поля для фильтрации, значения — массивы допустимых значений.

**Доступные поля для segments:**

| Поле | Описание | Возможные значения |
|------|----------|-------------------|
| `gender` | Фильтр по полу пользователя | `GENDER_MALE`, `GENDER_FEMALE`, `UNKNOWN` |
| `age` | Фильтр по возрастной группе | `AGE_18_24`, `AGE_25_34`, `AGE_35_44`, `AGE_45_54`, `AGE_55`, `UNKNOWN` |

**Примеры использования:**

Получить статистику только по мужчинам 25-34 года:
```json
{
    "segments": {
        "gender": ["GENDER_MALE"],
        "age": ["AGE_25_34"]
    }
}
```

Получить статистику по нескольким возрастным группам:
```json
{
    "segments": {
        "age": ["AGE_18_24", "AGE_25_34", "AGE_35_44"]
    }
}
```

Комбинировать несколько полов и возрастов:
```json
{
    "segments": {
        "gender": ["GENDER_MALE", "GENDER_FEMALE"],
        "age": ["AGE_25_34", "AGE_35_44"]
    }
}
```

**Важно:** При использовании `segments` с `breakdowns`, фильтрация применяется первой, затем происходит группировка отфильтрованных данных.

#### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [111, 222],
        'fields': ['expense','avg_pageviews', 'clicks', 'impressions', 'ctr', 'cpa'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345, 67890]
    }
)
print(response.json())
```

#### Пример запроса с breakdowns

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [76757740],
        'fields': ['expense', 'clicks', 'impressions', 'cpa', 'cpc', 'online_conversions', 'avg_pageviews'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345],
        'breakdowns': ['device', 'gender']
    }
)
print(response.json())
```

#### Пример запроса с segments (фильтрация)

```python
import requests

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
print(response.json())
```

#### Пример запроса с segments и breakdowns

Сначала фильтруем данные по мужчинам 18-34 лет, затем группируем по возрасту:

```python
import requests

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
        },
        'breakdowns': ['age']
    }
)
print(response.json())
```

#### Примеры использования различных breakdowns

**Группировка по возрасту и полу:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['age', 'gender']
}
```

**Группировка по типу площадки и устройству:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions', 'ctr'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['ad_network_type', 'device']
}
```

**Группировка по типу условия показа:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['criterion_type', 'criterion']
}
```

**Группировка по блоку показа и категории таргетинга:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions', 'ctr'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['slot', 'targeting_category']
}
```

**Группировка по типу соответствия и подобранной фразе:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['match_type', 'matched_keyword']
}
```

**Группировка по площадке и мобильной платформе:**
```python
{
    'campaign_ids': [76757740],
    'fields': ['expense', 'clicks', 'impressions', 'ctr'],
    'from': '2024-01-01',
    'to': '2024-01-31',
    'breakdowns': ['placement', 'mobile_platform']
}
```

#### Пример успешного ответа

```json
{
    "status": "ok",
    "statistics": [
        {
            "CampaignId": 111,
            "data": {
                "expense": 1500.50,
                "avg_pageviews":1.3,
                "clicks": 150,
                "impressions": 5000,
                "ctr": 3.00,
                "cpa": 75.03,
                "online_conversions": {
                    "total": 20,
                    "details": {
                        "12345": 15,
                        "67890": 5
                    }
                }
            }
        },
        {
            "CampaignId": 222,
            "data": {
                "expense": 850.25,
                "avg_pageviews": 2.34,
                "clicks": 95,
                "impressions": 3200,
                "ctr": 2.97,
                "cpa": 42.51,
                "online_conversions": {
                    "total": 20,
                    "details": {
                        "12345": 20
                    }
                }
            }
        }
    ]
}
```

#### Пример успешного ответа с breakdowns

```json
{
    "status": "ok",
    "statistics": [
        {
            "CampaignId": 76757740,
            "data": {
                "impressions": 30,
                "clicks": 4,
                "expense": 453.78,
                "cpc": 0,
                "online_conversions": {
                    "total": 0,
                    "details": {}
                },
                "cpa": 0,
                "avg_pageviews": 0
            },
            "breakdowns": [
                {
                    "fields": {
                        "device": "DESKTOP",
                        "gender": "GENDER_FEMALE"
                    },
                    "data": {
                        "impressions": 2,
                        "clicks": 2,
                        "expense": 257.4,
                        "cpc": 0,
                        "online_conversions": {
                            "total": 0,
                            "details": {}
                        },
                        "cpa": 0,
                        "avg_pageviews": 1
                    }
                },
                {
                    "fields": {
                        "device": "DESKTOP",
                        "gender": "GENDER_MALE"
                    },
                    "data": {
                        "impressions": 15,
                        "clicks": 1,
                        "expense": 149.28,
                        "cpc": 0,
                        "online_conversions": {
                            "total": 0,
                            "details": {}
                        },
                        "cpa": 0,
                        "avg_pageviews": 3
                    }
                },
                {
                    "fields": {
                        "device": "MOBILE",
                        "gender": "GENDER_FEMALE"
                    },
                    "data": {
                        "impressions": 4,
                        "clicks": 0,
                        "expense": 0,
                        "cpc": 0,
                        "online_conversions": {
                            "total": 0,
                            "details": {}
                        },
                        "cpa": 0,
                        "avg_pageviews": 0
                    }
                },
                {
                    "fields": {
                        "device": "MOBILE",
                        "gender": "GENDER_MALE"
                    },
                    "data": {
                        "impressions": 9,
                        "clicks": 1,
                        "expense": 47.1,
                        "cpc": 0,
                        "online_conversions": {
                            "total": 0,
                            "details": {}
                        },
                        "cpa": 0,
                        "avg_pageviews": 1
                    }
                },
                {
                    "fields": {
                        "device": "TABLET",
                        "gender": "GENDER_MALE"
                    },
                    "data": {
                        "impressions": 0,
                        "clicks": 0,
                        "expense": 0,
                        "cpc": 0,
                        "online_conversions": {
                            "total": 0,
                            "details": {}
                        },
                        "cpa": 0,
                        "avg_pageviews": 0
                    }
                }
            ]
        }
    ]
}
```

#### Пример успешного ответа с segments

```json
{
    "status": "ok",
    "statistics": [
        {
            "CampaignId": 76757745,
            "data": {
                "expense": 0
            },
            "segments": [
                {
                    "fields": {
                        "gender": "GENDER_MALE",
                        "age": "AGE_18_24"
                    },
                    "data": {
                        "expense": 0
                    }
                }
            ]
        }
    ]
}
```

**Примечание:** В ответе с `segments` структура данных похожа на `breakdowns`:
- `data` - общая статистика с учетом фильтров
- `segments` - массив с детализацией по каждой комбинации значений из фильтра
- `segments[].fields` - конкретные значения фильтров для данного сегмента
- `segments[].data` - статистика для данного сегмента

#### Структура данных статистики

| Поле | Тип | Описание |
|------|-----|----------|
| `CampaignId` | integer | ID кампании |
| `data` | object | Объект с запрошенными метриками |
| `data.expense` | number | Расход |
| `data.avg_pageviews` | number | Глубина просмотра страницы |
| `data.clicks` | integer | Количество кликов |
| `data.impressions` | integer | Количество показов |
| `data.ctr` | number | CTR в процентах |
| `data.cpa` | number | Стоимость онлайн конверсии |
| `data.cpl` | number | Стоимость оффлайн лида |
| `data.online_conversions.total` | integer | Общее количество онлайн конверсий |
| `data.online_conversions.details` | object | Конверсии по целям (goalId: count) |
| `data.qualified_leads.total` | integer | Общее количество оффлайн лидов |
| `data.qualified_leads.details` | object | Лиды по целям (goalId: count) |
| `breakdowns` | array | Массив объектов с группировкой по указанным полям (если параметр breakdowns указан) |
| `breakdowns[].fields` | object | Объект с полями группировки и их значениями |
| `breakdowns[].data` | object | Объект с метриками для данной группировки |
| `segments` | array | Массив объектов с детализацией по сегментам (если параметр segments указан) |
| `segments[].fields` | object | Объект с полями фильтрации и их конкретными значениями |
| `segments[].data` | object | Объект с метриками для данного сегмента |

#### Расчет метрик

**CPA (Cost Per Action):**
```
CPA = Расход / Количество онлайн конверсий
```

**CPL (Cost Per Lead):**
```
CPL = Расход / Количество оффлайн лидов
```

**CPC (Cost Per Conversion):**
```
CPC = Расход / Количество всех конверсий
```

**CTR (Click-Through Rate):**
```
CTR = (Клики / Показы) × 100%
```

#### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Тело запроса должно содержать JSON с параметрами` | Пустое или невалидное тело запроса |
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |
| `Параметр fields обязателен` | Не указаны поля для получения |
| `Параметры from и to обязательны` | Не указаны даты периода |
| `Для расчета CPA необходимо указать online_goals` | Запрошен CPA без онлайн целей |
| `Для расчета CPL необходимо указать offline_goals` | Запрошен CPL без оффлайн целей |

---

### 6. Получение статистики групп объявлений

Получение детальной статистики групп объявлений с расчетом различных метрик (аналогично статистике кампаний).

#### Endpoint
```
POST /webHooks/getGroupsStatistics
```

#### Параметры

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

#### Доступные поля (fields)

Аналогично полям для статистики кампаний:

| Поле | Описание | Требует целей |
|------|----------|---------------|
| `expense` | Расход в валюте аккаунта | - |
| `avg_pageviews` | Глубина просмотра (стр) | - |
| `clicks` | Количество кликов | - |
| `impressions` | Количество показов | - |
| `bounces` | Количество отказов | - |
| `ctr` | CTR - кликабельность (%) | - |
| `cpa` | CPA - стоимость онлайн конверсии | `online_goals` |
| `cpl` | CPL - стоимость оффлайн лида | `offline_goals` |
| `cpc` | CPC - стоимость конверсии (все цели) | `online_goals` или `offline_goals` |
| `online_conversions` | Детализация онлайн конверсий | `online_goals` |
| `qualified_leads` | Детализация оффлайн лидов | `offline_goals` |

#### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getGroupsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'group_ids': [111, 222],
        'fields': ['expense', 'clicks', 'impressions', 'ctr', 'cpa'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345, 67890]
    }
)
print(response.json())
```

#### Пример успешного ответа

```json
{
    "status": "ok",
    "statistics": [
        {
            "AdGroupId": 111,
            "data": {
                "expense": 750.25,
                "clicks": 80,
                "impressions": 2500,
                "ctr": 3.20,
                "cpa": 37.51,
                "online_conversions": {
                    "total": 20,
                    "details": {
                        "12345": 15,
                        "67890": 5
                    }
                }
            }
        },
        {
            "AdGroupId": 222,
            "data": {
                "expense": 425.50,
                "clicks": 45,
                "impressions": 1600,
                "ctr": 2.81,
                "cpa": 42.55,
                "online_conversions": {
                    "total": 10,
                    "details": {
                        "12345": 10
                    }
                }
            }
        }
    ]
}
```

#### Структура данных статистики

| Поле | Тип | Описание |
|------|-----|----------|
| `AdGroupId` | integer | ID группы объявлений |
| `data` | object | Объект с запрошенными метриками |
| `data.*` | mixed | Аналогично структуре статистики кампаний |

#### Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Тело запроса должно содержать JSON с параметрами` | Пустое или невалидное тело запроса |
| `Параметр group_ids обязателен` | Не указаны ID групп |
| `Параметр fields обязателен` | Не указаны поля для получения |
| `Параметры from и to обязательны` | Не указаны даты периода |
| `Для расчета CPA необходимо указать online_goals` | Запрошен CPA без онлайн целей |
| `Для расчета CPL необходимо указать offline_goals` | Запрошен CPL без оффлайн целей |

---

### 7. Асинхронное получение данных

Запуск фонового процесса для получения истории изменений, настроек стратегий или данных обучения кампаний.

#### Endpoint
```
POST /webHooks/startYandexDirectFetch
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | body | Да | string | Логин аккаунта |
| `client` | body | Да | string | Логин клиента |
| `type` | body | Да | string | Тип операции: `history`, `settings` или `strategy_study` |
| `params` | body | Нет | object | Дополнительные параметры |

#### Пример запроса

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

#### Пример успешного ответа

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

#### Типы операций

| Тип | Описание |
|-----|----------|
| `history` | Получение истории изменений кампаний |
| `settings` | Получение настроек и стратегий кампаний |
| `strategy_study` | Получение данных обучения стратегий |

---

### 8. Получение статуса асинхронного процесса

Получение текущего статуса выполнения фонового процесса.

#### Endpoint
```
GET /webHooks/getYandexDirectFetchStatus
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | query | Да | string | ID процесса |

#### Пример запроса

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getYandexDirectFetchStatus',
    params={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

#### Пример ответа (процесс выполняется)

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

#### Пример ответа (процесс завершен)

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

#### Статусы процесса

| Статус | Описание |
|--------|----------|
| `new` | Новый процесс |
| `pending` | Выполняется |
| `done` | Завершен успешно |
| `error` | Ошибка выполнения |
| `stopped` | Остановлен пользователем |

---

### 9. Получение результатов асинхронного процесса

Получение данных после завершения фонового процесса.

#### Endpoint
```
GET /webHooks/getYandexDirectFetchResults
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | query | Да | string | ID процесса |

#### Пример запроса

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getYandexDirectFetchResults',
    params={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

#### Пример успешного ответа (история)

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

#### Пример успешного ответа (настройки)

```json
{
    "status": "ok",
    "success": true,
    "processId": "YandexDirectDataFetchProcess_65a8b3f4c5d6e",
    "account": "myaccount",
    "client": "myclient",
    "type": "settings",
    "data": {
        "data": [
            {
                "campaign_id": 12345,
                "strategy_type": "WB_MAXIMUM_CLICKS",
                "weekly_spend_limit": 10000,
                "bid_ceiling": 100
            }
        ]
    },
    "timestamp": 1737033600,
    "status": "done",
    "dataCount": 25
}
```

#### Пример успешного ответа (обучение стратегий)

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
            },
            {
                "campaign_id": 33333333,
                "status": "Обучается",
                "conv_per_week": 8
            },
            {
                "campaign_id": 44444444,
                "status": null,
                "conv_per_week": null
            },
            {
                "campaign_id": 55555555,
                "status": "Недостаточно данных",
                "conv_per_week": 2
            }
        ],
        "message": "Данные обучения стратегий получены"
    },
    "timestamp": 1737033600,
    "status": "done",
    "dataCount": 5
}
```

#### Структура данных обучения стратегий

| Поле | Тип | Описание |
|------|-----|----------|
| `campaign_id` | integer | ID кампании |
| `status` | string/null | Статус обучения стратегии. Возвращает `null`, если данные об обучении отсутствуют (например, стратегия не использует обучение) |
| `conv_per_week` | integer/null | Количество конверсий за неделю. Возвращает `null`, если данные недоступны или стратегия не использует обучение |

**Примечание:** Для кампаний, которые не используют стратегии с обучением (например, ручные стратегии или стратегии без автоматической оптимизации), оба поля `status` и `conv_per_week` будут иметь значение `null`.

#### Возможные ошибки

| Сообщение об ошибке | Описание |
|---------------------|----------|
| `Не указан параметр processId` | Отсутствует обязательный параметр |
| `Процесс с ID ... не найден` | Процесс не существует или был удален |
| `Процесс еще не завершен или завершен с ошибкой` | Результаты недоступны |
| `Результаты процесса не найдены` | Данные не сохранены |

---

### 10. Остановка асинхронного процесса

Принудительная остановка выполняющегося процесса.

#### Endpoint
```
POST /webHooks/stopYandexDirectFetch
```

#### Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `processId` | body | Да | string | ID процесса |

#### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/stopYandexDirectFetch',
    json={'processId': 'YandexDirectDataFetchProcess_65a8b3f4c5d6e'}
)
print(response.json())
```

#### Пример успешного ответа

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

#### Возможные ошибки

| Сообщение об ошибке | Описание |
|---------------------|----------|
| `Не указан параметр processId` | Отсутствует обязательный параметр |
| `Процесс с ID ... не найден` | Процесс не существует |
| `Процесс не выполняется` | Процесс уже завершен или остановлен |

---

## Коды ошибок

### Общие ошибки

| HTTP код | Описание |
|----------|----------|
| 200 | Успешный запрос |
| 400 | Ошибка валидации параметров |
| 500 | Внутренняя ошибка сервера |

### Ошибки аутентификации

| Ошибка | Решение |
|--------|---------|
| `Не указан аккаунт или клиент` | Добавьте параметры account и client в URL |
| `Ошибка авторизации в Яндекс.Директ` | Проверьте корректность учетных данных |
| `У вас нет доступа к данному аккаунту` | Получите доступ через интерфейс Яндекс.Директ |
| `Invalid token` | Обновите токен доступа |

### Ошибки API Яндекс.Директ

| Ошибка | Решение |
|--------|---------|
| `API quota exceeded` | Подождите или увеличьте лимиты |
| `Campaign not found` | Проверьте ID кампании |
| `Goal not found` | Проверьте ID цели |

---

## Примеры использования

### Пример 1: Проверка аккаунта и получение списка кампаний

```python
import requests

def get_campaigns():
    account = 'myaccount'
    client = 'myclient'
    base_url = 'https://your-domain.com/webHooks'

    # Шаг 1: Валидация аккаунта
    validate_response = requests.post(
        f'{base_url}/validateYandexDirectAccount',
        params={'account': account, 'client': client}
    )
    validate_data = validate_response.json()

    if not validate_data.get('valid'):
        print(f"Аккаунт невалидный: {validate_data.get('error')}")
        return None

    # Шаг 2: Получение списка кампаний
    campaigns_response = requests.get(
        f'{base_url}/getCampaignsList',
        params={'account': account, 'client': client}
    )
    campaigns_data = campaigns_response.json()

    print('Кампании:', campaigns_data['campaigns'])
    return campaigns_data['campaigns']

# Использование
campaigns = get_campaigns()
```

### Пример 2: Получение статистики по нескольким кампаниям

```python
import requests

def get_campaign_statistics(campaign_ids):
    account = 'myaccount'
    client = 'myclient'

    response = requests.post(
        'https://your-domain.com/webHooks/getCampaignsStatistics',
        params={'account': account, 'client': client},
        json={
            'campaign_ids': campaign_ids,
            'fields': ['expense', 'clicks', 'impressions', 'ctr', 'cpa'],
            'from': '2024-01-01',
            'to': '2024-01-31',
            'online_goals': [12345]
        }
    )

    data = response.json()

    if data.get('status') == 'ok':
        for stat in data['statistics']:
            print(f"Кампания {stat['CampaignId']}:")
            print(f"  Расход: {stat['data']['expense']} руб.")
            print(f"  Клики: {stat['data']['clicks']}")
            print(f"  CTR: {stat['data']['ctr']}%")
            print(f"  CPA: {stat['data']['cpa']} руб.")
    else:
        print(f"Ошибка: {data.get('error')}")

# Использование
get_campaign_statistics([111, 222])
```

### Пример 3: Полный класс для работы с API (Python)

```python
import requests
from typing import List, Dict, Optional

class YandexDirectAPI:
    """Класс для работы с API Яндекс.Директ"""

    def __init__(self, account: str, client: str):
        self.base_url = 'https://your-domain.com/webHooks'
        self.account = account
        self.client = client

    def _make_request(self, endpoint: str, method: str = 'GET', data: Optional[Dict] = None) -> Dict:
        """Базовый метод для выполнения запросов к API"""
        url = f"{self.base_url}/{endpoint}"
        params = {'account': self.account, 'client': self.client}

        if method == 'GET':
            response = requests.get(url, params=params)
        else:
            response = requests.post(url, params=params, json=data)

        response.raise_for_status()
        return response.json()

    def validate_account(self) -> bool:
        """Проверка валидности аккаунта"""
        result = self._make_request('validateYandexDirectAccount', 'POST')
        return result.get('valid', False)

    def get_campaigns(self) -> List[Dict]:
        """Получение списка кампаний"""
        result = self._make_request('getCampaignsList')
        return result.get('campaigns', [])

    def get_statistics(
        self,
        campaign_ids: List[int],
        date_from: str,
        date_to: str,
        fields: List[str] = None,
        online_goals: List[int] = None,
        offline_goals: List[int] = None
    ) -> List[Dict]:
        """
        Получение статистики кампаний

        Args:
            campaign_ids: Список ID кампаний
            date_from: Дата начала периода (YYYY-MM-DD)
            date_to: Дата окончания периода (YYYY-MM-DD)
            fields: Список полей для получения
            online_goals: ID онлайн целей для расчета CPA
            offline_goals: ID оффлайн целей для расчета CPL

        Returns:
            Список статистики по кампаниям
        """
        if fields is None:
            fields = ['expense', 'clicks']

        data = {
            'campaign_ids': campaign_ids,
            'fields': fields,
            'from': date_from,
            'to': date_to
        }

        if online_goals:
            data['online_goals'] = online_goals
        if offline_goals:
            data['offline_goals'] = offline_goals

        result = self._make_request('getCampaignsStatistics', 'POST', data)
        return result.get('statistics', [])

    def get_groups(self, campaign_ids: List[int]) -> List[Dict]:
        """
        Получение списка групп объявлений для кампаний

        Args:
            campaign_ids: Список ID кампаний

        Returns:
            Список групп объявлений
        """
        import json
        result = self._make_request(
            'getGroupsList',
            params={'campaign_ids': json.dumps(campaign_ids)}
        )
        return result.get('groups', [])

    def get_groups_statistics(
        self,
        group_ids: List[int],
        date_from: str,
        date_to: str,
        fields: List[str] = None,
        online_goals: List[int] = None,
        offline_goals: List[int] = None
    ) -> List[Dict]:
        """
        Получение статистики групп объявлений

        Args:
            group_ids: Список ID групп объявлений
            date_from: Дата начала периода (YYYY-MM-DD)
            date_to: Дата окончания периода (YYYY-MM-DD)
            fields: Список полей для получения
            online_goals: ID онлайн целей для расчета CPA
            offline_goals: ID оффлайн целей для расчета CPL

        Returns:
            Список статистики по группам
        """
        if fields is None:
            fields = ['expense', 'clicks']

        data = {
            'group_ids': group_ids,
            'fields': fields,
            'from': date_from,
            'to': date_to
        }

        if online_goals:
            data['online_goals'] = online_goals
        if offline_goals:
            data['offline_goals'] = offline_goals

        result = self._make_request('getGroupsStatistics', 'POST', data)
        return result.get('statistics', [])

# Использование
if __name__ == '__main__':
    api = YandexDirectAPI('myaccount', 'myclient')

    # Проверяем доступ
    if api.validate_account():
        print("✓ Аккаунт валидный")

        # Получаем список кампаний
        campaigns = api.get_campaigns()
        print(f"Найдено кампаний: {len(campaigns)}")

        # Получаем ID первых 5 кампаний
        campaign_ids = [c['Id'] for c in campaigns[:5]]

        # Получаем статистику
        statistics = api.get_statistics(
            campaign_ids=campaign_ids,
            date_from='2024-01-01',
            date_to='2024-01-31',
            fields=['expense', 'clicks', 'impressions', 'ctr'],
            online_goals=[12345]
        )

        # Выводим результаты
        for stat in statistics:
            print(f"\nКампания {stat['CampaignId']}:")
            for field, value in stat['data'].items():
                print(f"  {field}: {value}")

        # Получаем группы объявлений для первой кампании
        if campaign_ids:
            groups = api.get_groups([campaign_ids[0]])
            print(f"\nНайдено групп в кампании {campaign_ids[0]}: {len(groups)}")

            # Получаем статистику первых 3 групп
            if groups:
                group_ids = [g['Id'] for g in groups[:3]]
                group_statistics = api.get_groups_statistics(
                    group_ids=group_ids,
                    date_from='2024-01-01',
                    date_to='2024-01-31',
                    fields=['expense', 'clicks', 'ctr']
                )

                # Выводим результаты по группам
                for stat in group_statistics:
                    print(f"\nГруппа {stat['AdGroupId']}:")
                    for field, value in stat['data'].items():
                        print(f"  {field}: {value}")
    else:
        print("✗ Ошибка доступа к аккаунту")
```

### Пример 4: Получение статистики с целями

```python
import requests

# Получение статистики с расчетом CPA и CPL
response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={
        'account': 'myaccount',
        'client': 'myclient'
    },
    json={
        'campaign_ids': [111],
        'fields': ['expense', 'clicks', 'cpa', 'cpl', 'online_conversions', 'qualified_leads'],
        'from': '2024-01-01',
        'to': '2024-01-31',
        'online_goals': [12345, 67890],
        'offline_goals': [11111, 22222]
    }
)

data = response.json()
if data.get('status') == 'ok':
    for stat in data['statistics']:
        print(f"Кампания {stat['CampaignId']}:")
        print(f"  Расход: {stat['data']['expense']}")
        print(f"  Клики: {stat['data']['clicks']}")
        print(f"  CPA: {stat['data']['cpa']}")
        print(f"  CPL: {stat['data']['cpl']}")
        print(f"  Онлайн конверсии: {stat['data']['online_conversions']}")
        print(f"  Квалифицированные лиды: {stat['data']['qualified_leads']}")
```

### Пример 5: Работа с группами объявлений

```python
import requests
import json

account = 'myaccount'
client = 'myclient'
base_url = 'https://your-domain.com/webHooks'

# Шаг 1: Получаем список кампаний
campaigns_response = requests.get(
    f'{base_url}/getCampaignsList',
    params={'account': account, 'client': client}
)
campaigns = campaigns_response.json()['campaigns']
campaign_ids = [c['Id'] for c in campaigns[:2]]

print(f"Выбрано кампаний: {len(campaign_ids)}")

# Шаг 2: Получаем группы объявлений для кампаний
groups_response = requests.get(
    f'{base_url}/getGroupsList',
    params={
        'account': account,
        'client': client,
        'campaign_ids': json.dumps(campaign_ids)
    }
)
groups = groups_response.json()['groups']
group_ids = [g['Id'] for g in groups[:5]]

print(f"Найдено групп: {len(groups)}")
print(f"Выбрано групп для статистики: {len(group_ids)}")

# Шаг 3: Получаем статистику по группам
stats_response = requests.post(
    f'{base_url}/getGroupsStatistics',
    params={'account': account, 'client': client},
    json={
        'group_ids': group_ids,
        'fields': ['expense', 'clicks', 'impressions', 'ctr'],
        'from': '2024-01-01',
        'to': '2024-01-31'
    }
)

stats_data = stats_response.json()
if stats_data.get('status') == 'ok':
    for stat in stats_data['statistics']:
        group_info = next((g for g in groups if g['Id'] == stat['AdGroupId']), None)
        group_name = group_info['Name'] if group_info else 'Неизвестная группа'

        print(f"\nГруппа {stat['AdGroupId']} ({group_name}):")
        print(f"  Расход: {stat['data']['expense']} руб.")
        print(f"  Клики: {stat['data']['clicks']}")
        print(f"  Показы: {stat['data']['impressions']}")
        print(f"  CTR: {stat['data']['ctr']}%")
else:
    print(f"Ошибка: {stats_data.get('error')}")
```

---

### Полный пример работы с асинхронными процессами

#### Класс для работы с асинхронными процессами

```python
import requests
import time
from typing import Optional, Dict, Any

class YandexDirectAsyncClient:
    """
    Клиент для работы с асинхронными процессами Яндекс.Директ API
    """
    
    def __init__(self, base_url: str, account: str, client: str):
        """
        Инициализация клиента
        
        Args:
            base_url: Базовый URL API (например: https://your-domain.com/webHooks)
            account: Логин аккаунта Яндекс.Директ
            client: Логин клиента
        """
        self.base_url = base_url.rstrip('/')
        self.account = account
        self.client = client
    
    def start_process(self, process_type: str, params: Optional[Dict] = None) -> str:
        """
        Запуск асинхронного процесса
        
        Args:
            process_type: Тип процесса ('history', 'settings' или 'strategy_study')
            params: Дополнительные параметры
            
        Returns:
            ID запущенного процесса
            
        Raises:
            Exception: Если процесс не удалось запустить
        """
        response = requests.post(
            f'{self.base_url}/startYandexDirectFetch',
            json={
                'account': self.account,
                'client': self.client,
                'type': process_type,
                'params': params or {}
            }
        )
        
        data = response.json()
        
        if data.get('status') != 'ok' or not data.get('success'):
            raise Exception(f"Ошибка запуска процесса: {data.get('error', 'Unknown error')}")
        
        process_id = data['processId']
        print(f"✓ Процесс {data.get('typeLabel')} запущен: {process_id}")
        
        return process_id
    
    def get_status(self, process_id: str) -> Dict[str, Any]:
        """
        Получение статуса процесса
        
        Args:
            process_id: ID процесса
            
        Returns:
            Информация о статусе процесса
        """
        response = requests.get(
            f'{self.base_url}/getYandexDirectFetchStatus',
            params={'processId': process_id}
        )
        
        data = response.json()
        
        if data.get('status') != 'ok':
            raise Exception(f"Ошибка получения статуса: {data.get('error', 'Unknown error')}")
        
        return data
    
    def get_results(self, process_id: str) -> Dict[str, Any]:
        """
        Получение результатов процесса
        
        Args:
            process_id: ID процесса
            
        Returns:
            Данные результата
            
        Raises:
            Exception: Если результаты недоступны
        """
        response = requests.get(
            f'{self.base_url}/getYandexDirectFetchResults',
            params={'processId': process_id}
        )
        
        data = response.json()
        
        if data.get('status') != 'ok' or not data.get('success'):
            raise Exception(f"Ошибка получения результатов: {data.get('error', 'Unknown error')}")
        
        return data
    
    def stop_process(self, process_id: str) -> bool:
        """
        Остановка процесса
        
        Args:
            process_id: ID процесса
            
        Returns:
            True если процесс остановлен
        """
        response = requests.post(
            f'{self.base_url}/stopYandexDirectFetch',
            json={'processId': process_id}
        )
        
        data = response.json()
        
        if data.get('status') != 'ok':
            raise Exception(f"Ошибка остановки процесса: {data.get('error', 'Unknown error')}")
        
        return data.get('success', False)
    
    def wait_for_completion(
        self, 
        process_id: str, 
        check_interval: int = 2, 
        max_wait_time: Optional[int] = None,
        verbose: bool = True
    ) -> Dict[str, Any]:
        """
        Ожидание завершения процесса с периодической проверкой статуса
        
        Args:
            process_id: ID процесса
            check_interval: Интервал проверки в секундах (по умолчанию 2)
            max_wait_time: Максимальное время ожидания в секундах (None = без ограничения)
            verbose: Выводить прогресс в консоль
            
        Returns:
            Финальный статус процесса
            
        Raises:
            TimeoutError: Если превышено максимальное время ожидания
            Exception: Если процесс завершился с ошибкой
        """
        start_time = time.time()
        last_progress = -1
        
        while True:
            # Проверка таймаута
            if max_wait_time and (time.time() - start_time) > max_wait_time:
                raise TimeoutError(f"Превышено максимальное время ожидания ({max_wait_time}s)")
            
            # Получение статуса
            status = self.get_status(process_id)
            current_status = status.get('status')
            progress = status.get('progress', 0)
            stage = status.get('stage', '')
            
            # Вывод прогресса
            if verbose and progress != last_progress:
                print(f"  [{progress}%] {stage}")
                last_progress = progress
            
            # Проверка завершения
            if current_status == 'done':
                if verbose:
                    print(f"✓ Процесс завершен успешно")
                return status
            
            elif current_status == 'error':
                error_msg = status.get('errorMessage', status.get('message', 'Unknown error'))
                raise Exception(f"Процесс завершился с ошибкой: {error_msg}")
            
            elif current_status == 'stopped':
                raise Exception("Процесс был остановлен")
            
            # Ожидание перед следующей проверкой
            time.sleep(check_interval)
    
    def fetch_history(self, wait: bool = True, **kwargs) -> Dict[str, Any]:
        """
        Получение истории изменений кампаний
        
        Args:
            wait: Ожидать завершения процесса
            **kwargs: Дополнительные параметры для wait_for_completion
            
        Returns:
            Данные истории изменений
        """
        # Запуск процесса
        process_id = self.start_process('history')
        
        if not wait:
            return {'processId': process_id, 'status': 'pending'}
        
        # Ожидание завершения
        self.wait_for_completion(process_id, **kwargs)
        
        # Получение результатов
        results = self.get_results(process_id)
        
        print(f"✓ Получено записей истории: {results.get('dataCount', 0)}")
        
        return results
    
    def fetch_settings(self, wait: bool = True, **kwargs) -> Dict[str, Any]:
        """
        Получение настроек и стратегий кампаний
        
        Args:
            wait: Ожидать завершения процесса
            **kwargs: Дополнительные параметры для wait_for_completion
            
        Returns:
            Данные настроек кампаний
        """
        # Запуск процесса
        process_id = self.start_process('settings')
        
        if not wait:
            return {'processId': process_id, 'status': 'pending'}
        
        # Ожидание завершения
        self.wait_for_completion(process_id, **kwargs)
        
        # Получение результатов
        results = self.get_results(process_id)
        
        print(f"✓ Получено настроек кампаний: {results.get('dataCount', 0)}")
        
        return results
    
    def fetch_strategy_study(self, wait: bool = True, **kwargs) -> Dict[str, Any]:
        """
        Получение данных обучения стратегий
        
        Args:
            wait: Ожидать завершения процесса
            **kwargs: Дополнительные параметры для wait_for_completion
            
        Returns:
            Данные обучения стратегий
        """
        # Запуск процесса
        process_id = self.start_process('strategy_study')
        
        if not wait:
            return {'processId': process_id, 'status': 'pending'}
        
        # Ожидание завершения
        self.wait_for_completion(process_id, **kwargs)
        
        # Получение результатов
        results = self.get_results(process_id)
        
        print(f"✓ Получено данных обучения стратегий: {results.get('dataCount', 0)}")
        
        return results


# ============================================================================
# ПРИМЕР 1: Простое использование - получение истории
# ============================================================================

def example_fetch_history():
    """Пример получения истории изменений"""
    
    # Инициализация клиента
    client = YandexDirectAsyncClient(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient'
    )
    
    try:
        # Получение истории (автоматически ждет завершения)
        results = client.fetch_history()
        
        # Работа с данными
        history_data = results['data']['data']
        
        print(f"\n=== История изменений ===")
        print(f"Всего записей: {len(history_data)}")
        
        # Вывод первых 5 записей
        for item in history_data[:5]:
            print(f"  - [{item.get('datetime')}] Кампания {item.get('cid')}: {item.get('change')}")
        
        return history_data
        
    except Exception as e:
        print(f"✗ Ошибка: {e}")
        return None


# ============================================================================
# ПРИМЕР 2: Простое использование - получение настроек
# ============================================================================

def example_fetch_settings():
    """Пример получения настроек стратегий"""
    
    # Инициализация клиента
    client = YandexDirectAsyncClient(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient'
    )
    
    try:
        # Получение настроек (автоматически ждет завершения)
        results = client.fetch_settings()
        
        # Работа с данными
        settings_data = results['data']['data']
        
        print(f"\n=== Настройки стратегий ===")
        print(f"Всего кампаний: {len(settings_data)}")
        
        # Вывод настроек
        for item in settings_data:
            print(f"  - Кампания {item.get('campaign_id')}")
            print(f"    Стратегия: {item.get('strategy_type')}")
            print(f"    Недельный лимит: {item.get('weekly_spend_limit')} руб.")
        
        return settings_data
        
    except Exception as e:
        print(f"✗ Ошибка: {e}")
        return None


# ============================================================================
# ПРИМЕР 3: Простое использование - получение данных обучения стратегий
# ============================================================================

def example_fetch_strategy_study():
    """Пример получения данных обучения стратегий"""
    
    # Инициализация клиента
    client = YandexDirectAsyncClient(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient'
    )
    
    try:
        # Получение данных обучения (автоматически ждет завершения)
        results = client.fetch_strategy_study()
        
        # Работа с данными
        study_data = results['data']['data']
        
        print(f"\n=== Данные обучения стратегий ===")
        print(f"Всего кампаний: {len(study_data)}")
        
        # Вывод данных обучения
        for item in study_data:
            print(f"  - Кампания {item.get('campaign_id')}")
            print(f"    Статус: {item.get('status')}")
            conv_per_week = item.get('conv_per_week')
            if conv_per_week is not None:
                print(f"    Конверсий за неделю: {conv_per_week}")
            else:
                print(f"    Конверсий за неделю: нет данных")
        
        return study_data
        
    except Exception as e:
        print(f"✗ Ошибка: {e}")
        return None


# ============================================================================
# ПРИМЕР 4: Продвинутое использование - параллельное получение
# ============================================================================

def example_parallel_fetch():
    """Пример параллельного получения истории, настроек и данных обучения"""
    
    client = YandexDirectAsyncClient(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient'
    )
    
    try:
        # Запуск процессов без ожидания
        print("Запуск процессов...")
        history_process = client.fetch_history(wait=False)
        settings_process = client.fetch_settings(wait=False)
        study_process = client.fetch_strategy_study(wait=False)
        
        history_id = history_process['processId']
        settings_id = settings_process['processId']
        study_id = study_process['processId']
        
        print(f"  История: {history_id}")
        print(f"  Настройки: {settings_id}")
        print(f"  Обучение стратегий: {study_id}")
        
        # Ожидание завершения всех процессов
        print("\nОжидание завершения...")
        
        print("\n1. Ожидание истории:")
        client.wait_for_completion(history_id)
        
        print("\n2. Ожидание настроек:")
        client.wait_for_completion(settings_id)
        
        print("\n3. Ожидание данных обучения:")
        client.wait_for_completion(study_id)
        
        # Получение результатов
        history_results = client.get_results(history_id)
        settings_results = client.get_results(settings_id)
        study_results = client.get_results(study_id)
        
        print(f"\n✓ Получено истории: {history_results.get('dataCount')} записей")
        print(f"✓ Получено настроек: {settings_results.get('dataCount')} кампаний")
        print(f"✓ Получено данных обучения: {study_results.get('dataCount')} кампаний")
        
        return {
            'history': history_results['data']['data'],
            'settings': settings_results['data']['data'],
            'strategy_study': study_results['data']['data']
        }
        
    except Exception as e:
        print(f"✗ Ошибка: {e}")
        return None


# ============================================================================
# ПРИМЕР 4: Получение с таймаутом и обработкой ошибок
# ============================================================================

def example_fetch_with_timeout():
    """Пример получения данных с таймаутом"""
    
    client = YandexDirectAsyncClient(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient'
    )
    
    try:
        # Запуск процесса
        process_id = client.start_process('history')
        
        # Ожидание с таймаутом 60 секунд
        client.wait_for_completion(
            process_id,
            check_interval=2,
            max_wait_time=60,
            verbose=True
        )
        
        # Получение результатов
        results = client.get_results(process_id)
        
        return results['data']['data']
        
    except TimeoutError as e:
        print(f"✗ Таймаут: {e}")
        # Попытка остановить процесс
        try:
            client.stop_process(process_id)
            print("  Процесс остановлен")
        except:
            pass
        return None
        
    except Exception as e:
        print(f"✗ Ошибка: {e}")
        return None


# ============================================================================
# ПРИМЕР 5: Готовая функция для получения истории и настроек
# ============================================================================

def get_yandex_direct_data(
    base_url: str,
    account: str,
    client: str,
    get_history: bool = True,
    get_settings: bool = True,
    timeout: int = 300
) -> Dict[str, Any]:
    """
    Универсальная функция для получения данных из Яндекс.Директ
    
    Args:
        base_url: Базовый URL API
        account: Логин аккаунта
        client: Логин клиента
        get_history: Получить историю изменений
        get_settings: Получить настройки стратегий
        timeout: Максимальное время ожидания в секундах
        
    Returns:
        Словарь с данными: {'history': [...], 'settings': [...]}
        
    Example:
        >>> data = get_yandex_direct_data(
        ...     'https://your-domain.com/webHooks',
        ...     'myaccount',
        ...     'myclient'
        ... )
        >>> print(f"История: {len(data['history'])} записей")
        >>> print(f"Настройки: {len(data['settings'])} кампаний")
    """
    
    client_api = YandexDirectAsyncClient(base_url, account, client)
    result = {}
    
    # Запуск процессов
    process_ids = {}
    
    if get_history:
        print("→ Запуск получения истории...")
        process_ids['history'] = client_api.start_process('history')
    
    if get_settings:
        print("→ Запуск получения настроек...")
        process_ids['settings'] = client_api.start_process('settings')
    
    # Ожидание завершения и получение результатов
    for data_type, process_id in process_ids.items():
        try:
            print(f"\n→ Ожидание завершения: {data_type}")
            client_api.wait_for_completion(
                process_id,
                check_interval=2,
                max_wait_time=timeout,
                verbose=True
            )
            
            # Получение результатов
            results = client_api.get_results(process_id)
            result[data_type] = results['data']['data']
            
            print(f"✓ Получено {data_type}: {len(result[data_type])} записей")
            
        except Exception as e:
            print(f"✗ Ошибка получения {data_type}: {e}")
            result[data_type] = []
    
    return result


# ============================================================================
# ИСПОЛЬЗОВАНИЕ
# ============================================================================

if __name__ == '__main__':
    # Пример 1: Простое получение истории
    print("=" * 60)
    print("ПРИМЕР 1: Получение истории")
    print("=" * 60)
    example_fetch_history()
    
    # Пример 2: Простое получение настроек
    print("\n" + "=" * 60)
    print("ПРИМЕР 2: Получение настроек")
    print("=" * 60)
    example_fetch_settings()
    
    # Пример 3: Параллельное получение
    print("\n" + "=" * 60)
    print("ПРИМЕР 3: Параллельное получение")
    print("=" * 60)
    example_parallel_fetch()
    
    # Пример 4: Использование готовой функции
    print("\n" + "=" * 60)
    print("ПРИМЕР 4: Готовая функция")
    print("=" * 60)
    
    data = get_yandex_direct_data(
        base_url='https://your-domain.com/webHooks',
        account='myaccount',
        client='myclient',
        get_history=True,
        get_settings=True,
        timeout=300
    )
    
    print(f"\n=== Итоговые данные ===")
    print(f"История изменений: {len(data.get('history', []))} записей")
    print(f"Настройки стратегий: {len(data.get('settings', []))} кампаний")
```

---

## Рекомендации

### Производительность

**Синхронные методы:**
- **Валидация аккаунта**: 200-500 мс
- **Получение списка кампаний**: 500-1000 мс
- **Получение списка групп**: 500-1500 мс (зависит от количества кампаний)
- **Получение статистики кампаний**: 1-3 секунды (зависит от количества кампаний)
- **Получение статистики групп**: 1-4 секунды (зависит от количества групп)

**Асинхронные методы:**
- **Запуск процесса**: 50-200 мс (мгновенный ответ с processId)
- **Получение истории изменений**: 5-30 секунд (выполняется в фоне)
- **Получение настроек стратегий**: 5-30 секунд (выполняется в фоне)
- **Проверка статуса**: 50-100 мс
- **Получение результатов**: 50-200 мс

### Лимиты

**Синхронные методы:**
- Не вызывайте валидацию чаще 1 раза в минуту для одного аккаунта
- Рекомендуется запрашивать статистику не более 10 кампаний за раз
- Рекомендуется запрашивать статистику не более 20 групп за раз
- Кэшируйте результаты на стороне клиента

**Асинхронные методы:**
- Можно запускать параллельно несколько процессов для одного аккаунта
- Рекомендуется проверять статус не чаще 1 раза в 2 секунды
- Процессы автоматически удаляются через 24 часа после завершения
- Храните processId для возможности получения результатов

### Выбор между синхронными и асинхронными методами

**Используйте синхронные методы когда:**
- Нужен быстрый ответ (список кампаний, групп)
- Объем данных небольшой
- Требуется немедленный результат

**Используйте асинхронные методы когда:**
- Операция занимает много времени (история, настройки)
- Нужно выполнить несколько операций параллельно
- Важно не блокировать основной поток выполнения
- Требуется получить большой объем данных
---

## Поддержка

По вопросам использования API обращайтесь к документации:
- [Яндекс.Директ API](https://yandex.ru/dev/direct/doc/dg/concepts/about.html)
- [Яндекс.Директ Reports API](https://yandex.ru/dev/direct/doc/reports/about.html)

---

**Версия документации:** 2.0  
**Дата последнего обновления:** 2026-01-16

**Изменения в версии 2.0:**
- Добавлены асинхронные методы для получения истории изменений
- Добавлены асинхронные методы для получения настроек стратегий
- Добавлен готовый Python-класс `YandexDirectAsyncClient`
- Добавлены примеры использования асинхронных методов
- Обновлены рекомендации по производительности
