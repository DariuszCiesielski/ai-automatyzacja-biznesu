# Inteligentne przypomnienia o płatnościach

> **Poziom trudności**: Początkujący
> **Szacowana oszczędność**: 2-3 godziny tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, system fakturowania (iFirma/Fakturownia/wFirma), Gmail

## Problem

Masz 30-50 faktur miesięcznie. Część klientów płaci na czas, część zapomina, część celowo opóźnia. Ręczne sprawdzanie kto zapłacił, pisanie przypomnień i eskalacja — to stresujące i czasochłonne. A każdy dzień opóźnienia to Twoja płynność finansowa.

## Rozwiązanie

### Krok 1: Połącz system fakturowania z n8n

Większość polskich systemów fakturowych ma API:
- **iFirma** → REST API z tokenem
- **Fakturownia** → API v3
- **wFirma** → REST API

Dodaj node **HTTP Request** pobierający niezapłacone faktury z terminem płatności < dzisiaj.

### Krok 2: Sklasyfikuj zaległości

Podziel niezapłacone faktury na grupy:

| Grupa | Opóźnienie | Akcja |
|-------|-----------|-------|
| Delikatne | 1-3 dni | Uprzejme przypomnienie |
| Stanowcze | 4-14 dni | Wyraźne wezwanie |
| Eskalacja | 15-30 dni | Ostateczne wezwanie + powiadomienie dla Ciebie |
| Windykacja | 30+ dni | Alert + przygotowanie noty odsetkowej |

### Krok 3: Generuj spersonalizowane przypomnienia

```
Prompt dla AI:
Napisz przypomnienie o niezapłaconej fakturze po polsku.
Klient: {{client_name}}
Numer faktury: {{invoice_number}}
Kwota: {{amount}} PLN
Termin płatności: {{due_date}}
Dni opóźnienia: {{days_overdue}}
Grupa: {{category}}

Ton: {{delikatny|stanowczy|ostateczny}} ale zawsze profesjonalny.
Podaj numer konta do wpłaty: [Twój numer konta]
Nie używaj gróźb prawnych w grupie "delikatne".
```

### Krok 4: Wyślij i zaloguj

- Wyślij email przez Gmail (jako odpowiedź w wątku z fakturą, jeśli możliwe)
- Zapisz w arkuszu: data wysłania, grupa, treść
- Przy grupie „Eskalacja" — wyślij SMS przez bramkę (np. SMSAPI)

### Krok 5: Powiadomienie o wpłacie

Dodaj drugi workflow sprawdzający wyciąg bankowy (przez API banku lub import CSV). Gdy wpłata się pojawi — oznacz fakturę jako zapłaconą i wyślij podziękowanie.

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **System fakturowy** — iFirma (od 0 PLN), Fakturownia (od 0 PLN), wFirma
- **[Gmail](https://gmail.com)** — wysyłka przypomnień (darmowe)
- **[SMSAPI](https://smsapi.pl)** — SMS dla eskalacji (od 0.07 PLN/SMS)

## Wskazówki

- **Nigdy nie wysyłaj przypomnień w piątek po 15:00** — nikt nie zapłaci w weekend, a zepsuje to nastrój
- Testuj na fejkowej fakturze zanim włączysz produkcyjnie
- Dodaj wykluczenia: klienci VIP, firmy z którymi masz ustalone dłuższe terminy
- Prowadź statystyki: ile przypomnień → ile wpłat → jaki średni czas reakcji

## Przykład z życia

Firma budowlana z Poznania (20 podwykonawców) wdrożyła automatyczne przypomnienia. Średni czas opłacania faktur spadł z 21 do 11 dni. Oszczędność na obsłudze windykacji: ~8 godzin miesięcznie.
