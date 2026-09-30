---
title: Sierpe 1.11.0 — events hand over their original bytes
date: 2026-09-30T19:45:00Z
summary: include=rawXdr on the events endpoint serves the stored ContractEvent beside every decoded row, unblocking consumers that re-emit indexed history into their own pipelines.
---

The first external consumer to build on a Sierpe instance was not a
dashboard — it was another pipeline: an escrow platform bridging a year
of indexed mainnet history into its own event queue. A bridge like that
does not want our decode of an event; it wants the event, the original
`ContractEvent` bytes, to carry through its own envelope untouched.

<!--more-->

**The bytes were always there; the API kept them to itself (1.11.0).**
Sierpe has stored the full raw event beside every decoded column since
M1, precisely so any derived form can be rebuilt offline. But the events
endpoint never served them, and the product doctrine that consumers read
the API and never the tables turned that omission into a blocker.
Movements set the precedent back in 1.6.0 — their `rawXdr` exists
because the emitting token usually is not registered, making the stored
copy the only one there is. Events now follow: `include=rawXdr` on
`GET /v1/contracts/:id/events` adds the base64 `ContractEvent` to every
event on the page.

**Opt-in, to the byte.** Without the parameter the response does not
change at all — a guard test pins the default body to byte-for-byte
stability, so nothing built against 1.10 notices the release. The
parameter is presentation only: it is accepted beside a cursor, never
encoded in one, and the cursor minted with it is identical to the one
minted without, so a client can turn raw bytes on mid-pagination.
An unknown `include` value is a 400, never a silent shrug. The raw
envelope deliberately gets no decoded JSON sibling — stellar-xdr's
serde for envelopes has changed between versions, and Sierpe only
promises shapes it can pin.

Also in this release: the grpc dependency moves to 1.83.1, clearing
GO-2026-6348 — flagged by the unpinned vulnerability scan doing exactly
the job it was left unpinned to do. Additive release, no schema
migration: upgrading is bumping the image tag.
