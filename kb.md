[← Spis treści](README.md)

# Baza Wiedzy (KB)

Tworzenie, organizacja i udostępnianie wiedzy - artykuły pomocy, helplinki, changelog, forum sugestii.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Bazy wiedzy (KnowledgeBases)](#bazy-wiedzy-knowledgebases)
2. [Artykuły (Entries)](#artykuły-entries)
3. [Kategorie (Categories)](#kategorie-categories)
4. [Helplinki](#helplinki)
5. [Komentarze (Comments)](#komentarze-comments)

---

## Bazy wiedzy (KnowledgeBases)

Każda baza wiedzy może mieć własną domenę, styl i przeznaczenie (dokumentacja, centrum pomocy, changelog).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/kb/knowledge_bases.json` | Lista baz wiedzy |
| GET | `/kb/knowledge_bases/:id.json` | Szczegóły bazy |
| POST | `/kb/knowledge_bases.json` | Utworzenie bazy |
| PATCH | `/kb/knowledge_bases/:id.json` | Aktualizacja bazy |
| DELETE | `/kb/knowledge_bases/:id.json` | Usunięcie bazy |

### Pola bazy wiedzy

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `title` | string | tak | Tytuł bazy |
| `description` | text | nie | Opis |
| `url` | string | nie | URL bazy (własna domena) |
| `kind` | string | nie | Typ bazy |
| `lang` | string | nie | Język bazy |
| `private` | boolean | nie | Czy dostęp wymaga logowania |

---

## Artykuły (Entries)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/kb/entries.json` | Lista artykułów |
| GET | `/kb/entries/:id.json` | Szczegóły artykułu |
| POST | `/kb/entries.json` | Utworzenie artykułu |
| PATCH | `/kb/entries/:id.json` | Aktualizacja artykułu |
| DELETE | `/kb/entries/:id.json` | Usunięcie artykułu |
| POST | `/kb/entries/save_draft.json` | Zapisanie szkicu |
| POST | `/kb/entries/:id/change_priority.json` | Zmiana priorytetu |
| POST | `/kb/entries/:id/change_category.json` | Zmiana kategorii |

### Pola artykułu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `title` | string | tak | Tytuł artykułu |
| `content` | text | tak | Treść (HTML/Markdown) |
| `kind` | string | nie | Typ artykułu |
| `knowledge_base_id` | integer | tak | ID bazy wiedzy |
| `category_id` | integer | nie | ID kategorii |
| `private` | boolean | nie | Czy prywatny |
| `tags` | string | nie | Tagi |

### Utworzenie artykułu

```
POST /kb/entries.jsonContent-Type: application/json

{
  "entry": {
    "title": "Jak skonfigurować konto",
    "content": "<p>Krok 1: ...</p>",
    "knowledge_base_id": 1,
    "category_id": 3
  }
}
```

### Zapisanie szkicu

```
POST /kb/entries/save_draft.jsonContent-Type: application/json

{
  "entry": {
    "uuid": "opcjonalny-uuid-szkicu",
    "title": "Szkic artykułu",
    "content": "<p>Treść robocza</p>",
    "knowledge_base_id": 1
  }
}
```

Odpowiedź: `{ "status": "ok", "message": "...", "id": 123 }`

---

## Kategorie (Categories)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/kb/categories.json` | Lista kategorii |
| GET | `/kb/categories/:id.json` | Szczegóły kategorii |
| POST | `/kb/categories.json` | Utworzenie kategorii |
| PATCH | `/kb/categories/:id.json` | Aktualizacja kategorii |
| DELETE | `/kb/categories/:id.json` | Usunięcie kategorii |
| POST | `/kb/categories/:id/up` | Przesuń wyżej |
| POST | `/kb/categories/:id/down` | Przesuń niżej |
| GET | `/kb/categories/get_changelog_content.json` | Treść changeloga (publiczny) |

### Pola kategorii

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa kategorii |
| `kind` | string | nie | Typ |
| `description` | text | nie | Opis |
| `priority` | integer | nie | Kolejność |
| `knowledge_base_id` | integer | tak | ID bazy wiedzy |
| `private` | boolean | nie | Czy prywatna |

---

## Helplinki

Kontekstowe podpowiedzi wyświetlane w aplikacjach. Helplink łączy ikonkę **?** w interfejsie z artykułem KB.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/kb/helplinks.json` | Lista helplinków |
| GET | `/kb/helplinks/:id.json` | Szczegóły helplinku |
| POST | `/kb/helplinks.json` | Utworzenie helplinku |
| PATCH | `/kb/helplinks/:id.json` | Aktualizacja helplinku |
| DELETE | `/kb/helplinks/:id.json` | Usunięcie helplinku |
| POST | `/kb/helplinks/get_helplinks_content.json` | Pobranie treści helplinków (publiczny) |

### Pola helplinku

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `key` | string | tak | Unikalny klucz helplinku |
| `knowledge_base_id` | integer | tak | ID bazy wiedzy |
| `entry_id` | integer | nie | ID artykułu KB |
| `content` | text | nie | Treść podpowiedzi |
| `section` | string | nie | Sekcja artykułu |
| `active` | boolean | nie | Czy aktywny |

### Pobranie treści helplinków (publiczny)

```
POST /kb/helplinks/get_helplinks_content.json
Content-Type: application/json

{
  "kb_token": "TOKEN_BAZY_WIEDZY",
  "keys": ["organize_team_name", "mail_inbox_filters"]
}
```

Odpowiedź:
```json
[
  {
    "key": "organize_team_name",
    "content": "Nazwa zespołu wyświetlana w menu...",
    "path": "/pomoc/artykul-1",
    "active": true
  }
]
```

### Osadzanie w aplikacji

```html
<script>
  var HelplinksWidget = {
    base_url: "https://pomoc.mojaaplikacja.pl",
    kb_token: "abc123xyz789",
    helplink_admin: false
  }
</script>
<script src="https://assets.intum.net/assets/src/intum-helplinks-widget.js"></script>

<!-- Dodawanie helplinków -->
<label>
  Nazwa zespołu
  <span class="intum_helplink" data-helplink="organize_team_name"></span>
</label>
```

---

## Komentarze (Comments)

Komentarze publiczne i wewnętrzne do artykułów KB.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/kb/comments.json` | Lista komentarzy |
| GET | `/kb/comments/:id.json` | Szczegóły komentarza |
| POST | `/kb/comments.json` | Utworzenie komentarza |
| PATCH | `/kb/comments/:id.json` | Aktualizacja komentarza |
| DELETE | `/kb/comments/:id.json` | Usunięcie komentarza |
| POST | `/kb/comments/:id/accept_comment` | Zaakceptowanie komentarza |
| POST | `/kb/comments/:id/operator_hide` | Ukrycie komentarza |
| POST | `/kb/comments/:id/operator_show` | Pokazanie komentarza |
| POST | `/kb/comments/create_public_comment` | Dodanie publicznego komentarza |
