[← Spis treści](README.md)

# Konto

Zarządzanie kontem, użytkownikami, domenami, tokenami API i webhookami.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Konto (Account)](#konto-account)
2. [Użytkownicy (UserSettings)](#użytkownicy-usersettings)
3. [Role (Roles)](#role-roles)
4. [Grupy (Groups)](#grupy-groups)
5. [Działy (Departments)](#działy-departments)
6. [Tokeny API (ApiTokens)](#tokeny-api-apitokens)
7. [Domeny (Domains)](#domeny-domains)
8. [Webhooks](#webhooks)
9. [Ustawienia (Settings)](#ustawienia-settings)

---

## Konto (Account)

Konto wyznacza wycinek danych dla klienta. Działa pod własnym URL (np. `firma.intum.com`).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/show.json` | Szczegóły konta |
| PATCH | `/account/update.json` | Aktualizacja konta |
| POST | `/account/accounts.json` | Utworzenie nowego konta |

### Pola konta

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa konta |
| `description` | text | nie | Opis |
| `domain` | string | nie | Domena konta |
| `timezone` | string | nie | Strefa czasowa |
| `locale` | string | nie | Język: `pl`, `en`, `fr`, `cs`, `sk`, `de`, `es`, `uk` |
| `prefix` | string | nie | Prefix konta |

### Utworzenie konta

```
POST /account/accounts.json
Content-Type: application/json

{
  "account": {
    "name": "Moja Firma",
    "prefix": "mojafirma",
    "domain": "mojafirma.intum.com",
    "timezone": "Warsaw"
  },
  "user": {
    "email": "admin@firma.pl",
    "login": "admin",
    "password": "haslo123"
  }
}
```

Odpowiedź zawiera `api_token` do dalszej komunikacji.

---

## Użytkownicy (UserSettings)

Dane użytkownika w ramach konkretnego konta. Mogą nadpisywać globalne dane użytkownika (np. email w ramach konta).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/users.json` | Lista użytkowników |
| GET | `/account/users/:user_id.json` | Szczegóły użytkownika |
| POST | `/account/users.json` | Dodanie użytkownika |
| PATCH | `/account/users/:user_id.json` | Aktualizacja użytkownika |
| DELETE | `/account/users/:user_id.json` | Usunięcie użytkownika z konta |
| GET | `/account/profile.json` | Profil aktualnego użytkownika |
| POST | `/account/users/create_integrated_users.json` | Masowe tworzenie użytkowników |
| POST | `/account/users/sync_integrated_users.json` | Synchronizacja użytkowników |

### Pola użytkownika

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `email` | string | tak | Email |
| `name` | string | nie | Imię i nazwisko |
| `first_name` | string | nie | Imię |
| `last_name` | string | nie | Nazwisko |
| `password` | string | nie | Hasło (przy tworzeniu) |
| `role` | string | nie | Rola: `guest`, `user`, `admin` |
| `active` | boolean | nie | Czy aktywny |
| `external_id` | string | nie | Zewnętrzne ID (do integracji) |

### Dodanie użytkownika

```
POST /account/users.json
Content-Type: application/json

{
  "user_setting": {
    "email": "jan@firma.pl",
    "first_name": "Jan",
    "last_name": "Kowalski",
    "role": "user",
    "password": "haslo123"
  }
}
```

### Masowe tworzenie użytkowników

```
POST /account/users/create_integrated_users.json
Content-Type: application/json

{
  "users": [
    { "id": "ext-1", "email": "jan@firma.pl", "role": "user" },
    { "id": "ext-2", "email": "anna@firma.pl", "role": "admin" }
  ]
}
```

Odpowiedź: `{ "results": [...], "errors": [...] }`

### Synchronizacja użytkowników

```
POST /account/users/sync_integrated_users.json
Content-Type: application/json

{
  "users": [
    { "external_id": "ext-1", "email": "jan@firma.pl", "role": "user" }
  ]
}
```

Odpowiedź: `{ "results": [{ "external_id": "ext-1", "id": 5, "status": "ok", "action": "create" }], "errors": [] }`

---

## Role (Roles)

Role definiują uprawnienia użytkowników. System posiada wbudowane role (owner, admin, user, guest) oraz pozwala tworzyć własne role z wybranymi uprawnieniami.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/roles.json` | Lista ról |
| GET | `/account/roles/:id.json` | Szczegóły roli |
| POST | `/account/roles.json` | Utworzenie roli |
| PATCH | `/account/roles/:id.json` | Aktualizacja roli |
| DELETE | `/account/roles/:id.json` | Usunięcie roli |

### Pola roli

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa roli |
| `privileges` | array | tak | Lista uprawnień |

### Wbudowane role

| Rola | Opis |
|------|------|
| `owner` | Właściciel - pełny dostęp |
| `admin` | Administrator - zarządzanie kontem, użytkownikami, ustawieniami |
| `user` | Użytkownik - dostęp do modułów |
| `guest` | Gość - ograniczony dostęp |

### Utworzenie własnej roli

```
POST /account/roles.json
Content-Type: application/json

{
  "role": {
    "name": "Menedżer sprzedaży",
    "privileges": ["crm", "organize", "mail", "voip", "insight"]
  }
}
```

---

## Grupy (Groups)

Grupy łączą użytkowników - mogą być odpowiedzialne za skrzynki mailowe, foldery, widgety czatu i zadania.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/groups.json` | Lista grup |
| GET | `/account/groups/:id.json` | Szczegóły grupy |
| POST | `/account/groups.json` | Utworzenie grupy |
| PATCH | `/account/groups/:id.json` | Aktualizacja grupy |
| DELETE | `/account/groups/:id.json` | Usunięcie grupy |
| GET | `/account/groups/search.json` | Wyszukiwanie grup |

### Pola grupy

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa grupy (unikalna w ramach konta) |
| `description` | text | nie | Opis |
| `kind` | string | tak | Typ: `mail` |
| `active` | boolean | nie | Czy aktywna |
| `user_ids` | array | tak | ID użytkowników (minimum 1) |

### Utworzenie grupy

```
POST /account/groups.json
Content-Type: application/json

{
  "group": {
    "name": "Zespół wsparcia",
    "kind": "mail",
    "description": "Obsługa zgłoszeń klientów",
    "user_ids": [1, 2, 3]
  }
}
```

---

## Działy (Departments)

Działy organizacyjne z przypisanymi użytkownikami i skrzynkami mailowymi. Mogą mieć własny telefon i email.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/departments.json` | Lista działów |
| GET | `/account/departments/:id.json` | Szczegóły działu |
| POST | `/account/departments.json` | Utworzenie działu |
| PATCH | `/account/departments/:id.json` | Aktualizacja działu |
| DELETE | `/account/departments/:id.json` | Usunięcie działu |
| GET | `/account/departments/search.json` | Wyszukiwanie działów |

### Pola działu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa działu (unikalna w ramach konta) |
| `description` | string | nie | Opis |
| `user_setting_ids` | array | nie | ID użytkowników w dziale |
| `mailbox_ids` | array | nie | ID skrzynek mailowych |
| `department_fields` | object | nie | Dane działu (telefon, email) |
| `fields` | object | nie | Własne pola |

### Utworzenie działu

```
POST /account/departments.json
Content-Type: application/json

{
  "department": {
    "name": "Dział IT",
    "description": "Wsparcie techniczne",
    "user_setting_ids": [1, 5, 8],
    "mailbox_ids": [2],
    "department_fields": {
      "department_phone": "+48 123 456 789",
      "department_email": "it@firma.pl"
    }
  }
}
```

---

## Tokeny API (ApiTokens)

### Uprawnienia

- Tokeny dziedziczą uprawnienia użytkownika
- Można ograniczyć uprawnienia per token
- Można dalej ograniczyć w payload JWT


### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/api_tokens.json` | Lista tokenów |
| GET | `/account/api_tokens/:id.json` | Szczegóły tokenu |
| POST | `/account/api_tokens.json` | Utworzenie tokenu |
| PATCH | `/account/api_tokens/:id.json` | Aktualizacja tokenu |
| DELETE | `/account/api_tokens/:id.json` | Usunięcie tokenu |

### Pola tokenu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa tokenu |
| `active` | boolean | nie | Czy aktywny |
| `expires_at` | datetime | nie | Data wygaśnięcia |
| `kind` | string | nie | Typ: `api_token` (domyślny), `web_token` |
| `privileges` | array | nie | Ograniczenia uprawnień |

### Typy tokenów

| Typ | Użycie w URL | Użycie w Bearer | Generowanie JWT |
|-----|-------------|----------------|----------------|
| **api_token** | `?api_token=TOKEN` | `Authorization: Bearer TOKEN` | Tak |
| **web_token** | Nie | Tylko przez JWT | Tak |

**web_token** jest bezpieczniejszy - wymusza użycie JWT (tokenu czasowego, ważnego 24h):

```
Authorization: Bearer token=jwt1.jwt2.jwt3, kind=jwt
```

---

## Domeny (Domains)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/domains.json` | Lista domen |
| GET | `/account/domains/:id.json` | Szczegóły domeny |
| POST | `/account/domains.json` | Dodanie domeny |
| PATCH | `/account/domains/:id.json` | Aktualizacja domeny |
| DELETE | `/account/domains/:id.json` | Usunięcie domeny |
| GET | `/account/domains/:id/set_primary` | Ustawienie jako główna |

### Pola domeny

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `domain` | string | tak | Nazwa domeny |
| `description` | text | nie | Opis |
| `kind` | string | nie | Typ domeny |
| `active` | boolean | nie | Czy aktywna |

### Weryfikacja domeny

Domena jest weryfikowana poprzez przekierowanie IP na serwery Intum.

---

## Webhooks

Konfiguracja webhooków do powiadamiania zewnętrznych usług o zdarzeniach.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/account/webhooks.json` | Lista webhooków |
| GET | `/account/webhooks/:id.json` | Szczegóły webhooka |
| POST | `/account/webhooks.json` | Utworzenie webhooka |
| PATCH | `/account/webhooks/:id.json` | Aktualizacja webhooka |
| DELETE | `/account/webhooks/:id.json` | Usunięcie webhooka |

### Pola webhooka

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `url` | string | tak | Adres endpointa |
| `source_type` | string | tak | Typ obiektu emitującego zdarzenie |
| `kind` | string | tak | Akcja: `create`, `update`, `destroy` |
| `api_token` | string | nie | Token do uwierzytelniania |
| `active` | boolean | nie | Czy webhook aktywny |

### Utworzenie webhooka

```
POST /account/webhooks.json
Content-Type: application/json

{
  "webhook": {
    "url": "https://example.com/webhook",
    "source_type": "Organize::Task",
    "kind": "create",
    "active": true
  }
}
```

Payload webhooka zawiera dane obiektu w formacie JSON.

---

## Ustawienia (Settings)

Uniwersalny endpoint do zmiany wartości w konfiguracji konta.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| POST | `/account/set_field` | Zmiana wartości w fields/config |

### Parametry

| Parametr | Opis |
|----------|------|
| `key` | Ścieżka klucza (np. `mail.inbox.per_page`) |
| `value` | Nowa wartość |
| `target` | Cel: `account` (domyślny), `user_setting`, `mail_setting` |
| `field` | Pole: `fields` (domyślny), `config` |
| `action_type` | Akcja: `add` (dodaj do tablicy), `remove` (usuń z tablicy) |

### Przykłady

```
POST /account/set_field?key=mail.inbox.per_page&value=50
POST /account/set_field?key=account.settings.modules&action_type=add&value=organize
POST /account/set_field?target=user_setting&key=intum.dashboard.view&value=organize
```
