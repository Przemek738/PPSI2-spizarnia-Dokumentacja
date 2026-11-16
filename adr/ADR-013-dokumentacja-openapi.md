# ADR-013: Dokumentacja API – wbudowany generator OpenAPI + Scalar, klient generowany dla frontendu

- **Status:** Zaakceptowana
- **Data:** 2026-11-04
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga udokumentowania backendu w formie OpenAPI. Frontend i backend rozwijają różne osoby, więc kontrakt API musi być jednoznaczny i aktualny. Od .NET 9 szablony ASP.NET Core nie zawierają już Swashbuckle – Microsoft dostarcza własny generator `Microsoft.AspNetCore.OpenApi`.

## 2. Rozważane opcje

### Opcja 1: `Microsoft.AspNetCore.OpenApi` (wbudowany) + Scalar jako interfejs przeglądania

- **Zalety:**
  - [+] Oficjalnie wspierany, generowanie także w czasie budowania.
  - [+] Scalar – nowoczesny UI z przykładami wywołań i obsługą tokenu Bearer.
- **Wady:**
  - [-] Mniej materiałów w sieci niż dla Swashbuckle.

### Opcja 2: Swashbuckle (Swagger UI)

- **Zalety:**
  - [+] Najbardziej znany, dużo przykładów.
- **Wady:**
  - [-] Nie jest już domyślny w szablonach .NET; dodatkowa zależność.

### Opcja 3: Podejście „API-first” (ręcznie pisany plik OpenAPI, generowanie kodu serwera)

- **Zalety:**
  - [+] Kontrakt ustalony przed implementacją.
- **Wady:**
  - [-] Duży narzut ręcznej pracy i synchronizacji.

## 3. Decyzja

Wybieramy **`Microsoft.AspNetCore.OpenApi`** (dokument `/openapi/v1.json`) z interfejsem **Scalar** pod `/docs` (dostępnym w środowisku dev i na produkcji). Zasady:
- każdy endpoint ma `summary`, opisane kody odpowiedzi (`ProducesResponseType`) i przykłady,
- schemat bezpieczeństwa Bearer JWT,
- plik `openapi/v1.json` generowany przy buildzie i commitowany – zmiana kontraktu jest widoczna w PR,
- frontend (Blazor WebAssembly) korzysta z klienta C# generowanego z tego pliku (NSwag lub Kiota) – bez referencji do projektów backendu.

## 4. Uzasadnienie

Wbudowany generator to kierunek wspierany przez Microsoft w aktualnych wersjach .NET. Commitowany plik specyfikacji i generowany klient zamieniają dokumentację w „żywy kontrakt” między podzespołami.

## 5. Konsekwencje

### Pozytywne
- [+] Spełnione wymaganie tematu, czytelne dla prowadzącego.
- [+] Rozjazd frontendu i backendu wykrywany na etapie kompilacji.

### Negatywne
- [-] Konieczność dbania o adnotacje endpointów.
