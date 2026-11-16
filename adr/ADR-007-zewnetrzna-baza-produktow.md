# ADR-007: Zewnętrzna baza produktów – Open Food Facts z lokalnym cache

- **Status:** Zaakceptowana
- **Data:** 2026-11-06
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga wykorzystania co najmniej jednej zewnętrznej bazy produktów lub kodów kreskowych. Po zeskanowaniu kodu system ma uzupełnić dane produktu (nazwa, kategoria, ilość/gramatura). Większość produktów w Polsce ma kody EAN-13 (prefiks 590). Projekt studencki – preferujemy darmowe API bez kart płatniczych.

## 2. Rozważane opcje

### Opcja 1: Open Food Facts (OFF) API

- **Zalety:**
  - [+] Darmowa, otwarta baza żywności (licencja ODbL), bez klucza API dla odczytu.
  - [+] Dane: nazwa, marka, ilość (np. „500 g”), kategorie (taksonomia z tagami `en:`), zdjęcie, Nutri-Score.
  - [+] Dostępne środowisko testowe (`world.openfoodfacts.net`).
- **Wady:**
  - [-] Dane tworzone przez społeczność – niekompletne lub niespójne; nie każdy kod jest w bazie.
  - [-] Limity zapytań (dla odczytu produktu ok. 100 zapytań/min z jednego IP, dla wyszukiwania znacznie mniej) i obowiązek ustawienia własnego nagłówka `User-Agent`.
  - [-] Wymagana atrybucja źródła.

### Opcja 2: Komercyjne bazy kodów (np. UPCitemdb, Barcode Lookup, GS1)

- **Zalety:**
  - [+] Szersze pokrycie produktów spoza żywności.
- **Wady:**
  - [-] Płatne lub bardzo ograniczone darmowe plany; słabsze pokrycie polskich produktów spożywczych; często wymagają rejestracji firmy.

## 3. Decyzja

Wybieramy **Open Food Facts API v2** (`GET /api/v2/product/{barcode}` z ograniczonym zestawem pól `fields=...`). Integracja przez wzorzec **cache-aside**:

1. Szukamy kodu w lokalnym słowniku `product_definitions` (po EAN).
2. Jeśli brak – wywołujemy OFF (typowany `HttpClient` z timeoutem 3 s, ponawianiem i circuit breakerem – `Microsoft.Extensions.Http.Resilience`).
3. Wynik mapujemy na naszą definicję produktu (nazwa PL jeśli dostępna, kategoria wg tabeli mapowania tagów OFF → nasze kategorie, ilość i jednostka z pola `quantity`) i zapisujemy w słowniku ze źródłem `OpenFoodFacts`.
4. Jeśli OFF nie zna kodu – zwracamy 404, a frontend proponuje ręczne uzupełnienie.

`User-Agent: Spizarnia/1.0 (kontakt: [e-mail zespołu])`. Adres bazowy konfigurowalny (w testach – WireMock).

## 4. Uzasadnienie

OFF jest jedyną dojrzałą, darmową i otwartą bazą żywności o dobrym pokryciu Europy. Lokalny cache ogranicza liczbę zapytań (limity), przyspiesza kolejne skany i pozwala administratorowi poprawiać dane w słowniku.

## 5. Konsekwencje

### Pozytywne
- [+] Szybkie dodawanie produktów ze skanu; słownik rośnie wraz z użyciem.
- [+] Niedostępność OFF nie blokuje aplikacji (fallback do ręcznego wpisu).

### Negatywne
- [-] Trzeba utrzymywać mapowanie kategorii OFF → nasze kategorie (zacząć od ~15 najczęstszych).
- [-] Obowiązek atrybucji (ODbL) – informacja w stopce / ekranie „O aplikacji”.
- [-] Parsowanie pola `quantity` (np. „4 x 125 g”) bywa nieoczywiste – przy niepewności zostawiamy pole do uzupełnienia przez użytkownika.
