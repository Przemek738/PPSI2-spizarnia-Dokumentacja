# Backlog Produktu - Spiżarnia (System do Zarządzania Domową Spiżarnią)

Ten dokument przedstawia backlog projektu Spiżarnia. Zawiera historyjki użytkownika (US), które reprezentują funkcjonalności do zaimplementowania, a także zadania techniczne (TS).

**Uwaga:** Ten sam backlog jest dostępny w formacie `backlog.csv`, który można zaimportować do narzędzi takich jak Jira, Trello czy GitHub Projects. Pełna treść historyjek z kryteriami akceptacji znajduje się w pliku [`user-stories.md`](user-stories.md), a uzasadnienie estymacji i plan sprintów w [`05-estymacja.md`](../estymacja.md).

---

## Legenda

- **Priorytet:**
  - **P0 (Must Have):** Funkcjonalności krytyczne, bez których produkt nie może istnieć (wymagania z tematu projektu i zasad zaliczenia).
  - **P1 (Should Have):** Ważne funkcjonalności, które powinny zostać zaimplementowane, jeśli pozwoli na to czas.
  - **P2 (Could Have):** Funkcjonalności "nice-to-have", które można zrealizować w późniejszym czasie.
- **Status:**
  - **Do zrobienia:** Zaplanowane do realizacji w przypisanym sprincie.
  - **W trakcie:** Aktualnie w implementacji.
  - **Ukończono:** Zaimplementowane i przetestowane (spełnia Definition of Done).
  - **W backlogu:** Pomysły i zadania do późniejszej priorytetyzacji.
- **Estymacja:** Złożoność zadania w Story Points (np. 1, 2, 3, 5, 8...).

---

## Historyjki Użytkownika

### Epik: Konto i Logowanie

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-001 | Rejestracja i logowanie przez e-mail | P0 | 13 | Do zrobienia | Sprint 1 |
| US-002 | Logowanie przez konto Google | P0 | 8 | Do zrobienia | Sprint 4 |

### Epik: Gospodarstwo Domowe

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-003 | Wspólne gospodarstwo z domownikami | P0 | 8 | Do zrobienia | Sprint 2 |

### Epik: Spiżarnia i Kody Kreskowe

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-004 | Dodawanie produktów do spiżarni | P0 | 5 | Do zrobienia | Sprint 2 |
| US-005 | Przeglądanie zawartości spiżarni | P0 | 8 | Do zrobienia | Sprint 2 |
| US-006 | Oznaczanie produktu jako zużyty, wyrzucony lub oddany | P0 | 5 | Do zrobienia | Sprint 3 |
| US-007 | Skanowanie kodu kreskowego | P0 | 8 | Do zrobienia | Sprint 3 |

### Epik: Przypomnienia

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-008 | Przypomnienia o kończących się terminach | P0 | 8 | Do zrobienia | Sprint 4 |

### Epik: Lista Zakupów

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-009 | Wspólna lista zakupów | P0 | 5 | Do zrobienia | Sprint 3 |
| US-010 | Przeniesienie zakupów do spiżarni | P0 | 5 | Do zrobienia | Sprint 4 |

### Epik: Przepisy

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-011 | Propozycje przepisów z tego, co mam w domu | P0 | 8 | Do zrobienia | Sprint 4 |

### Epik: Statystyki

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-012 | Statystyki zakupów i marnowania jedzenia | P0 | 8 | Do zrobienia | Sprint 5 |

### Epik: Panel Administracyjny

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| US-013 | Zarządzanie użytkownikami | P0 | 5 | Do zrobienia | Sprint 5 |
| US-014 | Zarządzanie słownikiem produktów | P0 | 5 | Do zrobienia | Sprint 5 |
| US-015 | Przeglądanie logów aplikacji | P0 | 5 | Do zrobienia | Sprint 5 |

---

## Zadania Techniczne i Zaliczeniowe

### Epik: Zadania Techniczne

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| TS-001 | Repozytorium, struktura solucji i CI | P0 | 3 | Do zrobienia | Sprint 0 |
| TS-002 | Konteneryzacja i docker compose | P0 | 5 | Do zrobienia | Sprint 0 |
| TS-003 | Model danych, migracje i dane startowe | P0 | 5 | Do zrobienia | Sprint 0 |
| TS-004 | Dokumentacja OpenAPI i klient frontendu | P0 | 2 | Do zrobienia | Sprint 0 |
| TS-005 | Logowanie strukturalne | P0 | 3 | Do zrobienia | Sprint 2 |
| TS-006 | Szkielet frontendu | P0 | 5 | Do zrobienia | Sprint 1 |
| TS-007 | Wdrożenie i ciągłe dostarczanie | P0 | 8 | Do zrobienia | Sprint 1 |
| TS-008 | Infrastruktura testów automatycznych | P0 | 5 | Do zrobienia | Sprint 2 |
| TS-009 | Obsługa błędów i walidacja | P0 | 2 | Do zrobienia | Sprint 2 |
| TS-010 | Zabezpieczenia API | P1 | 2 | Do zrobienia | Sprint 5 (cel ambitny) |

### Epik: Zaliczenie Projektu

| ID      | Tytuł | Priorytet | Estymacja | Status | Sprint |
|---------|-------|-----------|-----------|--------|--------|
| ZAD-001 | Sprawozdanie w LaTeX | P0 | 3 | Do zrobienia | Sprint 5 |
| ZAD-002 | Prezentacja działającej aplikacji | P0 | 2 | Do zrobienia | Sprint 5 |

---

## Podsumowanie

| Kategoria | Liczba pozycji | Story Points |
|---|---|---|
| Historyjki użytkownika (US) | 15 | 104 |
| Zadania techniczne (TS) | 10 | 40 |
| Zadania zaliczeniowe (ZAD) | 2 | 5 |
| **Razem** | **27** | **149** |

| Sprint | Termin | Story Points (plan) | Przewidywana pojemność |
|---|---|---|---|
| Sprint 0 | 16.11–22.11 | 15 | ~15 |
| Sprint 1 | 23.11–06.12 | 26 | ~25 |
| Sprint 2 | 07.12–20.12 | 31 | ~30 |
| Sprint 3 | 21.12–10.01 | 18 | ~18 |
| Sprint 4 | 11.01–24.01 | 29 | ~30 |
| Sprint 5 | 25.01–prezentacja | 30 | ~25 |

---

## Zarządzanie Backlogiem

- **Spotkania pielęgnacji backlogu (Backlog Refinement):** Na początku każdego sprintu przeglądamy historyjki zaplanowane na ten sprint i dzielimy je na zadania (backend, frontend, testy) jako osobne issues w GitHub Projects.
- **Definicja Gotowości (Definition of Ready - DoR):** Historyjka może wejść do sprintu, jeśli ma kryteria akceptacji, estymację uzgodnioną przez zespół i uzgodniony kontrakt API.
- **Definicja Ukończenia (Definition of Done - DoD):** Historyjka ma status "Ukończono", gdy spełnia kryteria akceptacji, ma testy, przeszła code review i działa na wdrożonym środowisku.
- **Priorytetyzacja:** Używamy skali P0/P1/P2 odpowiadającej metodzie MoSCoW (Must, Should, Could).

---
