---
title: The embedded UI
weight: 25
group: Interfaces & reference
description: A decoded contract viewer baked into the binary — searchable list, a page per contract, state diffs and deep links, with no build system and no external assets.
---

Open `/` in a browser. That is the whole setup.

Sierpe serves a contract viewer from the binary itself: **one
self-contained HTML page**, no build system, no external assets, no
separate service to deploy or keep in sync with the API. It covers the
same surface the REST API does — and since 1.13.0 it reads that surface
[decoded](/docs/api/#decoded-values), so you see
`{"symbol":"tw_fund"}` where the wire carries base64.

## What it gives you

- **A searchable contract list** — loaded once, searched in the page by
  contract-id substring or wasm-hash prefix, with code and type facets
  and sortable columns. Enter on a full id opens it.
- **A page per contract** — classification, declared events with
  per-name counts, per-kind coverage with its honesty notes, and
  prev/next through the filtered list.
- **A decoded data explorer** — a tab per kind the contract derives.
  Values render as readable JSON; state changes show their **diff by
  path** (`.storage[Escrow].flags.released false → true`); movement
  amounts are scaled only when the server
  [proved the token](/docs/api/#movements), and say so when it did not.
  Every row keeps an `xdr` disclosure with the base64, the JSON and the
  `stellar xdr decode` line that reproduces it.
- **Deep links everywhere** — the list with its search, a contract, a
  tab, a filtered query, the history of one state key: each is a URL
  fragment that survives a reload and can be shared.
- **Live instance status** — cursor position, tip lag, the archive leg
  verdict, gaps pending heal and deferred gaps with the ledgers never
  read.
- **Registration and unregistration** — behind an admin-token field that
  the page holds **only in memory**. Nothing is written to local storage;
  reload and the token is gone.

## Honesty in the rendering

The viewer inherits the API's honesty rules rather than papering over
them. Rows older than a kind's vouched range are marked and counted,
never hidden. A value the server could not decode says so and shows its
base64 intact. The per-token "Sum all movements" appears only once every
page is loaded, and is labelled **not a balance** — because a running
total of movements is not one. Chain strings are rendered as the bytes
they are: control and format characters appear as visible escapes, and
right-to-left text is isolated so it cannot reorder the values beside
it.

## Access

Reads work without credentials, matching the open-reads access model of
the API. Only mutations ask for the admin token.

If the instance faces a public domain, set `HTTP_BASIC_AUTH` — the browser
prompts natively and the UI inherits those credentials with no
configuration of its own. The page is served with
`Referrer-Policy: no-referrer`, so links out to Stellar Expert never
carry your instance's address. See
[the access model](/docs/api/#access-model).

## Why it is built this way

The appliance rule applies to its own interface: if you had to build,
host or configure the UI separately, it would stop being an appliance.
Baking one static page into the binary keeps deployment a single
container, and keeps the UI incapable of drifting out of sync with the
API version it ships with.
