[← Spis treści](README.md)

# VoIP

Telefonia internetowa - połączenia, konta SIP, widget callback.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Połączenia (Calls)](#połączenia-calls)
2. [Konta SIP (Sips)](#konta-sip-sips)
3. [Konfiguracja VoIP (Voips)](#konfiguracja-voip-voips)
4. [Widgety callback (Widgets)](#widgety-callback-widgets)
5. [Autocall](#autocall)
6. [Integracje](#integracje)

---

## Połączenia (Calls)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/voip/calls.json` | Lista połączeń |
| GET | `/voip/calls/:id.json` | Szczegóły połączenia |
| POST | `/voip/calls.json` | Utworzenie wpisu połączenia |
| PATCH | `/voip/calls/:id.json` | Aktualizacja połączenia |
| DELETE | `/voip/calls/:id.json` | Usunięcie połączenia |
| POST | `/voip/calls/make_call.json` | Zainicjowanie połączenia |
| POST | `/voip/calls/make_call_integrate.json` | Połączenie przez integrację |

### Pola połączenia

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `voip_id` | integer | nie | ID konfiguracji VoIP |
| `number_from` | string | nie | Numer dzwoniącego |
| `number_to` | string | nie | Numer odbiorcy |
| `start_time` | datetime | nie | Czas rozpoczęcia |
| `duration` | integer | nie | Czas trwania (sekundy) |
| `kind` | string | nie | Typ: przychodzące/wychodzące |
| `status` | string | nie | Status połączenia |
| `client_id` | integer | nie | ID klienta CRM |
| `contact_id` | integer | nie | ID kontaktu CRM |
| `description` | text | nie | Opis |

### Zainicjowanie połączenia

```
POST /voip/calls/make_call_integrate.jsonContent-Type: application/json

{
  "sip": "100",
  "number_to": "+48123456789"
}
```

---

## Konta SIP (Sips)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/voip/sips.json` | Lista kont SIP |
| GET | `/voip/sips/:id.json` | Szczegóły konta |
| POST | `/voip/sips.json` | Utworzenie konta SIP |
| PATCH | `/voip/sips/:id.json` | Aktualizacja konta |
| DELETE | `/voip/sips/:id.json` | Usunięcie konta |

### Pola konta SIP

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa konta |
| `sip` | string | nie | Numer SIP |
| `voip_id` | integer | nie | ID konfiguracji VoIP |
| `username` | string | nie | Login SIP |
| `password` | string | nie | Hasło SIP |
| `kind` | string | nie | Typ konta |
| `number` | string | nie | Numer telefonu |
| `active` | boolean | nie | Czy aktywne |

---

## Konfiguracja VoIP (Voips)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/voip/voips.json` | Lista konfiguracji |
| GET | `/voip/voips/:id.json` | Szczegóły konfiguracji |
| POST | `/voip/voips.json` | Utworzenie konfiguracji |
| PATCH | `/voip/voips/:id.json` | Aktualizacja |
| DELETE | `/voip/voips/:id.json` | Usunięcie |

### Pola konfiguracji

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa |
| `number` | string | nie | Numer główny |
| `provider` | string | nie | Dostawca VoIP |
| `extension_number` | string | nie | Numer wewnętrzny |
| `address` | string | nie | Adres serwera |
| `port` | string | nie | Port serwera |

---

## Widgety callback (Widgets)

Widget do osadzenia na zewnętrznych stronach - przycisk "Zadzwoń do nas" z opcją natychmiastowego połączenia lub zaplanowania rozmowy.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/voip/widgets.json` | Lista widgetów |
| GET | `/voip/widgets/:id.json` | Szczegóły widgetu |
| POST | `/voip/widgets.json` | Utworzenie widgetu |
| PATCH | `/voip/widgets/:id.json` | Aktualizacja widgetu |
| DELETE | `/voip/widgets/:id.json` | Usunięcie widgetu |
| GET | `/voip/widget_data.json` | Dane widgetu (publiczny) |

### Pola widgetu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa widgetu |
| `description` | text | nie | Opis |
| `active` | boolean | nie | Czy aktywny |
| `voip_id` | integer | nie | ID konfiguracji VoIP |
| `locale` | string | nie | Język: `pl`, `en`, `uk`, `es`, `cs` |
| `call_now` | boolean | nie | Zadzwoń teraz (autocall) |
| `call_future` | boolean | nie | Zaplanuj rozmowę |
| `opening_time` | object | nie | Harmonogram godzin otwarcia |

### Widget Data (publiczny endpoint)

```
GET /voip/widget_data.json?widget_token=TOKEN
```

---

## Autocall

Automatyczne połączenia zwrotne z widgetu.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/voip/autocalls.json` | Lista autocalli |
| GET | `/voip/autocalls/:id.json` | Szczegóły |
| POST | `/voip/autocalls.json` | Utworzenie |
| PATCH | `/voip/autocalls/:id.json` | Aktualizacja |
| DELETE | `/voip/autocalls/:id.json` | Usunięcie |
| POST | `/voip/autocalls/create_from_widget` | Utworzenie z widgetu (publiczny) |

### Utworzenie z widgetu (publiczny)

```
POST /voip/autocalls/create_from_widget
Content-Type: application/json

{
  "widget_token": "TOKEN",
  "autocall": {
    "number": "+48123456789"
  }
}
```

---

## Integracje

| Dostawca | Opis |
|----------|------|
| **Telecube** | Webhooks: queue_start, queue_hangup, call_start, call_end |
| **SuperVoIP** | Webhook ze statusami połączeń |
| **Datera** | Integracja VoIP |

### Statusy połączeń (Telecube)

| Status | Opis |
|--------|------|
| `ANSWERED` | Połączenie odebrane |
| `BUSY` | Zajęte |
| `FAILED` | Nieudane |
| `NO ANSWER` | Brak odpowiedzi |
