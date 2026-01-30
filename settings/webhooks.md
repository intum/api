[← Spis treści](../README.md)

# Webhooks

System webhooków powiadamia zewnętrzne usługi o zdarzeniach w platformie Intum (np. utworzenie zadania, aktualizacja klienta).

**Autoryzacja:** Konfiguracja webhooków wymaga `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## API

Webhooks konfiguruje się przez moduł [Konto](account.md#webhooks):

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/webhooks.json` | Lista webhooków |
| GET | `/account/webhooks/:id.json` | Szczegóły webhooka |
| POST | `/account/webhooks.json` | Utworzenie webhooka |
| PATCH | `/account/webhooks/:id.json` | Aktualizacja webhooka |
| DELETE | `/account/webhooks/:id.json` | Usunięcie webhooka |

## Konfiguracja

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `url` | string | tak | Adres endpointa do wywołania |
| `source_type` | string | tak | Typ obiektu emitującego zdarzenie |
| `kind` | string | tak | Akcja: `create`, `update`, `destroy` lub niestandardowa |
| `api_token` | string | nie | Token do uwierzytelniania requestów |
| `active` | boolean | nie | Czy webhook aktywny |

## Utworzenie webhooka

```
POST /account/webhooks.jsonContent-Type: application/json

{
  "webhook": {
    "url": "https://example.com/webhook",
    "source_type": "Organize::Task",
    "kind": "create",
    "active": true
  }
}
```

## Payload

Gdy zdarzenie nastąpi, webhook wyśle POST na skonfigurowany URL z danymi obiektu w formacie JSON:

```json
{
  "id": 123,
  "title": "Nowe zadanie",
  "status_id": 1,
  "created_at": "2026-01-30T12:00:00Z"
}
```

## Dostępne akcje

| Akcja | Opis |
|-------|------|
| `create` | Po utworzeniu obiektu |
| `update` | Po aktualizacji obiektu |
| `destroy` | Po usunięciu obiektu |

Dostępne kombinacje `source_type` + `kind` zależą od modelu. Lista dostępna w interfejsie konfiguracji webhooków.
