[← Spis treści](README.md)

# AppStore

Marketplace aplikacji i dodatków. Aplikacje tworzone za pomocą [InConnector](inconnector.md) lub [Noe](noe-intum.md) mogą być publikowane jako wtyczki lub płatne dodatki i instalowane w systemach klientów.

**Autoryzacja:** Endpoint publiczny nie wymaga autoryzacji. Endpoint klienta wymaga JWT. Endpoint administracyjny wymaga `api_token`.

## Spis treści

1. [Jak to działa](#jak-to-działa)
2. [Aplikacje (Apps)](#aplikacje-apps)
3. [Katalog publiczny](#katalog-publiczny)
4. [Katalog klienta (JWT)](#katalog-klienta-jwt)
5. [Instalacja i uruchamianie](#instalacja-i-uruchamianie)
6. [Klienci (Clients)](#klienci-clients)
7. [Kategorie (Categories)](#kategorie-categories)
8. [Tagi (Tags)](#tagi-tags)
9. [Zainstalowane aplikacje (InstalledApps)](#zainstalowane-aplikacje-installedapps)
10. [Widoczność (AppSubjects)](#widoczność-appsubjects)
11. [Konteksty](#konteksty)

---

## Jak to działa

AppStore umożliwia tworzenie i dystrybucję aplikacji do zewnętrznych systemów:

1. **Tworzysz aplikację** za pomocą InConnector (automatyzacje, integracje) lub Noe (interfejs użytkownika)
2. **Publikujesz** ją w AppStore z opisem, logo, kategorią i ceną
3. **Klienci** przeglądają katalog i instalują wybrane aplikacje w swoich systemach
4. **System śledzi** instalacje, uruchomienia i statystyki

### Typy aplikacji

| Typ (`kind`) | Opis | Zastosowanie |
|-------------|------|--------------|
| `inconnector` | Aplikacja InConnector | Automatyzacje, integracje z API, przepływy danych |
| `html` | Samodzielna strona HTML | Kalkulatory, widgety, narzędzia |
| `external_link` | Link do zewnętrznej strony | Przekierowanie do usługi |

### Przykład: Wtyczka do automatycznego importu faktur

```json
{
  "app": {
    "name": "Auto-import faktur z KSeF",
    "code": "ksef-auto-import",
    "kind": "inconnector",
    "description": "Automatycznie pobiera faktury z KSeF i importuje do systemu",
    "author": "Moja Firma",
    "content": "{konektor i flow InConnector}",
    "active": true,
    "tags": ["ksef", "faktury", "import"]
  }
}
```

### Przykład: Płatny kalkulator jako dodatek

```json
{
  "app": {
    "name": "Kalkulator marży",
    "code": "kalkulator-marzy",
    "kind": "html",
    "description": "Kalkulator marży i narzutów z eksportem do PDF",
    "content_show": "<div>...</div>",
    "restricted_to_plans": ["pro", "enterprise"],
    "active": true
  }
}
```

---

## Aplikacje (Apps)

### API (administracja)

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/apps.json` | Lista aplikacji |
| GET | `/appstore/apps/:id.json` | Szczegóły aplikacji |
| POST | `/appstore/apps.json` | Utworzenie aplikacji |
| PATCH | `/appstore/apps/:id.json` | Aktualizacja aplikacji |
| DELETE | `/appstore/apps/:id.json` | Usunięcie aplikacji |
| GET | `/appstore/apps/:id/attachments` | Lista załączników |
| GET | `/appstore/apps/:id/images` | Lista obrazów |

### Pola aplikacji

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa aplikacji |
| `code` | string | tak | Unikalny kod |
| `description` | string | tak | Opis (dla administratorów) |
| `description_public` | string | nie | Opis publiczny (dla klientów) |
| `kind` | string | tak | Typ: `inconnector`, `html`, `external_link` |
| `author` | string | nie | Autor |
| `content` | text | nie | Treść/kod aplikacji (wymagane dla `inconnector`) |
| `content_show` | text | nie | Treść wyświetlana w szczegółach |
| `content_show_public` | text | nie | Treść publiczna |
| `external_link` | string | nie | URL (dla `external_link`) |
| `action_url` | string | nie | Własny URL akcji |
| `action_name` | string | nie | Tekst przycisku akcji |
| `priority` | integer | nie | Kolejność wyświetlania |
| `active` | boolean | nie | Czy aktywna |
| `suggestable` | boolean | nie | Czy sugerowana |
| `context` | array | nie | Konteksty wyświetlania |
| `tags` | array | nie | Tagi |
| `logo` | file | nie | Logo aplikacji |
| `install_recipe` | string | nie | Instrukcja instalacji |
| `uninstall_recipe` | string | nie | Instrukcja deinstalacji |
| `restricted_to_plans` | array | nie | Ograniczenie do planów |
| `restricted_to_privileges` | array | nie | Wymagane uprawnienia |
| `fields` | object | nie | Konfiguracja (przyciski, ustawienia) |

### Utworzenie aplikacji

```
POST /appstore/apps.json
Content-Type: application/json

{
  "app": {
    "name": "Moja wtyczka",
    "code": "moja-wtyczka",
    "kind": "inconnector",
    "description": "Opis dla administratorów",
    "description_public": "Opis widoczny w sklepie",
    "author": "Jan Kowalski",
    "active": true,
    "tags": ["integracja", "faktury"]
  }
}
```

---

## Katalog publiczny

Publiczny katalog aplikacji dostępny bez autoryzacji - do osadzenia na stronie marketingowej.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/appstore_public.json` | Lista regionów |
| GET | `/appstore/appstore_public/:region.json` | Lista aplikacji w regionie |
| GET | `/appstore/appstore_public/:region/:code.json` | Szczegóły aplikacji |
| GET | `/appstore/appstore_public/:region/c/:category_code.json` | Aplikacje w kategorii |

### Przykład

```
GET /appstore/appstore_public/pl.json
```

Zwraca listę aplikacji dostępnych w regionie `pl` z publicznymi opisami i logo.

---

## Katalog klienta (JWT)

Katalog dla zalogowanych klientów - z możliwością instalacji, uruchamiania i zarządzania aplikacjami.

**Autoryzacja:** Token JWT generowany z `jwt_secret` klienta.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/appstore.json?jwt=TOKEN` | Lista dostępnych aplikacji |
| GET | `/appstore/appstore/:id.json?jwt=TOKEN` | Szczegóły aplikacji |
| GET | `/appstore/appstore/:id/run?jwt=TOKEN` | Uruchomienie aplikacji |
| GET | `/appstore/appstore/:id/install?jwt=TOKEN` | Instalacja aplikacji |
| GET | `/appstore/appstore/:id/uninstall?jwt=TOKEN` | Deinstalacja aplikacji |
| PATCH | `/appstore/appstore/:id/update_expiration?jwt=TOKEN` | Zmiana daty wygaśnięcia |
| PATCH | `/appstore/appstore/:id/block_client?jwt=TOKEN` | Zablokowanie aplikacji |
| GET | `/appstore/appstore/blocked_apps?jwt=TOKEN` | Lista zablokowanych aplikacji |
| POST | `/appstore/appstore/get_context_apps?jwt=TOKEN` | Aplikacje dla kontekstu |

### JWT

Token JWT generowany z `jwt_secret` klienta. Payload może zawierać:

```json
{
  "client_code": "moj-system",
  "exp": 1738300800,
  "privileges": ["admin"],
  "plan": "pro",
  "plan_paid": true,
  "user": { "email": "jan@firma.pl" }
}
```

---

## Instalacja i uruchamianie

### Proces instalacji

1. Klient wywołuje `GET /appstore/appstore/:id/install?jwt=TOKEN`
2. System sprawdza uprawnienia i plan klienta
3. Walidacja dostawcy (vendor) aplikacji
4. Utworzenie rekordu `InstalledApp`
5. Aplikacja gotowa do uruchomienia

### Uruchomienie

```
GET /appstore/appstore/:id/run?jwt=TOKEN
```

Zwraca wyrenderowaną treść aplikacji z kontekstem klienta (zmienne `{{client.*}}`, `{{name}}`, `{{code}}`).

### Deinstalacja

```
GET /appstore/appstore/:id/uninstall?jwt=TOKEN
```

---

## Klienci (Clients)

Klient AppStore reprezentuje zewnętrzny system, który korzysta z katalogu aplikacji.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/clients.json` | Lista klientów |
| GET | `/appstore/clients/:id.json` | Szczegóły klienta |
| POST | `/appstore/clients.json` | Utworzenie klienta |
| PATCH | `/appstore/clients/:id.json` | Aktualizacja klienta |
| DELETE | `/appstore/clients/:id.json` | Usunięcie klienta |

### Pola klienta

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod klienta |
| `name` | string | nie | Nazwa |
| `email` | string | nie | Email kontaktowy |
| `kind` | string | nie | Typ klienta |
| `active` | boolean | nie | Czy aktywny |
| `jwt_secret` | string | tak | Klucz do generowania JWT |
| `fields` | object | nie | Konfiguracja (InConnector URL, zablokowane aplikacje) |

### Utworzenie klienta

```
POST /appstore/clients.json
Content-Type: application/json

{
  "client": {
    "code": "moj-system",
    "name": "Mój System CRM",
    "email": "admin@system.pl",
    "jwt_secret": "tajny-klucz-jwt",
    "active": true
  }
}
```

---

## Kategorie (Categories)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/categories.json` | Lista kategorii |
| GET | `/appstore/categories/:id.json` | Szczegóły kategorii |
| POST | `/appstore/categories.json` | Utworzenie kategorii |
| PATCH | `/appstore/categories/:id.json` | Aktualizacja kategorii |
| DELETE | `/appstore/categories/:id.json` | Usunięcie kategorii |

### Pola kategorii

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod |
| `name` | string | tak | Nazwa |
| `kind` | string | nie | Region/język (np. `pl`, `en`) |
| `description` | string | nie | Opis |
| `priority` | decimal | nie | Kolejność wyświetlania |

---

## Tagi (Tags)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/tags.json` | Lista tagów |
| GET | `/appstore/tags/:id.json` | Szczegóły tagu |
| POST | `/appstore/tags.json` | Utworzenie tagu |
| PATCH | `/appstore/tags/:id.json` | Aktualizacja tagu |
| DELETE | `/appstore/tags/:id.json` | Usunięcie tagu |

### Pola tagu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa |
| `code` | string | nie | Kod |
| `color` | string | nie | Kolor |
| `active` | boolean | nie | Czy aktywny |

---

## Zainstalowane aplikacje (InstalledApps)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/installed_apps.json` | Lista instalacji |
| GET | `/appstore/installed_apps/:id.json` | Szczegóły instalacji |

### Pola instalacji

| Pole | Typ | Opis |
|------|-----|------|
| `app_id` | integer | ID aplikacji |
| `client_id` | integer | ID klienta |
| `active` | boolean | Czy instalacja aktywna |
| `active_from` | datetime | Data aktywacji |
| `active_to` | datetime | Data wygaśnięcia |
| `run_count` | integer | Liczba uruchomień |
| `last_view_at` | datetime | Ostatnie uruchomienie |

---

## Widoczność (AppSubjects)

Kontrola widoczności aplikacji dla różnych regionów i klientów. Ta sama aplikacja może mieć różne kody widoku i widoczność w zależności od odbiorcy.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/appstore/app_subjects.json` | Lista reguł widoczności |
| POST | `/appstore/app_subjects.json` | Utworzenie reguły |
| PATCH | `/appstore/app_subjects/:id.json` | Aktualizacja reguły |
| DELETE | `/appstore/app_subjects/:id.json` | Usunięcie reguły |

### Pola

| Pole | Typ | Opis |
|------|-----|------|
| `app_id` | integer | ID aplikacji |
| `subject_type` | string | Typ: region (`pl`, `en`, `fr`) lub `Appstore::Client` |
| `subject_id` | integer | ID klienta (0 dla regionu) |
| `visible_public` | boolean | Widoczna w katalogu publicznym |
| `visible_appstore` | boolean | Widoczna w katalogu klienta |
| `view_code` | string | Kod widoku (unikalny per subject) |

---

## Konteksty

Aplikacje mogą być wyświetlane w określonych kontekstach interfejsu klienta (np. przy tworzeniu faktury, na liście dokumentów).

Pole `context` aplikacji zawiera tablicę kontekstów:

| Kontekst | Opis |
|----------|------|
| `income_create` | Tworzenie dokumentu przychodu |
| `income_list_export` | Eksport listy przychodów |
| `income_list_options` | Opcje listy przychodów |
| `income_list_button` | Przycisk na liście przychodów |
| `income_list_row_menu` | Menu kontekstowe wiersza |
| `expense_create` | Tworzenie dokumentu wydatku |
| `expense_list_export` | Eksport listy wydatków |
| `import_list` | Lista importów |
| `balance_import` | Import salda |
| `payment_list_import` | Import listy płatności |

### Pobranie aplikacji dla kontekstu

```
POST /appstore/appstore/get_context_apps?jwt=TOKEN
Content-Type: application/json

{
  "context": "income_create"
}
```

Zwraca listę aplikacji skonfigurowanych dla danego kontekstu.
