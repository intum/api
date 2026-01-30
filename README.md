# Intum API

Dokumentacja API platformy [Intum](https://intum.pl) - system operacyjny firmy.

## Spis treści

### Moduły

- [Organize](organize.md) - zadania, projekty, zespoły, listy zadań, czas pracy
  - [Zadania (Tasks)](organize.md#zadania-tasks) - tworzenie, przypisywanie, filtrowanie, operacje zbiorcze
  - [Komentarze (Comments)](organize.md#komentarze-comments) - komentarze do zadań
  - [Projekty (Projects)](organize.md#projekty-projects) - organizacja zadań w projekty i podprojekty
  - [Listy zadań (Tasklists)](organize.md#listy-zadań-tasklists) - sprint, milestone, lista, cykl
  - [Zespoły (Teams)](organize.md#zespoły-teams) - grupy użytkowników
  - [Statusy (Statuses)](organize.md#statusy-statuses) - stany zadań
  - [Tagi (Tags)](organize.md#tagi-tags) - etykiety zadań
  - [Czas pracy (Workinfos)](organize.md#czas-pracy-workinfos) - logowanie czasu, timer
  - [Raporty czasu pracy (WorkinfoReports)](organize.md#raporty-czasu-pracy-workinforeports) - zatwierdzanie, eksport
- [Mail](mail.md) - poczta elektroniczna, skrzynki, szablony, filtry
  - [Emaile](mail.md#emaile) - wysyłanie, odbieranie, drafty, spam, archiwum, snooze
  - [Skrzynki (Mailboxes)](mail.md#skrzynki-mailboxes) - konfiguracja, weryfikacja, autoresponder
  - [Szablony (Templates)](mail.md#szablony-templates) - szablony wiadomości z Liquid
  - [Filtry (Filters)](mail.md#filtry-filters) - automatyczne reguły przetwarzania emaili
  - [Filtry spamu (SpamFilters)](mail.md#filtry-spamu-spamfilters) - blokowanie nadawców
  - [Foldery (Folders)](mail.md#foldery-folders) - organizacja emaili
  - [Notatki (Notes)](mail.md#notatki-notes) - wewnętrzne notatki do emaili
- [CRM](crm.md) - klienci, kontakty, deale, statusy
  - [Klienci (Clients)](crm.md#klienci-clients) - firmy i osoby, upsert, merge, operacje zbiorcze
  - [Kontakty (Contacts)](crm.md#kontakty-contacts) - osoby powiązane z klientem
  - [Deale (Deals)](crm.md#deale-deals) - szanse sprzedażowe
  - [Statusy (Statuses)](crm.md#statusy-statuses) - etapy procesu sprzedażowego
  - [Notatki (Notes)](crm.md#notatki-notes) - notatki do klientów
- [Chat](chat.md) - komunikacja w czasie rzeczywistym, widgety
  - [Przestrzenie (Spaces)](chat.md#przestrzenie-spaces) - rozmowy z klientami, otwieranie/zamykanie
  - [Wiadomości (Messages)](chat.md#wiadomości-messages) - wysyłanie wiadomości z załącznikami
  - [Widgety (Widgets)](chat.md#widgety-widgets) - widget czatu do osadzenia na stronach
  - [Szablony (Templates)](chat.md#szablony-templates) - szablony szybkich odpowiedzi
- [VoIP](voip.md) - telefonia internetowa, połączenia, konta SIP
  - [Połączenia (Calls)](voip.md#połączenia-calls) - historia, inicjowanie połączeń
  - [Konta SIP (Sips)](voip.md#konta-sip-sips) - konfiguracja kont SIP
  - [Widgety callback (Widgets)](voip.md#widgety-callback-widgets) - przycisk "Zadzwoń do nas" na stronach
- [Baza Wiedzy (KB)](kb.md) - bazy wiedzy, artykuły, helplinki, changelog
  - [Bazy wiedzy (KnowledgeBases)](kb.md#bazy-wiedzy-knowledgebases) - własna domena, styl, przeznaczenie
  - [Artykuły (Entries)](kb.md#artykuły-entries) - treści, drafty, publikacja
  - [Kategorie (Categories)](kb.md#kategorie-categories) - struktura artykułów
  - [Helplinki](kb.md#helplinki) - kontekstowe podpowiedzi, widget JS
  - [Komentarze (Comments)](kb.md#komentarze-comments) - komentarze publiczne i wewnętrzne
- [Insight](insight.md) - raporty, datasety, wykresy, alerty
  - [Raporty (Reports)](insight.md#raporty-reports) - wizualizacje danych, agregacja
  - [Alerty (Alerts)](insight.md#alerty-alerts) - powiadomienia i monitorowanie
- [Form](form.md) - formularze, pola, wyniki, osadzanie na stronach
- [CMS](cms.md) - strony internetowe, szablony, paragrafy, artykuły
  - [Strony (Sites)](cms.md#strony-sites) - witryny z własną domeną
  - [Podstrony (Pages)](cms.md#podstrony-pages) - treść, menu, SEO
  - [Layouty (Layouts)](cms.md#layouty-layouts) - szablony HTML/Liquid
  - [Paragrafy (Paragraphs)](cms.md#paragrafy-paragraphs) - bloki treści osadzane w stronach
  - [Artykuły (Articles)](cms.md#artykuły-articles) - blog, aktualności
  - [Pliki (Assets)](cms.md#pliki-assets) - JS, CSS, obrazy, foldery
- [InConnector](inconnector.md) - integracje z zewnętrznymi serwisami
- [Noe w Intum](noe-intum.md) - aplikacje Noe jako dodatki do modułów Intum
- [AppStore](appstore.md) - marketplace aplikacji i płatnych dodatków
- [Finance](finance.md) - fakturowanie, paragony fiskalne i płatności
  - [Faktury (Invoices)](finance.md#faktury-invoices) - Fakturownia: [github.com/fakturownia/API](https://github.com/fakturownia/API)
  - [Paragony (eParagony)](finance.md#paragony-eparagony) - eParagony: [github.com/e-paragony/api](https://github.com/
  - [Billing](billing.md) - plany, subskrypcje, płatności, kody promocyjne
e-paragony/api)
- [Konto](account.md) - użytkownicy, role, grupy, działy, domeny, tokeny API, webhooks
  - [Konto (Account)](account.md#konto-account) - dane konta, tworzenie nowych kont
  - [Użytkownicy (UserSettings)](account.md#użytkownicy-usersettings) - zarządzanie użytkownikami, masowe tworzenie, synchronizacja
  - [Role (Roles)](account.md#role-roles) - uprawnienia, wbudowane i własne role
  - [Grupy (Groups)](account.md#grupy-groups) - grupy użytkowników (mail, zadania, czat)
  - [Działy (Departments)](account.md#działy-departments) - działy organizacyjne z telefonem i emailem
  - [Tokeny API (ApiTokens)](account.md#tokeny-api-apitokens) - api_token, web_token, JWT
  - [Domeny (Domains)](account.md#domeny-domains) - własne domeny konta
  - [Webhooks](account.md#webhooks) - powiadomienia o zdarzeniach
  - [Ustawienia (Settings)](account.md#ustawienia-settings) - uniwersalny endpoint konfiguracji

### Platforma

- [MCP Server](settings/mcp.md) - Model Context Protocol - narzędzia dla aplikacji AI
  - [Endpointy](settings/mcp.md#endpointy) - `/mcp` (Organize) i `/noe/mcp` (Noe)
  - [Dostępne narzędzia](settings/mcp.md#dostępne-narzędzia) - task_list, task_show, app_create, connector_create, flow_create, transform_create
  - [Protokół JSON-RPC 2.0](settings/mcp.md#protokół-json-rpc-20) - initialize, tools/list, tools/call
- [Webhooks](settings/webhooks.md) - powiadamianie zewnętrznych usług o zdarzeniach
  - [Konfiguracja](settings/webhooks.md#konfiguracja) - url, source_type, kind
  - [Payload](settings/webhooks.md#payload) - format danych wysyłanych przez webhook
  - [Dostępne akcje](settings/webhooks.md#dostępne-akcje) - create, update, destroy
- [Pola dodatkowe](settings/additional_fields.md) - dynamiczne pola w modelach
  - [Konfiguracja](settings/additional_fields.md#konfiguracja) - definiowanie pól przez endpoint ustawień
  - [Typy pól](settings/additional_fields.md#typy-pól) - string, integer, float, date, datetime, boolean, select
  - [Obsługiwane modele](settings/additional_fields.md#obsługiwane-modele) - klient CRM, kontakt CRM, zadanie

## Autoryzacja

Wszystkie requesty API wymagają `api_token`:
- jako parametr GET: `?api_token=TOKEN`
- lub header: `Authorization: Bearer TOKEN`

