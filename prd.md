# PRD: Spiżarnia – system do zarządzania domową spiżarnią

- **Autor:** Przemysław Pyrzyński - Lider projektu
- **Data:** 2026-11-02
- **Status:** Zatwierdzony
- **Zespół:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Podsumowanie

Spiżarnia to aplikacja webowa, w której domownicy wspólnie prowadzą spis jedzenia w domu: co mają, gdzie to leży i do kiedy jest ważne. System sam przypomina o produktach, którym kończy się termin, podpowiada przepisy z tego, co jest w domu, prowadzi wspólną listę zakupów i pokazuje, ile jedzenia gospodarstwo marnuje. Jest przeznaczona dla rodzin, par i współlokatorów, którzy chcą mniej wyrzucać i rozsądniej kupować.

---

## 2. Opis Problemu

### Jaki problem rozwiązujemy?

W wielu domach jedzenie psuje się, bo nikt nie pamięta, co leży z tyłu lodówki ani kiedy kończy się jego termin. Zakupy robi kilka osób, więc te same produkty są kupowane podwójnie, a innych brakuje. Każdy domownik ma przy tym inny obraz zawartości lodówki.

Obecne rozwiązania nie wystarczają:
- **Pamięć i karteczki na lodówce** – nie przypominają o terminach i nie działają, gdy jesteśmy w sklepie.
- **Aplikacje do list zakupów** – kończą się w momencie zakupu; nie wiedzą, co już jest w domu i kiedy się zepsuje.
- **Arkusze kalkulacyjne** – wymagają ręcznego wpisywania wszystkiego, nie wysyłają przypomnień i są niewygodne na telefonie.

Brakuje jednego miejsca, które łączy listę zakupów, stan spiżarni, przypomnienia o terminach i podpowiedzi, co ugotować – wspólnego dla wszystkich domowników.

### Dla kogo jest ten produkt?

- **Główna persona:** *Kasia, 38 lat, mama dwójki dzieci* – robi większość zakupów dla czteroosobowej rodziny, pracuje na pełen etat. Regularnie wyrzuca jogurty i warzywa, o których zapomniała. Chce wiedzieć, co trzeba zjeść najpierw, i nie kupować rzeczy, które już są w domu.
- **Poboczne persony:**
  - *Michał, 22 lata, student* – mieszka z trzema współlokatorami, robią zakupy na zmianę. Chce wspólnej listy zakupów i szybkiego dodawania produktów ze skanu kodu kreskowego.
  - *Pan Tadeusz, 67 lat, emeryt* – mieszka z żoną, lubi mieć zapasy w spiżarni i piwnicy. Potrzebuje prostego, czytelnego interfejsu i przypomnień na e-mail.
  - *Administrator systemu* – dba o poprawność słownika produktów, zarządza kontami użytkowników i diagnozuje błędy na podstawie logów.

---

## 3. Cele i Mierniki Sukcesu

### Cele projektu

- **Cel 1:** Ograniczenie marnowania żywności w gospodarstwach domowych dzięki przypomnieniom o terminach i propozycjom przepisów.
- **Cel 2:** Ułatwienie domownikom wspólnego zarządzania zapasami i zakupami.
- **Cel 3:** Dostarczenie działającego, wdrożonego systemu spełniającego wszystkie wymagania tematu projektu na zaliczenie kursu.

### Mierniki sukcesu

- **Miernik 1:** Dodanie produktu przez zeskanowanie kodu kreskowego zajmuje mniej niż 30 sekund.
- **Miernik 2:** W gospodarstwach testowych ilość zmarnowanej żywności (wg statystyk aplikacji) spada o co najmniej 20% między pierwszym a drugim miesiącem użytkowania.
- **Miernik 3:** Co najmniej 30% produktów oznaczonych jako „kończy się termin” zostaje zużytych lub oddanych przed upływem daty (produkty uratowane).
- **Miernik 4:** 100% wymagań funkcjonalnych z tematu zademonstrowanych na wdrożonej aplikacji podczas prezentacji.
- **Miernik 5:** Wynik Lighthouse Accessibility ≥ 90 oraz czas odpowiedzi API p95 < 500 ms dla typowych operacji.

---

## 4. Wymagania i Zakres

### Kluczowe historyjki użytkownika

Pełne historyjki z kryteriami akceptacji: [`user-stories.md`](user-stories.md), backlog z priorytetami i estymacją: [`backlog.md`](backlog.md).

- **US-001:** Jako nowy użytkownik, chcę założyć konto za pomocą adresu e-mail i hasła, aby mieć dostęp do mojej spiżarni z każdego urządzenia.
- **US-002:** Jako osoba, która nie lubi pamiętać kolejnych haseł, chcę logować się kontem Google, aby szybciej zacząć korzystać z aplikacji.
- **US-003:** Jako osoba mieszkająca z rodziną lub współlokatorami, chcę utworzyć gospodarstwo i zaprosić domowników, aby wszyscy widzieli te same produkty.
- **US-004:** Jako osoba robiąca zakupy dla całego domu, chcę dodawać produkty, które mam w domu, aby wiedzieć, co mamy i do kiedy trzeba to zjeść.
- **US-005:** Jako osoba planująca posiłki, chcę szybko przejrzeć zawartość lodówki, zamrażarki i szafek, aby wiedzieć, co zjeść najpierw.
- **US-006:** Jako domownik, chcę oznaczyć produkt jako zużyty, wyrzucony albo oddany, aby lista była aktualna, a statystyki pokazywały, ile marnujemy.
- **US-007:** Jako osoba, która nie lubi przepisywać danych, chcę zeskanować kod kreskowy, aby dodać produkt w kilka sekund.
- **US-008:** Jako zapracowana osoba, chcę dostawać przypomnienia o kończących się terminach, aby zdążyć zjeść produkty, zanim się zepsują.
- **US-009:** Jako osoba robiąca zakupy dla rodziny, chcę tworzyć wspólną listę zakupów, aby nie kupować tego samego dwa razy.
- **US-010:** Jako osoba, która wróciła ze sklepu, chcę zamienić kupione pozycje w produkty w spiżarni, aby nie wpisywać ich drugi raz.
- **US-011:** Jako osoba, która nie wie, co ugotować, chcę zobaczyć przepisy z produktów, które mam, aby wykorzystać jedzenie, zanim się zmarnuje.
- **US-012:** Jako osoba, która chce mniej wyrzucać, chcę widzieć statystyki zakupów i marnowania, aby zmienić nawyki.
- **US-013:** Jako administrator, chcę zarządzać kontami użytkowników, aby dbać o bezpieczeństwo aplikacji.
- **US-014:** Jako administrator, chcę zarządzać słownikiem produktów, aby podpowiedzi i dane ze skanów były poprawne.
- **US-015:** Jako administrator, chcę przeglądać logi aplikacji, aby szybko znaleźć przyczynę błędu.

### Wymagania funkcjonalne

| ID | Wymaganie | Historyjka |
|---|---|---|
| **Konto i logowanie** | | |
| FR-01 | Użytkownik może zarejestrować konto, podając e-mail, nazwę wyświetlaną i hasło. | US-001 |
| FR-02 | Użytkownik potwierdza adres e-mail linkiem aktywacyjnym. | US-001 |
| FR-03 | Użytkownik może się zalogować i wylogować; po 5 nieudanych próbach konto jest czasowo blokowane. | US-001 |
| FR-04 | Użytkownik może zresetować zapomniane hasło przez link wysłany e-mailem. | US-001 |
| FR-05 | Użytkownik może zarejestrować się i logować kontem Google; konto Google z tym samym e-mailem jest łączone z istniejącym kontem. | US-002 |
| **Gospodarstwo domowe** | | |
| FR-06 | Użytkownik może utworzyć gospodarstwo domowe i zostaje jego właścicielem; gospodarstwo ma domyślne miejsca przechowywania. | US-003 |
| FR-07 | Właściciel może zaprosić innych użytkowników do gospodarstwa e-mailem (także osoby bez konta). | US-003 |
| FR-08 | Zaproszony może przyjąć lub odrzucić zaproszenie; zaproszenie wygasa po 7 dniach. | US-003 |
| FR-09 | Użytkownik ma dostęp wyłącznie do danych gospodarstw, do których należy. | US-003 |
| **Spiżarnia** | | |
| FR-10 | Członek gospodarstwa może dodać produkt z atrybutami: nazwa, kategoria, ilość i jednostka, data zakupu, opcjonalna data minimalnej trwałości lub termin przydatności, miejsce przechowywania. | US-004 |
| FR-11 | Przy wpisywaniu nazwy system podpowiada produkty ze słownika i uzupełnia kategorię oraz jednostkę. | US-004 |
| FR-12 | Członek może przeglądać produkty z oznaczeniem stanu terminu, filtrowaniem (miejsce, kategoria, stan terminu), wyszukiwaniem i sortowaniem. | US-005 |
| FR-13 | Członek może edytować i usuwać produkty (usunięcie błędnego wpisu nie wpływa na statystyki). | US-005 |
| FR-14 | Członek może oznaczyć produkt (w całości lub części) jako zużyty, wyrzucony lub oddany. | US-006 |
| FR-15 | System zapisuje historię zdarzeń produktu (kto, kiedy, ile, jaka akcja). | US-006 |
| **Kody kreskowe** | | |
| FR-16 | Użytkownik może zeskanować kod kreskowy (EAN-13, EAN-8, UPC) aparatem lub wpisać go ręcznie. | US-007 |
| FR-17 | System uzupełnia dane produktu z lokalnego słownika, a w razie braku – z bazy Open Food Facts, i zapamiętuje wynik. | US-007 |
| FR-18 | Produkt o nieznanym kodzie można dodać ręcznie; kod zostaje zapamiętany w słowniku. | US-007 |
| **Przypomnienia** | | |
| FR-19 | Zadanie wykonywane w tle codziennie wyszukuje produkty z kończącym się terminem (domyślnie ≤ 3 dni) i przeterminowane. | US-008 |
| FR-20 | Członkowie gospodarstwa otrzymują powiadomienia w aplikacji (z licznikiem nieprzeczytanych). | US-008 |
| FR-21 | Panel główny pokazuje sekcję „Kończy się termin”. | US-008 |
| FR-22 | Użytkownik ustawia, ile dni wcześniej chce być powiadamiany, i może włączyć dzienne podsumowanie e-mailem. | US-008 |
| **Lista zakupów** | | |
| FR-23 | Członek może utworzyć listę zakupów i dodawać do niej pozycje; lista jest wspólna dla gospodarstwa. | US-009 |
| FR-24 | Kupione pozycje można odhaczać. | US-009 |
| FR-25 | Kupione pozycje można jednym działaniem zamienić w produkty w spiżarni. | US-010 |
| **Przepisy** | | |
| FR-26 | System proponuje przepisy na podstawie produktów w gospodarstwie, preferując te z kończącym się terminem. | US-011 |
| FR-27 | Przepis pokazuje brakujące składniki, które można dodać do listy zakupów. | US-011 |
| **Statystyki** | | |
| FR-28 | System pokazuje najczęściej kupowane produkty. | US-012 |
| FR-29 | System pokazuje produkty najczęściej wyrzucane. | US-012 |
| FR-30 | System pokazuje ilość zmarnowanej żywności per miesiąc (w kg i w liczbie produktów). | US-012 |
| FR-31 | System pokazuje liczbę produktów uratowanych przed upływem terminu. | US-012 |
| **Administracja** | | |
| FR-32 | Administrator przegląda użytkowników, blokuje/odblokowuje konta i nadaje/odbiera rolę administratora. | US-013 |
| FR-33 | Administrator zarządza słownikiem produktów (dodawanie, edycja, usuwanie, kody kreskowe, łączenie duplikatów). | US-014 |
| FR-34 | Administrator przegląda logi aplikacji z filtrowaniem. | US-015 |

### Wymagania techniczne

| ID | Wymaganie | Realizacja |
|---|---|---|
| C-01 | Backend i frontend odseparowane, komunikacja przez REST API. | ADR-001, ADR-003 |
| C-02 | Backend udokumentowany w formacie OpenAPI. | ADR-013, TS-004 |
| C-03 | Wykorzystanie co najmniej jednej zewnętrznej bazy produktów lub kodów kreskowych. | ADR-007 (Open Food Facts) |
| C-04 | Przypomnienia generowane przez zadania wykonywane w tle. | ADR-006 (Hangfire) |
| C-05 | Rejestracja przez e-mail oraz zewnętrznego dostawcę OAuth. | ADR-005 |
| C-06 | Projekt skonteneryzowany. | ADR-011, TS-002 |
| C-07 | Projekt wyposażony w testy automatyczne. | ADR-012, TS-008 |
| C-08 | Projekt wdrożony i zaprezentowany jako działająca aplikacja. | ADR-011, TS-007, ZAD-002 |
| C-09 | Publiczne repozytorium; repo, temat, podział prac i stos. | TS-001 |
| C-10 | Sprawozdanie w LaTeX (PDF). | ZAD-001 |

### Wymagania niefunkcjonalne

| ID | Obszar | Wymaganie | Sposób weryfikacji |
|---|---|---|---|
| NFR-01 | Bezpieczeństwo | Hasła przechowywane wyłącznie jako hash; polityka haseł: min. 8 znaków, litera i cyfra. | Przegląd kodu, test rejestracji |
| NFR-02 | Bezpieczeństwo | Krótko żyjący token dostępowy (~15 min) i refresh token w ciasteczku `HttpOnly; Secure`. | Testy integracyjne |
| NFR-03 | Bezpieczeństwo | Użytkownik nie ma dostępu do danych cudzego gospodarstwa (odpowiedź 403/404). | Testy integracyjne |
| NFR-04 | Bezpieczeństwo | Cała komunikacja po HTTPS (wymagane też przez dostęp do aparatu w przeglądarce). | Wdrożenie z certyfikatem TLS |
| NFR-05 | Bezpieczeństwo | Ochrona przed typowymi atakami (OWASP Top 10): walidacja wejścia, zapytania parametryzowane, ograniczony CORS, limit zapytań przy logowaniu i rejestracji. | Checklista w PR, test limitu (429) |
| NFR-06 | Bezpieczeństwo | Sekrety (klucze OAuth, SMTP, hasło bazy) tylko w zmiennych środowiskowych, nigdy w repozytorium. | Skan sekretów w CI |
| NFR-07 | Wydajność | Typowe operacje API odpowiadają w czasie p95 < 500 ms przy 20 równoczesnych użytkownikach. | Test obciążeniowy (k6) |
| NFR-08 | Wydajność | Wyszukanie kodu z lokalnego słownika < 300 ms; z Open Food Facts < 3 s (timeout z komunikatem). | Testy integracyjne |
| NFR-09 | Wydajność | Listy są stronicowane; statystyki liczone zapytaniami agregującymi w bazie. | Przegląd kodu |
| NFR-10 | Niezawodność | Awaria usług zewnętrznych (Open Food Facts, SMTP) nie blokuje podstawowych funkcji spiżarni. | Testy z wyłączoną usługą |
| NFR-11 | Niezawodność | Zadania w tle są idempotentne (brak zdublowanych przypomnień) i ponawiane przy błędzie. | Test podwójnego uruchomienia |
| NFR-12 | Niezawodność | Endpointy `/health/live` i `/health/ready`; codzienny backup bazy na serwerze. | Healthcheck w docker compose |
| NFR-13 | Użyteczność | Interfejs responsywny, mobile-first, działa od 360 px szerokości. | Testy ręczne i E2E na widoku mobilnym |
| NFR-14 | Użyteczność | Interfejs po polsku; daty w formacie `dd.MM.rrrr`, strefa czasowa Europe/Warsaw. | Przegląd UI |
| NFR-15 | Dostępność | Zgodność z WCAG 2.1 AA w podstawowym zakresie (kontrast, etykiety, obsługa klawiaturą). | Lighthouse Accessibility ≥ 90 |
| NFR-16 | Utrzymywalność | Testy jednostkowe, integracyjne (na prawdziwym PostgreSQL) i E2E uruchamiane w CI na każdym PR. | Pipeline CI |
| NFR-17 | Utrzymywalność | Pokrycie testami warstwy domenowej i aplikacyjnej backendu ≥ 60%. | Raport pokrycia w CI |
| NFR-18 | Utrzymywalność | Każda zmiana przez Pull Request z min. 1 recenzją; spójny styl kodu (`dotnet format`). | Ochrona gałęzi `main` |
| NFR-19 | Przenośność | Cały system uruchamia się lokalnie jednym poleceniem `docker compose up`. | Instrukcja w README |
| NFR-20 | Przenośność | Konfiguracja przez zmienne środowiskowe; obrazy budowane automatycznie w CI. | Plik `.env.example`, pipeline CD |
| NFR-21 | Obserwowalność | Logi strukturalne z identyfikatorem żądania (correlation id), przechowywane 30 dni, bez danych wrażliwych. | Widok logów w panelu admina |
| NFR-22 | Prywatność (RODO) | Przetwarzamy minimum danych osobowych (e-mail, nazwa wyświetlana). | Model danych |
| NFR-23 | Licencje | Atrybucja Open Food Facts (licencja ODbL) widoczna w aplikacji. | Stopka / ekran „O aplikacji” |

### Zakres projektu

**Co jest w zakresie (In Scope):**
- Rejestracja i logowanie przez e-mail oraz konto Google.
- Gospodarstwa domowe z zapraszaniem domowników.
- Spis produktów w spiżarni z terminami ważności, miejscami przechowywania i statusami (zużyty, wyrzucony, oddany).
- Skanowanie kodów kreskowych i uzupełnianie danych z Open Food Facts.
- Codzienne przypomnienia o kończących się terminach (w aplikacji i opcjonalnie e-mailem).
- Wspólne listy zakupów z przenoszeniem kupionych pozycji do spiżarni.
- Propozycje przepisów z produktów dostępnych w domu (własny katalog przepisów).
- Statystyki: najczęściej kupowane, najczęściej wyrzucane, marnowanie per miesiąc, produkty uratowane.
- Panel administratora: użytkownicy, słownik produktów, logi.
- Wdrożenie na publiczny serwer z HTTPS.

**Co jest poza zakresem (Out of Scope):**
- Natywna aplikacja mobilna (aplikacja webowa działa w przeglądarce telefonu).
- Należenie do wielu gospodarstw i przełączanie się między nimi.
- Zarządzanie członkami gospodarstwa (usuwanie, przekazywanie własności), własne miejsca przechowywania.
- Usuwanie konta przez użytkownika i edycja profilu.
- Logowanie przez innych dostawców niż Google (np. Facebook, GitHub).
- Planowanie diety, liczenie kalorii i wartości odżywczych.
- Integracja ze sklepami internetowymi i porównywanie cen.
- Powiadomienia push i SMS.

---

## 5. Doświadczenie Użytkownika (UX) i Projekt Interfejsu (UI)

- **Link do projektu w Figmie:** https://www.figma.com/design/ZT48boYBlkZlEc0MSGpJKY/Projekt-PPSI2?node-id=0-1&t=fAxmIO0qQ838ZCqm-1
- **Kluczowy przepływ 1 – start:** Rejestracja (e-mail lub Google) → Utworzenie gospodarstwa → Zaproszenie domownika → Dodanie pierwszego produktu.
- **Kluczowy przepływ 2 – zakupy:** Lista zakupów → Odhaczenie kupionych pozycji w sklepie → „Przenieś kupione do spiżarni” → Uzupełnienie dat ważności.
- **Kluczowy przepływ 3 – skan:** „Skanuj” → Zeskanowanie kodu → Formularz uzupełniony danymi z Open Food Facts → Wpisanie daty ważności → Zapis.
- **Kluczowy przepływ 4 – ratowanie jedzenia:** Przypomnienie o kończącym się terminie → „Co ugotować?” → Wybór przepisu → Oznaczenie produktów jako zużyte → Wzrost liczby uratowanych produktów w statystykach.

### Kluczowe zasady projektowe
- **Mobile-first:** najczęściej używane akcje (skanuj, dodaj, oznacz jako zużyte) dostępne jednym kliknięciem na telefonie.
- **Czytelność terminów:** kolor i liczba dni do końca terminu przy każdym produkcie (świeży / kończy się / przeterminowany).
- **Minimum wpisywania:** podpowiedzi ze słownika, skan kodu, przenoszenie z listy zakupów, wartości domyślne (dzisiejsza data, cała ilość).
- **Jasność:** czytelne komunikaty o błędach przy polach formularza i zrozumiałe puste stany („Dodaj pierwszy produkt”).

---

## 6. Kwestie Techniczne

- **Stack technologiczny:** Backend: ASP.NET Core (.NET 10, C#), EF Core; Frontend: Blazor WebAssembly + MudBlazor; Baza danych: PostgreSQL; Zadania w tle: Hangfire; Logi: Serilog; Konteneryzacja: Docker, docker compose; CI/CD: GitHub Actions; Testy: xUnit, Testcontainers, bUnit, Playwright.
- **Integracje:** Logowanie przez konto Google (OAuth 2.0 / OpenID Connect); Open Food Facts API (dane produktów po kodzie kreskowym); serwer SMTP (e-maile).

| Decyzja | ADR |
|---|---|
| Modularny monolit + SPA, REST | [ADR-001](adr/ADR-001-architektura-systemu.md) |
| Frontend w Blazor WebAssembly | [ADR-003](adr/ADR-003-frontend.md) |
| Uwierzytelnianie (Identity, JWT, Google) | [ADR-005](adr/ADR-005-uwierzytelnianie.md) |
| Zadania w tle (Hangfire) | [ADR-006](adr/ADR-006-zadania-w-tle.md) |
| Open Food Facts z lokalnym cache | [ADR-007](adr/ADR-007-zewnetrzna-baza-produktow.md) |
| Model ilości, jednostek i statusów | [ADR-014](adr/ADR-014-model-ilosci-i-jednostek.md) |

---

## 7. Otwarte Pytania i Ryzyka

- **Otwarte pytanie:** Jak dokładnie definiujemy „produkt uratowany”?
- **Otwarte pytanie:** W jakiej jednostce raportować zmarnowaną żywność?
- **Otwarte pytanie:** Czy wystarczy jeden dostawca OAuth (Google)?
- **Otwarte pytanie:** Kiedy dokładnie jest termin oddania i prezentacji projektu?
- **Ryzyko:** Problemy z logowaniem przez Google (adresy przekierowań, ciasteczka między domenami).
  - **Plan mitygacji:** Spike ograniczony czasowo (SPIKE-1) w Sprincie 2, przed realizacją US-002; frontend i API pod jedną domeną.
- **Ryzyko:** Skanowanie kodów nie działa na części telefonów.
  - **Plan mitygacji:** Spike na Androidzie i iOS (SPIKE-2) w Sprincie 2; ręczne wpisanie kodu jako rozwiązanie awaryjne.
- **Ryzyko:** Niekompletne dane w Open Food Facts lub niedostępność usługi.
  - **Plan mitygacji:** Lokalny słownik produktów jako cache, timeouty, ręczne uzupełnianie danych.
- **Ryzyko:** Ograniczony czas zespołu.
  - **Plan mitygacji:** Skupienie się wyłącznie na zakresie z sekcji 4; planowanie według prędkości zespołu i ustalona kolejność upraszczania kryteriów akceptacji.

---
