# Automatyczne follow-upy emailowe

> **Poziom trudności**: Początkujący
> **Szacowana oszczędność**: 3-5 godzin tygodniowo
> **Potrzebne narzędzia**: n8n lub Make.com, Gmail/Outlook, CRM (opcjonalnie)

## Problem

Wysyłasz ofertę lub pierwszego maila do potencjalnego klienta — i cisza. Po 3 dniach zapominasz o follow-upie, a lead stygnie. Badania pokazują, że **80% sprzedaży wymaga co najmniej 5 kontaktów**, ale większość handlowców rezygnuje po jednym.

Ręczne śledzenie, kto odpowiedział, a kto nie — to godziny tygodniowo w arkuszach kalkulacyjnych i notatkach.

## Rozwiązanie

### Krok 1: Skonfiguruj trigger monitorujący wysłane maile

W n8n utwórz nowy workflow. Dodaj node **Gmail Trigger** (lub IMAP) nasłuchujący na folder „Wysłane". Ustaw filtr na maile oznaczone etykietą `do-followup` (tę etykietę będziesz ręcznie dodawać do maili wymagających follow-upu).

```
Trigger: Gmail → Watch Sent Emails
Filter: Label = "do-followup"
Schedule: Co 1 godzinę
```

### Krok 2: Sprawdź czy klient odpowiedział

Dodaj node **Gmail Search** szukający odpowiedzi od tego samego adresata w ciągu ostatnich 3 dni. Jeśli odpowiedź istnieje — workflow się kończy. Jeśli nie — przechodzimy dalej.

```
Search: from:{adres_klienta} after:{data_wysłania}
Warunek: Brak wyników → kontynuuj
```

### Krok 3: Wygeneruj spersonalizowany follow-up z AI

Dodaj node **OpenAI** lub **Claude** z promptem:

```
Napisz krótki, uprzejmy follow-up email po polsku.
Kontekst oryginalnego maila: {{original_subject}} — {{original_snippet}}
Ton: profesjonalny ale ciepły.
Maksymalnie 3 zdania + pytanie zamykające.
Nie przepraszaj za "ponowny kontakt".
```

### Krok 4: Wyślij follow-up i oznacz

Dodaj node **Gmail Send** wysyłający wygenerowaną wiadomość jako odpowiedź w tym samym wątku. Następnie usuń etykietę `do-followup` i dodaj `followup-sent`.

### Krok 5: Eskalacja po braku odpowiedzi na follow-up

Dodaj drugi workflow sprawdzający po 5 dniach od follow-upu. Jeśli nadal brak odpowiedzi — wyślij powiadomienie na Slacka/Telegram z listą „zimnych" leadów do decyzji.

## Potrzebne narzędzia

- **[n8n](https://n8n.io)** — orkiestracja workflow (darmowy self-hosted lub od 20€/mies. w chmurze)
- **[Gmail](https://gmail.com)** — poczta z API (darmowe)
- **[OpenAI API](https://platform.openai.com)** — generowanie treści follow-upu (~0.01 PLN/mail)

## Wskazówki

- Ogranicz do **maksymalnie 2 follow-upów** na lead — więcej to spam
- Testuj na swoim własnym adresie przed uruchomieniem produkcyjnym
- Dodaj losowe opóźnienie 1-3h, żeby maile nie wychodziły o równych godzinach
- Prowadź arkusz z wynikami: ile follow-upów → ile odpowiedzi → ile sprzedaży

## Przykład z życia

Agencja marketingowa z Krakowa wdrożyła ten przepis dla 3 handlowców. W pierwszym miesiącu odzyskali 12 leadów, które wcześniej by „przepadły" — 4 z nich zostały klientami. Oszczędność czasu: ~15 godzin miesięcznie na zespół.
