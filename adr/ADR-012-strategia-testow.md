# ADR-012: Strategia i narzędzia testów automatycznych

- **Status:** Zaakceptowana
- **Data:** 2026-11-10
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Projekt wymaga testów automatycznych. System ma logikę domenową (terminy, statusy, statystyki, dopasowanie przepisów), integracje zewnętrzne (OFF, Google, SMTP), zadania w tle i autoryzację na poziomie gospodarstwa. Testy muszą działać w CI (GitHub Actions) bez dostępu do prawdziwych usług zewnętrznych.

## 2. Rozważane opcje

### Opcja 1: Piramida testów – xUnit + Testcontainers (prawdziwy PostgreSQL) + WireMock.Net + bUnit + Playwright

- **Zalety:**
  - [+] Testy integracyjne na tej samej bazie co produkcja – wykrywają błędy mapowań, migracji i zapytań.
  - [+] Usługi zewnętrzne zastąpione serwerem atrap HTTP (WireMock) – testujemy prawdziwe klienty HTTP.
  - [+] E2E pokrywa kluczowe ścieżki demonstracyjne.
- **Wady:**
  - [-] Testcontainers wymaga Dockera w CI (dostępny na runnerach `ubuntu-latest`).

### Opcja 2: Testy integracyjne na bazie w pamięci (EF Core InMemory / SQLite)

- **Zalety:**
  - [+] Szybkie, bez Dockera.
- **Wady:**
  - [-] Inne zachowanie niż PostgreSQL (transakcje, typy, `ILIKE`, ograniczenia) – fałszywe poczucie bezpieczeństwa.

### Opcja 3: Tylko testy jednostkowe

- **Zalety:**
  - [+] Najprostsze.
- **Wady:**
  - [-] Nie wykrywają błędów integracji, autoryzacji ani konfiguracji – a te są najczęstsze w aplikacjach webowych.

## 3. Decyzja

Wybieramy **Opcję 1**:

| Poziom | Narzędzia | Co testujemy | Cel ilościowy |
|---|---|---|---|
| Jednostkowe (backend) | xUnit, **Shouldly** lub **AwesomeAssertions**, NSubstitute | reguły domenowe: statusy produktu, częściowe zużycie, wyliczanie „kończy się”, „uratowany”, przeliczanie jednostek, dopasowanie przepisów | ≥ 60% pokrycia Domain + Application |
| Integracyjne API | `WebApplicationFactory`, **Testcontainers.PostgreSql**, **WireMock.Net**, Respawn | endpointy z autoryzacją (w tym dostęp do cudzego gospodarstwa → 403/404), migracje, zadania w tle (wywołane bezpośrednio, z `FakeTimeProvider`) | każdy endpoint: ścieżka pozytywna + min. 1 negatywna |
| Komponenty (frontend) | **bUnit** (na xUnit), NSubstitute (atrapa klienta API) | komponenty Blazor: formularze, walidacja, obsługa błędów API, stan logowania | kluczowe komponenty |
| E2E | **Playwright for .NET** | 3–5 ścieżek: rejestracja → utworzenie gospodarstwa → dodanie produktu → oznaczenie jako zużyty; lista zakupów → produkt; panel admina | uruchamiane w CI na `docker compose` |

**FluentAssertions od wersji 8 ma licencję komercyjną** (darmową tylko do użytku niekomercyjnego) – dla uniknięcia wątpliwości używamy Shouldly lub otwartego forka AwesomeAssertions.

## 4. Uzasadnienie

Największe ryzyko w tym systemie to błędy na styku warstw (autoryzacja, zapytania, integracje), dlatego trzon stanowią testy integracyjne na prawdziwej bazie. Testy jednostkowe zabezpieczają reguły, od których zależą statystyki. E2E ograniczamy do ścieżek pokazywanych na prezentacji.

## 5. Konsekwencje

### Pozytywne
- [+] Wysoka pewność, że wdrożona wersja działa (CI blokuje merge przy czerwonych testach).
- [+] Testy dokumentują reguły biznesowe (przydatne w sprawozdaniu).

### Negatywne
- [-] Dłuższy czas CI (szac. 4–8 min) – akceptowalne.
