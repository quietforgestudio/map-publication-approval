# mailschema 0.3.2: notes from a first run

- **Install and commands.** `node -v` → `v24.14.0`, `npm -v` → `11.9.0`.
  `npm install mailschema@0.3.2` in an empty directory holding only a minimal
  `package.json`: `added 7 packages`, `found 0 vulnerabilities`. Then
  `npx mailschema@0.3.2 contract publication-approval-0.1.json` printed
  `sha-256:de91a49de4cba91209be253456e7c56709791fff877a060560d4eada0cc9468c`,
  equal to the `digest` the Registry already carries for that type. Then
  `npx mailschema@0.3.2 description description.json --contract publication-approval-0.1.json`
  printed `Valid MAP 0.3 description …/pub-2026-10-20-r1` and
  `sha-256:514768e856a13823500be8cc91043c36f6f6cb3e3c277372da2a6a4a30acf4ec`.
- **Time.** Install finished 05:03:38Z. The description validated on its first
  attempt; my next timestamped command was 05:07:01Z, so **under 3.4 minutes** —
  an upper bound, not a stopwatch reading.
- **The field I broke.** I flipped the last hex character of
  `type.contractDigest` (`…0cc9468c` → `…0cc9468d`). Exact output, on standard
  error, exit status 1: `/type/contractDigest: does not match`. Put back, it
  validates again.
- **Slowest step.** Composing the fifteen required top-level members. The
  package exports `mailschema/core.schema.json`, which lists them, and that was
  the quickest route to a first valid document.
- **Defect, with a repro.** The `description` command gates the file at the
  **contract** size limit. `bin/mailschema.js:32` declares
  `read(path, maxBytes = CONTRACT_MAX_BYTES …)` and line 57 calls `read(path)`
  for `description` with no limit, so 262144 applies where the core
  specification is normative at 65,536 octets. Padding `details.summary`:
  - 101,913 bytes → `The JSON exceeds 65536 bytes.` (correct; `parseDescription`
    catches it)
  - 301,913 bytes → `Expected a JSON file of at most 262144 bytes.`
  The second message names the wrong limit, and it is **byte-identical** to what
  `npx mailschema@0.3.2 contract d_300k.json` prints for the same file — so it
  cannot tell you which limit you broke. Both exit 1.
- **One change.** Pass `DESCRIPTION_MAX_BYTES` to `read()` for the `description`
  command, so its refusal quotes 65536.
