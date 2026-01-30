[← Spis treści](../README.md)

# Pola dodatkowe

System pól dodatkowych umożliwia definiowanie własnych pól w modelach CRM, Organize i innych. Pola konfiguruje się w ustawieniach konta i są automatycznie dostępne w formularzach, listach i API.

**Autoryzacja:** Konfiguracja wymaga `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Konfiguracja

Pola dodatkowe definiuje się przez [endpoint ustawień](account.md#ustawienia-settings) w `config` konta pod ścieżką `{moduł}.{model}.additional_fields`:

```
POST /account/set_field?field=config
Content-Type: application/json

{
  "key": "crm.client.additional_fields.field1",
  "value": {
    "type": "string",
    "label": "Nazwa alternatywna",
    "required": true,
    "default": "",
    "on_list": true
  }
}
```

## Typy pól

| Typ | Opis |
|-----|------|
| `string` | Tekst |
| `integer` | Liczba całkowita |
| `float` | Liczba zmiennoprzecinkowa |
| `date` | Data |
| `datetime` | Data i czas |
| `boolean` | Tak/Nie |
| `select` | Wybór z listy opcji |

## Opcje pola

### Wymagane

| Opcja | Typ | Opis |
|-------|-----|------|
| `type` | string | Typ pola (patrz tabela wyżej) |
| `label` | string | Etykieta wyświetlana w interfejsie |
| `required` | boolean | Czy pole jest wymagane |
| `default` | any | Wartość domyślna |
| `on_list` | boolean | Czy wyświetlać na liście |

### Opcjonalne

| Opcja | Typ | Opis |
|-------|-----|------|
| `options` | array | Tablica opcji dla pola typu `select` |
| `multiple` | boolean | Czy `select` pozwala na wielokrotny wybór |

## Przykład konfiguracji

```json
{
  "crm": {
    "client": {
      "additional_fields": {
        "nip": {
          "type": "string",
          "label": "NIP",
          "required": false,
          "default": "",
          "on_list": true
        },
        "credit_score": {
          "type": "integer",
          "label": "Scoring kredytowy",
          "required": false,
          "default": 0,
          "on_list": true
        },
        "category": {
          "type": "select",
          "label": "Kategoria",
          "required": true,
          "default": "standard",
          "on_list": true,
          "options": ["standard", "vip", "enterprise"],
          "multiple": false
        }
      }
    }
  }
}
```

## Obsługiwane modele

| Ścieżka config | Model |
|----------------|-------|
| `crm.client.additional_fields` | Klient CRM |
| `crm.contact.additional_fields` | Kontakt CRM |
| `organize.task.additional_fields` | Zadanie |

## Użycie w API

Po zdefiniowaniu pól dodatkowych, ich wartości są automatycznie dostępne w odpowiedziach API i można je ustawiać przy tworzeniu/aktualizacji obiektów:

```
POST /crm/clients.jsonContent-Type: application/json

{
  "client": {
    "name": "Firma ABC",
    "nip": "1234567890",
    "credit_score": 85,
    "category": "vip"
  }
}
```
