[← Spis treści](../README.md)

# MCP Server

**MCP** (Model Context Protocol) to protokół komunikacji między aplikacjami AI (Claude, ChatGPT) a platformą Intum. Umożliwia AI wywoływanie narzędzi i pobieranie danych z systemu.

**Autoryzacja:** Wymagana autoryzacja użytkownika

## Spis treści

1. [Endpointy](#endpointy)
2. [Dostępne narzędzia](#dostępne-narzędzia)
3. [Protokół JSON-RPC 2.0](#protokół-json-rpc-20)
4. [Użycie](#użycie)

---

## Endpointy

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| POST | `/mcp` | Główny serwer MCP (narzędzia Organize) |
| POST | `/noe/mcp` | Serwer MCP dla Noe (aplikacje, konektory, flow, transformacje) |

---

## Dostępne narzędzia

### Endpoint `/mcp`

| Narzędzie | Opis | Parametry |
|-----------|------|-----------|
| `task_list_tool` | Lista zadań | `status` (open/closed/all), `limit` (max 100), `responsible_id` |
| `task_show_tool` | Szczegóły zadania | `id` (ID zadania) |

### Endpoint `/noe/mcp`

| Narzędzie | Opis | Parametry |
|-----------|------|-----------|
| `app_list_tool` | Lista aplikacji Noe | `limit` (max 100) |
| `app_create_tool` | Tworzenie aplikacji | `name`, `source_code`, `app_engine`, `url_code`, `css_engine`, `public` |
| `connector_create_tool` | Tworzenie konektora | `name`, `code`, `url`, `methods`, `secret_token`, `active` |
| `flow_create_tool` | Tworzenie flow | `name`, `code`, `program`, `active` |
| `transform_create_tool` | Tworzenie transformacji | `name`, `code`, `kind`, `transform_code`, `from`, `to` |

---

## Protokół JSON-RPC 2.0

### Inicjalizacja sesji

```
POST /mcp
Content-Type: application/json

{
  "jsonrpc": "2.0",
  "method": "initialize",
  "params": {
    "protocolVersion": "2024-11-05",
    "capabilities": {},
    "clientInfo": { "name": "moj-klient", "version": "1.0" }
  },
  "id": 1
}
```

### Lista narzędzi

```
POST /mcp
Content-Type: application/json

{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 2
}
```

### Wywołanie narzędzia

```
POST /mcp
Content-Type: application/json

{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "task_list_tool",
    "arguments": { "status": "open", "limit": 10 }
  },
  "id": 3
}
```

### Dostępne metody

| Metoda | Opis |
|--------|------|
| `initialize` | Inicjalizacja sesji |
| `ping` | Sprawdzenie połączenia |
| `tools/list` | Lista dostępnych narzędzi |
| `tools/call` | Wywołanie narzędzia |
| `prompts/list` | Lista promptów |
| `prompts/get` | Pobranie promptu |

---

## Użycie

MCP może być użyte do integracji z:
- **Claude Desktop** - dodanie serwera MCP w konfiguracji
- **ChatGPT** - jako zewnętrzne narzędzie
- **Własne aplikacje AI** - przez HTTP JSON-RPC 2.0
