# API Яндекс.Директ - Документация

Полная документация для работы с API Яндекс.Директ. Методы позволяют получать информацию о кампаниях, группах объявлений, стратегиях, статистике и управлять асинхронными процессами.

## Навигация

### 📚 Основные разделы

1. **[Аутентификация](authentication.md)** - Как авторизоваться в API

### 📋 Методы API

#### Управление данными
- **[Валидация аккаунта](validateAccount.md)** - Проверка доступа к аккаунту
- **[Получение списка кампаний](getCampaignsList.md)** - Список рекламных кампаний
- **[Получение списка групп объявлений](getGroupsList.md)** - Группы объявлений в кампаниях
- **[Получение списка объявлений](getAdsListAPI.md)** - Список объявлений для указанных кампаний

#### Настройки и стратегии
- **[Получение стратегий кампаний](getCampaignStrategies.md)** - Настройки стратегий и бюджетов

#### Статистика
- **[Получение статистики кампаний](getCampaignsStatistics.md)** - Статистика по кампаниям с метриками
- **[Получение статистики групп объявлений](getGroupsStatistics.md)** - Статистика по группам объявлений
- **[Получение статистики аккаунта](getAccountStatistics.md)** - Статистика по всему аккаунту с группировкой
- **[Получение отфильтрованных площадок](getFilteredPlatforms.md)** - Фильтрация площадок по заданным критериям

#### Асинхронные процессы
- **[Асинхронные процессы](asyncProcesses.md)** - Запуск и управление фоновыми операциями
   - Запуск процесса (startYandexDirectFetch)
   - Получение статуса (getYandexDirectFetchStatus)
   - Получение результатов (getYandexDirectFetchResults)
   - Остановка процесса (stopYandexDirectFetch)

### 🚨 Справочная информация

- **[Коды ошибок](errors.md)** - Описание ошибок и способы их исправления

---

## Быстрый старт

### 1. Проверка доступа

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/validateYandexDirectAccount',
    params={'account': 'myaccount', 'client': 'myclient'}
)
print(response.json())
```

### 2. Получение списка кампаний

```python
import requests

response = requests.get(
    'https://your-domain.com/webHooks/getCampaignsList',
    params={
        'account': 'myaccount',
        'client': 'myclient',
        'from': '2026-01-01',
        'to': '2026-01-20'
    }
)
print(response.json())
```

### 3. Получение статистики

```python
import requests

response = requests.post(
    'https://your-domain.com/webHooks/getCampaignsStatistics',
    params={'account': 'myaccount', 'client': 'myclient'},
    json={
        'campaign_ids': [111, 222],
        'fields': ['expense', 'clicks', 'impressions'],
        'from': '2026-01-01',
        'to': '2026-01-31'
    }
)
print(response.json())
```

---

## Формат ответов

Все методы возвращают JSON в едином формате:

**Успешный ответ:**
```json
{
    "status": "ok",
    "data": {}
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

## Базовый URL

```
https://your-domain.com/webHooks
```

---

## Обязательные параметры

Почти все методы требуют два обязательных параметра:

| Параметр | Описание |
|----------|----------|
| `account` | Логин аккаунта Яндекс.Директ |
| `client` | Логин клиента |

---

## Поддерживаемые поля статистики

### Стандартные поля
- `impressions` - Количество показов
- `clicks` - Количество кликов
- `expense` - Расходы
- `bounces` - Отскоки
- `conversions` - Конверсии

### Вычисляемые поля
- `cpa` - Стоимость онлайн конверсии
- `cpl` - Стоимость оффлайн лида
- `cpc` - Стоимость клика
- `ctr` - Процент кликов
- `avg_pageviews` - Глубина просмотра

### Поля целей
- `online_conversions` - Онлайн конверсии
- `qualified_leads` - Квалифицированные лиды

Подробнее см. в документации методов статистики.

---

## Примеры интеграции

### Python
```python
import requests
import json

class DirectAPI:
    def __init__(self, base_url, account, client):
        self.base_url = base_url
        self.account = account
        self.client = client
    
    def get_campaigns(self, from_date, to_date):
        response = requests.get(
            f'{self.base_url}/getCampaignsList',
            params={
                'account': self.account,
                'client': self.client,
                'from': from_date,
                'to': to_date
            }
        )
        return response.json()
    
    def get_statistics(self, campaign_ids, fields, from_date, to_date):
        response = requests.post(
            f'{self.base_url}/getCampaignsStatistics',
            params={
                'account': self.account,
                'client': self.client
            },
            json={
                'campaign_ids': campaign_ids,
                'fields': fields,
                'from': from_date,
                'to': to_date
            }
        )
        return response.json()

# Использование
api = DirectAPI('https://your-domain.com/webHooks', 'myaccount', 'myclient')
campaigns = api.get_campaigns('2026-01-01', '2026-01-20')
print(campaigns)
```

### JavaScript
```javascript
const base_url = 'https://your-domain.com/webHooks';
const account = 'myaccount';
const client = 'myclient';

async function getCampaigns(fromDate, toDate) {
    const params = new URLSearchParams({
        account,
        client,
        from: fromDate,
        to: toDate
    });

    const response = await fetch(`${base_url}/getCampaignsList?${params}`);
    return await response.json();
}

async function getStatistics(campaignIds, fields, fromDate, toDate) {
    const response = await fetch(`${base_url}/getCampaignsStatistics?account=${account}&client=${client}`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            campaign_ids: campaignIds,
            fields: fields,
            from: fromDate,
            to: toDate
        })
    });
    return await response.json();
}

// Использование
getCampaigns('2026-01-01', '2026-01-20').then(data => console.log(data));
```

---

## Лимиты и ограничения

- Максимальный период запроса статистики: 2 года
- Максимальное количество целей: 100
- Максимальное количество кампаний: без ограничений
- Данные обновляются с задержкой в несколько часов

---

## Поддержка

Для вопросов и проблем обратитесь в службу поддержки или проверьте раздел [Коды ошибок](errors.md).

---

## История изменений

### v1.0
- Добавлены основные методы API
- Поддержка статистики кампаний и групп
- Асинхронные процессы
- Метод получения статистики аккаунта с агрегацией
