[← Spis treści](README.md)

# Chat

Komunikacja w czasie rzeczywistym z klientami - przestrzenie czatu, widgety do osadzenia na stronach.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Przestrzenie (Spaces)](#przestrzenie-spaces)
2. [Wiadomości (Messages)](#wiadomości-messages)
3. [Widgety (Widgets)](#widgety-widgets)
4. [Szablony (Templates)](#szablony-templates)

---

## Przestrzenie (Spaces)

Przestrzeń czatu to rozmowa z klientem. Może być otwarta lub zamknięta.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/chat/spaces.json` | Lista przestrzeni |
| GET | `/chat/spaces/:id.json` | Szczegóły przestrzeni |
| POST | `/chat/spaces.json` | Utworzenie przestrzeni |
| PATCH | `/chat/spaces/:id.json` | Aktualizacja przestrzeni |
| DELETE | `/chat/spaces/:id.json` | Usunięcie przestrzeni |
| GET | `/chat/spaces/my_spaces.json` | Moje przestrzenie |
| POST | `/chat/spaces/:id/open.json` | Otwarcie przestrzeni |
| POST | `/chat/spaces/:id/close.json` | Zamknięcie przestrzeni |
| POST | `/chat/spaces/:id/add_client.json` | Przypisanie klienta CRM |
| POST | `/chat/spaces/:id/remove_client.json` | Usunięcie klienta CRM |
| POST | `/chat/spaces/:id/change_responsible.json` | Zmiana odpowiedzialnego |

### Pola przestrzeni

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `widget_id` | integer | tak | ID widgetu |
| `client_id` | integer | nie | ID klienta CRM |
| `name` | string | nie | Nazwa klienta |
| `email` | string | nie | Email klienta |

---

## Wiadomości (Messages)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/chat/messages.json` | Lista wiadomości |
| GET | `/chat/messages/:id.json` | Szczegóły wiadomości |
| POST | `/chat/messages.json` | Wysłanie wiadomości |
| PATCH | `/chat/messages/:id.json` | Aktualizacja wiadomości |
| DELETE | `/chat/messages/:id.json` | Usunięcie wiadomości |
| POST | `/chat/messages/:id/attach.json` | Dodanie załączników |

### Pola wiadomości

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `space_token` | string | tak | Token przestrzeni |
| `message` | text | tak | Treść wiadomości |
| `user_id` | integer | nie | ID nadawcy (operator) |
| `widget_id` | integer | nie | ID widgetu |

### Wysłanie wiadomości

```
POST /chat/messages.jsonContent-Type: application/json

{
  "message": {
    "space_token": "abc123",
    "message": "Witamy, jak możemy pomóc?"
  }
}
```

---

## Widgety (Widgets)

Widget czatu do osadzenia na zewnętrznych stronach. Obsługuje dark mode, godziny otwarcia, wiadomości offline, formularz danych klienta i załączniki.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/chat/widgets.json` | Lista widgetów |
| GET | `/chat/widgets/:id.json` | Szczegóły widgetu |
| POST | `/chat/widgets.json` | Utworzenie widgetu |
| PATCH | `/chat/widgets/:id.json` | Aktualizacja widgetu |
| DELETE | `/chat/widgets/:id.json` | Usunięcie widgetu |
| GET | `/chat/widget_data.json` | Dane widgetu (publiczny) |

### Pola widgetu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa widgetu |
| `token` | string | nie | Token (auto-generowany) |
| `title` | string | nie | Tytuł wyświetlany klientom |
| `kind` | string | nie | Typ widgetu |
| `responsible_id` | integer | nie | ID domyślnego operatora |

### Widget Data (publiczny endpoint)

Pobieranie danych widgetu dla osadzenia na stronie (bez autoryzacji):

```
GET /chat/widget_data.json?token=WIDGET_TOKEN
```

Odpowiedź:
```json
{
  "messages": [...],
  "title": "Pomoc",
  "description": "Jak możemy pomóc?",
  "active": true,
  "open": true,
  "messages_after_hours": true,
  "require_client_data": false,
  "allow_client_attachments": true
}
```

---

## Szablony (Templates)

Szablony szybkich odpowiedzi dla operatorów.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/chat/templates.json` | Lista szablonów |
| GET | `/chat/templates/:id.json` | Szczegóły szablonu |
| POST | `/chat/templates.json` | Utworzenie szablonu |
| PATCH | `/chat/templates/:id.json` | Aktualizacja szablonu |
| DELETE | `/chat/templates/:id.json` | Usunięcie szablonu |
