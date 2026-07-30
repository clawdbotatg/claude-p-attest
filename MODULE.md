# attest — module attestations on EAS (Base) · EXPERIMENTAL

The module-trust layer, v0: third-party "I audited this exact
(repo, sha) and vouch" as onchain EAS attestations, with a **local** trust
list — a web of trust, not a global score. Read-first by design: this
version signs nothing and holds no keys.

## What it is

- `attest check REPO SHA` — queries the public EAS indexer
  (base.easscan.org) for attestations on that exact module version and
  marks which attesters are on your `trust.list`. No keys, no cost.
- `attest trust` — show the trust list (`trust.list`, one address/line).
- `attest schema` — the schema + the one-time human registration step and
  the easscan flow for publishing an attestation.
- `schema.uid` — committed here once the schema is registered, so every
  operator queries the same schema.

## What it needs

- Nothing for `check`/`trust` (stdlib + the public indexer).
- One-time human step before attestations exist at all: register the schema
  on base.easscan.org with a wallet (pennies of gas), commit the UID here.
- Your judgment for `trust.list`: only addresses whose audits you'd act on.

## Wiring

`tools/module add …`, then optionally seed `trust.list`. The brain's
`skills/module` already integrates both directions of the loop: the
install audit runs `attest check` on the pinned SHA (flagging zero trusted
attestations for money/credential modules), and the "Closing the loop"
section has the agent offer the human a pre-filled attest-back after a
module has proven itself in real use — including `safe: false` when one
misbehaved. Until the schema is registered, `check` says so and defers to
your own audit.

## What can go wrong

- **Attestations are signal, never proof.** Signed malware is an ancient
  tradition; the agent's own code audit is always the floor. `check` says
  this out loud in its output.
- The indexer is a centralized read path; if it's down, treat as zero
  attestations (the tool degrades that way explicitly).
- An empty/wrong trust.list makes every attester "unknown" — that's honest,
  not broken.
- v0 signs nothing on purpose: an unattended agent holding an attestation
  key is exactly the thing to design carefully, later (economic
  bonds/challenges are further still — deliberately deferred).

## How to uninstall

`tools/module remove attest`. Nothing onchain is affected (you never held
keys here).
