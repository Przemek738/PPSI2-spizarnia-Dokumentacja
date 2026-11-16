# ADR-003: Frontend – Blazor WebAssembly (.NET 10) z biblioteką MudBlazor

- **Status:** Zaakceptowana
- **Data:** 2026-11-05
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Frontend musi być osobną aplikacją komunikującą się z backendem przez REST API. Kluczowe wymagania frontendu:
- skanowanie kodów kreskowych **kamerą w przeglądarce telefonu**,
- responsywny interfejs mobile-first,
- wykresy statystyk,
- logowanie OAuth (przekierowania) i obsługa tokenów.

Zespół zna C# i .NET, ma doświadczenie z Blazorem, a nie pracował z Reactem ani TypeScriptem. Na realizację projektu jest ok. 10 tygodni, a ocenie podlega działająca i wdrożona aplikacja.

## 2. Rozważane opcje

### Opcja 1: Blazor WebAssembly (standalone) + MudBlazor

- **Zalety:**
  - [+] Jeden język (C#) i jedno środowisko w całym projekcie – członkowie zespołu mogą pomagać sobie między frontendem a backendem.
  - [+] MudBlazor zapewnia gotowe komponenty Material Design: formularze, tabele, okna dialogowe, wykresy, układ responsywny.
  - [+] Aplikacja działa w przeglądarce i komunikuje się z API wyłącznie przez HTTP – zachowana wymagana separacja frontendu i backendu.
  - [+] Testy komponentów w xUnit (bUnit) – te same narzędzia co na backendzie.
- **Wady:**
  - [-] Skanowanie kamerą wymaga JS interop (biblioteka JS opakowana w komponent Blazor).
  - [-] Większy pierwszy download aplikacji (runtime .NET w przeglądarce) – wolniejsze pierwsze uruchomienie na telefonie.

### Opcja 2: Blazor Server / Blazor Web App z renderowaniem po stronie serwera

- **Zalety:**
  - [+] Szybki start aplikacji, mały download.
- **Wady:**
  - [-] Interfejs działa na serwerze przez stałe połączenie SignalR – frontend nie jest odrębną aplikacją korzystającą z REST API, co osłabia spełnienie wymagania tematu.
  - [-] Zerwane połączenie (np. słaby zasięg w kuchni) przerywa pracę.

### Opcja 3: React + TypeScript (Vite)

- **Zalety:**
  - [+] Największy ekosystem bibliotek (skanowanie, wykresy, komponenty UI); lekki bundle.
  - [+] Umiejętność cenna na rynku pracy.
- **Wady:**
  - [-] Zespół nie miał styczności z Reactem i TypeScriptem – szacujemy 2–3 tygodnie wolniejszej pracy na starcie.
  - [-] Dwa ekosystemy narzędzi (dotnet + npm) i dwa zestawy testów.

## 3. Decyzja

Wybieramy **Blazor WebAssembly (standalone, .NET 10)** z biblioteką komponentów **MudBlazor**.

Ustalenia:
- projekt `src/Spizarnia.Web` w tej samej solucji, ale **bez referencji do projektów backendu** – kontrakt API pochodzi wyłącznie z OpenAPI (klient generowany przez NSwag lub Kiota),
- uwierzytelnianie: access token przechowywany w pamięci (serwis `AuthStateProvider`), refresh token w ciasteczku `HttpOnly` ustawianym przez API; `DelegatingHandler` dodaje nagłówek `Authorization` i odświeża token po odpowiedzi 401,
- skanowanie: komponent `BarcodeScanner.razor` korzystający przez JS interop z biblioteki `@zxing/browser` dołączonej jako plik JS w `wwwroot`,
- wykresy statystyk: `MudChart` (w razie potrzeby bardziej rozbudowanych wykresów – ApexCharts for Blazor),
- formularze: `EditForm` + walidacja FluentValidation (pakiet integrujący z Blazorem),
- publikacja: `dotnet publish` → pliki statyczne serwowane przez `nginx`,
- skrócenie czasu startu: kompresja (Brotli) i lazy loading mniej używanych modułów (np. panel administratora).

## 4. Uzasadnienie

Przy krótkim terminie i braku doświadczenia z Reactem największą wartością jest szybkość dostarczania i możliwość wzajemnej pomocy w zespole – dlatego wybieramy technologię, którą zespół już zna. Jedyna funkcja wymagająca JavaScriptu (skanowanie) to pojedynczy, odizolowany komponent. Wersja WebAssembly, w przeciwieństwie do Blazor Server, jest w pełni odrębną aplikacją klienta, która komunikuje się z backendem tylko przez REST – zgodnie z wymaganiem tematu.

## 5. Konsekwencje

### Pozytywne
- [+] Jeden język i jedno IDE; wspólny styl kodu (`dotnet format`) i wspólny pipeline CI.
- [+] Gotowe komponenty MudBlazor przyspieszają budowę UI i zapewniają responsywność.
- [+] Kontrakt API wymuszany przez generowany klient – zmiana na backendzie wymaga regeneracji klienta i jest widoczna w PR.

### Negatywne
- [-] Wolniejsze pierwsze ładowanie aplikacji na telefonie – akceptowalne dla tego projektu, łagodzone kompresją i cache przeglądarki.
- [-] Kod JS interop dla skanera trzeba przetestować ręcznie na telefonach z Androidem i iOS.
- [-] Pokusa współdzielenia klas DTO z backendem – zakazana konwencją projektu (separacja frontend/backend).
