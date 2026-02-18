# API получения списка объявлений Яндекс.Директ

## Описание

Метод `getAdsList` предназначен для получения списка рекламных объявлений для указанных кампаний Яндекс.Директ. Метод возвращает детальную информацию об объявлениях, включая их статусы, состояния, типы и содержимое.

---

## Endpoint

```
GET /webHooks/getAdsList
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
| `campaign_ids` | Да | string (JSON) | JSON массив ID кампаний | `[100000001,100000002]` |

**Важно:** Параметр `campaign_ids` должен быть передан как JSON строка, содержащая массив чисел.

---

## Формат ответа

В примерах ниже используются только анонимизированные (тестовые) данные; реальные ID, тексты и хеши в документации не приводятся.

### Успешный ответ

```json
{
    "status": "ok",
    "ads": [
        {
            "Id": 10000000001,
            "CampaignId": 100000001,
            "AdGroupId": 2000000001,
            "Status": "DRAFT",
            "State": "OFF",
            "Type": "TEXT_AD",
            "TextAd": {
                "Text": "Пример текста объявления. Описание предложения.",
                "Title": "Пример заголовка объявления",
                "Title2": "пример второго заголовка",
                "VideoExtension": null,
                "AdImageHash": "xxxxxxxxxxxxxxxxxxxxxxxxx",
                "AdImageName": "Пример названия изображения",
                "AdExtensions": [
                    {
                        "AdExtensionId": 1000001,
                        "Type": "CALLOUT",
                        "CalloutText": "Пример уточнения 1"
                    },
                    {
                        "AdExtensionId": 1000002,
                        "Type": "CALLOUT",
                        "CalloutText": "Пример уточнения 2"
                    },
                    {
                        "AdExtensionId": 1000003,
                        "Type": "CALLOUT",
                        "CalloutText": "Пример уточнения 3"
                    }
                ]
            }
        },
        {
            "Id": 10000000002,
            "CampaignId": 100000001,
            "AdGroupId": 2000000001,
            "Status": "DRAFT",
            "State": "OFF",
            "Type": "TEXT_AD",
            "TextAd": {
                "Text": "Пример текста объявления",
                "Title": "example-domain.com",
                "Title2": "пример",
                "VideoExtension": null,
                "AdImageHash": null,
                "AdImageName": null,
                "AdExtensions": [
                    {
                        "AdExtensionId": 1000001,
                        "Type": "CALLOUT",
                        "CalloutText": "Пример уточнения"
                    }
                ]
            }
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

## Структура данных объявления

### Основные поля

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer | Уникальный идентификатор объявления |
| `CampaignId` | integer | ID кампании, к которой относится объявление |
| `AdGroupId` | integer | ID группы объявлений |
| `Status` | string | Статус объявления (см. таблицу статусов ниже) |
| `State` | string | Состояние объявления (см. таблицу состояний ниже) |
| `Type` | string | Тип объявления (TEXT_AD, IMAGE_AD, MOBILE_APP_AD и т.д.) |
| `TextAd` | object\|null | Объект с данными текстового объявления (присутствует для типа TEXT_AD) |

### Поля объекта TextAd

| Поле | Тип | Описание |
|------|-----|----------|
| `Text` | string | Текст объявления |
| `Title` | string | Заголовок объявления |
| `Title2` | string | Второй заголовок объявления |
| `VideoExtension` | string\|null | Расширение видео (если применимо) |
| `AdImageHash` | string\|null | Хеш изображения объявления (если применимо) |
| `AdImageName` | string\|null | Название изображения объявления (присутствует только если есть AdImageHash). Получается отдельным запросом к API изображений Яндекс.Директ |
| `AdExtensions` | array\|null | Массив расширений объявления. Для типа CALLOUT содержит уточнения (если применимо) |

### Поля объекта AdExtension

| Поле | Тип | Описание |
|------|-----|----------|
| `AdExtensionId` | integer | Уникальный идентификатор расширения объявления |
| `Type` | string | Тип расширения (CALLOUT, SITELINK, STRUCTURED_SNIPPET и т.д.) |
| `CalloutText` | string\|null | Текст уточнения (присутствует только для типа CALLOUT). Получается отдельным запросом к API расширений объявлений |

**Примечания:**
- Для расширений типа `CALLOUT` API автоматически получает тексты уточнений через отдельный запрос к сервису расширений объявлений Яндекс.Директ. Если запрос не удался или расширение не найдено, поле `CalloutText` может отсутствовать.
- Для изображений объявлений (если присутствует `AdImageHash`) API автоматически получает название изображения через отдельный запрос к сервису изображений Яндекс.Директ. Если запрос не удался или изображение не найдено, поле `AdImageName` может отсутствовать.

**Типы расширений:**
- `CALLOUT` - Уточнения (дополнительные уточнения к объявлению)
- `SITELINK` - Быстрые ссылки
- `STRUCTURED_SNIPPET` - Структурированные фрагменты
- `PRICE` - Цены
- `VIDEO` - Видео
- `CALL` - Телефон

---

## Статусы объявлений

| Статус | Описание |
|--------|----------|
| `DRAFT` | Черновик - объявление создано, но не отправлено на модерацию |
| `MODERATION` | На модерации - объявление проходит проверку |
| `PREACCEPTED` | Предварительно принято - объявление принято, но еще не запущено |
| `ACCEPTED` | Принято - объявление прошло модерацию и может быть запущено |
| `REJECTED` | Отклонено - объявление не прошло модерацию |

---

## Состояния объявлений

| Состояние | Описание |
|-----------|----------|
| `ON` | Включено - объявление активно и показывается |
| `OFF` | Выключено - объявление создано, но не показывается |
| `SUSPENDED` | Приостановлено - объявление временно остановлено |
| `ARCHIVED` | Архивировано - объявление перемещено в архив |

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
    'https://your-domain.com/webHooks/getAdsList',
    params=params
)

# Обработка ответа
if response.status_code == 200:
    data = response.json()
    
    if data.get('status') == 'ok':
        ads = data.get('ads', [])
        print(f"Найдено объявлений: {len(ads)}")
        
        for ad in ads:
            print(f"\nОбъявление ID: {ad['Id']}")
            print(f"  Кампания: {ad['CampaignId']}")
            print(f"  Группа: {ad['AdGroupId']}")
            print(f"  Статус: {ad['Status']}")
            print(f"  Состояние: {ad['State']}")
            print(f"  Тип: {ad['Type']}")
            
            if ad.get('TextAd'):
                text_ad = ad['TextAd']
                print(f"  Заголовок: {text_ad['Title']}")
                print(f"  Заголовок 2: {text_ad['Title2']}")
                print(f"  Текст: {text_ad['Text']}")
    else:
        print(f"Ошибка: {data.get('error')}")
else:
    print(f"HTTP ошибка: {response.status_code}")
```

### Пример 2: Обработка ошибок

```python
import requests
import json

def get_ads_list(api_key, account, client, campaign_ids):
    """
    Получение списка объявлений с обработкой ошибок
    
    Args:
        api_key: API ключ
        account: Логин аккаунта
        client: Логин клиента
        campaign_ids: Список ID кампаний
    
    Returns:
        Список объявлений или None в случае ошибки
    """
    params = {
        'apiKey': api_key,
        'account': account,
        'client': client,
        'campaign_ids': json.dumps(campaign_ids)
    }
    
    try:
        response = requests.get(
            'https://your-domain.com/webHooks/getAdsList',
            params=params,
            timeout=30
        )
        response.raise_for_status()
        
        data = response.json()
        
        if data.get('status') == 'ok':
            return data.get('ads', [])
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
ads = get_ads_list(
    api_key='your_api_key',
    account='example_account',
    client='example_client',
    campaign_ids=[100000001, 100000002]
)

if ads:
    print(f"Получено объявлений: {len(ads)}")
```

### Пример 3: Фильтрация объявлений по статусу

```python
import requests
import json

def get_active_ads(api_key, account, client, campaign_ids):
    """
    Получение только активных объявлений (состояние ON, статус ACCEPTED)
    """
    params = {
        'apiKey': api_key,
        'account': account,
        'client': client,
        'campaign_ids': json.dumps(campaign_ids)
    }
    
    response = requests.get(
        'https://your-domain.com/webHooks/getAdsList',
        params=params
    )
    
    data = response.json()
    
    if data.get('status') == 'ok':
        all_ads = data.get('ads', [])
        
        # Фильтруем только активные объявления
        active_ads = [
            ad for ad in all_ads 
            if ad.get('State') == 'ON' and ad.get('Status') == 'ACCEPTED'
        ]
        
        return active_ads
    
    return []

# Использование
active_ads = get_active_ads(
    api_key='your_api_key',
    account='example_account',
    client='example_client',
    campaign_ids=[100000001]
)

print(f"Активных объявлений: {len(active_ads)}")
for ad in active_ads:
    print(f"  - ID: {ad['Id']}, Заголовок: {ad['TextAd']['Title']}")
```

### Пример 4: Полный класс для работы с API

```python
import requests
import json
from typing import List, Dict, Optional

class YandexDirectAdsAPI:
    """Класс для работы с API получения объявлений Яндекс.Директ"""
    
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
    
    def get_ads_list(self, campaign_ids: List[int]) -> Optional[List[Dict]]:
        """
        Получение списка объявлений для указанных кампаний
        
        Args:
            campaign_ids: Список ID кампаний
        
        Returns:
            Список объявлений или None в случае ошибки
        """
        params = {
            'apiKey': self.api_key,
            'account': self.account,
            'client': self.client,
            'campaign_ids': json.dumps(campaign_ids)
        }
        
        try:
            response = requests.get(
                f'{self.base_url}/getAdsList',
                params=params,
                timeout=30
            )
            response.raise_for_status()
            
            data = response.json()
            
            if data.get('status') == 'ok':
                return data.get('ads', [])
            else:
                error_msg = data.get('error', 'Неизвестная ошибка')
                raise Exception(f"API ошибка: {error_msg}")
                
        except requests.exceptions.RequestException as e:
            raise Exception(f"Ошибка запроса: {e}")
    
    def get_ads_by_status(self, campaign_ids: List[int], status: str) -> List[Dict]:
        """
        Получение объявлений с определенным статусом
        
        Args:
            campaign_ids: Список ID кампаний
            status: Статус объявлений (DRAFT, MODERATION, ACCEPTED и т.д.)
        
        Returns:
            Список объявлений с указанным статусом
        """
        all_ads = self.get_ads_list(campaign_ids)
        if all_ads is None:
            return []
        
        return [ad for ad in all_ads if ad.get('Status') == status]
    
    def get_ads_by_state(self, campaign_ids: List[int], state: str) -> List[Dict]:
        """
        Получение объявлений с определенным состоянием
        
        Args:
            campaign_ids: Список ID кампаний
            state: Состояние объявлений (ON, OFF, SUSPENDED, ARCHIVED)
        
        Returns:
            Список объявлений с указанным состоянием
        """
        all_ads = self.get_ads_list(campaign_ids)
        if all_ads is None:
            return []
        
        return [ad for ad in all_ads if ad.get('State') == state]
    
    def get_active_ads(self, campaign_ids: List[int]) -> List[Dict]:
        """
        Получение только активных объявлений (State=ON, Status=ACCEPTED)
        
        Args:
            campaign_ids: Список ID кампаний
        
        Returns:
            Список активных объявлений
        """
        all_ads = self.get_ads_list(campaign_ids)
        if all_ads is None:
            return []
        
        return [
            ad for ad in all_ads 
            if ad.get('State') == 'ON' and ad.get('Status') == 'ACCEPTED'
        ]

# Использование
if __name__ == '__main__':
    # Инициализация клиента
    api = YandexDirectAdsAPI(
        api_key='your_api_key',
        account='example_account',
        client='example_client'
    )
    
    # Получение всех объявлений
    campaign_ids = [100000001, 100000002]
    all_ads = api.get_ads_list(campaign_ids)
    print(f"Всего объявлений: {len(all_ads)}")
    
    # Получение активных объявлений
    active_ads = api.get_active_ads(campaign_ids)
    print(f"Активных объявлений: {len(active_ads)}")
    
    # Получение объявлений на модерации
    moderation_ads = api.get_ads_by_status(campaign_ids, 'MODERATION')
    print(f"На модерации: {len(moderation_ads)}")
    
    # Получение выключенных объявлений
    off_ads = api.get_ads_by_state(campaign_ids, 'OFF')
    print(f"Выключенных объявлений: {len(off_ads)}")
```

---

## Возможные ошибки

| Ошибка | HTTP код | Описание | Решение |
|--------|----------|----------|---------|
| `not provided apiKey` | 200 | Не указан API ключ | Убедитесь, что параметр `apiKey` передан в запросе |
| `not provided account or client` | 200 | Не указан аккаунт или клиент | Проверьте наличие параметров `account` и `client` |
| `not provided campaign_ids` | 200 | Не указаны ID кампаний | Убедитесь, что параметр `campaign_ids` содержит валидный JSON массив |
| `Ошибка авторизации в Яндекс.Директ` | 200 | Неверные учетные данные | Проверьте корректность логина аккаунта и клиента |
| `У вас нет доступа к данному аккаунту` | 200 | Нет прав доступа | Получите доступ к аккаунту через интерфейс Яндекс.Директ |

**Примечание:** Все ошибки возвращаются с HTTP статусом 200, но содержат поле `status: "error"` в теле ответа.

---

## Ограничения и рекомендации

### Производительность

- **Время ответа:** 500-2000 мс (зависит от количества кампаний и объявлений)
- **Рекомендуемое количество кампаний:** до 50 кампаний за один запрос
- **Максимальное количество объявлений в ответе:** зависит от настроек сервера

### Лимиты

- Не рекомендуется делать более 10 запросов в секунду
- Кэшируйте результаты на стороне клиента
- Используйте фильтрацию по статусу/состоянию для уменьшения объема данных

### Безопасность

- Используйте HTTPS для всех запросов
- Храните API ключ в безопасном месте (переменные окружения, секреты)
- Не логируйте API ключ в открытом виде
- Регулярно обновляйте API ключ при необходимости

---

## Дополнительная информация

### Типы объявлений

API может возвращать объявления различных типов:

- `TEXT_AD` - Текстовое объявление
- `IMAGE_AD` - Объявление с изображением
- `MOBILE_APP_AD` - Объявление мобильного приложения
- `DYNAMIC_TEXT_AD` - Динамическое текстовое объявление
- `CALL_AD` - Объявление с кнопкой звонка

Для каждого типа объявления структура данных может отличаться. В текущей версии API подробная информация возвращается только для типа `TEXT_AD`.

### Связанные методы API

- `getCampaignsList` - Получение списка кампаний
- `getGroupsList` - Получение списка групп объявлений
- `getCampaignsStatistics` - Получение статистики кампаний

---

## Поддержка

По вопросам использования API обращайтесь к документации:
- [Яндекс.Директ API](https://yandex.ru/dev/direct/doc/dg/concepts/about.html)
- [Яндекс.Директ Reports API](https://yandex.ru/dev/direct/doc/reports/about.html)

---

**Версия документации:** 1.0  
**Дата последнего обновления:** 2025-02-18
