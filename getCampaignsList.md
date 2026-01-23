# Получение списка кампаний - getCampaignsList

Получение списка рекламных кампаний для указанного аккаунта.

## Endpoint
```
GET /webHooks/getCampaignsList
```

## Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |
| `from` | query | Да | string | Начальная дата периода (формат: YYYY-MM-DD) |
| `to` | query | Да | string | Конечная дата периода (формат: YYYY-MM-DD) |

## Описание

Метод возвращает список кампаний, которые были активны в указанный период (`from` - `to`).
Для каждой кампании возвращается актуальная информация о статусе, состоянии и параметрах отслеживания.

## Пример запроса

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

## Пример успешного ответа

```json
{
    "status": "ok",
    "campaigns": [
        {
            "Id": 14446702,
            "Name": "Example Campaign 1",
            "Type": "TEXT_CAMPAIGN",
            "Status": "ACCEPTED",
            "State": "ON",
            "TextCampaign": {
                "TrackingParams": "utm_source=yandex&utm_medium=cpc&utm_campaign={campaign_name_lat}_{source_type}_{campaign_id}"
            }
        },
        {
            "Id": 14446741,
            "Name": "Example Campaign 2",
            "Type": "TEXT_CAMPAIGN",
            "Status": "ACCEPTED",
            "State": "ON",
            "TextCampaign": {
                "TrackingParams": null
            }
        }
    ]
}
```

## Поля кампании

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer/string | Уникальный идентификатор кампании |
| `Name` | string | Название рекламной кампании |
| `Type` | string | Тип кампании (TEXT_CAMPAIGN, UNKNOWN и т.д.) |
| `Status` | string | Статус кампании (ACCEPTED, MODERATION, UNKNOWN и т.д.) |
| `State` | string | Состояние кампании (ON, OFF, ARCHIVED, SUSPENDED, UNKNOWN) |
| `TextCampaign` | object | Параметры текстовой кампании (если применимо) |
| `TextCampaign.TrackingParams` | string/null | UTM-метки для отслеживания (может быть null) |

## Примечания

- Метод объединяет данные из отчетов (кампании, активные в период) с актуальной информацией о кампаниях
- Если кампания была в периоде, но сейчас удалена или недоступна, она будет иметь тип/статус/состояние "UNKNOWN"
- Параметры отслеживания (`TrackingParams`) могут быть `null`, если не настроены для кампании
