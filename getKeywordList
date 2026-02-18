# API получения списка ключевых фраз Яндекс.Директ

## Описание

Метод `getKeywordsList` предназначен для получения списка ключевых фраз (ключевых слов) для указанных кампаний и/или групп объявлений Яндекс.Директ. Метод возвращает детальную информацию о ключевых фразах, включая их статусы, состояния, ставки и другие параметры.

---

## Endpoint

```
GET /webHooks/getKeywordsList
```

**Базовый URL:**
```
https://your-domain.com/webHooks
```

---

## Аутентификация

Метод требует обязательные параметры для аутентификации:

| Параметр | Тип | Описание |
|----------|-----|----------|
| `apiKey` | string | API ключ для доступа к методу |
| `account` | string | Логин аккаунта Яндекс.Директ |
| `client` | string | Логин клиента |

---

## Параметры запроса

Все параметры передаются через query string (GET параметры).

| Параметр | Обязательный | Тип | Описание | Пример |
|----------|--------------|-----|----------|--------|
| `apiKey` | Да | string | API ключ для доступа | `your_api_key_here` |
| `account` | Да | string | Логин аккаунта Яндекс.Директ | `example_account` |
| `client` | Да | string | Логин клиента | `example_client` |
| `campaign_ids` | Нет* | string (JSON) | JSON массив ID кампаний | `[100000001,100000002]` |
| `group_ids` | Нет* | string (JSON) | JSON массив ID групп объявлений | `[2000000001,2000000002]` |

**Важно:** 
- Параметры `campaign_ids` и `group_ids` должны быть переданы как JSON строки, содержащие массивы чисел
- Хотя бы один из параметров `campaign_ids` или `group_ids` должен быть указан
- Можно указать оба параметра одновременно для получения ключевых фраз из указанных кампаний и групп

---

## Формат ответа

В примерах ниже используются только анонимизированные (тестовые) данные; реальные ID, тексты и значения в документации не приводятся.

### Успешный ответ

```json
{
    "status": "ok",
    "keywords": [
        {
            "Id": 10000000001,
            "Keyword": "пример ключевой фразы",
            "AdGroupId": 2000000001,
            "CampaignId": 100000001,
            "Bid": 300000,
            "State": "ON",
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        },
        {
            "Id": 10000000002,
            "Keyword": "---autotargeting",
            "AdGroupId": 2000000001,
            "CampaignId": 100000001,
            "Bid": 250000,
            "State": "ON",
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        }
    ]
}
```

### Ответ с ошибкой

```json
{
    "status": "error",
    "error": "not provided apiKey"
}
```

---

## Структура данных ключевой фразы

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer | Уникальный идентификатор ключевой фразы |
| `Keyword` | string | Текст ключевой фразы |
| `CampaignId` | integer | ID кампании, к которой относится ключевая фраза |
| `AdGroupId` | integer | ID группы объявлений, к которой относится ключевая фраза |
| `Status` | string | Статус ключевой фразы (см. таблицу статусов ниже) |
| `State` | string | Состояние ключевой фразы (см. таблицу состояний ниже) |
| `ServingStatus` | string | Статус показа ключевой фразы (см. таблицу статусов показа ниже) |
| `Bid` | integer | Ставка в микроединицах (1 рубль = 1 000 000 микроединиц) |

---

## Статусы ключевых фраз

| Статус | Описание |
|--------|----------|
| `DRAFT` | Черновик - ключевая фраза создана, но не отправлена на модерацию |
| `MODERATION` | На модерации - ключевая фраза проходит проверку |
| `PREACCEPTED` | Предварительно принято - ключевая фраза принята, но еще не запущена |
| `ACCEPTED` | Принято - ключевая фраза прошла модерацию и может быть запущена |
| `REJECTED` | Отклонено - ключевая фраза не прошла модерацию |

---

## Состояния ключевых фраз

| Состояние | Описание |
|-----------|----------|
| `ON` | Включено - ключевая фраза активна и участвует в показе |
| `OFF` | Выключено - ключевая фраза создана, но не участвует в показе |
| `SUSPENDED` | Приостановлено - ключевая фраза временно остановлена |

---

## Статусы показа ключевых фраз

| Статус показа | Описание |
|---------------|----------|
| `ELIGIBLE` | Готова к показу - ключевая фраза может участвовать в аукционе |
| `RARELY_SERVED` | Редко показывается - ключевая фраза показывается редко из-за низкой релевантности |
| `PENDING` | Ожидает модерации - ключевая фраза находится на модерации |
| `REJECTED` | Отклонена - ключевая фраза отклонена модерацией |

---

## Примеры использования

### Пример 1: Базовый запрос на Python

```python
import requests
import json

# Параметры запроса
params = {
    'apiKey': 'your_api_key_here',
    'account': 'example_account',
    'client': 'example_client',
    'campaign_ids': json.dumps([100000001, 100000002])  # JSON строка с массивом ID кампаний
}

# Выполнение запроса
response = requests.get(
    'https://your-domain.com/webHooks/getKeywordsList',
    params=params
)

# Обработка ответа
if response.status_code == 200:
    data = response.json()
    
    if data.get('status') == 'ok':
        keywords = data.get('keywords', [])
        print(f"Найдено ключевых фраз: {len(keywords)}")
        
        for keyword in keywords:
            print(f"\nКлючевая фраза ID: {keyword['Id']}")
            print(f"  Текст: {keyword['Keyword']}")
            print(f"  Кампания: {keyword['CampaignId']}")
            print(f"  Группа: {keyword['AdGroupId']}")
            print(f"  Статус: {keyword['Status']}")
            print(f"  Состояние: {keyword['State']}")
            print(f"  Статус показа: {keyword['ServingStatus']}")
            print(f"  Ставка: {keyword['Bid'] / 1000000} руб.")
    else:
        print(f"Ошибка: {data.get('error')}")
else:
    print(f"HTTP ошибка: {response.status_code}")
```

### Пример 2: Запрос по группам объявлений

```python
import requests
import json

# Параметры запроса для групп объявлений
params = {
    'apiKey': 'your_api_key_here',
    'account': 'example_account',
    'client': 'example_client',
    'group_ids': json.dumps([2000000001, 2000000002])  # JSON строка с массивом ID групп
}

response = requests.get(
    'https://your-domain.com/webHooks/getKeywordsList',
    params=params
)

data = response.json()

if data.get('status') == 'ok':
    keywords = data.get('keywords', [])
    print(f"Получено ключевых фраз: {len(keywords)}")
    
    # Группируем по группам объявлений
    keywords_by_group = {}
    for keyword in keywords:
        group_id = keyword['AdGroupId']
        if group_id not in keywords_by_group:
            keywords_by_group[group_id] = []
        keywords_by_group[group_id].append(keyword)
    
    for group_id, group_keywords in keywords_by_group.items():
        print(f"\nГруппа {group_id}: {len(group_keywords)} ключевых фраз")
```

### Пример 3: Запрос по кампаниям и группам одновременно

```python
import requests
import json

# Параметры запроса с указанием и кампаний, и групп
params = {
    'apiKey': 'your_api_key_here',
    'account': 'example_account',
    'client': 'example_client',
    'campaign_ids': json.dumps([100000001]),
    'group_ids': json.dumps([2000000001, 2000000002])
}

response = requests.get(
    'https://your-domain.com/webHooks/getKeywordsList',
    params=params
)

data = response.json()

if data.get('status') == 'ok':
    keywords = data.get('keywords', [])
    print(f"Всего ключевых фраз: {len(keywords)}")
```

### Пример 4: Обработка ошибок

```python
import requests
import json

def get_keywords_list(api_key, account, client, campaign_ids=None, group_ids=None):
    """
    Получение списка ключевых фраз с обработкой ошибок
    
    Args:
        api_key: API ключ
        account: Логин аккаунта
        client: Логин клиента
        campaign_ids: Список ID кампаний (опционально)
        group_ids: Список ID групп объявлений (опционально)
    
    Returns:
        Список ключевых фраз или None в случае ошибки
    """
    params = {
        'apiKey': api_key,
        'account': account,
        'client': client
    }
    
    if campaign_ids:
        params['campaign_ids'] = json.dumps(campaign_ids)
    if group_ids:
        params['group_ids'] = json.dumps(group_ids)
    
    try:
        response = requests.get(
            'https://your-domain.com/webHooks/getKeywordsList',
            params=params,
            timeout=30
        )
        response.raise_for_status()
        
        data = response.json()
        
        if data.get('status') == 'ok':
            return data.get('keywords', [])
        else:
            error_msg = data.get('error', 'Неизвестная ошибка')
            print(f"Ошибка API: {error_msg}")
            return None
            
    except requests.exceptions.Timeout:
        print("Ошибка: Превышено время ожидания запроса")
        return None
    except requests.exceptions.RequestException as e:
        print(f"Ошибка запроса: {e}")
        return None
    except json.JSONDecodeError:
        print("Ошибка: Неверный формат ответа от сервера")
        return None

# Использование
keywords = get_keywords_list(
    api_key='your_api_key',
    account='example_account',
    client='example_client',
    campaign_ids=[100000001, 100000002]
)

if keywords:
    print(f"Получено ключевых фраз: {len(keywords)}")
```

### Пример 5: Фильтрация ключевых фраз по статусу

```python
import requests
import json

def get_active_keywords(api_key, account, client, campaign_ids):
    """
    Получение только активных ключевых фраз (состояние ON, статус ACCEPTED)
    """
    params = {
        'apiKey': api_key,
        'account': account,
        'client': client,
        'campaign_ids': json.dumps(campaign_ids)
    }
    
    response = requests.get(
        'https://your-domain.com/webHooks/getKeywordsList',
        params=params
    )
    
    data = response.json()
    
    if data.get('status') == 'ok':
        all_keywords = data.get('keywords', [])
        
        # Фильтруем только активные ключевые фразы
        active_keywords = [
            kw for kw in all_keywords 
            if kw.get('State') == 'ON' and kw.get('Status') == 'ACCEPTED'
        ]
        
        return active_keywords
    
    return []

# Использование
active_keywords = get_active_keywords(
    api_key='your_api_key',
    account='example_account',
    client='example_client',
    campaign_ids=[100000001]
)

print(f"Активных ключевых фраз: {len(active_keywords)}")
for kw in active_keywords:
    print(f"  - ID: {kw['Id']}, Фраза: {kw['Keyword']}, Ставка: {kw['Bid'] / 1000000} руб.")
```

### Пример 6: Полный класс для работы с API

```python
import requests
import json
from typing import List, Dict, Optional

class YandexDirectKeywordsAPI:
    """Класс для работы с API получения ключевых фраз Яндекс.Директ"""
    
    def __init__(self, api_key: str, account: str, client: str, base_url: str = 'https://your-domain.com/webHooks'):
        """
        Инициализация клиента API
        
        Args:
            api_key: API ключ для доступа
            account: Логин аккаунта Яндекс.Директ
            client: Логин клиента
            base_url: Базовый URL API
        """
        self.api_key = api_key
        self.account = account
        self.client = client
        self.base_url = base_url
    
    def get_keywords_list(self, campaign_ids: List[int] = None, group_ids: List[int] = None) -> Optional[List[Dict]]:
        """
        Получение списка ключевых фраз для указанных кампаний и/или групп
        
        Args:
            campaign_ids: Список ID кампаний (опционально)
            group_ids: Список ID групп объявлений (опционально)
        
        Returns:
            Список ключевых фраз или None в случае ошибки
        """
        params = {
            'apiKey': self.api_key,
            'account': self.account,
            'client': self.client
        }
        
        if campaign_ids:
            params['campaign_ids'] = json.dumps(campaign_ids)
        if group_ids:
            params['group_ids'] = json.dumps(group_ids)
        
        try:
            response = requests.get(
                f'{self.base_url}/getKeywordsList',
                params=params,
                timeout=30
            )
            response.raise_for_status()
            
            data = response.json()
            
            if data.get('status') == 'ok':
                return data.get('keywords', [])
            else:
                error_msg = data.get('error', 'Неизвестная ошибка')
                raise Exception(f"API ошибка: {error_msg}")
                
        except requests.exceptions.RequestException as e:
            raise Exception(f"Ошибка запроса: {e}")
    
    def get_keywords_by_status(self, campaign_ids: List[int] = None, group_ids: List[int] = None, status: str = 'ACCEPTED') -> List[Dict]:
        """
        Получение ключевых фраз с определенным статусом
        
        Args:
            campaign_ids: Список ID кампаний (опционально)
            group_ids: Список ID групп объявлений (опционально)
            status: Статус ключевых фраз (DRAFT, MODERATION, ACCEPTED и т.д.)
        
        Returns:
            Список ключевых фраз с указанным статусом
        """
        all_keywords = self.get_keywords_list(campaign_ids, group_ids)
        if all_keywords is None:
            return []
        
        return [kw for kw in all_keywords if kw.get('Status') == status]
    
    def get_keywords_by_state(self, campaign_ids: List[int] = None, group_ids: List[int] = None, state: str = 'ON') -> List[Dict]:
        """
        Получение ключевых фраз с определенным состоянием
        
        Args:
            campaign_ids: Список ID кампаний (опционально)
            group_ids: Список ID групп объявлений (опционально)
            state: Состояние ключевых фраз (ON, OFF, SUSPENDED)
        
        Returns:
            Список ключевых фраз с указанным состоянием
        """
        all_keywords = self.get_keywords_list(campaign_ids, group_ids)
        if all_keywords is None:
            return []
        
        return [kw for kw in all_keywords if kw.get('State') == state]
    
    def get_active_keywords(self, campaign_ids: List[int] = None, group_ids: List[int] = None) -> List[Dict]:
        """
        Получение только активных ключевых фраз (State=ON, Status=ACCEPTED)
        
        Args:
            campaign_ids: Список ID кампаний (опционально)
            group_ids: Список ID групп объявлений (опционально)
        
        Returns:
            Список активных ключевых фраз
        """
        all_keywords = self.get_keywords_list(campaign_ids, group_ids)
        if all_keywords is None:
            return []
        
        return [
            kw for kw in all_keywords 
            if kw.get('State') == 'ON' and kw.get('Status') == 'ACCEPTED'
        ]

# Использование
if __name__ == '__main__':
    # Инициализация клиента
    api = YandexDirectKeywordsAPI(
        api_key='your_api_key',
        account='example_account',
        client='example_client'
    )
    
    # Получение всех ключевых фраз по кампаниям
    campaign_ids = [100000001, 100000002]
    all_keywords = api.get_keywords_list(campaign_ids=campaign_ids)
    print(f"Всего ключевых фраз: {len(all_keywords)}")
    
    # Получение активных ключевых фраз
    active_keywords = api.get_active_keywords(campaign_ids=campaign_ids)
    print(f"Активных ключевых фраз: {len(active_keywords)}")
    
    # Получение ключевых фраз на модерации
    moderation_keywords = api.get_keywords_by_status(campaign_ids=campaign_ids, status='MODERATION')
    print(f"На модерации: {len(moderation_keywords)}")
    
    # Получение ключевых фраз по группам
    group_ids = [2000000001, 2000000002]
    group_keywords = api.get_keywords_list(group_ids=group_ids)
    print(f"Ключевых фраз в группах: {len(group_keywords)}")
```

---

## Возможные ошибки

| Ошибка | HTTP код | Описание | Решение |
|--------|----------|----------|---------|
| `not provided apiKey` | 200 | Не указан API ключ | Убедитесь, что параметр `apiKey` передан в запросе |
| `not provided account or client` | 200 | Не указан аккаунт или клиент | Проверьте наличие параметров `account` и `client` |
| `not provided campaign_ids or group_ids` | 200 | Не указаны ID кампаний или групп | Убедитесь, что указан хотя бы один из параметров `campaign_ids` или `group_ids` |
| `Ошибка авторизации в Яндекс.Директ` | 200 | Неверные учетные данные | Проверьте корректность логина аккаунта и клиента |
| `У вас нет доступа к данному аккаунту` | 200 | Нет прав доступа | Получите доступ к аккаунту через интерфейс Яндекс.Директ |

**Примечание:** Все ошибки возвращаются с HTTP статусом 200, но содержат поле `status: "error"` в теле ответа.

---

## Ограничения и рекомендации

### Производительность

- **Время ответа:** 500-2000 мс (зависит от количества кампаний/групп и ключевых фраз)
- **Рекомендуемое количество кампаний:** до 50 кампаний за один запрос
- **Рекомендуемое количество групп:** до 100 групп за один запрос
- **Максимальное количество ключевых фраз в ответе:** зависит от настроек сервера

### Лимиты

- Не рекомендуется делать более 10 запросов в секунду
- Кэшируйте результаты на стороне клиента
- Используйте фильтрацию по статусу/состоянию для уменьшения объема данных

### Безопасность

- Используйте HTTPS для всех запросов
- Храните API ключ в безопасном месте (переменные окружения, секреты)
- Не логируйте API ключ в открытом виде
- Регулярно обновляйте API ключ при необходимости

### Особенности работы со ставками

Ставка (`Bid`) возвращается в микроединицах. Для конвертации в рубли:
```
Ставка в рублях = Bid / 1 000 000
```

Например, если `Bid = 300000`, то ставка составляет `0.30` рублей.

---

## Дополнительная информация

### Автотаргетинг

Ключевые фразы с текстом `---autotargeting` являются автоматическими ключевыми фразами, созданными системой Яндекс.Директ на основе содержимого сайта и объявлений.

### Связанные методы API

- `getCampaignsList` - Получение списка кампаний
- `getGroupsList` - Получение списка групп объявлений
- `getAdsList` - Получение списка объявлений
- `getCampaignsStatistics` - Получение статистики кампаний

---

## Поддержка

По вопросам использования API обращайтесь к документации:
- [Яндекс.Директ API](https://yandex.ru/dev/direct/doc/dg/concepts/about.html)
- [Яндекс.Директ Reports API](https://yandex.ru/dev/direct/doc/reports/about.html)

---

**Версия документации:** 1.0  
**Дата последнего обновления:** 2025-02-18
