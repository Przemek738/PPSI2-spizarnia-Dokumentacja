# ADR-010: Logowanie – Serilog z zapisem do PostgreSQL i podglądem w panelu administratora

- **Status:** Zaakceptowana
- **Data:** 2026-11-10
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga, aby **administrator widział logi z aplikacji**. Logi przydają się też zespołowi do diagnozy błędów po wdrożeniu. Mamy jeden serwer i ograniczone zasoby; chcemy uniknąć utrzymywania rozbudowanego stosu monitoringu.

## 2. Rozważane opcje

### Opcja 1: Serilog → PostgreSQL (tabela `logs.app_logs`) + stdout; podgląd przez własny endpoint i ekran w panelu admina

- **Zalety:**
  - [+] Logi dostępne w tej samej aplikacji.
  - [+] Filtrowanie zapytaniami SQL (poziom, czas, tekst, correlation id); brak dodatkowych kontenerów.
- **Wady:**
  - [-] Baza aplikacji przechowuje logi – trzeba ograniczać retencję i poziom.

### Opcja 2: Serilog → Seq (osobny kontener) i link do Seq dla admina

- **Zalety:**
  - [+] Świetne wyszukiwanie i wizualizacja.
- **Wady:**
  - [-] Admin korzysta z innego narzędzia niż aplikacja; dodatkowy kontener i jego zabezpieczenie.

### Opcja 3: Stos Grafana + Loki (+ OpenTelemetry)

- **Zalety:**
  - [+] Standard branżowy, metryki i tracing.
- **Wady:**
  - [-] Znacząca złożoność i zużycie zasobów nieproporcjonalne do projektu.

## 3. Decyzja

Wybieramy **Opcję 1**:
- **Serilog** z enricherami: `CorrelationId` (z nagłówka `X-Correlation-Id` lub generowany), `UserId`, `RequestPath`, `Environment`,
- sinki: **Console** (format JSON – widoczne w `docker logs`) oraz **PostgreSQL** (poziom ≥ `Information` dla naszej aplikacji, ≥ `Warning` dla frameworka),
- endpoint `GET /api/v1/admin/logs?level=&from=&to=&search=&correlationId=&page=` (rola `Admin`) + ekran w panelu,
- retencja 30 dni (`CleanupLogsJob`),
- maskowanie danych wrażliwych (hasła, tokeny, nagłówek `Authorization`, ciasteczka).

Dodatkowo (opcjonalnie, środowisko deweloperskie): kontener **Seq** w `docker-compose.override.yml` dla wygody zespołu.

## 4. Uzasadnienie

Najprostsza droga do spełnienia wymagania tematu wprost w aplikacji, bez dodatkowej infrastruktury na serwerze produkcyjnym. Structured logging z correlation id pozwala powiązać błąd zgłoszony przez użytkownika z wpisami w logach.

## 5. Konsekwencje

### Pozytywne
- [+] Funkcja „logi” jest pełnoprawnym ekranem aplikacji.
- [+] Spójna diagnostyka (correlation id zwracany też w ProblemDetails błędu).

### Negatywne
- [-] Zapisy logów zwiększają obciążenie bazy – sink działa w trybie wsadowym (batch), a poziomy są ograniczone.
- [-] Brak metryk i tracingu rozproszonego (świadomie poza zakresem; ewentualnie OpenTelemetry później).
