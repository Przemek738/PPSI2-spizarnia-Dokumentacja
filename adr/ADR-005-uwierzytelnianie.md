# ADR-005: Uwierzytelnianie – ASP.NET Core Identity + JWT + Google OAuth

- **Status:** Zaakceptowana
- **Data:** 2026-11-05
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Temat wymaga rejestracji klasycznej (e-mail + hasło) **oraz** przez zewnętrznego dostawcę OAuth. Frontend to SPA działające na innej domenie/porcie niż API. Potrzebujemy ról (Użytkownik, Administrator) i autoryzacji na poziomie gospodarstwa domowego. Rozwiązanie musi działać lokalnie w `docker compose` i po wdrożeniu.

## 2. Rozważane opcje

### Opcja 1: ASP.NET Core Identity (wbudowany) + własne JWT + Google jako zewnętrzny login

- **Zalety:**
  - [+] Wszystko w jednym procesie i jednej bazie; brak dodatkowych kontenerów.
  - [+] Identity daje hashowanie haseł, potwierdzanie e-maila, reset hasła, blokadę konta, logowania zewnętrzne (`AspNetUserLogins`).
  - [+] Pełna kontrola nad przepływem – dobry materiał do sprawozdania.
- **Wady:**
  - [-] Obsługę refresh tokenów i wymianę po OAuth trzeba napisać samodzielnie (ostrożnie).

### Opcja 2: Keycloak (osobny serwer tożsamości w kontenerze)

- **Zalety:**
  - [+] Gotowe ekrany logowania, rejestracji, OAuth, MFA; standard OIDC.
- **Wady:**
  - [-] Dodatkowy ciężki kontener (pamięć na tanim serwerze), konfiguracja realmów, synchronizacja użytkowników z bazą aplikacji.
  - [-] Administrator „zarządza użytkownikami” w dwóch miejscach.

### Opcja 3: Zewnętrzny SaaS (Auth0, Microsoft Entra External ID, Firebase Auth)

- **Zalety:**
  - [+] Najmniej kodu po naszej stronie.
- **Wady:**
  - [-] Uzależnienie od dostawcy i limitów darmowego planu; trudniej testować lokalnie i w CI.

## 3. Decyzja

Wybieramy **Opcję 1**:
- **ASP.NET Core Identity** (z `IdentityUser<Guid>`) w schemacie `identity`,
- **access token JWT** (ważny ~15 min) przechowywany w pamięci SPA i wysyłany w nagłówku `Authorization: Bearer`,
- **refresh token** (losowy, hash w bazie, rotowany przy każdym użyciu, ważny 14 dni) w ciasteczku `HttpOnly; Secure; SameSite=Strict`, ścieżka `/api/v1/auth`,
- **Google** jako dostawca OAuth 2.0 / OIDC obsługiwany **przez backend** (Authorization Code Flow):
  1. SPA przekierowuje na `GET /api/v1/auth/external/google?returnUrl=...`,
  2. backend przekierowuje do Google, odbiera callback, tworzy lub łączy konto (po zweryfikowanym e-mailu),
  3. backend ustawia ciasteczko z refresh tokenem i przekierowuje do SPA, które wywołuje `POST /auth/refresh`, by dostać access token (token nie trafia do URL-a),
- role: `User`, `Admin`; autoryzacja w gospodarstwie przez politykę sprawdzającą członkostwo (`HouseholdMember` / `HouseholdOwner`).

Opcjonalnie później: drugi dostawca (GitHub/Microsoft) – dodanie to głównie konfiguracja.

## 4. Uzasadnienie

Opcja 1 spełnia oba wymagania rejestracji bez dodatkowej infrastruktury i jest w pełni testowalna w CI. Trzymanie refresh tokena w ciasteczku `HttpOnly` chroni go przed XSS, a krótki access token w pamięci ogranicza skutki wycieku. Obsługa OAuth po stronie backendu chroni sekret klienta Google.

## 5. Konsekwencje

### Pozytywne
- [+] Jedno źródło prawdy o użytkownikach – panel administratora operuje na tej samej bazie.
- [+] Brak dodatkowych kontenerów i kosztów.

### Negatywne
- [-] Musimy poprawnie zaimplementować rotację i unieważnianie refresh tokenów (testy!).
- [-] Ciasteczka między domenami: frontend i API najlepiej wystawić pod jedną domeną (np. `app.domena.pl` i `app.domena.pl/api` przez reverse proxy) – patrz ADR-011. W przeciwnym razie potrzebne `SameSite=None` i poprawny CORS.
- [-] Wymagana rejestracja aplikacji w Google Cloud Console (osobne adresy callback dla dev i produkcji).
