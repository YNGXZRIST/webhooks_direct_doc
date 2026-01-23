# Получение списка групп объявлений - getGroupsList

Получение списка групп объявлений для указанных кампаний.

## Endpoint
```
GET /webHooks/getGroupsList
```

## Параметры

| Параметр | Расположение | Обязательный | Тип | Описание |
|----------|--------------|--------------|-----|----------|
| `account` | query | Да | string | Логин аккаунта |
| `client` | query | Да | string | Логин клиента |
| `campaign_ids` | query | Да | JSON array | JSON-массив ID кампаний |

## Пример запроса

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

## Пример успешного ответа

```json
{
    "status": "ok",
    "groups": [
        {
            "Id": 111,
            "Name": "Group 1",
            "CampaignId": 111,
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        },
        {
            "Id": 222,
            "Name": "Group 2",
            "CampaignId": 111,
            "Status": "ACCEPTED",
            "ServingStatus": "ELIGIBLE"
        }
    ]
}
```

## Поля группы

| Поле | Тип | Описание |
|------|-----|----------|
| `Id` | integer | Уникальный идентификатор группы объявлений |
| `Name` | string | Название группы объявлений |
| `CampaignId` | integer | ID родительской кампании |
| `Status` | string | Статус группы |
| `ServingStatus` | string | Статус показа группы |

## Возможные ошибки

| Ошибка | Описание |
|--------|----------|
| `Параметры account и client обязательны` | Отсутствуют параметры в URL |
| `Параметр campaign_ids обязателен` | Не указаны ID кампаний |
