# Monitoring konkurencji z AI

> **Poziom trudności**: Zaawansowany
> **Szacowana oszczędność**: 3-5 godzin tygodniowo
> **Potrzebne narzędzia**: n8n, scraper (Apify/ScrapingBee), OpenAI/Claude API, Slack/Telegram

## Problem

Konkurencja obniżyła ceny 2 tygodnie temu, a Ty dowiadujesz się od klienta. Albo konkurent opublikował artykuł, który kradnie Ci pozycje w Google. Ręczne sprawdzanie 5-10 stron konkurencji codziennie jest nierealne — ale brak informacji kosztuje utracone szanse.

## Rozwiązanie

### Krok 1: Zidentyfikuj co monitorować

Stwórz listę w arkuszu:

| Konkurent | URL cennika | URL bloga | Profil LinkedIn | Co śledzić |
|-----------|------------|----------|----------------|------------|
| Firma A | firma-a.pl/cennik | firma-a.pl/blog | linkedin.com/... | Ceny, nowe usługi |
| Firma B | firma-b.pl/oferta | firma-b.pl/aktualnosci | linkedin.com/... | Ceny, case studies |

### Krok 2: Skonfiguruj scraping

W n8n utwórz workflow uruchamiany codziennie o 6:00.

**Node Scraper** — dla każdego URL pobierz aktualną treść strony:
```
Apify Actor: web-scraper
URL: {{competitor_pricing_url}}
Output: czysty tekst strony
```

### Krok 3: Porównaj z poprzednią wersją

Zapisuj każdy scraping do bazy danych (Supabase/Airtable). Przy kolejnym scrapingu porównuj z poprzednią wersją:

```
Prompt dla AI:
Porównaj dwie wersje strony cennikowej konkurenta.

WERSJA POPRZEDNIA ({{previous_date}}):
{{previous_content}}

WERSJA AKTUALNA ({{current_date}}):
{{current_content}}

Zidentyfikuj:
1. Zmiany cen (podwyżki/obniżki z kwotami)
2. Nowe produkty/usługi
3. Usunięte produkty/usługi
4. Zmiany w opisach (nowe funkcje, zmiana pozycjonowania)

Zwróć JSON:
{
  "has_changes": true/false,
  "severity": "low|medium|high",
  "changes": ["lista zmian"],
  "recommendation": "co powinieneś zrobić"
}
```

### Krok 4: Alertuj o istotnych zmianach

- **Severity high** (zmiany cen >10%, nowy produkt) → natychmiastowy alert Telegram + email
- **Severity medium** (zmiany opisów, nowe artykuły) → raport dzienny o 8:00
- **Severity low** (drobne zmiany layoutu) → raport tygodniowy w piątek

### Krok 5: Generuj raport tygodniowy

Piątkowy workflow zbiera wszystkie zmiany z tygodnia i generuje podsumowanie:

```
Raport konkurencji — tydzień {{week_number}}

Kluczowe zmiany:
- Firma A obniżyła cenę pakietu Pro o 15%
- Firma B opublikowała case study w branży e-commerce

Rekomendacje:
- Rozważyć rewizję cennika pakietu Pro
- Przygotować własne case study w e-commerce
```

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Apify](https://apify.com)** — scraping (darmowy plan: 5$/mies. kredytów)
- **[Claude API](https://console.anthropic.com)** — analiza zmian (~0.20 PLN/porównanie)
- **[Supabase](https://supabase.com)** — baza danych na historię (darmowy plan)
- **[Telegram Bot](https://core.telegram.org/bots)** — alerty (darmowe)

## Wskazówki

- Przestrzegaj **robots.txt** — nie scrapuj stron, które tego zabraniają
- Dodaj losowe opóźnienia między requestami (2-5 sekund)
- Nie reaguj na każdą zmianę — ustal progi istotności
- Archiwizuj dane — po roku masz bezcenną analizę trendów cenowych

## Przykład z życia

Sklep e-commerce z elektroniką (Łódź) monitorował 8 konkurentów. W pierwszym miesiącu wykrył 3 zmiany cenowe, na które zareagował w ciągu 24h zamiast 2 tygodni. Szacowany wpływ: +12% utrzymanie klientów w monitorowanych kategoriach.
