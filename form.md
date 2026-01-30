[← Spis treści](README.md)

# Form

Dynamiczny kreator formularzy do osadzenia na dowolnej stronie internetowej.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Formularze (Forms)](#formularze-forms)
2. [Typy pól](#typy-pól)
3. [Wyniki (Results)](#wyniki-results)
4. [Osadzanie na stronie](#osadzanie-na-stronie)

---

## Formularze (Forms)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/form/forms.json` | Lista formularzy |
| GET | `/form/forms/:id.json` | Szczegóły formularza |
| POST | `/form/forms.json` | Utworzenie formularza |
| PATCH | `/form/forms/:id.json` | Aktualizacja formularza |
| DELETE | `/form/forms/:id.json` | Usunięcie formularza |
| GET | `/form/forms/:id/get_form` | Podgląd formularza (publiczny) |

### Pola formularza

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa formularza |
| `description` | text | nie | Opis formularza |
| `date_from` | date | nie | Data aktywacji |
| `date_to` | date | nie | Data dezaktywacji |
| `lang` | string | nie | Język formularza |
| `team_id` | integer | nie | ID zespołu |
| `department_id` | integer | nie | ID działu |
| `fields.form_anonymous` | boolean | nie | Formularz anonimowy (bez zbierania IP, przeglądarki) |
| `fields.form_redirect` | string | nie | URL przekierowania po wysłaniu |
| `fields.form_response` | string | nie | Treść wiadomości zwrotnej |
| `fields.form_class` | string | nie | Klasa CSS formularza |
| `fields.form_style` | string | nie | Styl CSS formularza |
| `fields.submit_text` | string | nie | Tekst przycisku wysłania |
| `fields.button_class` | string | nie | Klasa CSS przycisku |

### Utworzenie formularza

```
POST /form/forms.jsonContent-Type: application/json

{
  "form": {
    "name": "Formularz kontaktowy",
    "description": "Formularz do zbierania leadów",
    "fields": {
      "form_response": "Dziękujemy za wypełnienie formularza!",
      "form_redirect": "https://example.com/dziekujemy",
      "submit_text": "Wyślij"
    },
    "form_fields_attributes": [
      { "name": "Imię i nazwisko", "kind": "string", "required": true },
      { "name": "Email", "kind": "string", "required": true },
      { "name": "Wiadomość", "kind": "text", "required": false },
      { "name": "Akceptuję regulamin", "kind": "select", "required": true }
    ]
  }
}
```

---

## Typy pól

| Typ | Opis |
|-----|------|
| `string` | Pole tekstowe jednoliniowe |
| `text` | Pole tekstowe wieloliniowe |
| `select` | Pojedynczy checkbox (np. akceptacja regulaminu) |
| `radio` | Wybór jednokrotny z kilku opcji |
| `checkbox` | Wybór wielokrotny |
| `attachment` | Załącznik |
| `title` | Tytuł sekcji (nie do wypełniania) |
| `placeholder` | Paragraf opisowy (nie do wypełniania) |

### Pola konfiguracji pola formularza

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa pola (etykieta) |
| `description` | text | nie | Opis (dla typu placeholder) |
| `kind` | string | tak | Typ pola (patrz tabela wyżej) |
| `required` | boolean | nie | Czy pole wymagane |
| `fields.class` | string | nie | Klasa CSS pola |
| `fields.input_placeholder` | string | nie | Placeholder pola tekstowego |
| `fields.options` | text | nie | Opcje dla pól wyboru (oddzielane enterem) |

---

## Wyniki (Results)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/form/results.json` | Lista wyników |
| GET | `/form/results/:id.json` | Szczegóły wyniku |
| POST | `/form/results/save` | Zapisanie wyniku (publiczny, bez CSRF) |
| DELETE | `/form/results/:id.json` | Usunięcie wyniku |
| POST | `/form/results/:id/close` | Zamknięcie wyniku |
| POST | `/form/results/:id/open` | Otwarcie wyniku |

### Parametry filtrowania (index)

| Parametr | Opis |
|----------|------|
| `form_id` | ID formularza |
| `closed` | Filtr: zamknięte |

### Pola wyniku

| Pole | Typ | Opis |
|------|-----|------|
| `result` | object | Wyniki w formacie JSON |
| `ip` | string | IP wypełniającego (pomijane dla anonimowych) |
| `referrer` | string | Strona źródłowa (pomijane dla anonimowych) |
| `agent` | string | Przeglądarka (pomijane dla anonimowych) |

### Zapisanie wyniku (publiczny)

Ten endpoint jest publiczny - służy do odbierania danych z osadzonych formularzy.

```
POST /form/results/save
Content-Type: application/json

{
  "result": {
    "form_id": "TOKEN_FORMULARZA",
    "results": {
      "Imię i nazwisko": "Jan Kowalski",
      "Email": "jan@example.com",
      "Wiadomość": "Chciałbym uzyskać więcej informacji"
    }
  }
}
```

---

## Osadzanie na stronie

Formularz osadza się na stronie za pomocą dwóch skryptów:

1. **Skrypt ogólny** - wstawiamy raz w `<body>` strony (jeśli to pierwszy formularz)
2. **Skrypt formularza** - wstawiamy w miejscu gdzie ma się wyświetlić

Jeśli na stronie jest już osadzony inny formularz Intum, wystarczy dodać tylko skrypt konkretnego formularza. Skrypty nie zmieniają się po edycji formularza.
