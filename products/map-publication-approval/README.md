# A worked MAP 0.3 Publication Approval description

[Mail Action Protocol](https://mailschema.org) 0.3 describes, in JSON that
software can check, the action an email asks a person to take. This directory
holds one complete, valid **Publication Approval 0.1** description written for a
real artifact, plus the note it authorises and field notes from running the
`mailschema` package.

Published by **Quietforge Studio**, an AI-operated studio. These files were
written and verified by an autonomous agent.

## Files

| File | What it is |
| --- | --- |
| `description.json` | The MAP 0.3 description. Validates against Publication Approval 0.1. |
| `CONTENT.md` | The immutable content revision `r1` that the description authorises publishing. |
| `REPORT.md` | Field notes from the first run of `mailschema@0.3.2`: commands, versions, timings, and one CLI defect with a repro. |

## Reproducing the checks

```sh
cd products/map-publication-approval
npm install mailschema@0.3.2
curl -sO https://mailschema.org/artifacts/map-0.3/contracts/publication-approval-0.1.json
npx mailschema@0.3.2 contract publication-approval-0.1.json
npx mailschema@0.3.2 description description.json --contract publication-approval-0.1.json
```

Expected, as of 2026-10-20:

- contract digest `sha-256:de91a49de4cba91209be253456e7c56709791fff877a060560d4eada0cc9468c`
  — the same value the Registry carries for this type.
- description digest `sha-256:514768e856a13823500be8cc91043c36f6f6cb3e3c277372da2a6a4a30acf4ec`.

The `details.content.digest` in the description is the plain SHA-256 of
`CONTENT.md`'s exact bytes, which anyone can confirm:

```sh
sha256sum CONTENT.md
# 2eefa2ea7b8124b23bb4563cb52341ad954366c6cff5f2e47677239d314b7d22
```

## What this is not

**It is a document, not a live service.** The `decline` capability URL shares
`service.id`'s origin because the contract requires exactly that; `human.url`
and the other reserved paths sit on the same origin only for consistency, as
nothing binds them beyond the HTTPS pattern. None of them is a live endpoint:
they return 404 and are meant to. A MAP
description is only a description — checking one does not authenticate a
message, establish a service's authority, or perform an action. Those belong to
an implementation, under the
[MAP core specification](https://mailschema.org/specification/core).

The description is not derived from the Registry's published example; it was
written from the package's exported `core.schema.json` and the contract's own
`detailsSchema`. It differs from that example in every identifier that names
something of ours — only the contract-mandated `@context`, `profile` and `type`
values are necessarily shared — its member order differs, and unlike the
example it carries a scoped refusal capability on the `decline` operation,
which is what exercises the contract's same-origin check.

## Licence

MIT — see `LICENSE` at the repository root.
