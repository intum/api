[← Spis treści](README.md)

# Billing

Subskrypcje, plany, płatności i kody promocyjne.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Produkty (Products)](#produkty-products)
2. [Plany (Plans)](#plany-plans)
3. [Addony (Addons)](#addony-addons)
4. [Cennik (PricelistItems)](#cennik-pricelist-items)
5. [Subskrypcje (Subscriptions)](#subskrypcje-subscriptions)
6. [Płatności (Payments)](#płatności-payments)
7. [Klienci billingowi (Customers)](#klienci-billingowi-customers)
8. [Kody promocyjne (Promocodes)](#kody-promocyjne-promocodes)
9. [Kredyty (Credits)](#kredyty-credits)

---

## Produkty (Products)

Produkt odpowiada jednej aplikacji (np. Fakturownia). Cały billing jest scopowany per produkt.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/products.json` | Lista produktów |
| GET | `/billing/products/:id.json` | Szczegóły produktu |
| GET | `/billing/products/:id/prices.json` | Cennik produktu |
| GET | `/billing/products/:id/public_prices.json` | Cennik publiczny |
| GET | `/billing/products/:id/addons.json` | Lista addonów |
| GET | `/billing/products/:id/billing_info.json` | Informacje billingowe |
| POST | `/billing/products/:id/subscribe.json` | Zakup subskrypcji |
| POST | `/billing/products/:id/subscribe_free_plan.json` | Aktywacja darmowego planu |
| POST | `/billing/products/:id/subscribe_trial_plan.json` | Aktywacja trialu |
| POST | `/billing/products/:id/downgrade.json` | Downgrade planu |
| POST | `/billing/products/:id/reactivate.json` | Reaktywacja subskrypcji |

---

## Plany (Plans)

Plan subskrypcyjny z hierarchią. Wyższy `tier` = wyższy plan.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/plans.json` | Lista planów |
| GET | `/billing/plans/:id.json` | Szczegóły planu |
| POST | `/billing/plans.json` | Utworzenie planu |
| PATCH | `/billing/plans/:id.json` | Aktualizacja planu |
| DELETE | `/billing/plans/:id.json` | Usunięcie planu |

### Pola planu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `code` | string | tak | Unikalny kod planu |
| `tier` | integer | tak | Pozycja w hierarchii (0 = najniższy) |

---

## Addony (Addons)

Dodatki do planów. Mogą mieć niezależny trial.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/addons.json` | Lista addonów |
| GET | `/billing/addons/:id.json` | Szczegóły addonu |
| POST | `/billing/addons.json` | Utworzenie addonu |
| PATCH | `/billing/addons/:id.json` | Aktualizacja addonu |
| DELETE | `/billing/addons/:id.json` | Usunięcie addonu |

---

## Cennik (PricelistItems)

Elementy cennika z ceną, okresem i ilością. Przechowuje historyczne ceny (starsze mają `date_from = nil`).

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/pricelist_items.json` | Lista elementów cennika |
| GET | `/billing/pricelist_items/:id.json` | Szczegóły elementu |
| POST | `/billing/pricelist_items.json` | Utworzenie elementu |
| PATCH | `/billing/pricelist_items/:id.json` | Aktualizacja elementu |
| DELETE | `/billing/pricelist_items/:id.json` | Usunięcie elementu |

---

## Subskrypcje (Subscriptions)

Łączy klienta billingowego z elementem cennika.

**Cykl życia:**
1. Rozpoczęcie kupna → aktywna, nieopłacona
2. Opłacenie → aktywna, opłacona
3. Upgrade lub wygaśnięcie → nieaktywna, opłacona

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/subscriptions.json` | Lista subskrypcji |
| GET | `/billing/subscriptions/:id.json` | Szczegóły subskrypcji |
| POST | `/billing/subscriptions.json` | Utworzenie subskrypcji |
| PATCH | `/billing/subscriptions/:id.json` | Aktualizacja subskrypcji |
| DELETE | `/billing/subscriptions/:id.json` | Usunięcie subskrypcji |
| POST | `/billing/subscriptions/subscribe` | Zakup subskrypcji |

---

## Płatności (Payments)

Płatność za subskrypcję. Token płatności przekazywany do systemu fakturowania. Po opłaceniu subskrypcja staje się aktywna.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/payments.json` | Lista płatności |
| GET | `/billing/payments/:id.json` | Szczegóły płatności |

---

## Klienci billingowi (Customers)

Klient billingowy powiązany z kontem w systemie fakturowania.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/customers.json` | Lista klientów |
| GET | `/billing/customers/:id.json` | Szczegóły klienta |
| POST | `/billing/customers.json` | Utworzenie klienta |
| PATCH | `/billing/customers/:id.json` | Aktualizacja klienta |
| DELETE | `/billing/customers/:id.json` | Usunięcie klienta |
| GET | `/billing/customers/:id/update_billing_info` | Aktualizacja danych billingowych |

---

## Kody promocyjne (Promocodes)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/promocodes.json` | Lista kodów |
| GET | `/billing/promocodes/:id.json` | Szczegóły kodu |
| POST | `/billing/promocodes.json` | Utworzenie kodu |
| PATCH | `/billing/promocodes/:id.json` | Aktualizacja kodu |
| DELETE | `/billing/promocodes/:id.json` | Usunięcie kodu |
| GET | `/billing/promocodes/check.json` | Sprawdzenie kodu |

### Sprawdzenie kodu

```
GET /billing/promocodes/check.json?code=SUMMER2026
```

### Typy kodów

- **Procentowy** (domyślny) - zniżka procentowa
- **Kwotowy** - zniżka kwotowa
- **Dynamiczny** - zmienia się co tydzień, zniżka 20%
- **Partnerski** - kod polecenia, zniżka dla obu stron, naliczany automatycznie

---

## Kredyty (Credits)

Kredyty przyznawane przy downgrade planu. Wartość = różnica pozostałej kwoty wyższego i niższego planu. Kredyty wykorzystywane automatycznie przy upgrade.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/billing/credits.json` | Lista kredytów |
| GET | `/billing/credits/:id.json` | Szczegóły kredytu |
