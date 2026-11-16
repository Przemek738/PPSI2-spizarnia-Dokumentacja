# ADR-002: Backend – ASP.NET Core na .NET 10 (LTS), kontrolery Web API

- **Status:** Zaakceptowana
- **Data:** 2026-11-02
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Zespół zdecydował o stosie .NET / C#. Trzeba wybrać wersję platformy i styl budowy API. Projekt musi działać stabilnie do końca semestru, mieć dokumentację OpenAPI, walidację wejścia i spójną obsługę błędów.

## 2. Rozważane opcje

### Opcja 1: .NET 10 (LTS) + kontrolery [ApiController]

- **Zalety:**
  - [+] Wersja LTS – długie wsparcie, aktualne obrazy Dockera.
  - [+] Kontrolery to dobrze znany, opisany wzorzec; łatwe grupowanie endpointów modułu, filtry, atrybuty autoryzacji.
  - [+] Dobra integracja z generatorem OpenAPI.
- **Wady:**
  - [-] Nieco więcej kodu niż Minimal API.

### Opcja 2: .NET 10 (LTS) + Minimal API

- **Zalety:**
  - [+] Mniej boilerplate'u, nowoczesny styl, wbudowana walidacja w nowszych wersjach.
- **Wady:**
  - [-] Przy wielu endpointach wymaga własnej konwencji organizacji (grupy, rozszerzenia) – łatwo o bałagan w zespole uczącym się.

### Opcja 3: Starsza wersja (.NET 8 LTS)

- **Zalety:**
  - [+] Najwięcej materiałów w sieci.
- **Wady:**
  - [-] Krótsze pozostałe wsparcie; brak nowości platformy.

## 3. Decyzja

Wybieramy **ASP.NET Core na .NET 10 (LTS)** z **kontrolerami Web API**. Standardy:
- odpowiedzi błędów w formacie **ProblemDetails (RFC 9457)**, globalny handler wyjątków,
- walidacja żądań przez **FluentValidation**,
- wersjonowanie przez prefiks ścieżki /api/v1/...
- konfiguracja przez appsettings + zmienne środowiskowe.

## 4. Uzasadnienie

LTS daje stabilność na czas projektu i po nim. Kontrolery zapewniają przewidywalną strukturę przy ok. 50–70 endpointach i kilku osobach piszących backend; są też łatwiejsze do opisania w sprawozdaniu. Minimal API pozostaje dopuszczalne dla pojedynczych prostych endpointów (np. health checki).

## 5. Konsekwencje

### Pozytywne
- [+] Jednolity styl endpointów i obsługi błędów w całym API.
- [+] Przewidywalny kontrakt dla frontendu (ProblemDetails z listą błędów walidacji).

### Negatywne
- [-] Wszyscy członkowie zespołu muszą mieć zainstalowane SDK .NET 10 (lub pracować w kontenerze).
