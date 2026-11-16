# ADR-006: Zadania w tle – Hangfire z magazynem w PostgreSQL

- **Status:** Zaakceptowana
- **Data:** 2026-11-06
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga, aby przypomnienia o kończących się terminach były **generowane przez zadania wykonywane w tle**. Oprócz tego potrzebujemy:
- cyklicznego wyszukiwania produktów kończących się / przeterminowanych (np. codziennie o 7:00 czasu polskiego),
- wysyłki e-maili poza żądaniem HTTP (z ponawianiem),
- czyszczenia starych logów i wygasłych zaproszeń/tokenów,

Zadania nie mogą się dublować po restarcie kontenera, a administrator powinien móc zobaczyć ich stan.

## 2. Rozważane opcje

### Opcja 1: Hangfire + Hangfire.PostgreSql

- **Zalety:**
  - [+] Zadania cykliczne (CRON), jednorazowe i odroczone; automatyczne ponawianie.
  - [+] Stan zadań trwały w PostgreSQL – przeżywa restart.
  - [+] Wbudowany dashboard (możemy go udostępnić tylko administratorowi).
- **Wady:**
  - [-] Dodatkowe tabele w bazie; magazyn PostgreSQL to pakiet społecznościowy.

### Opcja 2: Quartz.NET

- **Zalety:**
  - [+] Bardzo elastyczne harmonogramy, trwały magazyn w bazie.
- **Wady:**
  - [-] Więcej konfiguracji; brak gotowego dashboardu; brak prostej kolejki „wyślij e-mail teraz”.

### Opcja 3: Własny `BackgroundService` z `PeriodicTimer`

- **Zalety:**
  - [+] Zero zależności, wbudowane w .NET.
- **Wady:**
  - [-] Brak trwałości, ponawiania i podglądu – wszystko trzeba napisać; łatwo o duplikaty przy wielu instancjach.

## 3. Decyzja

Wybieramy **Hangfire** z magazynem **Hangfire.PostgreSql** (schemat `hangfire`), uruchamiany w procesie API. Zadania:

| Zadanie | Harmonogram | Opis |
|---|---|---|
| `ExpiryScanJob` | codziennie 07:00 Europe/Warsaw | Znajduje produkty kończące się (≤ N dni wg ustawień) i przeterminowane, tworzy powiadomienia (idempotentnie – unikalny klucz *produkt + typ + dzień*). |
| `DailyDigestEmailJob` | po `ExpiryScanJob` | Wysyła dzienne podsumowanie e-mail użytkownikom, którzy je włączyli. |
| `SendEmailJob` | na żądanie | Kolejka e-maili (potwierdzenie konta, zaproszenia, reset hasła) z ponawianiem. |
| `CleanupLogsJob` | codziennie 03:00 | Usuwa logi starsze niż 30 dni. |
| `ExpireInvitationsJob` | co godzinę | Oznacza wygasłe zaproszenia. |

Dashboard Hangfire pod `/admin/jobs`, dostępny wyłącznie dla roli `Admin`.

## 4. Uzasadnienie

Hangfire najszybciej spełnia wymaganie tematu i daje „za darmo” ponawianie, trwałość i podgląd stanu, który można pokazać na prezentacji. Wykorzystuje już posiadaną bazę – nie potrzeba Redis ani brokera wiadomości.

## 5. Konsekwencje

### Pozytywne
- [+] Przypomnienia działają niezależnie od aktywności użytkowników.
- [+] Łatwe ręczne wyzwolenie zadania z dashboardu („Trigger now”).

### Negatywne
- [-] Zadania obciążają ten sam proces co API (przy tej skali – bez znaczenia; w razie potrzeby można uruchomić osobny kontener „worker” z tym samym obrazem).
- [-] Logika zadań musi być idempotentna i testowana (testy integracyjne wywołujące zadanie bezpośrednio, ze sztucznym zegarem `TimeProvider`).
