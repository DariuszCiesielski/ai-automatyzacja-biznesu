# Chatbot FAQ z AI do obsługi klienta

> **Poziom trudności**: Średniozaawansowany
> **Szacowana oszczędność**: 5-10 godzin tygodniowo
> **Potrzebne narzędzia**: n8n, OpenAI/Claude API, widget czatu (Crisp/Tidio), baza wiedzy

## Problem

Twój zespół obsługi klienta odpowiada na te same pytania 50 razy dziennie: „Ile kosztuje?", „Jaki jest czas realizacji?", „Jak złożyć reklamację?". Każda odpowiedź zajmuje 5 minut — to 4+ godziny dziennie na powtarzalne zapytania. Klient czeka, pracownik się frustruje, a złożone sprawy czekają w kolejce.

## Rozwiązanie

### Krok 1: Zbuduj bazę wiedzy

Zbierz 30-50 najczęstszych pytań i odpowiedzi. Zapisz w strukturyzowanym formacie:

```json
{
  "faq": [
    {
      "category": "Cennik",
      "question": "Ile kosztuje pakiet podstawowy?",
      "answer": "Pakiet podstawowy kosztuje 199 PLN/mies. netto. Zawiera...",
      "keywords": ["cena", "koszt", "pakiet", "cennik"]
    },
    {
      "category": "Realizacja",
      "question": "Jaki jest czas realizacji zamówienia?",
      "answer": "Standardowy czas realizacji to 3-5 dni roboczych...",
      "keywords": ["czas", "realizacja", "dostawa", "kiedy"]
    }
  ]
}
```

Zapisz w Supabase, Airtable, lub pliku JSON na serwerze.

### Krok 2: Skonfiguruj widget czatu

Dodaj widget czatu na stronie (Crisp, Tidio, lub własny). Widget wysyła pytanie klienta na webhook n8n.

### Krok 3: Wyszukaj odpowiedź z AI

```
Prompt systemu:
Jesteś asystentem obsługi klienta firmy {{company_name}}.
Odpowiadasz po polsku, uprzejmie i konkretnie.

Twoja baza wiedzy:
{{faq_database}}

Zasady:
1. Odpowiadaj TYLKO na podstawie bazy wiedzy
2. Jeśli nie znasz odpowiedzi → powiedz wprost i zaproponuj
   kontakt z konsultantem
3. Nie wymyślaj informacji (cen, terminów, warunków)
4. Maksymalnie 3-4 zdania w odpowiedzi
5. Jeśli pytanie dotyczy reklamacji/skargi → natychmiast
   przekieruj do człowieka

Pytanie klienta: {{user_message}}
```

### Krok 4: Eskalacja do człowieka

Chatbot powinien przekazywać sprawę człowiekowi gdy:
- Pytanie nie pasuje do żadnej kategorii FAQ
- Klient jest niezadowolony (wykryj sentiment)
- Pytanie dotyczy płatności/reklamacji/danych osobowych
- Klient prosi o rozmowę z człowiekiem

Przy eskalacji: wyślij powiadomienie na Slack z kontekstem rozmowy.

### Krok 5: Ucz się z rozmów

Workflow dzienny analizuje pytania, na które chatbot nie umiał odpowiedzieć:
- Grupuje podobne pytania
- Sugeruje nowe wpisy do FAQ
- Raportuje najczęstsze tematy eskalacji

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Crisp](https://crisp.chat)** — widget czatu (darmowy plan: 2 operatorów)
- **[Claude API](https://console.anthropic.com)** — odpowiedzi AI (~0.02 PLN/rozmowa)
- **[Supabase](https://supabase.com)** — baza wiedzy FAQ (darmowy plan)

## Wskazówki

- Zacznij od **20 pytań** — nie buduj bazy 200 pytań na start
- Pierwszych 2 tygodnie: **każda odpowiedź AI przechodzi review** człowieka
- Dodaj przycisk „Czy odpowiedź była pomocna?" — zbieraj feedback
- Chatbot to **filtr**, nie zamiennik obsługi — ścieżka do człowieka musi być łatwa
- Aktualizuj FAQ co 2 tygodnie na podstawie nowych pytań

## Przykład z życia

Sklep internetowy z kosmetykami (Kraków, 3 osoby w obsłudze) wdrożył chatbota FAQ. Bot odpowiada na 65% pytań samodzielnie. Czas odpowiedzi spadł z 2h do 30 sekund. Zespół obsługi skupia się na złożonych sprawach i reklamacjach.
