# Automatyczne raporty tygodniowe z AI

> **Poziom trudności**: Zaawansowany
> **Szacowana oszczędność**: 4-6 godzin tygodniowo
> **Potrzebne narzędzia**: n8n, źródła danych (GA, CRM, fakturowanie), OpenAI/Claude API, Google Docs

## Problem

Co piątek spędzasz 2-3 godziny zbierając dane z 5 różnych narzędzi: Google Analytics, CRM, system fakturowy, social media, projekty. Kopiujesz do Excela, robisz wykresy, piszesz wnioski. Raport wygląda za każdym razem inaczej, a wnioski są powierzchowne, bo brakuje czasu na głębszą analizę.

## Rozwiązanie

### Krok 1: Zidentyfikuj źródła danych

Typowe źródła dla firmy usługowej:

| Źródło | Co zbierać | API |
|--------|-----------|-----|
| Google Analytics | Ruch, konwersje, źródła | GA4 Data API |
| CRM (HubSpot/Pipedrive) | Nowe leady, pipeline, zamknięte deale | REST API |
| System fakturowy | Przychód, niezapłacone, średnia wartość | REST API |
| Social media | Zasięgi, engagement, nowi obserwujący | Buffer/natywne API |
| Projekty (Asana/ClickUp) | Ukończone zadania, opóźnienia | REST API |

### Krok 2: Zbierz dane automatycznie

W n8n utwórz workflow uruchamiany w piątek o 14:00. Równoległe node'y pobierają dane z każdego źródła:

```
Node 1: Google Analytics → ruch tygodniowy, top strony, konwersje
Node 2: CRM API → nowe leady, zmiana pipeline, wygrane
Node 3: Fakturownia API → wystawione faktury, wpłaty, zaległości
Node 4: Buffer API → posty, zasięg, engagement
Node 5: ClickUp API → ukończone taski, nadchodzące terminy
```

### Krok 3: Wygeneruj raport z AI

Wyślij wszystkie zebrane dane do Claude:

```
Prompt:
Jesteś analitykiem biznesowym. Na podstawie danych wygeneruj
tygodniowy raport dla zarządu małej firmy po polsku.

DANE:
{{all_collected_data}}

DANE Z POPRZEDNIEGO TYGODNIA (do porównań):
{{last_week_data}}

Format raportu:
1. **Podsumowanie wykonawcze** (3 zdania — najważniejsze wnioski)
2. **Sprzedaż i leady**
   - Nowe leady: X (zmiana tygodniowa: +/-Y%)
   - Pipeline: X PLN
   - Zamknięte: X PLN
3. **Ruch na stronie**
   - Sesje: X (zmiana: +/-Y%)
   - Top 3 strony
   - Źródła ruchu
4. **Social media**
   - Zasięg łączny: X
   - Najlepszy post
5. **Finanse**
   - Przychód: X PLN
   - Niezapłacone: X PLN
6. **Projekty**
   - Ukończone: X zadań
   - Opóźnione: X
7. **Rekomendacje AI** (3 konkretne sugestie na podstawie trendów)

Porównuj z poprzednim tygodniem. Wyróżnij anomalie.
Rekomendacje muszą być actionable — "co zrobić" nie "co obserwować".
```

### Krok 4: Sformatuj i wyślij

- Utwórz Google Doc z raportem (szablon z logo firmy)
- Wyślij emailem do zarządu/zespołu
- Zapisz w folderze `Raporty/{{rok}}/Tydzień {{numer}}`

### Krok 5: Dashboard na żywo (bonus)

Opcjonalnie: dane z kroku 2 zapisuj też do Supabase. Prosta strona z wykresami (Recharts) pokazuje metryki w czasie rzeczywistym.

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Google Analytics](https://analytics.google.com)** — dane o ruchu (darmowe)
- **CRM z API** — HubSpot Free, Pipedrive
- **[Claude API](https://console.anthropic.com)** — analiza i generowanie (~0.30 PLN/raport)
- **[Google Docs](https://docs.google.com)** — formatowanie raportu (darmowe)

## Wskazówki

- Zacznij od 2-3 źródeł danych — nie próbuj integrować wszystkiego na raz
- Pierwszych 4 raporty AI **zawsze recenzuj** ręcznie — kalibruj prompty
- Dodaj sekcję „Cele na następny tydzień" — raport staje się narzędziem planowania
- Archiwizuj raporty — po kwartale masz gotowy materiał na spotkanie strategiczne

## Przykład z życia

Agencja digitalowa z Gdyni (8 osób) przygotowywała raporty ręcznie — 3h/tydzień × 4 osoby = 12h. Po automatyzacji: 0h pracy ręcznej, raporty generowane automatycznie w piątek o 15:00. Jakość wniosków wzrosła, bo AI porównuje dane z 12 tygodni wstecz.
