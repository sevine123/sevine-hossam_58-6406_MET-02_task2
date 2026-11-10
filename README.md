# Task 2: Contracts, nested data and a star schema

**Name:** Sevine Hossam
**ID:** 58-6406
**Major / lab:** MET, P2

Run `pip install -r requirements.txt`, then run `task2.ipynb` top to bottom. It reads `DATA = "bookings.jsonl"` and writes `quarantine.jsonl` and the Parquet star schema in `star/`.

## What I did (Part D)

I read 283 lines and quarantined 24. Twenty-one failed the contract, each for one reason: 4 malformed `student.id`, 2 null `event`, 2 empty `tickets` lists, 3 ticket types outside standard/vip/student (`early-bird` ×2, `premium`), 3 with `qty` below 1, 4 whose `total` didn't equal Σ qty × price, and 3 booked after the event's date. The other 3 were duplicate `booking_id`s, which leaves 259 bookings (314 ticket rows). Deduplication runs after validation so only valid copies compete for an id. If it ran first, an invalid first copy would claim the id and the valid re-send would be discarded as a "duplicate", losing a good booking. In this file the three duplicate pairs are identical valid copies, so swapping the order wouldn't change the counts, but with other data it could. The drifting field is a top-level `promo_code` (`CLUB10`, `EARLY` or null). It first appears on B0125, booked 2026-10-20T01:05Z, and 90 documents don't have it. The model declares it `Optional[str] = None`, so those documents stay valid and get `promo_code = NULL`. `total` describes a whole booking, but the fact table has one row per ticket entry. A booking with two ticket types has two fact rows, so `SUM(total)` over the fact would count its total twice and overstate revenue. `SUM(amount)` adds each ticket line once and matches the real revenue.
