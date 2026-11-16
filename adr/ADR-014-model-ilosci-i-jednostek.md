# ADR-014: Model ilości, jednostek i statusów produktu

- **Status:** Zaakceptowana
- **Data:** 2026-11-09
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Produkt ma „ilość i jednostkę”, a system ma liczyć **ilość zmarnowanej żywności per miesiąc** i **produkty uratowane**. Problemy:
- jednostki są różne (g, kg, ml, l, szt., opak.) – nie da się ich bezpośrednio sumować,
- użytkownik często zużywa **część** produktu (pół litra mleka), a resztę wyrzuca,
- statystyki muszą być liczone na podstawie historii, a nie tylko bieżącego stanu.

## 2. Rozważane opcje

### Opcja 1: Pojedynczy status produktu (aktywny / zużyty / wyrzucony / oddany), bez historii ilości

- **Zalety:**
  - [+] Najprostsza implementacja.
- **Wady:**
  - [-] Brak częściowego zużycia; statystyki „ile zmarnowano” nieprecyzyjne.

### Opcja 2: Produkt z ilością bieżącą + dziennik zdarzeń (`pantry_item_events`) z ilością i typem; ilości normalizowane do jednostki bazowej

- **Zalety:**
  - [+] Obsługa częściowego zużycia/wyrzucenia.
  - [+] Statystyki to proste agregacje dziennika (per miesiąc, per produkt, per typ).
  - [+] Historia produktu „za darmo”.
- **Wady:**
  - [-] Więcej logiki domenowej i danych.

### Opcja 3: Pełny Event Sourcing

- **Zalety:**
  - [+] Kompletna historia, odtwarzanie stanu.
- **Wady:**
  - [-] Nadmiarowa złożoność dla zespołu.

## 3. Decyzja

Wybieramy **Opcję 2**.

**Jednostki:** `g`, `kg`, `ml`, `l`, `szt`, `opak`. Każda ilość zapisywana jest także w **jednostce bazowej**: masa → g, objętość → ml, liczba → szt.

**Przeliczenie na kg do statystyki marnowania:**
- masa: wprost,
- objętość: przybliżenie 1 ml ≈ 1 g (oznaczone w UI jako szacunek),
- sztuki/opakowania: jeśli definicja produktu zna masę netto sztuki (np. z OFF: „500 g”) – mnożymy; w przeciwnym razie wliczamy tylko do statystyki „liczba produktów”.

Dlatego statystyka marnowania pokazuje **dwie miary**: szacowane **kg** i **liczbę pozycji**.

**Cykl życia produktu (`PantryItem`):**
- `Active` → (zdarzenia `Consumed` / `Discarded` / `Donated` z ilością) → gdy ilość bieżąca = 0 → `Closed` (z dominującym powodem zamknięcia).
- Zdarzenie zapisuje: typ, ilość (w jednostce produktu i bazowej), datę, użytkownika, **stan terminu w chwili zdarzenia** (`Fresh` / `ExpiringSoon` / `Expired`).
- **Uratowany** = zdarzenie `Consumed` lub `Donated` ze stanem `ExpiringSoon` (przed upływem daty).
- **Zakup** = zdarzenie `Purchased` przy utworzeniu produktu (ręcznie, ze skanu lub z listy zakupów) – podstawa statystyki „najczęściej kupowane” (grupowanie po definicji produktu).
- Usunięcie produktu (pomyłka) = miękkie usunięcie z wykluczeniem ze statystyk.

**Typ daty:** `BestBefore` (DMT) lub `UseBy` (termin przydatności). Produkt z `UseBy` po terminie jest oznaczany jako przeterminowany; z `BestBefore` – jako „po dacie minimalnej trwałości” (łagodniejszy komunikat).

## 4. Uzasadnienie

Dziennik zdarzeń daje dokładne i wiarygodne statystyki wymagane w temacie przy umiarkowanej złożoności. Zapisanie stanu terminu w chwili zdarzenia sprawia, że „uratowany” jest wyliczany jednoznacznie i nie zmienia się z upływem czasu.

## 5. Konsekwencje

### Pozytywne
- [+] Wszystkie cztery statystyki liczone jednym mechanizmem (agregacje po `pantry_item_events`).
- [+] Reguły łatwo pokryć testami jednostkowymi.

### Negatywne
- [-] Statystyka w kg jest szacunkowa dla płynów i produktów na sztuki – trzeba to jasno komunikować w UI i sprawozdaniu.
- [-] Zmiana jednostki produktu po utworzeniu wymaga przeliczenia historii – blokujemy zmianę jednostki, gdy istnieją zdarzenia.
