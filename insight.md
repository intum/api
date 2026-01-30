[← Spis treści](README.md)

# Insight

Analityka i raportowanie danych - raporty, wykresy, datasety, alerty.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Raporty (Reports)](#raporty-reports)
2. [Dane wykresu (Chart)](#dane-wykresu-chart)
3. [Datasety (Datasets)](#datasety-datasets)
4. [Pola datasetu (DatasetFields)](#pola-datasetu-datasetfields)
5. [Wartości datasetu (DatasetValues)](#wartości-datasetu-datasetvalues)
6. [Alerty (Alerts)](#alerty-alerts)

---

## Raporty (Reports)

Konfiguracja wizualizacji danych z określonymi polami, zakresem dat i typem agregacji.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/reports.json` | Lista raportów |
| GET | `/insight/reports/:code.json` | Szczegóły raportu (po code) |
| POST | `/insight/reports.json` | Utworzenie raportu |
| PATCH | `/insight/reports/:code.json` | Aktualizacja raportu |
| DELETE | `/insight/reports/:code.json` | Usunięcie raportu |

### Pola raportu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa raportu |
| `description` | text | nie | Opis |
| `code` | string | nie | Unikalny kod (slug) |
| `use_entity` | boolean | nie | Dane per użytkownik (entity) |

### Utworzenie raportu

```
POST /insight/reports.jsonContent-Type: application/json

{
  "report": {
    "name": "Sprzedaż miesięczna",
    "code": "monthly-sales",
    "description": "Raport sprzedaży per miesiąc"
  }
}
```

---

## Dane wykresu (Chart)

Pobieranie danych do wykresów (liniowe, słupkowe, kołowe).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/reports/:code/chart.json` | Dane raportu (JSON) |
| GET | `/insight/reports/:code/chart.csv` | Eksport do CSV |

### Parametry

| Parametr | Opis |
|----------|------|
| `date_from` | Data początkowa (wymagane) |
| `date_to` | Data końcowa (wymagane) |
| `group` | Grupowanie: `day`, `week`, `month`, `year` |
| `transform` | `2` = format zoptymalizowany dla wykresów |
| `aggregate` | Typ agregacji |
| `formatted` | Sformatowane wartości |

### Pobranie danych wykresu

```
GET /insight/reports/monthly-sales/chart.json?date_from=2026-01-01&date_to=2026-01-31&group=day&transform=2
```

---

## Datasety (Datasets)

Zestawy danych do analizy.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/datasets.json` | Lista datasetów |
| GET | `/insight/datasets/:id.json` | Szczegóły datasetu |
| POST | `/insight/datasets.json` | Utworzenie datasetu |
| PATCH | `/insight/datasets/:id.json` | Aktualizacja datasetu |
| DELETE | `/insight/datasets/:id.json` | Usunięcie datasetu |

### Pola datasetu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa datasetu |
| `kind` | string | nie | Typ datasetu |
| `description` | text | nie | Opis |
| `code` | string | nie | Unikalny kod |

---

## Pola datasetu (DatasetFields)

Definicja struktury danych w datasecie.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/dataset_fields.json` | Lista pól |
| GET | `/insight/dataset_fields/:id.json` | Szczegóły pola |
| POST | `/insight/dataset_fields.json` | Utworzenie pola |
| PATCH | `/insight/dataset_fields/:id.json` | Aktualizacja pola |
| DELETE | `/insight/dataset_fields/:id.json` | Usunięcie pola |

### Pola

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa pola |
| `code` | string | nie | Kod pola |
| `kind` | string | nie | Typ pola |
| `description` | text | nie | Opis |

---

## Wartości datasetu (DatasetValues)

Przechowywane dane w datasecie.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/dataset_values.json` | Lista wartości |
| GET | `/insight/dataset_values/:id.json` | Szczegóły wartości |
| POST | `/insight/dataset_values.json` | Dodanie wartości |
| PATCH | `/insight/dataset_values/:id.json` | Aktualizacja wartości |
| DELETE | `/insight/dataset_values/:id.json` | Usunięcie wartości |

### Pola

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `datetime` | datetime | tak | Data/czas wartości |
| `dataset_id` | integer | tak | ID datasetu |
| `dataset_field_id` | integer | tak | ID pola datasetu |
| `value` | decimal | tak | Wartość |

### Dodanie wartości

```
POST /insight/dataset_values.jsonContent-Type: application/json

{
  "insight_dataset_value": {
    "datetime": "2026-01-30T12:00:00",
    "dataset_id": 1,
    "dataset_field_id": 2,
    "value": 150.5
  }
}
```

---

## Alerty (Alerts)

Powiadomienia o zdarzeniach i monitorowanie stanu systemu.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/insight/alerts.json` | Lista alertów |
| GET | `/insight/alerts/:id.json` | Szczegóły alertu |
| POST | `/insight/alerts.json` | Utworzenie alertu |
| PATCH | `/insight/alerts/:id.json` | Aktualizacja alertu |
| DELETE | `/insight/alerts/:id.json` | Usunięcie alertu |
| GET | `/insight/alerts/system_checker.json` | Status systemu (publiczny) |

### System Checker (publiczny)

```
GET /insight/alerts/system_checker.json
```

Odpowiedź:
```json
{
  "status": "ok",
  "checks": [...],
  "errors": [],
  "time": "2026-01-30T12:00:00"
}
```

---

## Entity - dane per użytkownik

Pole `entity` umożliwia grupowanie danych raportów per użytkownik. Format: `u_{user_id}`.

## Wskazówki

- Używaj `transform=2` dla danych wykresowych (format zoptymalizowany dla Chart.js)
- Zawsze podawaj `date_from` i `date_to` przy pobieraniu danych wykresu
- Cachuj dane po stronie klienta
