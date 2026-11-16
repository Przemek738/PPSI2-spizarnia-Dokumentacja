# ADR-001: Architektura systemu – modularny monolit + SPA komunikujące się przez REST

- **Status:** Zaakceptowana
- **Data:** 2026-11-02
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga odseparowanego backendu i frontendu komunikujących się przez REST API. System ma kilka wyraźnych obszarów (konta, gospodarstwa, spiżarnia, katalog produktów, listy zakupów, przypomnienia, przepisy, statystyki, administracja), zadania w tle oraz integracje zewnętrzne. Zespół liczy 3 osoby, ma ok. 10 tygodni na realizację i ograniczone doświadczenie w utrzymaniu infrastruktury rozproszonej.

## 2. Rozważane opcje

### Opcja 1: Modularny monolit (jeden proces API) + osobna aplikacja SPA

- **Zalety:**
  - [+] Jedna aplikacja do zbudowania, przetestowania i wdrożenia; proste transakcje w jednej bazie.
  - [+] Moduły z jasnymi granicami (folder/projekt na moduł) – porządek bez kosztów sieci.
  - [+] Łatwo zmieścić w docker compose i na jednym serwerze.
- **Wady:**
  - [-] Skalowanie tylko całości (dla tego projektu bez znaczenia).
  - [-] Wymaga dyscypliny, żeby moduły nie „przeciekały” do siebie.

### Opcja 2: Mikroserwisy (np. osobne usługi: konta, spiżarnia, przypomnienia, przepisy)

- **Zalety:**
  - [+] Niezależne wdrażanie i skalowanie usług.
  - [+] Dobre do pokazania wzorców rozproszonych.
- **Wady:**
  - [-] Ogromny narzut: komunikacja między usługami, spójność danych, broker wiadomości, wiele baz, tracing.
  - [-] Nieproporcjonalne do wielkości zespołu i czasu; ryzyko niedokończenia funkcji.

## 3. Decyzja

Wybieramy **modularny monolit w ASP.NET Core** wystawiający **REST API** oraz **osobną aplikację SPA w Blazor WebAssembly** (ADR-003). Wewnątrz backendu stosujemy podział na moduły zgodny z kontekstami z event stormingu (Identity, Households, Pantry, Catalog, Shopping, Reminders, Recipes, Statistics, Admin) oraz uproszczoną architekturę warstwową:

```
src/
  Spizarnia.Api/            # endpointy, auth, konfiguracja, OpenAPI
  Spizarnia.Application/    # przypadki użycia (handlery), walidacja, DTO
  Spizarnia.Domain/         # encje, agregaty, reguły biznesowe, zdarzenia domenowe
  Spizarnia.Infrastructure/ # EF Core, integracje (OFF, SMTP), Hangfire
  Spizarnia.Web/            # frontend Blazor WebAssembly (bez referencji do projektów backendu)
tests/
  Spizarnia.UnitTests/
  Spizarnia.IntegrationTests/
  Spizarnia.Web.Tests/      # testy komponentów
```

Przypadki użycia implementujemy jako proste klasy-handlery wywoływane bezpośrednio (bez biblioteki MediatR).

## 4. Uzasadnienie

Monolit modularny daje najlepszy stosunek wartości do kosztu przy małym zespole i krótkim terminie. Podział na moduły odpowiada kontekstom domenowym, więc w razie potrzeby da się wydzielić usługę w przyszłości. Mikroserwisy nie przynoszą w tym projekcie korzyści, które uzasadniałyby ich koszt.

## 5. Konsekwencje

### Pozytywne
- [+] Jedna baza i jedna transakcja – proste zachowanie spójności (np. zamiana pozycji listy zakupów w produkt).
- [+] Prosty pipeline CI/CD i wdrożenie (2 obrazy: API i frontend); jedna solucja .NET dla całego kodu.
- [+] Testy integracyjne obejmują całą aplikację jednym `WebApplicationFactory`.

### Negatywne
- [-] Zadania w tle działają w tym samym procesie co API.
- [-] Trzeba pilnować granic modułów (przeglądy kodu, brak bezpośrednich odwołań do encji innych modułów poza identyfikatorami).
- [-] Rezygnujemy z MediatR i AutoMappera – od 2025 r. przechodzą na licencje komercyjne; mapowanie piszemy ręcznie lub generatorem źródeł (np. Mapperly).
