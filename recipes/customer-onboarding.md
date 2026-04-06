# Automatyczny onboarding klienta

> **Poziom trudności**: Średniozaawansowany
> **Szacowana oszczędność**: 5-7 godzin tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, CRM, Gmail, Google Docs, Calendly

## Problem

Nowy klient podpisał umowę — i co teraz? Trzeba wysłać welcome email, umówić kick-off, przygotować dostępy, stworzyć folder w Drive, dodać do CRM, przypisać opiekuna... Każdy klient przechodzi inną ścieżkę, bo procedura jest w głowie, nie w systemie. Efekt: pominięte kroki, niezadowoleni klienci na starcie współpracy.

## Rozwiązanie

### Krok 1: Zmapuj ścieżkę onboardingu

Zdefiniuj kroki dla nowego klienta (przykład agencji):

| Dzień | Krok | Odpowiedzialny |
|-------|------|---------------|
| 0 | Welcome email + ankieta | Automatyczny |
| 0 | Folder w Google Drive | Automatyczny |
| 0 | Karta klienta w CRM | Automatyczny |
| 1 | Zaproszenie na kick-off (Calendly) | Automatyczny |
| 1 | Dostępy do narzędzi | Częściowo automatyczny |
| 3 | Sprawdzenie ankiety + brief | Opiekun |
| 7 | Pierwszy status email | Automatyczny |

### Krok 2: Utwórz trigger w CRM

W n8n dodaj **Webhook** lub **CRM Trigger** uruchamiany, gdy deal przechodzi do statusu „Wygrana". Zbierz dane klienta: nazwa firmy, NIP, osoba kontaktowa, email, pakiet/plan.

### Krok 3: Zautomatyzuj cały dzień 0

Jeden workflow realizuje wszystkie kroki dnia 0:

**Node 1 — Google Drive**: Utwórz folder `Klienci/{{nazwa_firmy}}` z podfolderami: Briefy, Materiały, Raporty, Faktury.

**Node 2 — Google Docs**: Skopiuj szablon briefu do folderu klienta, wypełnij dane firmy.

**Node 3 — Gmail**: Wyślij welcome email:
```
Prompt dla AI:
Napisz welcome email dla nowego klienta po polsku.
Klient: {{client_name}}, pakiet: {{plan_name}}
Zawrzyj: powitanie, co się wydarzy w pierwszym tygodniu,
link do ankiety onboardingowej, dane kontaktowe opiekuna.
Ton: ciepły, profesjonalny, budujący entuzjazm.
```

**Node 4 — Calendly**: Wyślij link do umówienia kick-offu.

**Node 5 — CRM**: Utwórz kartę klienta, przypisz opiekuna, ustaw reminder na dzień 3 i 7.

### Krok 4: Follow-up na dzień 7

Automatyczny email z podsumowaniem pierwszego tygodnia i planem na kolejne 30 dni.

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Calendly](https://calendly.com)** — umawianie spotkań (darmowy plan)
- **[Google Workspace](https://workspace.google.com)** — Drive, Docs, Gmail
- **CRM** — HubSpot Free, Pipedrive, lub inny z API

## Wskazówki

- Przetestuj cały flow na **fikcyjnym kliencie** zanim uruchomisz na żywo
- Dodaj checklist w CRM — opiekun musi potwierdzić kroki ręczne
- Zbieraj NPS po 30 dniach — mierzysz jakość onboardingu
- Nie automatyzuj wszystkiego — kick-off powinien być osobisty

## Przykład z życia

Agencja SEO z Wrocławia (10 osób, ~8 nowych klientów/mies.) wdrożyła automatyczny onboarding. Czas onboardingu spadł z 4h do 30 min na klienta. NPS nowych klientów wzrósł z 7.2 do 9.1.
