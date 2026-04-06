# Automatyczny harmonogram social media

> **Poziom trudności**: Początkujący
> **Szacowana oszczędność**: 4-6 godzin tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, Buffer/Postiz, OpenAI/Claude API, Canva (opcjonalnie)

## Problem

Prowadzisz firmowy profil na 3-4 platformach. Codziennie musisz wymyślić temat, napisać post, dostosować do każdej platformy, znaleźć grafikę i opublikować. To zajmuje 1-2 godziny dziennie — i często odpuszczasz, bo „nie ma czasu". Efekt? Nieregularne publikacje i spadające zasięgi.

## Rozwiązanie

### Krok 1: Przygotuj bank tematów

Utwórz arkusz Google Sheets z kolumnami:
- Temat
- Kategoria (edukacyjny / sprzedażowy / rozrywkowy / case study)
- Status (do napisania / gotowy / opublikowany)
- Data publikacji

Raz w tygodniu (poniedziałek rano) wypełnij 5-7 tematów na cały tydzień.

### Krok 2: Generuj treści z AI

W n8n utwórz workflow uruchamiany codziennie o 7:00. Pobiera dzisiejszy temat z arkusza i generuje treści:

```
Prompt dla AI:
Napisz post na social media po polsku.
Temat: {{topic}}
Kategoria: {{category}}
Ton: profesjonalny ale przystępny, z elementem storytellingu.

Wygeneruj 3 wersje:
1. LinkedIn (2-3 akapity, profesjonalny, hashtagi branżowe)
2. Facebook (krótszy, bardziej bezpośredni, emoji OK)
3. Instagram (caption pod zdjęcie, hashtagi popularne)

Każda wersja powinna kończyć się CTA (call to action).
```

### Krok 3: Zaplanuj publikacje

Dodaj node wysyłający wygenerowane posty do Buffer lub bezpośrednio przez API platform:
- **LinkedIn** → publikuj o 8:30 (przed pracą)
- **Facebook** → publikuj o 12:00 (przerwa obiadowa)
- **Instagram** → publikuj o 18:00 (po pracy)

### Krok 4: Monitoruj wyniki

Raz w tygodniu (piątek) workflow zbiera statystyki:
- Zasięg, polubienia, komentarze, udostępnienia
- Zapisuje do arkusza obok każdego postu
- AI analizuje co działało najlepiej i sugeruje tematy na przyszły tydzień

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja workflow (darmowy self-hosted)
- **[Buffer](https://buffer.com)** — planowanie postów (darmowy plan: 3 kanały)
- **[Claude API](https://console.anthropic.com)** — generowanie treści (~0.10 PLN/post)
- **[Google Sheets](https://sheets.google.com)** — bank tematów (darmowe)

## Wskazówki

- **Nie publikuj treści AI bez przeglądu** — zawsze przeczytaj przed publikacją
- Zasada 80/20: 80% wartości, 20% sprzedaży
- Dodaj swoje zdjęcia i doświadczenia — AI pisze strukturę, Ty dodajesz autentyczność
- Testuj różne godziny publikacji przez 2 tygodnie, potem zostań przy najlepszych

## Przykład z życia

Biuro rachunkowe z Gdańska (5 osób) wdrożyło ten pipeline. Wcześniej publikowali raz na 2 tygodnie, po wdrożeniu — 5 razy w tygodniu. W 3 miesiące: +340% obserwujących na LinkedIn, 8 nowych klientów z social media.
