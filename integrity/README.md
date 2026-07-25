# Agent Almanac — integrity witness

This directory is an **append-only, externally-witnessed record** of every figure
[The Agent Almanac](https://agentalmanac.org) has published.

Every time our measurement pipeline runs it seals that run's headline numbers into a
hash chain. `seals.jsonl` publishes those seals here, in a repository hosted by a third
party, so that GitHub independently records **when** each one appeared.

## Why this exists

The live chain at <https://agentalmanac.org/integrity> proves that no number we
published was ever quietly revised: altering any historical payload breaks every link
after it. What a self-hosted chain *cannot* prove is when a link came into existence,
since in principle the whole chain could be regenerated at any time.

This repository is the external witness for that. It is **not** a blockchain and we do
not claim it is one. It is a public, third-party-hosted, independently-dated log, which
is a meaningful and checkable improvement over our word alone.

## Files

| file | what it is |
|---|---|
| `seals.jsonl` | append-only, one JSON object per seal, including the exact payload that was hashed |
| `HEAD.json` | the current chain head |

## Reading the sealed fields

A seal is a raw record, not a curated one. Two fields invite a conclusion our data does
not support, so we flag them here rather than let someone divide them and publish it:

- **`endpoints_reachable` / `endpoints_probeable`** — **not a liveness rate.**
  `endpoints_probeable` counts catalogued endpoints, not endpoints we tested. Only a few
  hundred services have ever been HTTP-probed, so the remainder are untested defaults
  rather than confirmed-dead services. Dividing one by the other yields a "share of the
  agent economy that is dead" figure we will not stand behind. Real deadness exists and
  is being measured properly; until that lands, the honest answer is that we don't know
  the rate.
- **`settled_volume_raw_usd`** — wash-**inclusive**. `settled_volume_validated_usd` is the
  wash-adjusted number. Roughly 43% of raw activity is suspect. See
  [methodology](https://agentalmanac.org/methodology).

## Verify it yourself

```bash
curl -O https://agentalmanac.org/static/verify_integrity.py
python3 verify_integrity.py                                  # verify the live chain
python3 verify_integrity.py --witness                        # AND cross-check it against this repo
```

The cross-check confirms that every seal witnessed here appears in the live chain with
identical hashes. If we ever regenerated the chain, the two would disagree and the
verifier would say so.

You can also check the dates yourself, without trusting us at all:

```bash
git log --format='%H %ad %s' --date=iso -- seals.jsonl
```

Those commit dates are recorded by GitHub, not by us.
