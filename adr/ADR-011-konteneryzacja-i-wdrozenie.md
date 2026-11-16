# ADR-011: Konteneryzacja (Docker Compose) i darmowe wdrożenie na maszynę wirtualną Azure for Students

- **Status:** Zaakceptowana
- **Data:** 2026-11-10
- **Autorzy:** Przemysław Pyrzyński, Michał Napora, Maksymilian Wojtkowski

---

## 1. Kontekst

Projekt wymaga, aby system był **skonteneryzowany i wdrożony**: prezentacja ma pokazać działającą, wdrożoną aplikację, a sprawozdanie musi zawierać instrukcję lokalnego i zdalnego uruchomienia. Ograniczenia:
- **zespół nie chce ponosić żadnych kosztów**,
- wymagany jest **HTTPS**: przeglądarka udostępnia aparat (skanowanie kodów) tylko na stronach HTTPS, ciasteczko z refresh tokenem ma flagę `Secure`, a logowanie przez Google wymaga adresu HTTPS,
- aplikacja składa się z kilku kontenerów (API, frontend, PostgreSQL) i powinna działać tak samo lokalnie i na serwerze,
- zespół nie ma doświadczenia w administrowaniu serwerami.

## 2. Rozważane opcje

### Opcja 1: Maszyna wirtualna w ramach Azure for Students + Docker Compose + Caddy + obrazy z GHCR budowane w GitHub Actions

- **Zalety:**
  - [+] **Darmowe:** Azure for Students daje studentom studiów 100 USD kredytu na 12 miesięcy (weryfikacja uczelnianym e-mailem). Mała maszyna wirtualna mieści się w tym kredycie z zapasem na czas semestru.
  - [+] Na serwerze uruchamiamy **ten sam `docker compose`** co lokalnie – instrukcje uruchomienia lokalnego i zdalnego są prawie identyczne.
  - [+] **Bez kupowania domeny:** publiczny adres IP maszyny w Azure może dostać darmową nazwę DNS (np. `spizarnia.westeurope.cloudapp.azure.com`), dla której Caddy uzyska certyfikat HTTPS.
  - [+] Caddy automatycznie pobiera i odnawia certyfikat Let's Encrypt; frontend i API pod jednym adresem (`/` i `/api`) – brak problemów z CORS i ciasteczkami.
  - [+] GitHub Actions i GitHub Container Registry (GHCR) są bezpłatne dla publicznych repozytoriów; obrazy budują się w CI, a nie na słabym serwerze.
- **Wady:**
  - [-] Trzeba pilnować zużycia i wybrać mały rozmiar maszyny.
  - [-] Konto Azure for Students zakłada jedna osoba; pozostali dostają dostęp przez SSH lub rolę w subskrypcji.
  - [-] Serwerem trzeba się zająć samodzielnie (aktualizacje, firewall, backup).

### Opcja 2: Oracle Cloud Always Free (maszyna ARM)

- **Zalety:**
  - [+] Darmowa bezterminowo; maszyna ARM z 2 OCPU i 12 GB RAM wystarcza na ten projekt z dużym zapasem.
- **Wady:**
  - [-] Darmowe maszyny są oblegane – w popularnych regionach utworzenie maszyny może się nie udawać przez długi czas („Out of capacity”).
  - [-] Oracle może odebrać darmowe maszyny o niskim obciążeniu.

### Opcja 3: Płatny VPS lub platforma PaaS (np. Azure Container Apps, Render)

- **Zalety:**
  - [+] Przewidywalne działanie; PaaS zdejmuje z zespołu administrację serwerem.
- **Wady:**
  - [-] Kosztuje; darmowe plany PaaS usypiają aplikacje lub nie oferują darmowej bazy PostgreSQL, a konfiguracja różni się od lokalnej.

## 3. Decyzja

Wybieramy **Opcję 1 – maszyna wirtualna z Linuxem w ramach Azure for Students**, na której działa ten sam `docker compose` co lokalnie, z Caddy jako reverse proxy z automatycznym HTTPS. **Opcja 2 (Oracle Cloud Always Free) jest planem awaryjnym**, gdyby Azure for Students okazał się niedostępny dla członków zespołu.

**Kontenery (`docker-compose.yml`):**

| Usługa | Obraz | Uwagi |
|---|---|---|
| `db` | `postgres:18` | wolumen danych, healthcheck `pg_isready` |
| `api` | `ghcr.io/<org>/spizarnia-api` | wieloetapowy Dockerfile (`sdk` → `aspnet`), użytkownik nie-root, healthcheck `/health/ready` |
| `web` | `ghcr.io/<org>/spizarnia-web` | `dotnet publish` (Blazor WebAssembly) → `nginx:alpine` z plikami statycznymi |
| `proxy` | `caddy:2` | tylko na serwerze: TLS, `/api/*` → `api`, reszta → `web` |
| `mailpit` | `axllent/mailpit` | tylko lokalnie: przechwytywanie e-maili |

**Pipeline (GitHub Actions):**
1. **CI** (każdy PR): build, sprawdzenie formatowania, testy backendu (z Testcontainers), testy komponentów, build obrazów.
2. **CD** (merge do `main`): push obrazów do GHCR → SSH na serwer → `docker compose pull && docker compose up -d` → migracje bazy.

**Kolejność wdrażania:** w Sprincie 1 najpierw wdrożenie ręczne (`git pull` + `docker compose up -d` na serwerze), a gdy aplikacja działa – automatyzacja przez GitHub Actions.

## 4. Uzasadnienie

Azure for Students jest jedyną opcją, która jest jednocześnie darmowa, nie wymaga karty kredytowej i daje przewidywalny dostęp do maszyny od razu. Uruchamianie tego samego `docker compose` lokalnie i na serwerze minimalizuje rozbieżności („u mnie działa”) i upraszcza instrukcje w sprawozdaniu. Jeden adres dla frontendu i API upraszcza bezpieczeństwo ciasteczek. Automatyczne wdrożenie z CI sprawia, że osoba odpowiedzialna za DevOps nie jest wąskim gardłem.

## 5. Konsekwencje

### Pozytywne
- [+] Zerowy koszt dla zespołu: Docker, Caddy, Let's Encrypt, GitHub Actions i GHCR są darmowe.
- [+] `docker compose up` uruchamia całość lokalnie, a na serwerze działa identyczna konfiguracja.
- [+] Powtarzalne wdrożenia i łatwy powrót do poprzedniej wersji.

### Negatywne
- [-] Trzeba pilnować kredytu – po jego wyczerpaniu maszyna przestanie działać. Maszyna musi działać co najmniej do prezentacji i oceny projektu.
- [-] Konto i subskrypcja są przypisane do jednej osoby – musi ona nadać pozostałym dostęp i przechowywać sekrety (`.env` na serwerze, GitHub Secrets).
- [-] Odpowiedzialność za serwer (aktualizacje, backup bazy, dostępność przed prezentacją).
