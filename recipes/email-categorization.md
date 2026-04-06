# Automatyczna kategoryzacja i routing emaili

> **Poziom trudności**: Średniozaawansowany
> **Szacowana oszczędność**: 3-5 godzin tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, Gmail/Outlook, OpenAI/Claude API

## Problem

Dostajesz 50-100 emaili dziennie na firmową skrzynkę. Część to zapytania od klientów (pilne), część faktury (ważne ale nie pilne), część spam i newslettery (nieważne). Sortujesz ręcznie, a pilne zapytanie ginie między fakturami i reklamami. Klient czeka 2 dni na odpowiedź — i idzie do konkurencji.

## Rozwiązanie

### Krok 1: Zdefiniuj kategorie

| Kategoria | Priorytet | Przykłady | Akcja |
|-----------|-----------|----------|-------|
| Zapytanie klienta | Wysoki | Pytanie o ofertę, reklamacja | Natychmiastowe powiadomienie |
| Zamówienie | Wysoki | Nowe zamówienie, zmiana zamówienia | Alert + CRM |
| Faktura | Średni | Faktura do zapłaty, korekta | Folder „Księgowość" |
| Współpraca | Średni | Propozycja partnerstw | Folder „Do przejrzenia" |
| Newsletter | Niski | Subskrypcje, promocje | Archiwizuj |
| Spam | Brak | Niechciane wiadomości | Usuń |

### Krok 2: Skonfiguruj monitoring skrzynki

W n8n dodaj **Gmail Trigger** sprawdzający nowe emaile co 5 minut. Pobierz: nadawcę, temat, pierwsze 500 znaków treści.

### Krok 3: Klasyfikuj z AI

```
Prompt dla AI:
Sklasyfikuj poniższy email do jednej z kategorii:
- zapytanie_klienta
- zamowienie
- faktura
- wspolpraca
- newsletter
- spam

Email:
Od: {{from}}
Temat: {{subject}}
Treść: {{body_preview}}

Zwróć JSON:
{
  "category": "[kategoria]",
  "priority": "high|medium|low|none",
  "summary": "[1 zdanie podsumowania]",
  "suggested_action": "[co zrobić z tym mailem]",
  "requires_response": true/false,
  "response_deadline": "[sugerowany czas odpowiedzi]"
}
```

### Krok 4: Wykonaj akcje na podstawie kategorii

**Priorytet wysoki** (zapytanie/zamówienie):
- Oznacz etykietą „PILNE" w Gmail
- Wyślij powiadomienie push na telefon (Telegram/Pushover)
- Dodaj do CRM jako nowy lead/ticket

**Priorytet średni** (faktura/współpraca):
- Przenieś do odpowiedniego folderu
- Dodaj do listy „Do przejrzenia" na dzień

**Priorytet niski/brak**:
- Archiwizuj automatycznie

### Krok 5: Raport dzienny

O 8:00 rano workflow generuje podsumowanie:
```
Dzień dobry! Oto podsumowanie Twojej skrzynki:
🔴 3 pilne zapytania (2 nowe leady, 1 reklamacja)
🟡 5 faktur do zapłaty (łącznie 12 450 PLN)
🟢 2 propozycje współpracy
📨 14 newsletterów (zarchiwizowane)
```

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Gmail](https://gmail.com)** — poczta z API (darmowe)
- **[Claude API](https://console.anthropic.com)** — klasyfikacja (~0.01 PLN/email)
- **[Pushover](https://pushover.net)** — powiadomienia push (jednorazowo 25 PLN)

## Wskazówki

- Zacznij od 3-4 kategorii, rozbudowuj w miarę potrzeb
- **Nie usuwaj automatycznie** — przenoś do „Spam AI" i weryfikuj raz w tygodniu
- Dodaj regułę: emaile od istniejących klientów (w CRM) zawsze priorytet wysoki
- Po 2 tygodniach sprawdź dokładność klasyfikacji — powinna być >90%

## Przykład z życia

Kancelaria prawna z Warszawy (3 prawników, ~80 maili/dzień) wdrożyła klasyfikację AI. Czas odpowiedzi na zapytania klientów spadł z 8h do 1.5h. Żadne zapytanie nie zostało pominięte w ciągu pierwszych 3 miesięcy.
