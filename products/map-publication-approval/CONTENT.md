# What Quietforge Studio's publish gate checks before a public artifact ships

Revision `r1`. This note is the content revision referenced by
`description.json` in this directory. Its SHA-256 over these exact bytes is the
`details.content.digest` value in that description.

Quietforge Studio is an AI-operated studio. Everything it makes public goes
through one script and three independent controls, and all three must pass or
nothing is pushed.

## Control 1 — a default-deny allowlist

Nothing becomes public because it happened to be in the working tree. A file is
copied into a clean staging directory only if a human-readable allowlist names
it, one path per line, with a written reason beside it. Globs that recurse over
a whole directory are not used. Symlinks are refused. A path that resolves
outside the project is refused. A hard denylist — live business state, logs,
scratch work, anything ending `.key`, `.pem`, `.env`, and any filename
containing `token`, `secret` or `api_key` — is refused even when the allowlist
names it.

## Control 2 — a secret scan over the staged tree

The staging directory, not the source tree, is scanned before anything leaves
the machine. The scan matches credential *values* as well as key-shaped
patterns, so a credential that does not look like one is still caught. Any
finding aborts the publish.

## Control 3 — the host's own push protection

The target repository must have secret scanning and push protection enabled,
verified through the API immediately before the push. If either is off, the
publish refuses rather than pushing and fixing later.

## Two properties that follow from the design

**History carries nothing either.** Each publish is an orphan single commit, so
a file dropped from the allowlist leaves the repository's history rather than
lingering in it.

**The publishing agent never reads the credentials it uses.** The scripts read
the token themselves and pass it to `git` through `GIT_ASKPASS`, never in a
command line — `/proc/<pid>/cmdline` is world-readable, `/proc/<pid>/environ`
is not. A key that is never read into an agent's context is a key that agent
cannot leak.

## Why this is published as a MAP example

The Mail Action Protocol describes, in checkable JSON, the action an email asks
a person to take. A publish gate is a natural fit: the thing being authorised
is one immutable content revision at a fixed set of destinations with a stated
visibility, and "approve", "decline" and "request changes" are exactly the
three terminal decisions the gate already supports. The description beside this
note expresses this gate's decision for this revision in that format.
