# ADR-004: Baza danych PostgreSQL i dostęp przez EF Core

- **Status:** Zaakceptowana
- **Data:** 2026-11-02
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

System przechowuje dane relacyjne: użytkownicy, gospodarstwa i członkostwa, produkty z historią zdarzeń, listy zakupów, słowniki, przypomnienia, logi. Potrzebna jest transakcyjność (np. „zamiana pozycji listy w produkt” musi być atomowa), zapytania agregujące do statystyk i wyszukiwanie po nazwie (autouzupełnianie). Zespół zdecydował o PostgreSQL; do ustalenia jest sposób dostępu do danych z .NET.

## 2. Rozważane opcje

### Opcja 1: PostgreSQL + Entity Framework Core (Npgsql)

- **Zalety:**
  - [+] Migracje schematu wersjonowane w repozytorium.
  - [+] LINQ, śledzenie zmian, łatwe transakcje; dobra integracja z ASP.NET Core Identity i Hangfire (osobny schemat).
  - [+] Wsparcie typów PostgreSQL (`jsonb`, `citext`, rozszerzenie `pg_trgm` do wyszukiwania podobnych nazw).
- **Wady:**
  - [-] Ryzyko nieefektywnych zapytań (N+1) bez kontroli.

### Opcja 2: PostgreSQL + Dapper (ręczny SQL)

- **Zalety:**
  - [+] Pełna kontrola nad SQL, wysoka wydajność.
- **Wady:**
  - [-] Ręczne migracje (np. DbUp), więcej kodu, brak integracji z Identity.

### Opcja 3: Baza dokumentowa (MongoDB)

- **Zalety:**
  - [+] Elastyczny schemat.
- **Wady:**
  - [-] Dane są wyraźnie relacyjne; statystyki i spójność między agregatami trudniejsze; niezgodne z decyzją zespołu.

## 3. Decyzja

Wybieramy **PostgreSQL 18** (obraz `postgres:18`; ewentualnie 17, jeśli hosting nie oferuje 18) jako jedyną bazę systemu oraz **EF Core + Npgsql** jako warstwę dostępu do danych. Dla złożonych zapytań statystycznych dopuszczamy surowy SQL przez EF Core (`SqlQuery`) lub widoki bazodanowe.

Konwencje:
- nazwy tabel/kolumn w `snake_case` (`EFCore.NamingConventions`),
- identyfikatory `uuid` (v7 – sortowalne w czasie),
- daty w UTC (`timestamptz`), daty bez czasu (`date`) dla DMT/terminu,
- miękkie usuwanie tam, gdzie wymagają tego statystyki (produkty, definicje),
- osobne schematy: `app` (dane), `identity`, `hangfire`, `logs`.

## 4. Uzasadnienie

Dane są relacyjne i wymagają ACID. EF Core przyspiesza pracę zespołu, daje migracje i integruje się z resztą stosu (Identity, testy z Testcontainers). Ryzyko wydajności kontrolujemy przeglądami i projekcjami (`Select` do DTO, `AsNoTracking`).

## 5. Konsekwencje

### Pozytywne
- [+] Jeden silnik danych dla aplikacji, logów i zadań w tle – prostsze wdrożenie i backup.
- [+] Testy integracyjne na prawdziwym PostgreSQL (Testcontainers) zamiast bazy w pamięci.

### Negatywne
- [-] Każda zmiana modelu wymaga migracji – trzeba ustalić zasadę: jedna osoba scala migracje przy konfliktach.
- [-] Logi w tej samej bazie zwiększają jej rozmiar – ograniczamy retencją.
