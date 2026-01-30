[← Spis treści](README.md)

# Organize

Zarządzanie zadaniami, projektami, zespołami i czasem pracy.

**Autoryzacja:** Wszystkie requesty wymagają `api_token` (parametr GET lub header `Authorization: Bearer TOKEN`)

## Spis treści

1. [Zadania (Tasks)](#zadania-tasks)
2. [Komentarze (Comments)](#komentarze-comments)
3. [Projekty (Projects)](#projekty-projects)
4. [Listy zadań (Tasklists)](#listy-zadań-tasklists)
5. [Zespoły (Teams)](#zespoły-teams)
6. [Statusy (Statuses)](#statusy-statuses)
7. [Tagi (Tags)](#tagi-tags)
8. [Czas pracy (Workinfos)](#czas-pracy-workinfos)
9. [Raporty czasu pracy (WorkinfoReports)](#raporty-czasu-pracy-workinforeports)

---

## Zadania (Tasks)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/tasks.json` | Lista zadań |
| GET | `/organize/tasks/:id.json` | Szczegóły zadania |
| POST | `/organize/tasks.json` | Utworzenie zadania |
| PATCH | `/organize/tasks/:id.json` | Aktualizacja zadania |
| DELETE | `/organize/tasks/:id.json` | Usunięcie zadania |
| POST | `/organize/tasks/:id/close.json` | Zamknięcie zadania |
| POST | `/organize/tasks/:id/change_priority.json` | Zmiana priorytetu |
| POST | `/organize/tasks/:id/change_bulk_option.json` | Zmiana opcji (status, zespół itp.) |
| GET | `/organize/tasks/:id/todos.json` | Lista podzadań (todo) |
| GET | `/organize/tasks/:id/attachments.json` | Załączniki zadania |
| GET | `/organize/tasks/find.json` | Wyszukiwanie zadań |

### Parametry filtrowania (index)

| Parametr | Opis |
|----------|------|
| `q` | Wyszukiwanie pełnotekstowe (max 1024 znaki) |
| `closed` | Filtr: zamknięte (`true`/`false`) |
| `responsible` | ID odpowiedzialnego użytkownika |
| `start_time` | `future`, `current`, `all` |
| `view` | Widok: `kanban`, `table`, `list` |
| `group_by` | Grupowanie (dla widoku kanban) |
| `order_by` | Sortowanie |

### Pola zadania

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `title` | string | tak | Tytuł zadania |
| `content` | text | nie | Opis zadania |
| `status_id` | integer | nie | ID statusu |
| `team_id` | integer | nie | ID zespołu |
| `project_id` | integer | nie | ID projektu |
| `tasklist_id` | integer | nie | ID listy zadań |
| `responsible_id` | integer | nie | ID odpowiedzialnego |
| `deadline` | datetime | nie | Termin wykonania |
| `priority` | integer | nie | Priorytet |
| `client_id` | integer | nie | ID klienta CRM |
| `tags` | string | nie | Tagi (nazwy oddzielone przecinkami) |

### Utworzenie zadania

```
POST /organize/tasks.jsonContent-Type: application/json

{
  "task": {
    "title": "Nowe zadanie",
    "content": "Opis zadania",
    "status_id": 1,
    "team_id": 2,
    "responsible_id": 5,
    "deadline": "2026-02-15",
    "priority": 10
  }
}
```

### Zmiana opcji

```
POST /organize/tasks/:id/change_bulk_option.jsonContent-Type: application/json

{
  "type": "status_id",
  "value": 3
}
```

Dostępne typy: `status_id`, `team_id`, `project_id`, `responsible_id`, `tasklist_id`, `priority`, `deadline`

---

## Komentarze (Comments)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/comments.json` | Lista komentarzy |
| GET | `/organize/comments/:id.json` | Szczegóły komentarza |
| POST | `/organize/comments.json` | Utworzenie komentarza |
| PATCH | `/organize/comments/:id.json` | Aktualizacja komentarza |
| DELETE | `/organize/comments/:id.json` | Usunięcie komentarza |
| POST | `/organize/comments/:id/solve_comment` | Oznaczenie jako rozwiązany |

### Pola komentarza

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `content` | text | tak | Treść komentarza |
| `commentable_id` | integer | tak | ID zadania |
| `commentable_type` | string | tak | Typ: `Organize::Task` |
| `responsible_id` | integer | nie | ID osoby przypisanej (pytanie do kogoś) |

### Utworzenie komentarza

```
POST /organize/comments.jsonContent-Type: application/json

{
  "comment": {
    "content": "Treść komentarza",
    "commentable_id": 123,
    "commentable_type": "Organize::Task"
  }
}
```

---

## Projekty (Projects)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/projects.json` | Lista projektów |
| GET | `/organize/projects/:id.json` | Szczegóły projektu |
| POST | `/organize/projects.json` | Utworzenie projektu |
| PATCH | `/organize/projects/:id.json` | Aktualizacja projektu |
| DELETE | `/organize/projects/:id.json` | Usunięcie projektu |
| GET | `/organize/projects/find.json` | Wyszukiwanie projektów |

### Pola projektu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa projektu |
| `description` | text | nie | Opis projektu |
| `kind` | string | nie | Typ projektu |
| `team_id` | integer | nie | ID zespołu |
| `responsible_id` | integer | nie | ID odpowiedzialnego |
| `project_id` | integer | nie | ID projektu nadrzędnego (subprojekty) |
| `client_id` | integer | nie | ID klienta CRM |

### Utworzenie projektu

```
POST /organize/projects.jsonContent-Type: application/json

{
  "project": {
    "name": "Projekt X",
    "description": "Opis projektu",
    "team_id": 2,
    "responsible_id": 5
  }
}
```

---

## Listy zadań (Tasklists)

Listy zadań grupują zadania. Typy: Sprint, Milestone, Lista, Cykl.

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/tasklists.json` | Lista list zadań |
| GET | `/organize/tasklists/:id.json` | Szczegóły listy |
| POST | `/organize/tasklists.json` | Utworzenie listy |
| PATCH | `/organize/tasklists/:id.json` | Aktualizacja listy |
| DELETE | `/organize/tasklists/:id.json` | Usunięcie listy |
| GET | `/organize/tasklists/find.json` | Wyszukiwanie list |

### Pola listy zadań

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa listy |
| `description` | text | nie | Opis listy |
| `kind` | string | nie | Typ: `sprint`, `milestone`, `list`, `cycle` |
| `team_id` | integer | nie | ID zespołu |
| `project_id` | integer | nie | ID projektu |
| `status_id` | integer | nie | ID statusu |
| `date_from` | date | nie | Data rozpoczęcia |
| `date_to` | date | nie | Data zakończenia |

---

## Zespoły (Teams)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/teams.json` | Lista zespołów |
| GET | `/organize/teams/:id.json` | Szczegóły zespołu |
| POST | `/organize/teams.json` | Utworzenie zespołu |
| PATCH | `/organize/teams/:id.json` | Aktualizacja zespołu |
| DELETE | `/organize/teams/:id.json` | Usunięcie zespołu |
| GET | `/organize/teams/your.json` | Twoje zespoły |
| GET | `/organize/teams/find.json` | Wyszukiwanie zespołów |

### Pola zespołu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa zespołu |
| `description` | text | nie | Opis zespołu |
| `responsible_id` | integer | nie | ID lidera zespołu |

---

## Statusy (Statuses)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/statuses.json` | Lista statusów |
| GET | `/organize/statuses/:id.json` | Szczegóły statusu |
| POST | `/organize/statuses.json` | Utworzenie statusu |
| PATCH | `/organize/statuses/:id.json` | Aktualizacja statusu |
| DELETE | `/organize/statuses/:id.json` | Usunięcie statusu |
| POST | `/organize/statuses/:id/up` | Przesuń wyżej (priorytet) |
| POST | `/organize/statuses/:id/down` | Przesuń niżej (priorytet) |

### Pola statusu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa statusu |
| `description` | text | nie | Opis statusu |
| `category` | string | nie | Kategoria statusu |
| `color` | string | nie | Kolor (hex) |
| `priority` | integer | nie | Kolejność wyświetlania |

---

## Tagi (Tags)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/tags.json` | Lista tagów |
| GET | `/organize/tags/:id.json` | Szczegóły tagu |
| POST | `/organize/tags.json` | Utworzenie tagu |
| PATCH | `/organize/tags/:id.json` | Aktualizacja tagu |
| DELETE | `/organize/tags/:id.json` | Usunięcie tagu |
| POST | `/organize/tags/find_or_create` | Znajdź lub utwórz tag i przypisz |
| POST | `/organize/tags/join_tags.json` | Scalenie tagów |

### Pola tagu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `name` | string | tak | Nazwa tagu |
| `description` | text | nie | Opis tagu |
| `color` | string | nie | Kolor (hex) |
| `taggable_type` | string | nie | Typ obiektu do przypisania |
| `taggable_id` | integer | nie | ID obiektu do przypisania |

---

## Czas pracy (Workinfos)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/workinfos.json` | Lista wpisów czasu pracy |
| GET | `/organize/workinfos/:id.json` | Szczegóły wpisu |
| POST | `/organize/workinfos.json` | Utworzenie wpisu |
| PATCH | `/organize/workinfos/:id.json` | Aktualizacja wpisu |
| DELETE | `/organize/workinfos/:id.json` | Usunięcie wpisu |
| POST | `/organize/workinfos/start.json` | Start timera |
| POST | `/organize/workinfos/stop.json` | Stop timera |
| GET | `/organize/workinfos/report.json` | Raport czasu pracy |

### Parametry filtrowania (index)

| Parametr | Opis |
|----------|------|
| `date` | Data (YYYY-MM-DD) |
| `user_id` | ID użytkownika |
| `task_id` | ID zadania |

### Pola wpisu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `content` | text | nie | Opis pracy |
| `date` | date | tak | Data wpisu |
| `hours` | decimal | nie | Liczba godzin |
| `time_from` | time | nie | Czas rozpoczęcia |
| `time_to` | time | nie | Czas zakończenia |
| `user_id` | integer | nie | ID użytkownika |
| `task_id` | integer | nie | ID zadania |
| `project_id` | integer | nie | ID projektu |
| `team_id` | integer | nie | ID zespołu |
| `client_id` | integer | nie | ID klienta CRM |

### Start/Stop timera

```
POST /organize/workinfos/start.jsonContent-Type: application/json

{
  "task_id": 123
}
```

```
POST /organize/workinfos/stop.jsonContent-Type: application/json

{
  "task_id": 123
}
```

---

## Raporty czasu pracy (WorkinfoReports)

### API

| Metoda | Ścieżka | Opis |
|--------|---------|------|
| GET | `/organize/workinfo_reports.json` | Lista raportów |
| GET | `/organize/workinfo_reports/:id.json` | Szczegóły raportu |
| POST | `/organize/workinfo_reports.json` | Utworzenie raportu |
| PATCH | `/organize/workinfo_reports/:id.json` | Aktualizacja raportu |
| DELETE | `/organize/workinfo_reports/:id.json` | Usunięcie raportu |
| POST | `/organize/workinfo_reports/:id/approve.json` | Zatwierdzenie raportu |
| POST | `/organize/workinfo_reports/:id/unapprove.json` | Cofnięcie zatwierdzenia |
| GET | `/organize/workinfo_reports/:id/export` | Eksport (PDF/CSV) |

### Parametry filtrowania (index)

| Parametr | Opis |
|----------|------|
| `user_id` | ID użytkownika |
| `team_id` | ID zespołu |
| `date_from` | Data od |
| `date_to` | Data do |
| `approved` | Filtr: zatwierdzone |
| `q` | Wyszukiwanie |

### Pola raportu

| Pole | Typ | Wymagane | Opis |
|------|-----|----------|------|
| `user_id` | integer | tak | ID użytkownika |
| `date_from` | date | tak | Data od |
| `date_to` | date | tak | Data do |
| `team_id` | integer | nie | ID zespołu |
| `corrected_hours` | decimal | nie | Skorygowane godziny |
