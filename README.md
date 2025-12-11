# API Яндекс.Директ - Документация

## Оглавление

1. [Общая информация](#общая-информация)
2. [Аутентификация](#аутентификация)
3. [Методы API](#методы-api)
    - [Валидация аккаунта](#1-валидация-аккаунта)
    - [Получение списка кампаний](#2-получение-списка-кампаний)
    - [Получение списка групп объявлений](#3-получение-списка-групп-объявлений)
    - [Получение статистики кампаний](#4-получение-статистики-кампаний)
    - [Получение статистики групп объявлений](#5-получение-статистики-групп-объявлений)
4. [Коды ошибок](#коды-ошибок)
5. [Примеры использования](#примеры-использования)

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
            "Id": 16433668,
            "Name": "Кошелек",
            "Status": "MODERATION",
            "State": "ARCHIVED"
        },
        {
            "Id": 16433669,
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
        'campaign_ids': json.dumps([16433668, 16433669])
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
            "Id": 4567890,
            "Name": "Группа 1",
            "CampaignId": 16433668,
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        },
        {
            "Id": 4567891,
            "Name": "Группа 2",
            "CampaignId": 16433668,
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

### 4. Получение статистики кампаний

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

#### Пример запроса

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [16433668, 16433669],
        'fields': ['expense','avg_pageviews', 'clicks', 'impressions', 'ctr', 'cpa'],
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
            "CampaignId": 16433668,
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
            "CampaignId": 16433669,
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

### 5. Получение статистики групп объявлений

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
        'group_ids': [4567890, 4567891],
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
            "AdGroupId": 4567890,
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
            "AdGroupId": 4567891,
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
get_campaign_statistics([16433668, 16433669])
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
        'campaign_ids': [16433668],
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

## Рекомендации

### Производительность

- **Валидация аккаунта**: 200-500 мс
- **Получение списка кампаний**: 500-1000 мс
- **Получение списка групп**: 500-1500 мс (зависит от количества кампаний)
- **Получение статистики кампаний**: 1-3 секунды (зависит от количества кампаний)
- **Получение статистики групп**: 1-4 секунды (зависит от количества групп)

### Лимиты

- Не вызывайте валидацию чаще 1 раза в минуту для одного аккаунта
- Рекомендуется запрашивать статистику не более 10 кампаний за раз
- Рекомендуется запрашивать статистику не более 20 групп за раз
- Кэшируйте результаты на стороне клиента
---

## Поддержка

По вопросам использования API обращайтесь к документации:
- [Яндекс.Директ API](https://yandex.ru/dev/direct/doc/dg/concepts/about.html)
- [Яндекс.Директ Reports API](https://yandex.ru/dev/direct/doc/reports/about.html)

---

**Версия документации:** 1.1
**Дата последнего обновления:** 2025-12-11
