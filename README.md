# journal-proof

Fingerprints of a market-forecast journal, published so that no forecast can
be changed after its outcome is known - not even by its author - without the
change showing.

Each line of `hashes.csv` is one trading session on one market (`tase` = Tel
Aviv, `eu` = Europe, `us` = New York). It is added within about an hour after
that market opens, when the session's forecasts are final. The commit time on
GitHub is the proof of when the line existed.

## Columns

- `date`, `session_day` - the session (`session_day` = days since 1970-01-01).
- `market` - which exchange's session.
- `rows` - how many forecasts it covers.
- `sha256` - the fingerprint of those forecasts (below).
- `chain` - SHA-256 of the previous line's `chain`, a newline, and this line's
  `date,session_day,market,rows,sha256`; `""` before the first line. Removing
  or changing any earlier line breaks every chain after it.
- `recorded_at` - when the line was computed (UTC).

## How a fingerprint is computed

Take the journal's live forecasts for that `session_day` on that market, sorted
by `target` then `horizon`. For each forecast, join these values with `|`,
writing a missing value as an empty string and a number as JavaScript's
`String(x)` prints it:

```
target|horizon|session_day|made_at|base_close|pred|lo|hi|p_up|engine|last_pred|last_lo|last_hi|last_p_up|last_at|last_engine
```

Join the forecasts with `\n` and take SHA-256, in lowercase hex. The outcome of
the session is not part of it: it comes from the market itself, and anyone can
check it.

Given a copy of the journal's rows, anyone can recompute every line here and
compare.
