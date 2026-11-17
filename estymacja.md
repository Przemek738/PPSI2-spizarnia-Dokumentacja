# Estymacja projektu Spiżarnia

## 1. Skala i historyjki referencyjne

| SP | Poziom | Pozycja referencyjna z naszego backlogu | Dlaczego |
|---|---|---|---|
| **1** | Banalne | brak w backlogu – np. zmiana etykiety lub tekstu komunikatu | Zmiana w jednym miejscu, zero niepewności. |
| **2** | Bardzo małe | TS-004 *Dokumentacja OpenAPI i klient frontendu* | Konfiguracja gotowych narzędzi, mało kodu. |
| **3** | Małe | TS-001 *Repozytorium, struktura solucji i CI* | Kilka plików konfiguracyjnych, znany proces. |
| **5** | Średnie | US-004 *Dodawanie produktów do spiżarni* | Formularz z walidacją, endpoint, zapis do bazy, testy. |
| **8** | Duże | US-002 *Logowanie przez konto Google* | Integracja OAuth z zewnętrznym dostawcą. |
| **13** | Bardzo duże | US-001 *Rejestracja i logowanie przez e-mail* | Rejestracja, logowanie z tokenami, potwierdzanie e-maila i reset hasła w jednej historyjce. |
| 21+ | Epik | brak – każdy epik jest podzielony na historyjki | Wymaga rozbicia. |

## 2. Estymacja backlogu

### Historyjki użytkownika

| ID | Tytuł | Koszulka | SP    | Uzasadnienie (złożoność / niepewność / wysiłek) |
|---|---|---|-------|----------------------|
| US-001 | Rejestracja i logowanie przez e-mail | XL | **8** | Cztery procesy w jednej historyjce: rejestracja z linkiem aktywacyjnym, logowanie z tokenami JWT i odświeżaniem, blokada po nieudanych próbach |
| US-002 | Logowanie przez konto Google | L | **8** | Wartość referencyjna z przewodnika. Wysoka niepewność: przekierowania, ciasteczka, łączenie kont. |
| US-003 | Wspólne gospodarstwo z domownikami | L | **5** | Model członkostwa, zaproszenie przez link z kodem, rejestracja z linku, wygasanie zaproszeń, autoryzacja dostępu do danych gospodarstwa. |
| US-004 | Dodawanie produktów do spiżarni | M | **5** | Referencja dla 5 SP. |
| US-005 | Przeglądanie zawartości spiżarni | L | **5** | Lista z filtrami, wyszukiwaniem, sortowaniem i stronicowaniem oraz edycja i usuwanie. |
| US-006 | Oznaczanie produktu jako zużyty, wyrzucony lub oddany | M | **3** | Reguły domenowe, historia zdarzeń, cofanie akcji. |
| US-007 | Skanowanie kodu kreskowego | L | **8** | Dwie niewiadome: aparat w przeglądarce przez JS interop oraz zewnętrzne API Open Food Facts z cache. |
| US-008 | Przypomnienia o kończących się terminach | L | **5** | Zadanie w tle (Hangfire), powiadomienia w aplikacji, ustawienia, brak duplikatów. |
| US-009 | Wspólna lista zakupów | M | **5** | CRUD listy i pozycji, odhaczanie, podpowiedzi. |
| US-010 | Przeniesienie zakupów do spiżarni | M | **5** | Formularz zbiorczy i zapis wielu produktów w jednej transakcji. |
| US-011 | Propozycje przepisów z tego, co mam w domu | L | **5** | Algorytm dopasowania, przygotowanie ok. 25 przepisów, dodawanie braków do listy. |
| US-012 | Statystyki zakupów i marnowania jedzenia | L | **5** | Cztery zestawienia, wykres, przeliczanie jednostek, zapytania agregujące. |
| US-013 | Zarządzanie użytkownikami | M | **3** | Lista, blokowanie, role; logika na gotowym ASP.NET Core Identity. |
| US-014 | Zarządzanie słownikiem produktów | M | **3** | CRUD z kodami kreskowymi i łączeniem duplikatów. |
| US-015 | Przeglądanie logów aplikacji | M | **5** | Zapis logów do bazy, filtrowanie i stronicowanie, widok szczegółów, retencja. |

### Zadania techniczne

| ID | Tytuł | SP | Uzasadnienie |
|---|---|----|---|
| TS-001 | Repozytorium, struktura solucji i CI | 3  | Referencja dla 3 SP. |
| TS-002 | Konteneryzacja i docker compose | 5  | Kilka kontenerów, healthchecki, różnice między systemami w zespole. |
| TS-003 | Model danych, migracje i dane startowe | 5  | Pełny model domeny i dane startowe. |
| TS-004 | Dokumentacja OpenAPI i klient frontendu | 2  | Referencja dla 2 SP. |
| TS-005 | Logowanie strukturalne | 3  | Konfiguracja Seriloga, zapis do bazy i maskowanie danych. |
| TS-006 | Szkielet frontendu | 5  | Layout, routing, stan logowania i klient API w Blazorze. |
| TS-007 | Wdrożenie i ciągłe dostarczanie | 5  | Maszyna w Azure for Students, adres DNS, HTTPS, pipeline CD, sekrety. Duża niepewność. |
| TS-008 | Infrastruktura testów automatycznych | 3  | Testcontainers, WireMock, bUnit, Playwright w CI. |
| TS-009 | Obsługa błędów i walidacja | 2  | Jeden mechanizm wpięty globalnie. |
| TS-010 | Zabezpieczenia API | 0  | Limity zapytań, CORS i nagłówki bezpieczeństwa. |
| ZAD-001 | Sprawozdanie w LaTeX | 3  | Większość treści już jest w dokumentacji; trzeba ją złożyć w LaTeX. |

## 3. Estymacja trzypunktowa

Wartość oczekiwana: **E = (O + 4·M + P) / 6**, gdzie O to wariant optymistyczny, M najbardziej prawdopodobny, a P pesymistyczny. Wynik zaokrąglamy do najbliższej wartości z ciągu Fibonacciego.

| ID | Optymistyczna (O) | Najbardziej prawdopodobna (M) | Pesymistyczna (P) | E | Przyjęte SP | Co może pójść źle |
|---|---|---|---|---|---|---|
| US-001 | 8 | 13 | 21 | 13,5 | **13** | Odświeżanie tokenów w Blazor WebAssembly, wysyłka e-maili. |
| US-002 | 5 | 8 | 13 | 8,3 | **8** | Adresy przekierowań w Google, ciasteczka między domenami. |
| US-007 | 5 | 8 | 13 | 8,3 | **8** | Działanie aparatu na iOS, niepełne dane w Open Food Facts. |
| US-008 | 5 | 8 | 13 | 8,3 | **8** | Duplikaty powiadomień, testowanie zadań zależnych od czasu. |
| TS-007 | 5 | 8 | 13 | 8,3 | **8** | Konfiguracja serwera, certyfikat, sekrety w pipeline. |

## 4. Zadania badawcze – ograniczone czasowo

| Spike | Sprint | Time-box | Oczekiwany rezultat |
|---|---|---|---|
| SPIKE-1: Logowanie Google w Blazor WebAssembly + ciasteczko refresh tokena | Sprint 2 | 4 h | Działający prototyp przekierowania i decyzja, czy wystarczy jedna domena dla frontendu i API. |
| SPIKE-2: Skanowanie ZXing przez JS interop na telefonach (Android, iOS) | Sprint 2 | 4 h | Prototyp komponentu skanera sprawdzony na dwóch telefonach. |
| SPIKE-3: Mapowanie kategorii Open Food Facts na nasze kategorie | Sprint 3 | 2 h | Tabela mapowania dla ~15 najczęstszych kategorii. |

## 5. Prędkość i pojemność zespołu

**Założenia:**
- **Sprint 0** trwa tylko tydzień: pojemność ~15 SP.
- **Sprint 1** to sprint kalibracyjny: zespół dopiero uczy się narzędzi i wzajemnej współpracy, więc ~25 SP.
- **Sprint 3** obejmuje przerwę świąteczną: ~18 SP.
- **Sprint 5** to sprint przed prezentacją: ~25 SP, bo część czasu zajmą próby i poprawki.

**Czy zmieścimy się w terminie?** Backlog ma 114 SP, a łączna przewidywana pojemność to 11 + 19 + 22 + 13 + 22 + 19 = **106 SP**. Plan przekracza pojemność o ok. **8 SP**. Dlatego:
- TS-010 jest **celem ambitnym** Sprintu 5: robimy je tylko, jeśli zostanie czas,
- jeśli po Sprincie 1 prędkość okaże się niższa, upraszczamy kryteria akceptacji,

## 6. Plan wydania (sprinty)


| Sprint | Termin | Cel sprintu | Zobowiązanie | SP | Pojemność |
|---|---|---|---|----|-----|
| **Sprint 0** | 16.11–22.11 | Zespół ma gotowe repozytorium, CI, środowisko Docker i model danych. | TS-001, TS-002, TS-003, TS-004 | 15 | ~11 |
| **Sprint 1** | 23.11–06.12 | Użytkownik może założyć konto i zalogować się w aplikacji wdrożonej publicznie pod HTTPS. | US-001, TS-006, TS-007 | 18 | ~19 |
| **Sprint 2** | 07.12–20.12 | Domownicy mogą wspólnie prowadzić spiżarnię: dodawać i przeglądać produkty. | US-003, US-004, US-005, TS-005, TS-008, TS-009 + SPIKE-1, SPIKE-2 | 23 | ~22 |
| **Sprint 3** | 21.12–10.01 | Użytkownik może dodać produkt skanem, oznaczać zużycie i prowadzić listę zakupów. | US-006, US-007, US-009 + SPIKE-3 | 16 | ~13 |
| **Sprint 4** | 11.01–24.01 | System sam przypomina o terminach i podpowiada przepisy; logowanie przez Google. | US-002, US-008, US-010, US-011 | 23 | ~22 |
| **Sprint 5** | 25.01–prezentacja | Statystyki, panel administratora, sprawozdanie i gotowość do prezentacji. | US-012, US-013, US-014, US-015, ZAD-001 | 19 | ~19 |

**Kamienie milowe:**
1. **koniec Sprintu 1** – aplikacja dostępna publicznie pod HTTPS,
2. **koniec Sprintu 4** – wszystkie funkcje dla zwykłego użytkownika gotowe,
3. **3 dni przed prezentacją** – zamrożenie funkcji, tylko poprawki błędów; gotowy PDF sprawozdania.

## 7. Śledzenie prędkości i reestymacja

**Prędkość** to suma SP pozycji ukończonych zgodnie z Definition of Done. Niedokończone pozycje się nie liczą i przechodzą do kolejnego sprintu z niezmienioną estymacją.

| Sprint | Zaplanowane SP | Ukończone SP (prędkość) | Uwagi |
|---|----|---|--|
| Sprint 0 | 15 | | |
| Sprint 1 | 18 | | |
| Sprint 2 | 23 | | |
| Sprint 3 | 16 | | przerwa świąteczna |
| Sprint 4 | 23 | | |
| Sprint 5 | 19 | | |
| **Średnia** | 19 | | |

**Zasady:**
- Po każdym sprincie porównujemy prędkość z planem: **pozostałe SP / średnia prędkość = liczba potrzebnych sprintów**.
- Reestymujemy tylko wtedy, gdy odkryjemy coś, co drastycznie zmienia złożoność, gdy zmienia się zakres historyjki albo gdy na retrospektywie zauważymy, że systematycznie się mylimy. Nie reestymujemy przy drobnych korektach, ze względu na tempo jednej osoby ani po zakończeniu historyjki.
- **Jeśli zabraknie pojemności**, upraszczamy w obrębie kryteriów akceptacji i w tej kolejności rezygnujemy z:
  1. TS-010 (zabezpieczenia API),
  2. podsumowania e-mail w US-008,
  3. łączenia duplikatów w US-014,
  4. cofania akcji w US-006,
  5. resetu hasła w US-001.

## 8. Ryzyka estymacji

| Ryzyko | Wpływ | Działanie |
|---|---|---|
| Logowanie Google + ciasteczka między domenami | US-002 może urosnąć do 13 SP | SPIKE-1 w Sprincie 2; frontend i API pod jedną domeną. |
| Skanowanie aparatem na iOS/Android | US-007 może urosnąć | SPIKE-2 w Sprincie 2; ręczne wpisanie kodu jako fallback. |
| US-001 nie zmieści się w Sprincie 1 | Opóźnienie kolejnych sprintów | Podział na rejestrację i logowanie oraz potwierdzenie e-maila i reset hasła; drugą część można przenieść do Sprintu 2. |
| Wdrożenie „na ostatnią chwilę” | Brak oceny za wdrożenie | TS-007 już w Sprincie 1. |
| Niedoszacowanie pierwszego sprintu | Niższa prędkość | Sprint 1 traktujemy jako kalibracyjny; po nim aktualizujemy plan. |
| Przerwa świąteczna i sesja | Spadek prędkości | Mniejsze zobowiązanie w Sprincie 3. |