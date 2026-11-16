# ADR-008: Skanowanie kodów kreskowych po stronie przeglądarki

- **Status:** Zaakceptowana
- **Data:** 2026-11-06
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Użytkownik ma móc zeskanować kod kreskowy produktu, aby uzupełnić jego dane. Najczęściej będzie to robił telefonem w kuchni, w aplikacji webowej (bez natywnej aplikacji mobilnej). Obsługiwane formaty: EAN-13, EAN-8, UPC-A/UPC-E.

## 2. Rozważane opcje

### Opcja 1: Dekodowanie w przeglądarce (strumień z kamery + biblioteka JS)

- **Zalety:**
  - [+] Natychmiastowy wynik, działa na żywo z podglądu kamery.
  - [+] Nie przesyłamy obrazu na serwer (prywatność, transfer).
  - [+] Dojrzałe biblioteki: `@zxing/browser`, `html5-qrcode`; natywne `BarcodeDetector` API tam, gdzie dostępne (Chrome/Android).
- **Wady:**
  - [-] Wymaga HTTPS (API kamery działa tylko w bezpiecznym kontekście) i zgody użytkownika.
  - [-] Jakość zależy od kamery i oświetlenia.

### Opcja 2: Wysłanie zdjęcia na serwer i dekodowanie w .NET (np. ZXing.Net)

- **Zalety:**
  - [+] Logika w C#; działa też z wgranego pliku.
- **Wady:**
  - [-] Brak podglądu na żywo, wolniej (upload), więcej obciążenia serwera.

### Opcja 3: Tylko ręczne wpisanie kodu

- **Zalety:**
  - [+] Trywialne.
- **Wady:**
  - [-] Słabe doświadczenie użytkownika.

## 3. Decyzja

Wybieramy **dekodowanie w przeglądarce** biblioteką **`@zxing/browser`** wywoływaną z Blazora przez **JS interop** (komponent `BarcodeScanner.razor` + mały moduł JS w `wwwroot`) (z próbą użycia natywnego `BarcodeDetector`, jeśli jest dostępny), z **awaryjnym ręcznym wpisaniem kodu** (oraz – opcjonalnie – wyborem zdjęcia z galerii dekodowanym tą samą biblioteką). Backend przyjmuje wyłącznie ciąg cyfr kodu i waliduje sumę kontrolną EAN.

## 4. Uzasadnienie

Skanowanie na żywo daje najlepsze wrażenia i nie obciąża serwera. Ręczny wpis i plik z galerii pokrywają przypadki braku kamery, brak zgody czy komputer bez kamery.

## 5. Konsekwencje

### Pozytywne
- [+] Obraz z kamery nie opuszcza urządzenia.
- [+] Endpoint backendu jest prosty i łatwo testowalny (`GET /api/v1/catalog/barcodes/{ean}`).

### Negatywne
- [-] Lokalne testowanie na telefonie wymaga HTTPS (np. `mkcert` lub tunel).
- [-] Testy E2E skanowania ograniczone (Playwright może podać sztuczny strumień wideo; w praktyce testujemy ścieżkę z ręcznym wpisem).
- [-] Konieczny przycisk demonstracyjny / przygotowane produkty z kodami na prezentację.
