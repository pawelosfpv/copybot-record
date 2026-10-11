# copybot-record

Tamper-evidence for **paper run 3** of a Polymarket copy-trading research bot.
Paper trading only: no real orders are placed. Not financial advice.

The bot keeps an append-only, hash-chained journal of everything that moves its
paper book (every simulated fill with the order book it was priced against, every
skipped copy, every settlement). Each journal entry's hash covers all entries
before it, so the last hash of a day commits to the entire record up to that day.

This repository receives one line per finished UTC day, pushed automatically
by the machine running the bot:

| file | what |
|---|---|
| `digests.csv` | `day_utc, last_seq, entries, hash` — the day's last journal entry |
| `proofs/<day>.txt` + `.ots` | the same digest, stamped with [OpenTimestamps](https://opentimestamps.org): anchored in a Bitcoin block, so it proves the digest existed by then independently of git or GitHub dates |
| `preregistration.sha256` (+ `.ots`) | hash of the run's pre-registered success criteria |

Trades, wallets and performance are deliberately **not** published here while the
run is in progress. When the full journal is released, anyone can check it:

```
copybot audit                                  # chain intact, every fill re-prices from its stored order book
copybot audit --check-digests digests.csv      # the released journal is the one these digests committed to
ots verify proofs/2026-10-12.txt.ots           # the digest existed at the Bitcoin-attested time
```

A rewritten journal can be made internally consistent again by recomputing its
hashes, but it can no longer match the digests published here on earlier days.

**About the pre-registration hash:** it was computed and stamped on 2026-10-11,
the day run 3 started (first trade ~06:00 UTC). The criteria were written before
the first trade, but this public stamp only proves they existed by its Bitcoin
time, not before the run began.
