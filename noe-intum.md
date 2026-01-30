[← Spis treści](README.md)

# Noe w Intum

Moduł [Noe (No codE)](https://github.com/noeai/api) umożliwia tworzenie aplikacji webowych, które mogą działać jako **dodatki do modułów Intum** lub jako **samodzielne strony**.

Pełna dokumentacja API Noe: **[github.com/noeai/api](https://github.com/noeai/api)**

## Tryby działania

Aplikacja Noe może działać na trzy sposoby:

| Tryb | `show_in_module` | Opis |
|------|------------------|------|
| **Dodatek do modułu** | nazwa modułu (np. `organize`) | Pojawia się w menu bocznym modułu, z breadcrumbs i layoutem |
| **Wstrzyknięcie na stronę** | ścieżka strony (np. `tasks/show`) | Renderuje się inline na konkretnej stronie |
| **Samodzielna strona** | puste | Działa pod własnym URL `/a/:url_code` |

---

## Dodatek do modułu

Aplikacja z `show_in_module` ustawionym na nazwę modułu pojawia się w menu bocznym tego modułu i wyświetla się z pełnym layoutem (breadcrumbs, sidebar, nagłówek).

**Dostępne moduły:**

| Wartość | Moduł |
|---------|-------|
| `organize` | Organizacja (zadania, projekty) |
| `mail` | Poczta |
| `crm` | CRM |
| `kb` | Baza wiedzy |
| `cms` | CMS |
| `connect` | Konektory |
| `voip` | VoIP |
| `form` | Formularze |
| `insight` | Analityka |
| `billing` | Rozliczenia |
| `drive` | Dysk |
| `commerce` | E-commerce |
| `account` | Konto |

### Przykład: Kalkulator prowizji w CRM

```json
{
  "app": {
    "name": "Kalkulator prowizji",
    "url_code": "kalkulator-prowizji",
    "app_engine": "svelte",
    "css_engine": "tailwind_all",
    "show_in_module": "crm",
    "source_code": "<script>\n  let amount = 0;\n  $: commission = amount * 0.15;\n</script>\n\n<div class=\"p-4\">\n  <h2 class=\"text-lg font-bold\">Kalkulator prowizji</h2>\n  <input type=\"number\" bind:value={amount} class=\"border p-2 mt-2\" placeholder=\"Kwota transakcji\" />\n  <p class=\"mt-2\">Prowizja (15%): <strong>{commission.toFixed(2)} PLN</strong></p>\n</div>"
  }
}
```

Efekt: w module CRM w menu bocznym pojawia się pozycja "Kalkulator prowizji". Po kliknięciu wyświetla się aplikacja z breadcrumbs `CRM > Kalkulator prowizji`.

### Przykład: Dashboard sprzedażowy w Organize

```json
{
  "app": {
    "name": "Dashboard sprzedaży",
    "url_code": "dashboard-sprzedazy",
    "app_engine": "vue",
    "css_engine": "tailwind_all",
    "show_in_module": "organize",
    "source_code": "<template>\n  <div class=\"p-4\">\n    <h2 class=\"text-lg font-bold mb-4\">Dashboard sprzedaży</h2>\n    <div class=\"grid grid-cols-3 gap-4\">\n      <div class=\"bg-blue-50 p-4 rounded\">\n        <p class=\"text-sm text-gray-600\">Nowe deale</p>\n        <p class=\"text-2xl font-bold\">{{ deals }}</p>\n      </div>\n      <div class=\"bg-green-50 p-4 rounded\">\n        <p class=\"text-sm text-gray-600\">Zamknięte</p>\n        <p class=\"text-2xl font-bold\">{{ closed }}</p>\n      </div>\n      <div class=\"bg-yellow-50 p-4 rounded\">\n        <p class=\"text-sm text-gray-600\">W trakcie</p>\n        <p class=\"text-2xl font-bold\">{{ pending }}</p>\n      </div>\n    </div>\n  </div>\n</template>\n\n<script setup>\nimport { ref } from 'vue';\nconst deals = ref(12);\nconst closed = ref(8);\nconst pending = ref(4);\n</script>"
  }
}
```

---

## Wstrzykiwanie na stronę

Aplikacja z `show_in_module` ustawionym na ścieżkę strony (format `{controller}/{action}`) renderuje się **inline** bezpośrednio na tej stronie, obok oryginalnej treści.

**Przykładowe strony:**

| Wartość | Strona |
|---------|--------|
| `organize/tasks/show` | Szczegóły zadania |
| `organize/tasks/index` | Lista zadań |
| `organize/projects/show` | Szczegóły projektu |
| `crm/contacts/show` | Szczegóły kontaktu |
| `crm/contacts/index` | Lista kontaktów |
| `crm/clients/show` | Szczegóły klienta |
| `mail/emails/show` | Szczegóły emaila |

### Przykład: Notatki na stronie kontaktu

```json
{
  "app": {
    "name": "Szybkie notatki",
    "url_code": "szybkie-notatki",
    "app_engine": "svelte",
    "css_engine": "tailwind_all",
    "show_in_module": "crm/contacts/show",
    "source_code": "<script>\n  let notes = [];\n  let text = '';\n  function add() {\n    if (text) {\n      notes = [...notes, { text, date: new Date().toLocaleString() }];\n      text = '';\n    }\n  }\n</script>\n\n<div class=\"p-4 border rounded mt-4\">\n  <h3 class=\"font-bold mb-2\">Szybkie notatki</h3>\n  <div class=\"flex gap-2 mb-2\">\n    <input bind:value={text} class=\"border p-1 flex-1 rounded\" placeholder=\"Nowa notatka...\" />\n    <button on:click={add} class=\"bg-blue-500 text-white px-3 py-1 rounded\">Dodaj</button>\n  </div>\n  {#each notes as note}\n    <div class=\"bg-gray-50 p-2 rounded mb-1\">\n      <span class=\"text-xs text-gray-500\">{note.date}</span>\n      <p>{note.text}</p>\n    </div>\n  {/each}\n</div>"
  }
}
```

### Przykład: Banner na liście zadań

```json
{
  "app": {
    "name": "Banner informacyjny",
    "url_code": "banner-info",
    "app_engine": "js",
    "show_in_module": "organize/tasks/index",
    "source_code": "(function() {\n  const container = document.getElementById('noe-app');\n  const div = document.createElement('div');\n  div.style.cssText = 'padding:12px;background:#eff6ff;border:1px solid #bfdbfe;border-radius:8px;margin-bottom:12px;';\n  div.innerHTML = '<strong>Przypomnienie:</strong> Proszę aktualizować statusy zadań do końca dnia.';\n  container.appendChild(div);\n})();"
  }
}
```

**Uwagi:**
- Można wstrzyknąć wiele aplikacji na tę samą stronę
- Aplikacje dzielą wspólny kontener `<div id="noe-app">` - używaj `appendChild`, nie `innerHTML`
- Owijaj kod JS w IIFE `(function() { ... })();` aby unikać konfliktów zmiennych
- Pole `active` kontroluje czy aplikacja jest wstrzykiwana

---

## Samodzielna strona

Aplikacja bez `show_in_module` (lub z `public: true`) działa jako samodzielna strona pod adresem `/a/:url_code`.

### Przykład: Publiczny formularz rejestracji

```json
{
  "app": {
    "name": "Rejestracja na event",
    "url_code": "rejestracja-event",
    "app_engine": "svelte",
    "css_engine": "tailwind_all",
    "public": true,
    "source_code": "<script>\n  let name = '';\n  let email = '';\n  let submitted = false;\n  async function submit() { submitted = true; }\n</script>\n\n<div class=\"max-w-md mx-auto p-8\">\n  <h1 class=\"text-2xl font-bold mb-6\">Rejestracja na event</h1>\n  {#if !submitted}\n    <form on:submit|preventDefault={submit}>\n      <input bind:value={name} class=\"w-full border p-2 rounded mb-3\" placeholder=\"Imię i nazwisko\" required />\n      <input bind:value={email} type=\"email\" class=\"w-full border p-2 rounded mb-3\" placeholder=\"Email\" required />\n      <button type=\"submit\" class=\"w-full bg-blue-500 text-white p-2 rounded\">Zarejestruj się</button>\n    </form>\n  {:else}\n    <p class=\"text-green-600 font-bold\">Dziękujemy za rejestrację!</p>\n  {/if}\n</div>"
  }
}
```

Dostępna publicznie pod adresem: `https://firma.intum.com/a/rejestracja-event`

---

## Kontekst aplikacji

Wstrzykiwane aplikacje mają dostęp do kontekstu użytkownika przez `window.NoeAppContext`:

```javascript
window.NoeAppContext = {
  user: {
    login: "jan.kowalski",
    email: "jan@example.com",
    name: "Jan Kowalski",
    role: "admin",
    team_ids: [1, 2],
    team_names: ["Sprzedaż", "IT"],
    group_ids: [5],
    group_names: ["Menedżerowie"],
    department_ids: [3],
    department_names: ["Dział IT"]
  }
}
```

---

## Akcje (wywoływanie Flow z frontendu)

Aplikacja może wywoływać procesy [Flow](https://github.com/noeai/api/blob/master/flows.md) z poziomu frontendu, dzięki czemu może komunikować się z zewnętrznymi API, bazami danych i innymi usługami.

```javascript
// Wywołanie akcji
const response = await fetch('/noe/apps/APP_ID/action/get_data', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ param1: "value1" })
});
const result = await response.json();
// { flow_process_id: "...", status: "pending"|"finished", data: {...} }

// Sprawdzenie statusu (jeśli status = "pending")
const status = await fetch(
  `/noe/apps/APP_ID/action/get_data/status?flow_process_id=${result.flow_process_id}`
).then(r => r.json());
// { status: "finished", data: {...} }
```

---

## Silniki aplikacji

| Silnik (`app_engine`) | Opis |
|-----------------------|------|
| `svelte` | Svelte 5 |
| `vue` | Vue.js 3 |
| `react` | React 19 |
| `js` | Czysty JavaScript |
| `html` | Statyczny HTML |
| `kaboom` | Framework gier Kaboom.js |
| `phaser` | Framework gier Phaser |

| CSS (`css_engine`) | Opis |
|--------------------|------|
| `tailwind_all` | Tailwind CSS z CDN (wszystkie klasy) |
| `tailwind` | Tailwind CSS (kompilowany) |
| `none` | Bez CSS |
