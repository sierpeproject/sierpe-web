---
title: Sierpe 1.12.0 — movements learn their transaction hash
date: 2026-09-30T23:30:00Z
summary: Movements carry txHash, and an archive-backed reconciliation fills the stored history without replaying a single ledger — because the archives still remember what Horizon no longer does.
---

The pipeline consuming a Sierpe instance deduplicates on a key built
from the transaction hash — a reasonable key, since it is the one thing
every Stellar tool agrees on. A movement row could not provide it: its
id encodes the ledger, the transaction's position and the event's
position, but never the hash. 1.12.0 closes that gap twice over — for
every row still to come, and for every row already stored.

<!--more-->

**New movements carry `txHash` from birth.** The decoded transfer the
movement is derived from always had the hash in hand; it now travels the
last step onto the row and out through the API.

**Stored history reconciles without replay.** The interesting problem
was the year of movements already indexed. Re-deriving them would mean
re-running a 49-hour archive replay; asking Horizon would mean
discovering, as we did, that the SDF public Horizon now keeps weeks of
history, not years. The answer was already in the room: the History
Archives — the permanent record this appliance heals from — publish a
results file per checkpoint that pairs every transaction hash with its
ledger **in application order**, which is exactly the coordinate system
movement ids use. So `POST /v1/admin/movements/tx-hashes` fills the
column with no captive core and no retention wall: two local joins over
rows the database already trusts, then a static-file lookup for the
rest, one fetch per 64 ledgers. Idempotent, paced by the caller, repeat
until `done: true`.

The mechanism is pinned by a live test that resolves a real mainnet
transaction from the real archives and compares the result against the
hash Horizon serves — the reference, never this code. Additive release:
nullable column, `omitempty` field, cursors untouched. Upgrading is
bumping the image tag.
