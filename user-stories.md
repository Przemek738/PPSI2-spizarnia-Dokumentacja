# Historyjki Użytkownika - System do Zarządzania Domową Spiżarnią

Najważniejsze historyjki użytkownika dla projektu systemu do zarządzania domową spiżarnią.

## Epik: Konto i Logowanie

### US-001: Rejestracja i logowanie przez e-mail

**Jako** nowy użytkownik
**Chcę** założyć konto za pomocą adresu e-mail i hasła
**Aby** móc zapisywać produkty z mojej domowej spiżarni i mieć do nich dostęp z każdego urządzenia

**Kryteria akceptacji:**
- Użytkownik może zarejestrować się podając e-mail, nazwę wyświetlaną i hasło.
- Hasło ma co najmniej 8 znaków i zawiera co najmniej jedną literę i jedną cyfrę.
- Adres e-mail musi być unikalny i mieć poprawny format.
- Po rejestracji wysyłany jest e-mail z linkiem potwierdzającym adres.
- Użytkownik może się zalogować i wylogować; sesja nie wygasa po odświeżeniu strony.
- Po 5 nieudanych próbach logowania konto jest czasowo blokowane.
- Użytkownik, który zapomniał hasła, może je zresetować przez link wysłany e-mailem.

---

### US-002: Logowanie przez konto Google

**Jako** osoba, która nie lubi pamiętać kolejnych haseł
**Chcę** zarejestrować się i logować kontem Google
**Aby** szybciej zacząć korzystać z aplikacji

**Kryteria akceptacji:**
- Na stronach logowania i rejestracji widoczny jest przycisk "Kontynuuj z Google".
- Pierwsze logowanie przez Google automatycznie tworzy konto.
- Jeśli konto z tym samym adresem e-mail już istnieje, konto Google zostaje do niego podpięte (bez tworzenia duplikatu).
- Anulowanie logowania w Google wraca do strony logowania z czytelnym komunikatem.
- Po zalogowaniu użytkownik trafia na panel główny swojego gospodarstwa.

---

## Epik: Gospodarstwo Domowe

### US-003: Wspólne gospodarstwo z domownikami

**Jako** osoba mieszkająca z rodziną lub współlokatorami
**Chcę** utworzyć gospodarstwo domowe i zaprosić do niego domowników
**Aby** wszyscy widzieli te same produkty i mogli razem dbać o spiżarnię

**Kryteria akceptacji:**
- Użytkownik może utworzyć gospodarstwo, podając jego nazwę, i staje się jego właścicielem.
- Nowe gospodarstwo ma domyślne miejsca przechowywania: lodówka, zamrażarka, szafka, spiżarnia.
- Właściciel może zaprosić domownika, podając jego adres e-mail; zaproszony dostaje e-mail z linkiem.
- Jeśli zaproszona osoba nie ma konta, link prowadzi do rejestracji, po której automatycznie dołącza do gospodarstwa.
- Zaproszenie można przyjąć lub odrzucić; wygasa po 7 dniach.
- Właściciel widzi listę członków i oczekujących zaproszeń.
- Użytkownik nie widzi danych gospodarstw, do których nie należy.

---

## Epik: Spiżarnia

### US-004: Dodawanie produktów do spiżarni

**Jako** osoba robiąca zakupy dla całego domu
**Chcę** dodawać produkty spożywcze, które mam w domu
**Aby** wiedzieć, co mamy, gdzie to leży i do kiedy trzeba to zjeść

**Kryteria akceptacji:**
- Formularz zawiera: nazwę, kategorię, ilość, jednostkę (g, kg, ml, l, szt., opak.), datę zakupu i miejsce przechowywania.
- Można opcjonalnie podać datę ważności i wybrać, czy to "najlepiej spożyć przed", czy "należy spożyć do".
- Data zakupu domyślnie ustawia się na dzisiejszą.
- Podczas wpisywania nazwy system podpowiada produkty ze słownika i uzupełnia kategorię oraz jednostkę.
- Brakujące lub błędne dane są wyraźnie oznaczone przy polach formularza.
- Po zapisaniu produkt od razu pojawia się w spiżarni.

---

### US-005: Przeglądanie zawartości spiżarni

**Jako** osoba planująca posiłki
**Chcę** szybko przejrzeć, co mamy w lodówce, zamrażarce i szafkach
**Aby** nie kupować rzeczy, które już mam, i wiedzieć, co zjeść najpierw

**Kryteria akceptacji:**
- Lista pokazuje nazwę, ilość, miejsce, kategorię i datę ważności produktu.
- Produkty są oznaczone kolorem: świeży, termin się kończy, przeterminowany.
- Domyślnie na górze są produkty z najbliższym terminem ważności.
- Można filtrować po miejscu przechowywania, kategorii i stanie terminu oraz wyszukiwać po nazwie.
- Można edytować produkt (np. przenieść go do zamrażarki) lub usunąć błędnie dodany.
- Lista jest wygodna w obsłudze na telefonie.

---

### US-006: Oznaczanie produktu jako zużyty, wyrzucony lub oddany

**Jako** domownik, który chce mieć porządek w spiżarni
**Chcę** oznaczyć produkt jako zużyty, wyrzucony albo oddany
**Aby** lista produktów była zawsze aktualna, a statystyki pokazywały, ile jedzenia marnujemy

**Kryteria akceptacji:**
- Przy każdym produkcie dostępne są akcje "Zużyty", "Wyrzucony" i "Oddany".
- Domyślnie akcja dotyczy całej ilości, ale można podać tylko część (np. zużyto 0,5 l z 1 l mleka).
- Nie można oznaczyć większej ilości, niż zostało.
- Produkt, którego ilość spadła do zera, znika z listy aktywnych produktów.
- Akcję można cofnąć przez kilka sekund po jej wykonaniu.
- Każda akcja jest zapisywana w historii produktu (kto, kiedy, ile).

---

### US-007: Skanowanie kodu kreskowego

**Jako** osoba, która nie lubi ręcznie przepisywać danych
**Chcę** zeskanować kod kreskowy produktu aparatem telefonu
**Aby** dodać produkt do spiżarni w kilka sekund

**Kryteria akceptacji:**
- Przycisk "Skanuj" otwiera aparat i rozpoznaje kody EAN-13, EAN-8 i UPC.
- Po zeskanowaniu formularz dodawania produktu uzupełnia się danymi (nazwa, kategoria, ilość) z bazy Open Food Facts.
- Raz pobrane dane produktu są zapamiętywane, więc kolejny skan tego samego kodu działa natychmiast.
- Jeśli aparat jest niedostępny, kod można wpisać ręcznie.
- Jeśli produktu nie ma w żadnej bazie, użytkownik uzupełnia dane sam, a kod zostaje zapamiętany na przyszłość.
- Gdy zewnętrzna baza nie odpowiada, użytkownik widzi komunikat i może dodać produkt ręcznie.

---

## Epik: Przypomnienia

### US-008: Przypomnienia o kończących się terminach

**Jako** zapracowana osoba, która zapomina o produktach w lodówce
**Chcę** dostawać przypomnienia o produktach, którym kończy się termin ważności
**Aby** zdążyć je zjeść, zanim się zepsują

**Kryteria akceptacji:**
- System codziennie rano automatycznie (w tle) sprawdza terminy ważności produktów w gospodarstwie.
- Każdy domownik dostaje powiadomienie w aplikacji o produktach, którym termin kończy się w ciągu 3 dni, oraz o produktach przeterminowanych.
- Ikona dzwonka pokazuje liczbę nieprzeczytanych powiadomień; kliknięcie powiadomienia prowadzi do produktu.
- Na panelu głównym widoczna jest sekcja "Kończy się termin".
- Użytkownik może zmienić, ile dni wcześniej chce być powiadamiany, i włączyć dzienne podsumowanie e-mailem.
- Ten sam produkt nie generuje kilku powiadomień tego samego dnia.

---

## Epik: Lista Zakupów

### US-009: Wspólna lista zakupów

**Jako** osoba robiąca zakupy dla całej rodziny
**Chcę** tworzyć listę zakupów, którą widzą wszyscy domownicy
**Aby** nie zapomnieć, co trzeba kupić, i nie kupować tego samego dwa razy

**Kryteria akceptacji:**
- Użytkownik może utworzyć listę zakupów i dodawać do niej pozycje (nazwa, ilość, jednostka).
- Podczas wpisywania nazwy system podpowiada produkty ze słownika.
- Lista jest widoczna dla wszystkich członków gospodarstwa.
- Kupione pozycje można odhaczyć; przesuwają się na dół listy.
- Pozycje można edytować i usuwać.

---

### US-010: Przeniesienie zakupów do spiżarni

**Jako** osoba, która właśnie wróciła ze sklepu
**Chcę** jednym ruchem zamienić kupione pozycje z listy w produkty w spiżarni
**Aby** nie wpisywać tych samych rzeczy drugi raz

**Kryteria akceptacji:**
- Przycisk "Przenieś kupione do spiżarni" otwiera formularz ze wszystkimi odhaczonymi pozycjami.
- Dla każdej pozycji można uzupełnić datę ważności i miejsce przechowywania; kategoria i jednostka są wstępnie uzupełnione.
- Wszystkie pozycje trafiają do spiżarni naraz, z dzisiejszą datą zakupu.
- Przeniesione pozycje są oznaczone na liście i nie można ich przenieść ponownie.

---

## Epik: Przepisy

### US-011: Propozycje przepisów z tego, co mam w domu

**Jako** osoba, która nie wie, co dziś ugotować
**Chcę** zobaczyć przepisy, które mogę przygotować z produktów w mojej spiżarni
**Aby** wykorzystać jedzenie, zanim się zmarnuje

**Kryteria akceptacji:**
- Ekran "Co ugotować?" pokazuje do 10 przepisów posortowanych według tego, ile składników mam w domu.
- Przepisy wykorzystujące produkty z kończącym się terminem są wyżej na liście i wyraźnie oznaczone.
- Przy przepisie widać, których składników brakuje.
- Brakujące składniki można jednym przyciskiem dodać do listy zakupów.
- Podstawowe składniki (sól, pieprz, olej, woda) traktowane są jako zawsze dostępne.
- Szczegóły przepisu pokazują listę składników i kroki przygotowania.

---

## Epik: Statystyki

### US-012: Statystyki zakupów i marnowania jedzenia

**Jako** osoba, która chce mniej wyrzucać i mądrzej kupować
**Chcę** widzieć statystyki tego, co kupujemy i co marnujemy
**Aby** zmienić nawyki zakupowe i oszczędzać pieniądze

**Kryteria akceptacji:**
- Widoczny jest ranking najczęściej kupowanych produktów.
- Widoczny jest ranking produktów najczęściej wyrzucanych.
- Wykres pokazuje ilość zmarnowanej żywności w kolejnych miesiącach (w kg i w liczbie produktów).
- Widoczna jest liczba produktów uratowanych przed upływem terminu (zużytych lub oddanych, gdy termin już się kończył).
- Można wybrać okres: ostatnie 30 dni, 3 miesiące lub 12 miesięcy.
- Przy braku danych wyświetla się czytelny komunikat zamiast pustych wykresów.

---

## Epik: Administracja

### US-013: Zarządzanie użytkownikami

**Jako** administrator systemu
**Chcę** przeglądać konta użytkowników i blokować te, które łamią zasady
**Aby** dbać o bezpieczeństwo i porządek w aplikacji

**Kryteria akceptacji:**
- Administrator widzi listę użytkowników z wyszukiwaniem po e-mailu, datą rejestracji i sposobem logowania.
- Administrator może zablokować i odblokować konto; zablokowany użytkownik zostaje wylogowany i nie może się zalogować.
- Administrator może nadać lub odebrać innej osobie uprawnienia administratora.
- Nie można odebrać uprawnień ostatniemu administratorowi w systemie.

---

### US-014: Zarządzanie słownikiem produktów

**Jako** administrator systemu
**Chcę** dodawać, poprawiać i usuwać produkty w słowniku
**Aby** podpowiedzi i dane po zeskanowaniu kodu były poprawne

**Kryteria akceptacji:**
- Administrator widzi listę produktów ze słownika z wyszukiwaniem i filtrem kategorii.
- Widać, skąd pochodzi produkt: dodany przez administratora, przez użytkownika czy pobrany z Open Food Facts.
- Administrator może dodać, edytować i usunąć produkt oraz przypisać mu kody kreskowe.
- System nie pozwala zapisać błędnego ani zduplikowanego kodu kreskowego.
- Administrator może połączyć zduplikowane produkty w jeden.

---

### US-015: Przeglądanie logów aplikacji

**Jako** administrator systemu
**Chcę** przeglądać logi z działania aplikacji
**Aby** szybko znaleźć przyczynę błędu zgłoszonego przez użytkownika

**Kryteria akceptacji:**
- Administrator widzi tabelę logów: czas, poziom (informacja, ostrzeżenie, błąd), treść i użytkownika.
- Logi można filtrować po poziomie, zakresie dat i treści.
- Kliknięcie wpisu pokazuje jego szczegóły, w tym opis błędu.
- Logi nie zawierają haseł ani innych poufnych danych.
- Logi starsze niż 30 dni są automatycznie usuwane.
