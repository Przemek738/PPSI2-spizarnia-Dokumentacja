# ADR-009: Źródło przepisów i algorytm dopasowania

- **Status:** Zaakceptowana
- **Data:** 2026-11-09
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

System ma proponować przepisy na podstawie produktów dostępnych w gospodarstwie, najlepiej preferując te, którym kończy się termin. Problemy:
- produkty w spiżarni mają **polskie nazwy wpisywane dowolnie** („mleko 2% Łaciate”), a zewnętrzne bazy przepisów operują na **angielskich nazwach składników**,
- darmowe API przepisów mają niskie limity,
- wynik musi być przewidywalny i testowalny.

## 2. Rozważane opcje

### Opcja 1: Własny katalog przepisów (seed) powiązany ze słownikiem produktów

Składnik przepisu wskazuje **kategorię lub definicję produktu** (np. „jajka”, „mleko”, „makaron”), a nie dowolny tekst.
- **Zalety:**
  - [+] Pełna kontrola, język polski, brak limitów i kosztów.
  - [+] Dopasowanie deterministyczne – łatwe testy jednostkowe.
- **Wady:**
  - [-] Trzeba przygotować dane (ok. 30–50 prostych przepisów) – praca „redakcyjna”.

### Opcja 2: Spoonacular API (`findByIngredients`)

- **Zalety:**
  - [+] Ogromna baza przepisów, gotowe dopasowanie po składnikach.
- **Wady:**
  - [-] Darmowy plan to niewielki dzienny limit punktów (rzędu kilkudziesięciu–150 dziennie, zależnie od planu – do weryfikacji), wymagany link zwrotny i ograniczenia cache.
  - [-] Konieczne tłumaczenie polskich nazw na angielskie składniki; przepisy po angielsku.

### Opcja 3: TheMealDB

- **Zalety:**
  - [+] Darmowe API.
- **Wady:**
  - [-] Filtrowanie po wielu składnikach jest w planie płatnym; mała baza; język angielski.

## 3. Decyzja

**Opcja 1 – własny katalog przepisów** powiązany ze słownikiem produktów.

Składniki „podstawowe” (sól, pieprz, olej, woda) oznaczamy jako zawsze dostępne. Lista wyników: top 10, z oznaczeniem brakujących składników i składników z kończącym się terminem.

Seed: 30–50 przepisów w pliku JSON w repozytorium, ładowany seederem. (Opcjonalnie później: administrator zarządza przepisami.)

## 4. Uzasadnienie

Podstawowa, wymagana funkcja nie powinna zależeć od limitów darmowych API ani od tłumaczenia nazw. Powiązanie składników ze słownikiem produktów sprawia, że dopasowanie działa także dla produktów dodanych ze skanu (OFF → nasza kategoria). Premiowanie składników z kończącym się terminem realizuje cel systemu – ograniczanie marnowania żywności.

## 5. Konsekwencje

### Pozytywne
- [+] Funkcja nie zależy od usług zewnętrznych i jest w pełni testowalna.
- [+] Polskie przepisy

### Negatywne
- [-] Ograniczona liczba przepisów – trzeba przygotować sensowny zestaw (zadanie dla całego zespołu, np. po 10 przepisów na osobę).
- [-] Dopasowanie zależy od jakości przypisania produktów do kategorii/definicji.
