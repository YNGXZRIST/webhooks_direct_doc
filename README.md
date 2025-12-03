# API Яндекс.Директ - Документация

## Оглавление

1. [Общая информация](#общая-информация)
2. [Аутентификация](#аутентификация)
3. [Методы API](#методы-api)
   - [Валидация аккаунта](#1-валидация-аккаунта)
   - [Получение списка кампаний](#2-получение-списка-кампаний)
   - [Получение статистики кампаний](#3-получение-статистики-кампаний)
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

```bash
curl -X POST "https://your-domain.com/webHooks/validateYandexDirectAccount?account=myaccount&client=myclient"
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

```bash
curl -X GET "https://your-domain.com/webHooks/getCampaignsList?account=myaccount&client=myclient"
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

### 3. Получение статистики кампаний

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
| `cpa` | CPA - стоимость онлайн конверсии | `online_goals` |
| `cpl` | CPL - стоимость оффлайн лида | `offline_goals` |
| `cpc` | CPC - стоимость конверсии (все цели) | `online_goals` или `offline_goals` |
| `online_conversions` | Детализация онлайн конверсий | `online_goals` |
| `qualified_leads` | Детализация оффлайн лидов | `offline_goals` |

#### Пример запроса

```bash
curl -X POST "https://your-domain.com/webHooks/getCampaignsStatistics?account=myaccount&client=myclient" \
  -H "Content-Type: application/json" \
  -d '{
    "campaign_ids": [16433668, 16433669],
    "fields": ["expense", "clicks", "impressions", "ctr", "cpa"],
    "from": "2024-01-01",
    "to": "2024-01-31",
    "online_goals": [12345, 67890]
  }'
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

```javascript
async function getCampaigns() {
    const account = 'myaccount';
    const client = 'myclient';
    const baseUrl = 'https://your-domain.com/webHooks';

    // Шаг 1: Валидация аккаунта
    const validateResponse = await fetch(
        `${baseUrl}/validateYandexDirectAccount?account=${account}&client=${client}`,
        { method: 'POST' }
    );
    const validateData = await validateResponse.json();

    if (!validateData.valid) {
        console.error('Аккаунт невалидный:', validateData.error);
        return;
    }

    // Шаг 2: Получение списка кампаний
    const campaignsResponse = await fetch(
        `${baseUrl}/getCampaignsList?account=${account}&client=${client}`
    );
    const campaignsData = await campaignsResponse.json();

    console.log('Кампании:', campaignsData.campaigns);
    return campaignsData.campaigns;
}
```

### Пример 2: Получение статистики по нескольким кампаниям

```javascript
async function getCampaignStatistics(campaignIds) {
    const account = 'myaccount';
    const client = 'myclient';

    const response = await fetch(
        `https://your-domain.com/webHooks/getCampaignsStatistics?account=${account}&client=${client}`,
        {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                campaign_ids: campaignIds,
                fields: ['expense', 'clicks', 'impressions', 'ctr', 'cpa'],
                from: '2024-01-01',
                to: '2024-01-31',
                online_goals: [12345]
            })
        }
    );

    const data = await response.json();

    if (data.status === 'ok') {
        data.statistics.forEach(stat => {
            console.log(`Кампания ${stat.CampaignId}:`);
            console.log(`  Расход: ${stat.data.expense} руб.`);
            console.log(`  Клики: ${stat.data.clicks}`);
            console.log(`  CTR: ${stat.data.ctr}%`);
            console.log(`  CPA: ${stat.data.cpa} руб.`);
        });
    } else {
        console.error('Ошибка:', data.error);
    }
}
```

### Пример 3: Полный цикл работы с API (PHP)

```php
<?php

class YandexDirectAPI
{
    private $baseUrl = 'https://your-domain.com/webHooks';
    private $account;
    private $client;

    public function __construct($account, $client)
    {
        $this->account = $account;
        $this->client = $client;
    }

    private function makeRequest($endpoint, $method = 'GET', $body = null)
    {
        $url = "{$this->baseUrl}/{$endpoint}?account={$this->account}&client={$this->client}";

        $options = [
            'http' => [
                'method' => $method,
                'header' => 'Content-Type: application/json'
            ]
        ];

        if ($body) {
            $options['http']['content'] = json_encode($body);
        }

        $context = stream_context_create($options);
        $response = file_get_contents($url, false, $context);

        return json_decode($response, true);
    }

    public function validateAccount()
    {
        $result = $this->makeRequest('validateYandexDirectAccount', 'POST');
        return $result['valid'] ?? false;
    }

    public function getCampaigns()
    {
        $result = $this->makeRequest('getCampaignsList');
        return $result['campaigns'] ?? [];
    }

    public function getStatistics($campaignIds, $from, $to, $fields = ['expense', 'clicks'])
    {
        $body = [
            'campaign_ids' => $campaignIds,
            'fields' => $fields,
            'from' => $from,
            'to' => $to
        ];

        $result = $this->makeRequest('getCampaignsStatistics', 'POST', $body);
        return $result['statistics'] ?? [];
    }
}

// Использование
$api = new YandexDirectAPI('myaccount', 'myclient');

// Проверяем доступ
if ($api->validateAccount()) {
    // Получаем список кампаний
    $campaigns = $api->getCampaigns();

    // Получаем ID первых 5 кампаний
    $campaignIds = array_slice(array_column($campaigns, 'Id'), 0, 5);

    // Получаем статистику
    $statistics = $api->getStatistics(
        $campaignIds,
        '2024-01-01',
        '2024-01-31',
        ['expense', 'clicks', 'impressions', 'ctr']
    );

    print_r($statistics);
} else {
    echo "Ошибка доступа к аккаунту";
}
```

### Пример 4: Получение статистики с целями

```bash
#!/bin/bash

ACCOUNT="myaccount"
CLIENT="myclient"
BASE_URL="https://your-domain.com/webHooks"

# Получение статистики с расчетом CPA и CPL
curl -X POST "${BASE_URL}/getCampaignsStatistics?account=${ACCOUNT}&client=${CLIENT}" \
  -H "Content-Type: application/json" \
  -d '{
    "campaign_ids": [16433668],
    "fields": ["expense", "clicks", "cpa", "cpl", "online_conversions", "qualified_leads"],
    "from": "2024-01-01",
    "to": "2024-01-31",
    "online_goals": [12345, 67890],
    "offline_goals": [11111, 22222]
  }'
```

---

## Рекомендации

### Производительность

- **Валидация аккаунта**: 200-500 мс
- **Получение списка кампаний**: 500-1000 мс
- **Получение статистики**: 1-3 секунды (зависит от количества кампаний)

### Лимиты

- Не вызывайте валидацию чаще 1 раза в минуту для одного аккаунта
- Рекомендуется запрашивать статистику не более 10 кампаний за раз
- Кэшируйте результаты на стороне клиента

### Безопасность

- Используйте HTTPS для всех запросов
- Не передавайте учетные данные в теле запроса
- Логируйте все ошибки для отладки

---

## Поддержка

По вопросам использования API обращайтесь к документации:
- [Яндекс.Директ API](https://yandex.ru/dev/direct/doc/dg/concepts/about.html)
- [Яндекс.Директ Reports API](https://yandex.ru/dev/direct/doc/reports/about.html)

---

**Версия документации:** 1.0
**Дата последнего обновления:** 2025-12-03
