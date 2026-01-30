[← Spis treści](README.md)

# Mail

Zarządzanie pocztą elektroniczną - wysyłanie, odbieranie, filtrowanie, szablony.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Emaile](#emaile)
2. [Skrzynki (Mailboxes)](#skrzynki-mailboxes)
3. [Szablony (Templates)](#szablony-templates)
4. [Filtry (Filters)](#filtry-filters)
5. [Filtry spamu (SpamFilters)](#filtry-spamu-spamfilters)
6. [Foldery (Folders)](#foldery-folders)
7. [Notatki (Notes)](#notatki-notes)

---

## Emaile

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/emails.json` | Lista emaili |
| GET | `/mail/emails/:id.json` | Szczegóły emaila |
| POST | `/mail/emails.json` | Wysłanie emaila |
| PATCH | `/mail/emails/:id.json` | Aktualizacja emaila |
| DELETE | `/mail/emails/:id.json` | Usunięcie emaila |
| POST | `/mail/emails/:id/change_priority.json` | Zmiana priorytetu |
| POST | `/mail/emails/:id/change_bulk_option.json` | Zmiana opcji |
| POST | `/mail/emails/change_bulk_option_multiple.json` | Zmiana opcji dla wielu |
| POST | `/mail/emails/delete.json` | Usunięcie wielu emaili |
| POST | `/mail/emails/recover.json` | Przywrócenie usuniętych |
| POST | `/mail/emails/set_as_spam.json` | Oznaczenie jako spam |
| POST | `/mail/emails/unspam.json` | Cofnięcie oznaczenia spam |
| GET | `/mail/emails/:id/attachments.json` | Załączniki emaila |
| POST | `/mail/emails/save_draft.json` | Zapisanie szkicu |
| POST | `/mail/emails/:id/snooze.json` | Odłożenie emaila |
| POST | `/mail/emails/:id/archive_and_next.json` | Archiwizuj i przejdź dalej |
| POST | `/mail/emails/:id/add_connected_task.json` | Powiązanie z zadaniem |
| POST | `/mail/emails/:id/remove_connected_task.json` | Usunięcie powiązania z zadaniem |
| POST | `/mail/emails/:id/add_contact.json` | Przypisanie kontaktu |
| POST | `/mail/emails/:id/remove_contact.json` | Usunięcie kontaktu |

### Parametry filtrowania (index)

| Parametr | Opis |
|----------|------|
| `q` | Wyszukiwanie pełnotekstowe |
| `responsible` | ID odpowiedzialnego |
| `archived` | Filtr: zarchiwizowane |
| `send_status` | Status wysyłki |
| `view` | Widok: `inbox`, `sent`, `draft`, `spam`, `trash` |
| `mailbox_id` | ID skrzynki |

### Pola emaila

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `subject` | string | tak | Temat |
| `to` | string | tak | Adresat |
| `cc` | string | nie | Kopia |
| `bcc` | string | nie | Ukryta kopia |
| `content` | text | tak | Treść (HTML) |
| `mailbox_id` | integer | tak | ID skrzynki nadawczej |

### Wysłanie emaila

```
POST /mail/emails.jsonContent-Type: application/json

{
  "email": {
    "subject": "Temat wiadomości",
    "to": "odbiorca@example.com",
    "cc": "kopia@example.com",
    "content": "<p>Treść wiadomości</p>",
    "mailbox_id": 1
  }
}
```

### Zapisanie szkicu

```
POST /mail/emails/save_draft.jsonContent-Type: application/json

{
  "email": {
    "subject": "Szkic",
    "content": "<p>Treść szkicu</p>",
    "draft_id": "uuid-istniejacego-szkicu"
  }
}
```

Odpowiedź: `{ "status": "ok", "message": "...", "id": 123 }`

---

## Skrzynki (Mailboxes)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/mailboxes.json` | Lista skrzynek |
| GET | `/mail/mailboxes/:id.json` | Szczegóły skrzynki |
| POST | `/mail/mailboxes.json` | Utworzenie skrzynki |
| PATCH | `/mail/mailboxes/:id.json` | Aktualizacja skrzynki |
| DELETE | `/mail/mailboxes/:id.json` | Usunięcie skrzynki |
| GET | `/mail/my_mailboxes.json` | Moje skrzynki |
| GET | `/mail/mailboxes/:id/send_verification_code` | Wysłanie kodu weryfikacyjnego |
| POST | `/mail/mailboxes/turn_off_autoresponders` | Wyłączenie autoresponderów |
| POST | `/mail/mailboxes/turn_on_autoresponders` | Włączenie autoresponderów |

### Pola skrzynki

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa skrzynki |
| `email` | string | tak | Adres email |
| `kind` | string | nie | Typ: `imap`, `ses` |
| `smtp_kind` | string | nie | Typ SMTP |

---

## Szablony (Templates)

Szablony wiadomości email z obsługą Liquid templates.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/templates.json` | Lista szablonów |
| GET | `/mail/templates/:id.json` | Szczegóły szablonu |
| POST | `/mail/templates.json` | Utworzenie szablonu |
| PATCH | `/mail/templates/:id.json` | Aktualizacja szablonu |
| DELETE | `/mail/templates/:id.json` | Usunięcie szablonu |

### Pola szablonu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa szablonu |
| `subject` | string | nie | Temat emaila |
| `content` | text | tak | Treść (HTML/Liquid) |
| `language` | string | nie | Język szablonu |
| `department_id` | integer | nie | ID działu |

---

## Filtry (Filters)

Automatyczne reguły przetwarzania przychodzących emaili.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/filters.json` | Lista filtrów |
| GET | `/mail/filters/:id.json` | Szczegóły filtru |
| POST | `/mail/filters.json` | Utworzenie filtru |
| PATCH | `/mail/filters/:id.json` | Aktualizacja filtru |
| DELETE | `/mail/filters/:id.json` | Usunięcie filtru |
| POST | `/mail/filters/:id/run_test_filter.json` | Testowanie filtru |

---

## Filtry spamu (SpamFilters)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/spam_filters.json` | Lista filtrów spamu |
| GET | `/mail/spam_filters/:id.json` | Szczegóły filtru |
| POST | `/mail/spam_filters.json` | Utworzenie filtru |
| PATCH | `/mail/spam_filters/:id.json` | Aktualizacja filtru |
| DELETE | `/mail/spam_filters/:id.json` | Usunięcie filtru |

---

## Foldery (Folders)

Własne foldery do organizacji emaili.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/folders.json` | Lista folderów |
| GET | `/mail/folders/:id.json` | Szczegóły folderu |
| POST | `/mail/folders.json` | Utworzenie folderu |
| PATCH | `/mail/folders/:id.json` | Aktualizacja folderu |
| DELETE | `/mail/folders/:id.json` | Usunięcie folderu |

### Pola folderu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa folderu |
| `description` | text | nie | Opis folderu |
| `hidden` | boolean | nie | Ukryty folder |
| `responsible_id` | integer | nie | ID odpowiedzialnego |
| `team_id` | integer | nie | ID zespołu |
| `department_id` | integer | nie | ID działu |
| `group_id` | integer | nie | ID grupy |

---

## Notatki (Notes)

Wewnętrzne notatki przypisane do emaili.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/mail/notes.json` | Lista notatek |
| GET | `/mail/notes/:id.json` | Szczegóły notatki |
| POST | `/mail/notes.json` | Utworzenie notatki |
| PATCH | `/mail/notes/:id.json` | Aktualizacja notatki |
| DELETE | `/mail/notes/:id.json` | Usunięcie notatki |

### Pola notatki

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `content` | text | tak | Treść notatki |
| `email_id` | integer | tak | ID emaila |
