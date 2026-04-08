# Gotowe workflow do importu

Tutaj znajdziesz gotowe pliki workflow, które możesz zaimportować bezpośrednio do n8n lub Make.com. Każdy plik wymaga jedynie podmiany placeholderów (klucze API, ID arkuszy) na Twoje własne dane.

> **Powiązane:** Szczegółowe opisy krok po kroku znajdziesz w katalogu [/recipes/](../recipes/).

---

## Dostępne przykłady

### 1. `n8n-email-followup.json` — Automatyczny follow-up e-mail

Workflow n8n wysyłający spersonalizowane follow-upy do osób, które nie odpowiedziały w ciągu 48 godzin.

**Co robi:**
- Codziennie o 8:00 sprawdza wysłane wiadomości bez odpowiedzi
- AI (OpenAI) generuje uprzejmy, spersonalizowany follow-up po polsku
- Wysyła follow-up jako odpowiedź w tym samym wątku
- Loguje każdy follow-up w Google Sheets

**Potrzebne dane:**
- Konto Gmail z OAuth2 w n8n
- Klucz API OpenAI
- Arkusz Google Sheets z zakładką „Follow-upy" (kolumny: Data, Odbiorca, Temat, Treść follow-upu, Status)

---

### 2. `n8n-lead-scoring.json` — Scoring leadów z AI

Workflow n8n oceniający przychodzące leady w skali 1-10 i kierujący je do odpowiednich kanałów.

**Co robi:**
- Odbiera dane leada przez webhook (np. z formularza na stronie)
- AI ocenia lead wg 5 kryteriów (wielkość firmy, budżet, pilność, dopasowanie, kompletność)
- Gorące leady (>7) → CRM + powiadomienie Slack
- Ciepłe leady (4-7) → lista nurture w Google Sheets
- Zimne leady (<4) → archiwum

**Potrzebne dane:**
- Klucz API OpenAI
- URL i klucz API Twojego CRM-a (lub usuń ten node jeśli nie masz CRM-a)
- Token Slack z dostępem do kanału `#sprzedaz`
- Arkusz Google Sheets z zakładkami „Nurture" i „Archiwum"

**Webhook:** Po aktywacji workflow, n8n wygeneruje URL webhooka. Podepnij go pod formularz kontaktowy na swojej stronie.

---

### 3. `make-competitor-monitor.json` — Monitoring konkurencji

Blueprint Make.com monitorujący strony konkurentów i alertujący o istotnych zmianach.

**Co robi:**
- Co tydzień (poniedziałek o 7:00) pobiera strony konkurentów
- AI porównuje aktualną wersję z poprzednim snapshotem
- Ocenia znaczenie zmian (wysoka/średnia/niska/brak)
- Zapisuje snapshot i analizę w Google Sheets
- Wysyła e-mail z alertem tylko gdy wykryje istotne zmiany

**Potrzebne dane:**
- Klucz API OpenAI (w Make.com: Connections → OpenAI)
- Konto Gmail w Make.com
- Arkusz Google Sheets z zakładką „Snapshoty" (kolumny: A=Nazwa, B=URL, C=Data, D=Analiza, E=Snapshot)
- Adresy URL konkurentów (zamień `REPLACE_WITH_COMPETITOR_*_URL`)

---

## Jak importować

### n8n (pliki `n8n-*.json`)

1. Otwórz n8n (self-hosted lub n8n.cloud)
2. Kliknij **„+ Add workflow"** → **„Import from File"**
3. Wybierz plik `.json` z tego katalogu
4. Po imporcie otwórz każdy node i podłącz swoje dane uwierzytelniające (credentials)
5. Zamień wszystkie wartości `REPLACE_WITH_*` na swoje dane
6. Zapisz i aktywuj workflow

### Make.com (pliki `make-*.json`)

1. Zaloguj się do [Make.com](https://www.make.com)
2. Utwórz nowy scenariusz → kliknij **„..."** (więcej) → **„Import Blueprint"**
3. Wklej zawartość pliku JSON
4. Skonfiguruj połączenia (Connections) — Gmail, Google Sheets, OpenAI
5. Zamień wszystkie wartości `REPLACE_WITH_*` na swoje dane
6. Włącz scheduling i zapisz

---

## Placeholdery do zamiany

Wszystkie pliki zawierają placeholdery, które musisz zamienić na swoje dane:

| Placeholder | Co wstawić |
|------------|-----------|
| `REPLACE_WITH_YOUR_OPENAI_CREDENTIAL_ID` | ID credential OpenAI w n8n |
| `REPLACE_WITH_YOUR_GMAIL_CREDENTIAL_ID` | ID credential Gmail w n8n |
| `REPLACE_WITH_YOUR_SHEETS_CREDENTIAL_ID` | ID credential Google Sheets w n8n |
| `REPLACE_WITH_YOUR_SLACK_CREDENTIAL_ID` | ID credential Slack w n8n |
| `REPLACE_WITH_YOUR_SPREADSHEET_ID` | ID arkusza Google Sheets (z URL) |
| `REPLACE_WITH_YOUR_CRM_URL` | Adres API Twojego CRM-a |
| `REPLACE_WITH_YOUR_CRM_API_KEY` | Klucz API CRM-a |
| `REPLACE_WITH_YOUR_EMAIL` | Twój adres e-mail |
| `REPLACE_WITH_COMPETITOR_*_URL` | Adresy stron konkurentów |

---

## Licencja

MIT — używaj, modyfikuj, udostępniaj. Szczegóły w [LICENSE](../LICENSE).

[← Wróć do głównego README](../README.md)
