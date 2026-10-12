# Orbit daily research: reference

## Weather

Forecast, 7 days (free, no key, US only). Take lat/lon from `orbit_query`: `SELECT lat, lon, zone FROM restaurants`.

```sh
curl -sA "orbit-research (owner's email)" https://api.weather.gov/points/<lat>,<lon>   # then fetch properties.forecast
```

History per day: `weather_days` (NOAA: high, low, precipitation in inches).

## Calendar

US holidays and the days around them (eve, day after), Mother's Day, Valentine's Day, NYE, Thanksgiving Eve,
graduation weekends, and regional ones (Ohio: Sweetest Day, the third Saturday in October). School breaks for the restaurant's county.
Big games and concerts in the nearest city count. Note whether an event pulls guests toward the restaurant or away.

## Bookings vs usual, same lead

```sql
-- covers booked now for one day vs covers booked by the same lead on the same weekday, last 8 weeks.
-- America/New_York here: use the restaurant's own zone (restaurants.zone).
WITH d AS (SELECT DATE '2026-10-10' AS day),
lead AS (SELECT timezone('America/New_York', (SELECT day FROM d)::TIMESTAMP) - now() AS l),
wk AS (SELECT w, day - INTERVAL (7 * w) DAY AS wd FROM d, range(0, 9) t(w))
SELECT w, sum(party_size) covers
FROM reservations r, wk, lead
WHERE kind = 'RESERVATION' AND state <> 'cancelled'
  AND (starts_at AT TIME ZONE 'America/New_York')::DATE = wd::DATE
  AND (w = 0 OR booked_at < timezone('America/New_York', wd::TIMESTAMP) - l)
GROUP BY w ORDER BY w;
```

w = 0 is now; average w 1–8 for "usual". Bookings made since the 3:15 am copy aren't in it (usually none overnight).

## Traps that have given wrong numbers

- **Money is cents** in every table. Timestamps are UTC: use `AT TIME ZONE` with the restaurant's zone.
- **Use the views** (`items_sold`, `table_times`, `scheduled_shifts`, `staff`): they apply Orbit's counting rules
  (voids, modifiers, re-stored days).
- **A punch is not always a person.** Punches with a $0 wage are usually a shared login; salaried managers also show
  $0. Check the job name.
- **Inn, manager, training and meeting jobs aren't demand-driven.** Leave them out of staffing-vs-volume questions.
- **Overtime:** salaried jobs (memory brief) never earn it. Workweek starts Monday.
- **Time a table took:** paid minus opened (`table_times.minutes`), 5–300 minutes.
- **Schedules aren't who works.** Compare `scheduled_shifts` with `time_entries` before judging a schedule.
- **Worked vs needed is circular.** A slow surprise day carries a crew sized for a busy one; compare crews on days
  that landed near what was expected.
- **First visits:** guests without an identified profile (`people.identity_status`) are invisible to repeat-visit
  counts; say how many.
- Table and field meanings: `orbit_guide('tables')`, or `orbit_schema` with `table`.

## Patterns to test

When no candidate is open, take the next untested one. Join two sources where you can, compare like days with like
days (same weekday, season and weather), and say "too small to tell" when it is. When one holds, post it as a move
(SKILL.md step 5b).

| Pattern | Tables | What it would change |
| --- | --- | --- |
| Inn guests' restaurant visits: share of stays with a dinner booking, by night | lodging_stays, lodging_nights, reservations | Offer a table when a room is booked |
| Inn nights on the books as a sign of a busy restaurant night 1–4 weeks out | lodging_nights, pos_days, forecast_log | Staffing and prep for those nights |
| Website and social visits leading online bookings (lag in days) | web_days, social_days, reservations | When to post or advertise before a slow night |
| Posts and stories against social visits and booking clicks | social_days, web_days | Which kind of post to make, and when |
| Weather × patio share × labor | weather_days, orders, time_entries | Move tables inside, cut or add patio staff |
| How far ahead people book × no-shows and cancellations | reservations | Reminders and deposits where they pay off |
| Party size × spend per guest | reservations, orders | Large-party menus or minimums |
| First visit's weekday and daypart × coming back within 90 days | reservations, people | Where to point first-time offers |
| Server or bartender × a category's attach rate (wine, dessert, NA drinks) | items_sold, staff | Who teaches what at pre-shift |
| A price change × orders of that item, against the rest of its category | items_sold, menu | Keep, raise or undo the price |
| Holiday weekends: Sunday against the Saturday before | pos_days | Staff Sundays of holiday weekends like Saturdays |
| Comps and discounts drifting up, by reason and day | orders, catalog | Who approves comps, and why |
