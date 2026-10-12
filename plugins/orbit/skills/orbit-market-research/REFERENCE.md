# Orbit market research: reference

## Choosing comps

Look our property up first (location, style, rooms, typical price, what guests come for) so the
comps match what it is, not just where it is.

- **Regional** (`scope: "regional"`): up to 12 properties within about 40 miles that a guest would
  weigh against us: inns, B&Bs, boutique hotels, winery or vineyard lodging. Closest in style and
  price first; include `distanceMiles`.
- **National** (`scope: "national"`): up to 10 properties like us elsewhere (for a winery inn:
  small, roughly 5–15 rooms, at wineries or in US wine regions). They show where our positioning
  could carry us, not what a local guest compares.
- Each needs its own website (not a travel-site listing), city, state, `rooms` if known, and one
  sentence in `why`. Retire a comp that closed or no longer fits by posting it with `"active": false`.

## Reading prices

For each comp and each date in `quoteNights`, check its own booking page for a two-adult price
→ `kind: "night"` with that `date`. If the site only posts rates, use `weekday` / `weekend` /
`from` with `date: null`. Always give the exact page `url` and a short `note` (room type, season,
minimum stay). Regional comps first; skip a property whose price you can't find.

## Suggesting prices

Work from the brief's `nights` (next 60: `ourPriceUsd`, `rooms`, `sold`, `open`, `lastYearSold`)
and `comparison` (our weekday/weekend price, the nearby median, our rank, `flags`). Skip closed
nights (`rooms: 0`) and nights that already have an open suggestion unless something changed.

| Signal | Suggestion |
| --- | --- |
| Priced 15%+ above nearby, half or more of rooms open, within 21 days | Lower toward the nearby median, 5–15% |
| Sold out or one room left, 7+ days out, at or below nearby | Raise the similar nights still open (same weekday, nearby dates) 5–15% |
| Selling ahead of last year's same weekday by 2+ rooms | Hold or raise 5–10% |
| Behind last year with rooms open within 30 days | Lower 5–10% |
| A local event or holiday you found a source for | Raise those nights; name the event in the reason |

- One step stays within ±20% of today's price (Orbit rejects anything past 50%).
- Nights inside 45 days first; at most about 15 suggestions per run.
- `reason`: one sentence with the numbers behind it, e.g. "7 of 8 open 12 days out; nearby
  Saturdays are $279–$309; last year 6 were sold by now."
- `confidence`: `high` when comps quoted that exact night and the signal is strong; `medium` for
  posted rates or a single signal; `low` for thin data.
- No signal, no suggestion. An empty list is a fine result.

## Posting

One `POST /record` per run (the key decides the property). All arrays are optional. Write the JSON
to a file and send it with `--data @file`.

```json
{
  "agent": "orbit-market-research",
  "note": "Weekly run: refreshed 3 comps, 18 prices, 4 suggestions",
  "properties": [
    { "name": "The Lakehouse Inn", "scope": "regional", "city": "Geneva-on-the-Lake", "state": "OH", "rooms": 8,
      "website": "https://lakehouseinn.com", "distanceMiles": 10, "why": "Lakefront winery inn with a restaurant" }
  ],
  "rates": [
    { "property": "The Lakehouse Inn", "date": null, "kind": "weekend", "priceUsd": 329, "url": "https://lakehouseinn.com/rates" },
    { "property": "the-lakehouse-inn-geneva-on-the-lake", "date": "2026-10-10", "kind": "night", "priceUsd": 289,
      "url": "https://lakehouseinn.com/book", "note": "King, 2-night minimum" }
  ],
  "recommendations": [
    { "date": "2026-10-10", "suggestedPriceUsd": 299, "confidence": "medium",
      "reason": "7 of 8 open 12 days out; nearby Saturdays are $279–$309; last year 6 were sold by now." }
  ]
}
```

- A rate names its property by `key` (from the brief) or exact name, and can refer to a property
  posted in the same call.
- Orbit fills in our current price for each suggestion itself; a newer suggestion for the same
  night replaces the open one.
- The reply lists what landed and a `rejected` reason for everything that didn't (travel-site URL,
  price outside $40–$3,000, undated night quote, closed night, step over 50%, missing reason).
