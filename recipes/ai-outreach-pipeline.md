# Pipeline outreach z AI — od zera do pierwszego leada

> **Poziom trudności**: Średnio-zaawansowany
> **Szacowana oszczędność**: 5-10 godzin tygodniowo
> **Potrzebne narzędzia**: Claude Code (lub API), scraper, email sender (SMTP/SendGrid)

## Problem

Masz produkt SaaS, ale zero outreachu. Ręczne wyszukiwanie firm, personalizowanie emaili i follow-up to 2-3 godziny na 10 maili. Przy 50 maili tygodniowo to pełny etat.

## Rozwiązanie: 4-etapowy pipeline AI

### Etap 1: Wyszukanie firm (research agent)

Agent przeszukuje Google Maps, katalogi branżowe lub LinkedIn szukając firm pasujących do Twojego ICP (Ideal Customer Profile):

```
Wejście: "Hotele 3-4★ w Polsce, 20-100 pokoi"
Wyjście: Lista 50 firm z: nazwa, strona www, email kontaktowy, wielkość
```

**Wzorzec z open-source:**
Na podstawie [competitor-researcher](https://github.com/ComposioHQ/competitor-researcher) — agent wyszukuje → scrapuje stronę → analizuje fit.

### Etap 2: Scoring (ocena dopasowania)

Każda firma oceniana A-F na podstawie kryteriów:

| Kryterium | Waga | A (najlepsza) | F (odrzuć) |
|-----------|------|--------------|------------|
| Strona www | 30% | Nowoczesna, bez chatbota | Brak strony |
| Wielkość | 25% | 20-100 pokoi | <5 lub >500 |
| Lokalizacja | 20% | Turystyczne miasto | Brak ruchu |
| Tech stack | 15% | Booking.com, bez AI | Już ma AI chatbota |
| Social media | 10% | Aktywne profile | Brak obecności |

**Tylko A i B idą do outreachu.** Reszta → baza na przyszłość.

### Etap 3: Personalizacja emaila (outreach agent)

Agent generuje spersonalizowany email na podstawie profilu firmy:

```markdown
Szablon:
---
Temat: {{pain_point}} — rozwiązanie dla {{firma}}

Cześć {{imię}},

Zauważyłem, że {{konkretna obserwacja ze strony www}}.

{{firma}} obsługuje {{szacowana_ilość}} gości miesięcznie — 
co jeśli {{konkretna wartość naszego produktu}}?

[1-2 zdania o produkcie z perspektywy ich korzyści]

Czy mają Państwo 15 minut w tym tygodniu na krótką rozmowę?

Pozdrawiam,
{{podpis}}
---
```

**Klucz:** `{{konkretna obserwacja}}` — agent MUSI wstawić coś specyficznego dla tej firmy (np. "widzę, że goście pytają o parking w Google Reviews").

### Etap 4: Sekwencja follow-up

Nie jeden email, ale sekwencja 3-4:

| Dzień | Email | Ton |
|-------|-------|-----|
| 0 | Pierwszy kontakt | Ciekawość + wartość |
| +3 | Follow-up #1 | Dodatkowa wartość (case study, statystyka) |
| +7 | Follow-up #2 | Social proof ("Hotel X zwiększył rezerwacje o 23%") |
| +14 | Break-up | "Ostatni raz piszę — jeśli to nie jest dobry moment..." |

**Wzorzec z open-source:** [outreach-agent](https://github.com/ComposioHQ/outreach-agent) — template sequencing z auto-personalizacją.

## Minimalny setup (start w 2h)

1. **Ręcznie** znajdź 20 firm (Google Maps + notatki w arkuszu)
2. **AI** wygeneruj spersonalizowane emaile (Claude API / Claude Code)
3. **Ręcznie** wyślij 5 maili dziennie (Gmail, nie automation)
4. **Śledź** odpowiedzi w arkuszu

**Dopiero gdy masz 3+ odpowiedzi:** automatyzuj cały pipeline.

## Częste błędy

| Błąd | Dlaczego to nie działa |
|------|----------------------|
| Wysyłanie 100 maili dziennie | Spam filter, ban domeny |
| Brak personalizacji | "Szanowny Panie" = kosz |
| Za długi email | Max 5-7 zdań. Nikt nie czyta esejów. |
| Brak follow-up | 80% odpowiedzi przychodzi po 2. lub 3. emailu |
| Za szybka automatyzacja | Najpierw waliduj ręcznie, potem skaluj |

## Koszty

| Element | Koszt |
|---------|-------|
| Scraping (20 firm) | 0 zł (ręcznie) |
| Claude API (20 emaili) | ~2 zł |
| Gmail (wysyłka) | 0 zł |
| **Razem na start** | **~2 zł** |

## Następne kroki

Gdy pipeline działa ręcznie (>5% response rate):
1. Automatyzuj scraping (Apify / ScrapingBee)
2. Automatyzuj wysyłkę (SendGrid + sekwencja)
3. Dodaj CRM tracking (response → lead → deal)
