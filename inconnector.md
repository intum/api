[← Spis treści](README.md)

# InConnector

Platforma integracji z zewnętrznymi serwisami - konektory, flow (automatyzacje), obiekty danych, aplikacje.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`). Publiczne akcje konektorów mogą używać `token` konektora.

## Spis treści

1. [Konektory (Connectors)](#konektory-connectors)
2. [Flow](#flow)
3. [Procesy Flow (FlowProcesses)](#procesy-flow-flowprocesses)
4. [Obiekty danych (DataObjects)](#obiekty-danych-dataobjects)
5. [Aplikacje (Apps)](#aplikacje-apps)
6. [Typy konektorów](#typy-konektorów)

---

## Konektory (Connectors)

Konektor reprezentuje połączenie z zewnętrznym serwisem (API, bramka płatności, e-commerce, AI).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/connect/connectors.json` | Lista konektorów |
| GET | `/connect/connectors/:id.json` | Szczegóły konektora |
| POST | `/connect/connectors.json` | Utworzenie konektora |
| PATCH | `/connect/connectors/:id.json` | Aktualizacja konektora |
| DELETE | `/connect/connectors/:id.json` | Usunięcie konektora |
| GET | `/connect/connectors/:id/check.json` | Test połączenia |
| POST | `/connect/connectors/:id/action.json` | Wykonanie akcji konektora |
| GET | `/connect/connectors/definitions.json` | Lista typów konektorów |
| GET | `/connect/connectors/:id/method/:method` | Wywołanie publicznej metody konektora |

### Pola konektora

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa |
| `kind` | string | tak | Typ konektora (patrz [Typy konektorów](#typy-konektorów)) |
| `code` | string | nie | Unikalny kod |
| `url` | string | nie | URL bazowy API (dla konektorów API) |
| `secret_token` | string | nie | Token/klucz API (szyfrowany) |
| `active` | boolean | nie | Czy aktywny |
| `log_request` | boolean | nie | Czy logować requesty |
| `fields` | object | nie | Konfiguracja użytkownika |
| `methods` | object | nie | Definicje metod (dla custom connector) |

### Utworzenie konektora REST API

```
POST /connect/connectors.json
Content-Type: application/json

{
  "connector": {
    "name": "Moje API",
    "kind": "api/rest",
    "url": "https://api.example.com",
    "secret_token": "sk-123456",
    "active": true
  }
}
```

### Test połączenia

```
GET /connect/connectors/:id/check.json
```

Zwraca status połączenia z zewnętrznym serwisem.

---

## Flow

Flow to automatyzacja - definiuje przepływ danych między konektorami, transformacje i akcje.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/connect/flows.json` | Lista flow |
| GET | `/connect/flows/:id.json` | Szczegóły flow |
| POST | `/connect/flows.json` | Utworzenie flow |
| PATCH | `/connect/flows/:id.json` | Aktualizacja flow |
| DELETE | `/connect/flows/:id.json` | Usunięcie flow |
| POST | `/connect/flows/:id/start.json` | Ręczne uruchomienie flow |
| GET | `/connect/flows/transforms.json` | Lista dostępnych transformacji |

### Pola flow

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa |
| `code` | string | nie | Unikalny kod |
| `kind` | string | nie | Typ flow |
| `source` | string | nie | Źródło danych: `email`, `payment`, `json_object`, `json_array` |
| `active` | boolean | nie | Czy aktywne |
| `condition` | string | nie | Warunek uruchomienia |
| `fields` | object | nie | Konfiguracja i zmienne |

### Ręczne uruchomienie flow

```
POST /connect/flows/:id/start.json
Content-Type: application/json

{
  "data": {
    "key": "value"
  }
}
```

---

## Procesy Flow (FlowProcesses)

Proces to pojedyncze wykonanie flow - śledzi postęp, dane wejściowe/wyjściowe i status.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/connect/flow_processes.json` | Lista procesów |
| GET | `/connect/flow_processes/:id.json` | Szczegóły procesu |
| GET | `/connect/flow_processes/:id/stop.json` | Zatrzymanie procesu |

### Pola procesu

| Pole | Typ | Opis |
|------|-----|------|
| `stage` | string | Bieżący etap przetwarzania |
| `status` | string | Status: `pending`, `running`, `suspended`, `completed`, `error` |
| `object` | object | Bieżące dane |
| `env` | object | Zmienne środowiskowe |
| `debug` | object | Informacje debugowania (błędy, logi requestów) |
| `duration` | float | Czas wykonania (sekundy) |

---

## Obiekty danych (DataObjects)

Obiekt danych przechowuje dane przepływające przez integracje (faktury, zamówienia, dokumenty).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/connect/data_objects.json` | Lista obiektów |
| GET | `/connect/data_objects/:id.json` | Szczegóły obiektu |
| POST | `/connect/data_objects.json` | Utworzenie obiektu |
| PATCH | `/connect/data_objects/:id.json` | Aktualizacja obiektu |
| DELETE | `/connect/data_objects/:id.json` | Usunięcie obiektu |
| POST | `/connect/data_objects/:id/integration_trigger.json` | Uruchomienie flow z danymi |
| GET | `/connect/data_objects/:id/transform/:from/:to.json` | Transformacja formatu danych |

### Pola obiektu danych

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `object_name` | string | tak | Typ obiektu (np. `json_object`, `fakturownia_invoice`) |
| `object_id` | string | nie | Zewnętrzne ID |
| `data` | object | nie | Dane obiektu (JSON) |
| `content` | text | nie | Treść (XML, CSV, tekst) |
| `content_format` | string | nie | Format: `xml`, `json`, `csv`, `pdf`, `txt`, `mt940`, `xlsx`, `zip` |

### Utworzenie obiektu i uruchomienie flow

```
POST /connect/data_objects.json
Content-Type: application/json

{
  "data_object": {
    "object_name": "json_object",
    "data": {
      "invoice_number": "FV/2026/001",
      "amount": 1500.00,
      "currency": "PLN"
    }
  }
}
```

```
POST /connect/data_objects/:id/integration_trigger.json
```

---

## Aplikacje (Apps)

Aplikacje InConnector to gotowe widgety i usługi działające na konektorach (płatności, e-commerce, import/eksport).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/connect/apps.json` | Lista aplikacji |
| GET | `/connect/apps/:id.json` | Szczegóły aplikacji |
| POST | `/connect/apps.json` | Utworzenie aplikacji |
| PATCH | `/connect/apps/:id.json` | Aktualizacja aplikacji |
| DELETE | `/connect/apps/:id.json` | Usunięcie aplikacji |
| GET | `/connect/apps/:id/widget_content` | Treść widgetu (HTML) |

### Pola aplikacji

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa |
| `code` | string | nie | Unikalny kod |
| `kind` | string | tak | Typ aplikacji |
| `active` | boolean | nie | Czy aktywna |
| `fields` | object | nie | Konfiguracja |
| `secret_token` | string | nie | Klucz API (szyfrowany) |

---

## Typy konektorów

### API

| Typ (`kind`) | Opis |
|-------------|------|
| `api/rest` | Uniwersalny konektor REST API |
| `api/fakturownia` | Fakturownia.pl |
| `api/ksef` | KSeF (Krajowy System e-Faktur) |
| `api/sugester` | Sugester CRM |
| `api/inconnector` | Inny system InConnector |

### Płatności

| Typ (`kind`) | Opis |
|-------------|------|
| `payment/payu` | PayU |
| `payment/stripe` | Stripe |
| `payment/przelewy24` | Przelewy24 |
| `payment/tpay` | Tpay |
| `payment/dotpay` | Dotpay |
| `payment/blue_media` | Blue Media |
| `payment/braintree` | Braintree |
| `payment/pay_pal` | PayPal |
| `payment/paynow` | Paynow |
| `payment/bank_transfer` | Przelew bankowy |

### E-commerce

| Typ (`kind`) | Opis |
|-------------|------|
| `ecommerce/shopify` | Shopify |
| `ecommerce/shoper` | Shoper |
| `ecommerce/allegro` | Allegro |
| `ecommerce/woocommerce` | WooCommerce |
| `ecommerce/shoptet` | Shoptet |
| `ecommerce/ido_sell` | IdoSell |
| `ecommerce/google_merchant` | Google Merchant Center |

### Wiadomości

| Typ (`kind`) | Opis |
|-------------|------|
| `message/emaillabs` | EmailLabs |
| `message/sendgrid` | SendGrid |
| `message/smsapi` | SMSAPI |
| `message/google_chat` | Google Chat |

### AI

| Typ (`kind`) | Opis |
|-------------|------|
| `ai/openai` | OpenAI (GPT) |
| `ai/claude` | Claude (Anthropic) |
| `ai/gemini` | Gemini (Google) |

### Inne

| Typ (`kind`) | Opis |
|-------------|------|
| `service/vies` | VIES (weryfikacja VAT UE) |
| `service/gus` | GUS (dane firm z REGON) |
| `api/peppol` | Peppol (e-invoicing) |
| `api/saltedge` | Salt Edge (bankowość open banking) |
| `api/get_response_api` | GetResponse |
| `service/github_webhook` | GitHub Webhook |
| `service/amazon_sqs` | Amazon SQS |
