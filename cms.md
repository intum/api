[← Spis treści](README.md)

# CMS

System zarządzania treścią - strony internetowe, szablony, paragrafy, artykuły, pliki.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`). Strony publiczne nie wymagają autoryzacji.

## Spis treści

1. [Strony (Sites)](#strony-sites)
2. [Podstrony (Pages)](#podstrony-pages)
3. [Layouty (Layouts)](#layouty-layouts)
4. [Paragrafy (Paragraphs)](#paragrafy-paragraphs)
5. [Artykuły (Articles)](#artykuły-articles)
6. [Pliki (Assets)](#pliki-assets)
7. [Szablony Liquid](#szablony-liquid)
8. [Publikacja i domeny](#publikacja-i-domeny)

---

## Strony (Sites)

Strona to główny kontener CMS - reprezentuje jedną witrynę internetową z własną domeną, layoutem i podstronami.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/sites.json` | Lista stron |
| GET | `/cms/sites/:code.json` | Szczegóły strony (po `code` lub `id`) |
| POST | `/cms/sites.json` | Utworzenie strony |
| PATCH | `/cms/sites/:code.json` | Aktualizacja strony |
| DELETE | `/cms/sites/:code.json` | Usunięcie strony |
| GET | `/cms/sites/:id/view` | Podgląd wyrenderowanej strony |
| PATCH | `/cms/sites/:id/replace.json` | Podmiana treści (LLM) |

### Pola strony

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod strony |
| `name` | string | tak | Nazwa |
| `kind` | string | nie | Typ: `www` (domyślny), `kb` |
| `content` | text | nie | Treść HTML/Liquid |
| `path_prefix` | string | nie | Prefix ścieżki URL |
| `locale` | string | nie | Język strony |
| `layout_code` | string | nie | Kod layoutu |
| `domain_id` | integer | nie | ID domeny |
| `fields` | object | nie | Własne pola (dostępne w Liquid) |

### Utworzenie strony

```
POST /cms/sites.json
Content-Type: application/json

{
  "site": {
    "code": "moja-firma",
    "name": "Strona firmowa",
    "content": "<h1>{{ name }}</h1><p>Witamy na stronie {{ site.name }}</p>",
    "layout_code": "main-layout"
  }
}
```

### Klonowanie strony

Dodaj parametr `deep_clone=1` aby skopiować stronę wraz z layoutami, podstronami i paragrafami:

```
POST /cms/sites.json?deep_clone=1
```

---

## Podstrony (Pages)

Podstrona należy do strony (site). Ma własną ścieżkę URL, treść i może mieć podstrony (zagnieżdżanie).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/pages.json` | Lista podstron |
| GET | `/cms/pages/:code.json` | Szczegóły podstrony |
| POST | `/cms/pages.json` | Utworzenie podstrony |
| PATCH | `/cms/pages/:code.json` | Aktualizacja podstrony |
| DELETE | `/cms/pages/:code.json` | Usunięcie podstrony |
| GET | `/cms/pages/:id/view` | Podgląd wyrenderowanej podstrony |
| GET | `/cms/pages/:id/env.json` | Dane środowiskowe podstrony |
| PATCH | `/cms/pages/:id/replace.json` | Podmiana treści (LLM) |

### Pola podstrony

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod |
| `name` | string | tak | Nazwa |
| `path` | string | tak | Ścieżka URL (np. `/o-nas`) |
| `content` | text | nie | Treść HTML/Liquid |
| `site_code` | string | tak | Kod strony nadrzędnej |
| `layout_code` | string | nie | Kod layoutu |
| `page_code` | string | nie | Kod strony nadrzędnej (zagnieżdżanie) |
| `in_menu` | boolean | nie | Czy wyświetlać w menu |
| `menu_code` | string | nie | Kod pozycji menu |
| `priority` | integer | nie | Kolejność w menu |
| `redirect_to` | string | nie | Przekierowanie na inny URL |
| `locale` | string | nie | Język |
| `html_title` | string | nie | Meta title |
| `html_description` | string | nie | Meta description |
| `html_keywords` | string | nie | Meta keywords |
| `html_script` | text | nie | Dodatkowy skrypt HTML |
| `fields` | object | nie | Własne pola |

### Utworzenie podstrony

```
POST /cms/pages.json
Content-Type: application/json

{
  "page": {
    "code": "o-nas",
    "name": "O nas",
    "path": "/o-nas",
    "site_code": "moja-firma",
    "in_menu": true,
    "priority": 1,
    "content": "<h1>O naszej firmie</h1><p>{{ fields.description }}</p>",
    "fields": {
      "description": "Jesteśmy firmą technologiczną."
    }
  }
}
```

---

## Layouty (Layouts)

Layout to szablon HTML opakowujący treść strony/podstrony. Definiuje nagłówek, stopkę, menu i strukturę strony.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/layouts.json` | Lista layoutów |
| GET | `/cms/layouts/:code.json` | Szczegóły layoutu |
| POST | `/cms/layouts.json` | Utworzenie layoutu |
| PATCH | `/cms/layouts/:code.json` | Aktualizacja layoutu |
| DELETE | `/cms/layouts/:code.json` | Usunięcie layoutu |
| GET | `/cms/layouts/:id/view` | Podgląd wyrenderowanego layoutu |
| PATCH | `/cms/layouts/:id/replace.json` | Podmiana treści (LLM) |

### Pola layoutu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod |
| `name` | string | tak | Nazwa |
| `kind` | string | nie | Typ: `page` (domyślny), `paragraph` |
| `content` | text | nie | Treść HTML/Liquid szablonu |
| `example_content` | text | nie | Przykładowa treść (dla podglądu) |

### Utworzenie layoutu

```
POST /cms/layouts.json
Content-Type: application/json

{
  "layout": {
    "code": "main-layout",
    "name": "Layout główny",
    "content": "<!DOCTYPE html>\n<html>\n<head><title>{{ html_title }}</title></head>\n<body>\n  <nav>{{ pages | menu }}</nav>\n  <main>{{ content }}</main>\n  <footer>&copy; {{ year }} {{ site.name }}</footer>\n</body>\n</html>"
  }
}
```

---

## Paragrafy (Paragraphs)

Paragrafy to bloki treści osadzane w stronach za pomocą tagu `<cms type="box">`. Umożliwiają podział strony na edytowalne sekcje.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/paragraphs.json` | Lista paragrafów |
| GET | `/cms/paragraphs/:code.json` | Szczegóły paragrafu |
| POST | `/cms/paragraphs.json` | Utworzenie paragrafu |
| PATCH | `/cms/paragraphs/:code.json` | Aktualizacja paragrafu |
| DELETE | `/cms/paragraphs/:code.json` | Usunięcie paragrafu |
| GET | `/cms/paragraphs/:id/view` | Podgląd wyrenderowanego paragrafu |
| PATCH | `/cms/paragraphs/:id/replace.json` | Podmiana treści (LLM) |

### Pola paragrafu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod |
| `name` | string | tak | Nazwa |
| `box_code` | string | nie | Kod boksu (do osadzania przez tag `<cms>`) |
| `content` | text | nie | Treść HTML/Liquid |
| `priority` | integer | nie | Kolejność wyświetlania |
| `site_code` | string | tak | Kod strony |
| `layout_code` | string | nie | Kod layoutu paragrafu |
| `page_code` | string | nie | Kod podstrony (przypisanie do konkretnej podstrony) |
| `fields` | object | nie | Własne pola |

### Osadzanie paragrafów w stronie

W treści strony lub layoutu użyj tagu `<cms>`:

```html
<cms type="box" id="hero">
<!-- Tu wyrenderują się paragrafy z box_code="hero" -->
```

---

## Artykuły (Articles)

Artykuły blogowe/newsowe powiązane ze stroną. Obsługują publikację z datą i harmonogramem.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/articles.json` | Lista artykułów |
| GET | `/cms/articles/:id.json` | Szczegóły artykułu |
| POST | `/cms/articles.json` | Utworzenie artykułu |
| PATCH | `/cms/articles/:id.json` | Aktualizacja artykułu |
| DELETE | `/cms/articles/:id.json` | Usunięcie artykułu |
| PATCH | `/cms/articles/:id/replace.json` | Podmiana treści (LLM) |

### Pola artykułu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `title` | string | tak | Tytuł |
| `path` | string | nie | Ścieżka URL (generowana automatycznie z tytułu) |
| `content` | text | nie | Treść artykułu |
| `abstract` | text | nie | Streszczenie |
| `author` | string | nie | Autor |
| `category_code` | string | nie | Kod kategorii |
| `image_url` | string | nie | URL obrazka |
| `published` | boolean | nie | Czy opublikowany |
| `published_at` | datetime | nie | Data publikacji |
| `publish_to` | datetime | nie | Data zakończenia publikacji |
| `html_title` | string | nie | Meta title |
| `html_description` | string | nie | Meta description |

### Wyświetlanie artykułów w stronie

W treści strony użyj tagu `<cms>`:

```html
<cms type="article">
<!-- Lista artykułów z paginacją -->
```

---

## Pliki (Assets)

Pliki statyczne: JavaScript, CSS, obrazy. Obsługują foldery i upload ZIP.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/cms/assets.json` | Lista plików |
| GET | `/cms/assets/:id.json` | Szczegóły pliku |
| POST | `/cms/assets.json` | Upload pliku |
| PATCH | `/cms/assets/:id.json` | Aktualizacja pliku |
| DELETE | `/cms/assets/:id.json` | Usunięcie pliku |
| GET | `/cms/assets/view/*name` | Pobranie zawartości pliku (publiczne) |
| POST | `/cms/assets/create_folder.json` | Utworzenie folderu |
| DELETE | `/cms/assets/destroy_folder.json` | Usunięcie folderu |

### Pola pliku

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa pliku |
| `kind` | string | nie | Typ: `js`, `css`, `image`, `zip` (auto-wykrywany) |
| `content` | text | nie | Treść (dla JS/CSS) |
| `file` | attachment | nie | Plik binarny (obrazy, ZIP) |
| `folder_id` | integer | nie | ID folderu |

Upload ZIP automatycznie rozpakowuje archiwum i tworzy strukturę folderów z plikami.

---

## Szablony Liquid

Treści stron, layoutów i paragrafów obsługują szablony [Liquid](https://shopify.github.io/liquid/).

### Dostępne zmienne

| Zmienna | Opis |
|---------|------|
| `{{ name }}` | Nazwa bieżącej strony/podstrony |
| `{{ path }}` | Ścieżka URL |
| `{{ url }}` | Pełny URL |
| `{{ content }}` | Treść (w layoucie - treść strony) |
| `{{ site }}` | Obiekt strony (site) |
| `{{ layout }}` | Obiekt layoutu |
| `{{ pages }}` | Lista podstron |
| `{{ paragraphs }}` | Lista paragrafów |
| `{{ year }}` | Bieżący rok |
| `{{ powered_by }}` | Link "Powered by" |

### Własne pola w Liquid

Pola z `fields` są dostępne jako zmienne najwyższego poziomu:

```json
{
  "fields": {
    "company_name": "Firma ABC",
    "phone": "+48 123 456 789"
  }
}
```

```html
<h1>{{ company_name }}</h1>
<p>Telefon: {{ phone }}</p>
```

### Tagi CMS

| Tag | Opis |
|-----|------|
| `<cms type="box" id="NAZWA">` | Renderuje paragrafy z danym `box_code` |
| `<cms type="article">` | Renderuje listę artykułów z paginacją |

---

## Publikacja i domeny

### Podgląd (preview)

Strona dostępna pod adresem podglądu bez konfiguracji domeny:

```
GET /w/:site_code
GET /w/:site_code/:page_path
```

### Własna domena

Po skonfigurowaniu domeny w [ustawieniach konta](account.md#domeny-domains) i przypisaniu jej do strony, witryna jest dostępna pod własnym adresem:

```
https://www.mojafirma.pl/          → strona główna
https://www.mojafirma.pl/o-nas     → podstrona
https://www.mojafirma.pl/blog      → artykuły
```

### Identyfikacja po kodzie

Wszystkie zasoby CMS (strony, podstrony, layouty, paragrafy) są identyfikowane po polu `code` zamiast `id`, co ułatwia pracę z API:

```
GET /cms/sites/moja-firma.json
GET /cms/pages/o-nas.json
GET /cms/layouts/main-layout.json
```
