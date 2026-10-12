---
title: Sierpe 1.13.0 — the stored history becomes readable
date: 2026-10-12T22:15:00Z
summary: xdrFormat=json serves every ScVal decoded beside its base64, and the embedded UI grows into a contract viewer that reads it — searchable list, a page per contract, state diffs, deep links.
---

Verify one indexed event by eye and base64 is a minor nuisance; verify
thirteen thousand and it is the whole job. Every value Sierpe serves —
topics, state keys, movement amounts — has always been exact and always
been opaque: `AAAADwAAAAd0d19mdW5kAA==` where a human needed
`{"symbol":"tw_fund"}`. 1.13.0 makes the stored history readable twice
over: once in the API, once in the page the binary already ships.

<!--more-->

**The API speaks RPC's JSON (1.13.0).** `xdrFormat=json` — Stellar RPC's
own parameter, with its values and its exact rendering — now works on
events, state, state history and movements. Every base64 ScVal gets a
decoded sibling (`topicJson`, `valueJson`, `keyJson`, `preJson`,
`postJson`), and the base64 stays, so a JSON page is a strict superset
of the default one. RPC produces that rendering through CGO and the Rust
stellar-xdr, which a static Go binary cannot link, so Sierpe re-renders
it in pure Go — pinned by golden vectors whose expected side comes from
the stellar CLI and RPC, never from the encoder itself, and checked
against every stored row of a 1,285-contract mainnet instance: 100,337
values, byte-identical. A value that cannot render arrives as `null`
with its base64 intact and a metric counting it; one bad row never fails
a page.

**Amounts get honest labels.** A movement now carries `asset` and
`decimals` — but only when the emitting token is proven to be the Stellar
Asset Contract for that asset under the instance's own network
passphrase. Any custom token can claim any name in its events, so an
unproven token gets neither field rather than a guess. On the pilot
instance that labels all 6,211 movements, which is what lets an amount
read as `1.0000000 USDC` without a single RPC call.

**The embedded UI becomes a contract viewer.** Still one self-contained
HTML page inside the binary, no build system, no external assets — but
now it reads the decoded API instead of showing you base64. The contract
list loads once and is searched in the page, with code and type facets
and sortable columns; each contract gets a full page with prev/next
through the filtered list; state changes show their diff by path
(`.storage[Escrow].flags.released false → true`); and every view is a
deep link that survives a reload. Every row keeps an `xdr` disclosure
with the base64, the JSON, and the `stellar xdr decode` line that
reproduces it — the decoded view is a convenience, never a substitute
for the bytes. Chain strings are rendered as the bytes they are:
invisible code points are drawn as visible escapes, right-to-left text
is isolated, and outbound links are built only from validated ids.

This cut also retires the old list view's growing pains — the detail
that rendered 37,600 px below a 1,285-row table now has a page of its
own — and moves the Go toolchain to 1.26.9 with the vulnerable indirect
dependencies bumped, so the unpinned vulnerability scan exits clean
again. Additive release, no schema migration: default responses are
byte-identical without the parameter, cursors never carry it, and
upgrading is bumping the image tag.
