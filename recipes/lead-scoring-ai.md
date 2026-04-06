# Scoring leadów z AI

> **Poziom trudności**: Średniozaawansowany
> **Szacowana oszczędność**: 5-8 godzin tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, CRM, OpenAI/Claude API, Google Analytics (opcjonalnie)

## Problem

Twój zespół sprzedażowy traktuje wszystkie leady jednakowo. Handlowiec spędza godzinę na rozmowie z firmą, która nigdy nie kupi, a w tym czasie naprawdę zainteresowany klient czeka. Ręczna ocena leadów jest subiektywna i niespójna — każdy handlowiec ma inne kryteria.

## Rozwiązanie

### Krok 1: Zdefiniuj kryteria scoringu

Utwórz arkusz z kryteriami dopasowanymi do Twojego biznesu:

| Kryterium | Waga | Przykład |
|-----------|------|---------|
| Wielkość firmy | 20% | 1-10 osób: 3 pkt, 11-50: 7 pkt, 50+: 10 pkt |
| Branża | 15% | Docelowa branża: 10 pkt, pokrewna: 5 pkt, inna: 2 pkt |
| Zachowanie na stronie | 25% | Cennik: 10 pkt, case study: 7 pkt, blog: 3 pkt |
| Źródło leada | 15% | Polecenie: 10 pkt, Google Ads: 7 pkt, social: 4 pkt |
| Budżet | 25% | Podany i wystarczający: 10 pkt, "do ustalenia": 5 pkt |

### Krok 2: Zbierz dane o leadzie automatycznie

W n8n utwórz workflow uruchamiany przy nowym leadzie w CRM. Dodaj node'y:

1. **HTTP Request** → sprawdź firmę w KRS/CEIDG API
2. **HTTP Request** → pobierz profil LinkedIn firmy (opcjonalnie)
3. **Google Analytics** → pobierz historię wizyt tego IP/maila
4. **CRM** → pobierz historię interakcji

### Krok 3: Oceń leada z AI

Wyślij zebrane dane do Claude/OpenAI z promptem:

```
Jesteś ekspertem od kwalifikacji leadów B2B w Polsce.

Dane leada:
- Firma: {{company_name}}
- Branża: {{industry}}
- Wielkość: {{company_size}}
- Odwiedzone strony: {{pages_visited}}
- Źródło: {{lead_source}}
- Wiadomość kontaktowa: {{message}}

Oceń leada w skali 1-100 według kryteriów:
{{scoring_criteria}}

Zwróć JSON:
{
  "score": [1-100],
  "priority": "hot|warm|cold",
  "reasoning": "[2-3 zdania dlaczego taki scoring]",
  "recommended_action": "[co powinien zrobić handlowiec]"
}
```

### Krok 4: Zaktualizuj CRM i powiadom zespół

Na podstawie wyniku:
- **Hot (70-100)** → natychmiastowe powiadomienie na Slacka + przypisanie do najlepszego handlowca
- **Warm (40-69)** → dodaj do kolejki nurturingu, follow-up za 2 dni
- **Cold (1-39)** → automatyczny email z materiałami, rewizja za 30 dni

### Krok 5: Kalibruj model co miesiąc

Porównuj scoring AI z rzeczywistymi wynikami sprzedaży. Aktualizuj kryteria i wagi na podstawie danych.

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **CRM** — HubSpot Free, Pipedrive, lub dowolny z API
- **[Claude API](https://console.anthropic.com)** — scoring AI (~0.05 PLN/lead)
- **Google Analytics** — dane o zachowaniu na stronie (darmowe)

## Wskazówki

- Zacznij od prostego scoringu (5 kryteriów) — nie komplikuj na starcie
- AI scoring to **wsparcie**, nie zamiennik intuicji handlowca
- Loguj wszystkie scoringi — po miesiącu masz dane do kalibracji
- Unikaj scoringu opartego wyłącznie na wielkości firmy — małe firmy też kupują

## Przykład z życia

Firma SaaS z Warszawy (15 osób) wdrożyła AI scoring dla ~200 leadów miesięcznie. Handlowcy skupili się na leadach 70+ i zwiększyli konwersję z 8% do 14%. Czas reakcji na gorące leady spadł z 24h do 2h.
