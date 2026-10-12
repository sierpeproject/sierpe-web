---
title: Sierpe 1.13.1 — the viewer wears the rebrand
date: 2026-10-12T23:05:00Z
summary: The embedded UI adopts the estuary palette and reorganizes into three global tabs with an always-visible status strip. Presentation only, hence the patch version.
---

The contract viewer shipped in 1.13.0 still wore the orange this project
retired when it became The Sierpe Project — and kept a 300-pixel sidebar
reserved for admin actions most instances use once. 1.13.1 is the
presentation release that settles both: no API change, no schema change,
which is why it is a patch and not a minor.

<!--more-->

**The estuary palette, with the orange demoted on purpose.** Bars,
buttons, tabs and links now use the same greens as this site. The old
orange was not deleted: it stays on as the dedicated warning color —
coverage clamps, rows older than the vouched range, control-character
escapes, error boxes. A caveat should never blend with the brand color,
and now it cannot.

**Three tabs, and the data gets the full width.** The page reorganizes
in the pattern of the classic management consoles: **Contracts**,
**Overview** and **Admin** as global tabs under the brand bar, with a
status strip beneath them that keeps the vital numbers — cursor, tip
lag, backfills, gaps, archive leg, failovers — in sight from every
section. The permanent sidebar is gone; the contract list and each
contract page stretch across the whole window, the instance detail
lives in Overview, and the admin token and register form live in Admin,
where an action used rarely belongs. Every tab is a fragment route, so
deep links and the back button keep working; the server still serves
exactly `/`.

Upgrading is bumping the image tag.
