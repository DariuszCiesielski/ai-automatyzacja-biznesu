# Szablon promptu: Klasyfikacja emaili

```
Sklasyfikuj poniższy email do jednej z kategorii:
- zapytanie_klienta (pytanie o ofertę, cenę, dostępność)
- zamowienie (nowe zamówienie, zmiana zamówienia)
- faktura (faktura do zapłaty, korekta, przypomnienie)
- wspolpraca (propozycja partnerstwa, współpracy)
- reklamacja (skarga, problem, niezadowolenie)
- newsletter (subskrypcja, promocja, marketing)
- spam (niechciane wiadomości)

Email:
Od: {{from}}
Temat: {{subject}}
Treść (pierwsze 500 znaków): {{body}}

Zwróć JSON:
{
  "category": "[kategoria]",
  "priority": "high|medium|low|none",
  "summary": "[1 zdanie podsumowania]",
  "requires_response": true/false,
  "suggested_response_time": "[np. 1h, 24h, 3 dni]"
}
```

## Kategorie i priorytety

| Kategoria | Priorytet | Czas odpowiedzi |
|-----------|-----------|----------------|
| zapytanie_klienta | high | < 2h |
| zamowienie | high | < 1h |
| reklamacja | high | < 4h |
| faktura | medium | < 24h |
| wspolpraca | medium | < 48h |
| newsletter | low | - |
| spam | none | - |
