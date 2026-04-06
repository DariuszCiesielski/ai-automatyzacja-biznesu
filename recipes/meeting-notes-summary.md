# Notatki ze spotkań i action items z AI

> **Poziom trudności**: Początkujący
> **Szacowana oszczędność**: 2-4 godziny tygodniowo
> **Potrzebne narzędzia**: narzędzie do transkrypcji (Tactiq/Whisper), OpenAI/Claude API, n8n, Slack/email

## Problem

Spotkanie trwa godzinę. Ktoś „robi notatki", ale zapisuje połowę ustaleń. Po tygodniu nikt nie pamięta kto miał co zrobić. Action items giną, decyzje się powtarzają, a spotkania mnożą się, bo „trzeba jeszcze raz omówić".

## Rozwiązanie

### Krok 1: Nagrywaj i transkrybuj spotkania

Wybierz narzędzie do transkrypcji:
- **Tactiq** (rozszerzenie Chrome) — transkrybuje Google Meet/Zoom na żywo
- **Whisper** (self-hosted) — transkrypcja offline z pliku audio
- **Fireflies.ai** — bot dołączający do spotkań automatycznie

Po spotkaniu eksportuj transkrypcję jako tekst.

### Krok 2: Wyślij transkrypcję do AI

W n8n utwórz workflow uruchamiany webhookiem (lub ręcznie po spotkaniu):

```
Prompt dla AI:
Przeanalizuj transkrypcję spotkania i wygeneruj podsumowanie po polsku.

Transkrypcja:
{{transcript}}

Wygeneruj:
1. **Podsumowanie** (3-5 zdań — o czym było spotkanie)
2. **Kluczowe decyzje** (lista podjętych decyzji)
3. **Action items** w formacie:
   - [ ] [Zadanie] → [Osoba odpowiedzialna] → [Termin]
4. **Otwarte pytania** (tematy wymagające dalszej dyskusji)
5. **Następne kroki** (co powinno się wydarzyć przed kolejnym spotkaniem)

Bądź konkretny — "przygotować ofertę dla klienta X do piątku"
jest lepsze niż "przygotować ofertę".
```

### Krok 3: Rozdziel action items

Na podstawie wyniku AI automatycznie:
- Utwórz zadania w menedżerze (Asana, Trello, ClickUp) z przypisaną osobą i terminem
- Wyślij podsumowanie na kanał Slack/Teams
- Zapisz notatkę w folderze Google Drive `Spotkania/{{data}}`

### Krok 4: Przypomnienia o action items

Workflow uruchamiany 24h przed terminem zadania — wysyła przypomnienie do odpowiedzialnej osoby.

## Potrzebne narzędzia

- **[Tactiq](https://tactiq.io)** — transkrypcja na żywo (darmowy plan: 5 spotkań/mies.)
- **[n8n](https://n8n.io)** — orkiestracja (darmowy self-hosted)
- **[Claude API](https://console.anthropic.com)** — analiza transkrypcji (~0.15 PLN/spotkanie)
- **[Google Drive](https://drive.google.com)** — archiwizacja (darmowe)

## Wskazówki

- **Poinformuj uczestników o nagrywaniu** — RODO wymaga zgody
- Transkrypcje po polsku mają ~90% dokładności — zawsze przejrzyj action items przed wysłaniem
- Ustal konwencję: „na koniec każdego spotkania potwierdzamy action items głosowo"
- Archiwizuj transkrypcje — po roku masz bazę wiedzy o decyzjach w firmie

## Przykład z życia

Startup technologiczny z Katowic (12 osób, ~15 spotkań/tydzień) wdrożył automatyczne notatki. Liczba „powtórkowych" spotkań spadła o 40%. Realizacja action items wzrosła z ~60% do ~85%.
