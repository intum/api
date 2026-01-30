[← Spis treści](README.md)

# CRM

Zarządzanie relacjami z klientami - klienci, kontakty, deale, statusy.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Klienci (Clients)](#klienci-clients)
2. [Kontakty (Contacts)](#kontakty-contacts)
3. [Deale (Deals)](#deale-deals)
4. [Statusy (Statuses)](#statusy-statuses)
5. [Notatki (Notes)](#notatki-notes)

---

## Klienci (Clients)

Główny obiekt CRM - firma lub osoba. Agreguje kontakty, emaile, połączenia, zadania.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/crm/clients.json` | Lista klientów |
| GET | `/crm/clients/:id.json` | Szczegóły klienta |
| POST | `/crm/clients.json` | Utworzenie klienta |
| PATCH | `/crm/clients/:id.json` | Aktualizacja klienta |
| DELETE | `/crm/clients/:id.json` | Usunięcie klienta |
| POST | `/crm/clients/upsert.json` | Utwórz lub zaktualizuj (po external_id) |
| POST | `/crm/clients/:id/change_bulk_option.json` | Zmiana opcji |
| POST | `/crm/clients/change_bulk_option_multiple.json` | Zmiana opcji dla wielu |
| POST | `/crm/clients/merge_clients.json` | Scalenie klientów |
| GET | `/crm/clients/e/:external_id.json` | Wyszukiwanie po external_id |
| GET | `/crm/clients/e/:app_code/:external_id.json` | Wyszukiwanie po app_code i external_id |

### Pola klienta

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | nie | Nazwa (auto-generowana z telefonu/emaila jeśli nie podana) |
| `email` | string | nie | Email |
| `phone` | string | nie | Telefon |
| `description` | text | nie | Opis |
| `external_id` | string | nie | Zewnętrzne ID (do integracji) |
| `tags` | string | nie | Tagi (nazwy oddzielone przecinkami) |

### Utworzenie klienta

```
POST /crm/clients.jsonContent-Type: application/json

{
  "client": {
    "name": "Firma ABC",
    "email": "kontakt@firma.pl",
    "phone": "+48123456789",
    "tags": "vip,partner"
  }
}
```

### Upsert (utwórz lub zaktualizuj)

Wyszukuje klienta po `external_id` (i opcjonalnie `app_code`). Jeśli istnieje - aktualizuje, jeśli nie - tworzy nowego.

```
POST /crm/clients/upsert.jsonContent-Type: application/json

{
  "app_code": "moj-system",
  "external_id": "client-123",
  "client": {
    "name": "Firma ABC",
    "email": "kontakt@firma.pl"
  }
}
```

---

## Kontakty (Contacts)

Osoby powiązane z klientem (np. pracownicy firmy-klienta).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/crm/contacts.json` | Lista kontaktów |
| GET | `/crm/contacts/:id.json` | Szczegóły kontaktu |
| POST | `/crm/contacts.json` | Utworzenie kontaktu |
| PATCH | `/crm/contacts/:id.json` | Aktualizacja kontaktu |
| DELETE | `/crm/contacts/:id.json` | Usunięcie kontaktu |

### Pola kontaktu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | nie | Imię i nazwisko |
| `email` | string | nie | Email |
| `phone` | string | nie | Telefon |
| `client_id` | integer | nie | ID klienta nadrzędnego |

### Utworzenie kontaktu

```
POST /crm/contacts.jsonContent-Type: application/json

{
  "contact": {
    "name": "Jan Kowalski",
    "email": "jan@firma.pl",
    "phone": "+48111222333",
    "client_id": 1
  }
}
```

---

## Deale (Deals)

Szanse sprzedażowe powiązane z klientami.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/crm/deals.json` | Lista deali |
| GET | `/crm/deals/:id.json` | Szczegóły deala |
| POST | `/crm/deals.json` | Utworzenie deala |
| PATCH | `/crm/deals/:id.json` | Aktualizacja deala |
| DELETE | `/crm/deals/:id.json` | Usunięcie deala |
| GET | `/crm/deals/e/:app_code/:external_id.json` | Wyszukiwanie po external_id |

### Pola deala

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa deala |
| `client_id` | integer | nie | ID klienta |
| `status_id` | integer | nie | ID statusu CRM |
| `total_amount_net` | decimal | nie | Kwota netto |

### Utworzenie deala

```
POST /crm/deals.jsonContent-Type: application/json

{
  "deal": {
    "name": "Wdrożenie systemu",
    "client_id": 1,
    "status_id": 2,
    "total_amount_net": 50000.00
  }
}
```

---

## Statusy (Statuses)

Etapy procesu sprzedażowego.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/crm/statuses.json` | Lista statusów |
| GET | `/crm/statuses/:id.json` | Szczegóły statusu |
| POST | `/crm/statuses.json` | Utworzenie statusu |
| PATCH | `/crm/statuses/:id.json` | Aktualizacja statusu |
| DELETE | `/crm/statuses/:id.json` | Usunięcie statusu |
| POST | `/crm/statuses/:id/up` | Przesuń wyżej |
| POST | `/crm/statuses/:id/down` | Przesuń niżej |

### Pola statusu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa statusu |
| `description` | text | nie | Opis |
| `active` | boolean | nie | Czy aktywny |
| `closed` | boolean | nie | Czy zamknięty |
| `color` | string | nie | Kolor (hex) |
| `priority` | integer | nie | Kolejność |

---

## Notatki (Notes)

Notatki przypisane do klientów.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/crm/notes.json` | Lista notatek |
| GET | `/crm/notes/:id.json` | Szczegóły notatki |
| POST | `/crm/notes.json` | Utworzenie notatki |
| PATCH | `/crm/notes/:id.json` | Aktualizacja notatki |
| DELETE | `/crm/notes/:id.json` | Usunięcie notatki |
