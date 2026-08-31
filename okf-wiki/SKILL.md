---
name: okf-wiki
description: Builds an Open Knowledge Format (OKF) knowledge base from existing docs, notes, or a repo. Use to scaffold an OKF wiki.
license: MIT
metadata:
  author: jamditis
  version: "0.8.3"
  okf_spec: v1
---

# okf-wiki: scaffold an Open Knowledge Format knowledge base

OKF (Open Knowledge Format) stores knowledge as small markdown files: one concept per
file, each carrying its own provenance in YAML frontmatter, with directory `index.md`
files for navigation and a validator that enforces the contract. It is built for
knowledge bases that both people and agents read and edit, newsroom institutional
memory, a research atlas, a team's decision log, an infrastructure map.

This skill scaffolds a conforming OKF project and validates it. The format contract is
in `spec/SPEC.md` (in this skill's directory), read it before changing structure.

## When to use

- The user wants to start an OKF knowledge base, atlas, or wiki.
- They want docs structured as one-concept-per-file with provenance, not prose pages.
- They want to "initialize OKF" in a repo, optionally publishing into its GitHub wiki.

## Start here: scope the wiki with the user

Before you scaffold anything, settle four things with the user. They shape what gets created and
how it is published, and they are awkward to retrofit once concepts exist. Ask with `AskUserQuestion`
rather than in prose, in two steps: the first three questions in one call, then the publish question
as a follow-up call only if the audience came back public or both (it does not apply to an
internal-only wiki, and its relevant options depend on that answer, so it cannot share the first
batch). Infer the title from the repo or project and confirm it. Skip any question the user already
answered in their request, do not re-ask what they have told you.

1. **Audience**, who reads this wiki? This answer sets the others:
   - **Internal (agents and teammates):** the orientation hooks earn their keep, so keep them on.
     The bundle may hold infrastructure detail, so it usually lives in a private repo. The in-repo
     `bundle/` is the source of truth.
   - **Public (people browsing):** readability and secret-scrubbing come first; the hooks matter
     less, since people read it and agents do not. Plan a published view (see Publish below).
   - **Both:** the in-repo `bundle/` is the source of truth with hooks on for agents, plus a
     published view for people. Default here when the user is unsure.
2. **Title and sections**, the knowledge-base title (infer it, then confirm) and the starting
   sections. Offer sections as a use-case preset, not a blank prompt:
   - Newsroom institutional memory: `people, orgs, sources, decisions, beats`
   - Research atlas: `concepts, sources, methods, findings`
   - Infrastructure or fleet map: `machines, services, networks, credentials, processes`
   - Decision log: `decisions, context, events`
   The chosen title and list feed `--title` and `--sections` below; the user can edit the list.
3. **Populate now or later**, author concepts now from existing material (a repo, docs, notes, or a
   URL: gather it and enter the authoring loop after scaffolding), or scaffold an empty tree the user
   fills in later.
4. **Publish target**, a follow-up `AskUserQuestion` call, made only after the audience comes back
   public or both (skip it entirely for an internal-only wiki):
   - **In-repo bundle only (default):** the validator and relative links work directly, with no
     extra surface to maintain. Right for most wikis.
   - **GitHub wiki:** an optional reading surface. Advanced and manual; see "Optional: publish into
     a GitHub wiki" below.
   - **GitHub Pages:** a browsable site rendered from the bundle. Not built yet, treat it as a
     goal and keep the in-repo bundle as the source of truth.

Carry the answers into the file-creation plan and populate step. The audience answer is
also the visibility decision the "Before finishing" section asks you to make deliberately, you are
making it here, up front, where it can steer the rest of the setup.

## What gets created

Create this portable, prompt-only project shape:

```
<target>/
  SPEC.md                 the OKF format contract
  README.md               how to use and review the bundle
  VALIDATION.md           the manual conformance checklist
  bundle/                 the OKF bundle
    index.md              carries okf_version: "0.3" by default; "0.4" with --trust-signals
    <section>/
      index.md
      example-concept.md  a starter concept with full frontmatter
```

Docs and the review checklist sit at the project root. Keep them separate from
`bundle/`: every non-reserved Markdown file inside the bundle is a concept and
needs the required frontmatter.

## How to build it in chat

Use the onboarding answers to draft `SPEC.md`, `README.md`, `VALIDATION.md`, the
root `bundle/index.md`, and one section index at a time. Show the proposed title
and section list first, wait for confirmation, then emit complete file contents
or edit files only when the current environment provides that capability. Never
claim that files, hooks, validation scripts, or a remote wiki were created when
they were not.

## Populate the bundle: author concepts from existing material

Scaffolding leaves an empty tree with one placeholder concept. The usual next request,
"here are my docs / plans / notes / repo, build the wiki", has no importer script, and
can't have one: deciding what counts as a single concept, writing its one-line description,
choosing its `type`, and pointing `source` at real provenance is judgment work, not a
mechanical transform. So you (Claude) author the concepts directly, in this loop:

1. **Gather the source.** Read what the user pointed you at, a file, a folder, a repo, or a
   URL (fetch a URL first). Skim the whole thing before writing anything, so you can see the
   natural concept boundaries.
2. **Decide concept boundaries.** One file is one concept: one thing a reader would look up on
   its own (a service, a decision, a path, a person, an event). Split a doc that covers five
   things into five concepts; merge fragments that only mean something together into one. A
   heading is a hint, not a rule, do not blindly map one `##` to one file.
3. **Draft each concept** at `bundle/<section>/<slug>.md` with the full frontmatter. Read the
   bundle-root `index.md` before writing so the verification key matches its declared format:
   use `verified` for `okf_version` `0.1` through `0.3`; use `verified_on` for `okf_version` `0.4`.
   Emit that exact key with `type, title, description, source, timestamp, tags`:
   - `type` from the vocab. Infrastructure: Machine, Network, Service, Session, Project,
     Repo, Credential, Path, Process. Domain-neutral: Concept, Decision, Event, Person,
     Org, Source. Plus Reference (the catch-all). The set is closed; an unlisted type fails.
   - `description` is one line. `source`, quote every element, points at where the fact
     actually came from (the origin file path, URL, command, or event), not at this skill.
   - Set `timestamp` to today. `verified`/`verified_on` is the date the fact was last confirmed
     true, set it by how you came to know it, not reflexively to today:
     - You re-checked it against reality now, or the user is the authority for it (a decision,
       preference, or intent they state in this session): today.
     - The user is recalling external or system state (a spec, a path, a config): their memory is a
       source claim, not a re-check, so date it to when that state was last checked or to the
       recollection's own date, not today just because it came up now.
     - It was copied from a dated source without re-checking: the date it was last known true (the
       source's own date), not today.
     - It came from an undated record you cannot re-confirm (a memory file, an old conversation):
       the oldest date you can evidence, file timestamp, introducing commit, or the date it was
       said, never today. If you cannot evidence any date at all, it is not yet a verifiable fact;
       find a datable source or leave the concept out.
     When the date is uncertain, round it down: an older `verified` correctly reads as "may be
     stale, re-check," while today reads as "just confirmed." The frontmatter date is the contract;
     a caveat in the body does not undo an overstated value, because the validator and tools read
     only the date.
   - Strip secret values as you go: a credential concept names the key and its retrieval path,
     never the value. The validator fails the build on a leaked secret.
4. **Place and link.** Put each concept in the right section (create sections as needed), add a
   bullet for it to that section's `index.md`, and cross-link related concepts with relative
   `[text](path.md)` links, not `[[slug]]` wikilinks. `[[slug]]` is the auto-memory idiom; the
   OKF validator rejects it and never resolves it, so a typo'd or deleted reference passes silently.
   When you create a new section, also link it from the bundle-root `index.md`, that root is the
   navigation map the session anchor loads, so a section missing from it is invisible to orientation
   even though validation still passes.
5. **Clear the placeholder.** If you scaffolded fresh, delete the starter `example-concept.md` (and
   its bullet in the section `index.md`) once real concepts exist, otherwise the sample ships in
   the finished wiki and still passes validation.
6. **Review in a loop.** Apply every item in `VALIDATION.md`, fix the findings,
   and repeat until the checklist is clean. Unquoted `source` elements and
   missing frontmatter keys are common failures. Author and review in batches.

### When the source is already OKF

If the user points you at an existing OKF bundle (e.g. an upstream example: an `index.md`
carrying `okf_version` plus concept files with frontmatter), you are adopting it, not importing
it. Copy or clone the tree in, point the validator at the new root, and fix any links that broke
in the move. To keep it as its own area beside other content, give it a uniquely named top
directory, then create one combined-root `index.md` that carries `okf_version` and strip the
frontmatter from each adopted bundle's own root `index.md`, turning it into a normal section index
(the validator allows `okf_version` on the one combined root only; a nested `index.md` that still
carries it fails validation). Write cross-links as relative paths and validate the combined root.
Re-authoring an already-conforming bundle into your own concepts is wasted work; only reshape it if
that is the actual goal.

## The format, briefly

Full contract in `spec/SPEC.md`. This spec is a strict fork of Google's upstream OKF: it
requires all seven frontmatter keys, uses a `source` list in place of upstream's `resource`
and `# Citations`, adds a verification-date key, closes the type vocab, and enforces link
resolution. `spec/SPEC.md` ("Relationship to upstream OKF") lists every difference. The
load-bearing rules:

- **Required frontmatter** on every concept: `type, title, description, source`, the
  version-specific verification key described above, `timestamp, tags`. `type` is one of:
  Machine, Network, Service, Session, Project, Repo, Credential, Path, Process
  (infrastructure); Concept, Decision, Event, Person, Org, Source (domain-neutral); or
  Reference (catch-all).
- **Quote every `source` element**, source pointers carry `#` and `: ` which break YAML
  if unquoted. `source: ["README.md", "issue #445"]`.
- **`verified`/`verified_on`** is the date the fact was last confirmed true, a re-check
  against reality, or the user stating a fact they are the authority for (a decision, a
  preference); a fact they merely recall about external state is a source claim, not a
  re-check. **`timestamp`** is when the concept was authored/updated. The verification date is
  ISO `YYYY-MM-DD`; `timestamp` may also be a full ISO 8601 datetime in `0.3` and `0.4`. See
  the authoring loop above for the full date rules.
- **No secret values, ever.** A credential concept documents the key name and retrieval
  path, never the value. The validator fails the build on a leaked secret.
- **`index.md` and `log.md` are reserved**, no frontmatter (except the bundle-root
  `index.md`, which carries `okf_version` only).

### Optional: upstream v0.2 trust/provenance signals

Upstream Google OKF v0.2 (July 2026) added an optional vocabulary for a consumer to judge a
concept before reading it: `generated` (who/what produced it), `verified` (a list of
independent confirmations, not this fork's own single-date field), `sources` (structured,
per-pointer credibility signals), `status` (draft/stable/deprecated), `stale_after` (an
absolute expiry date), and an `Attested Computation` type for a sanctioned, checkable
computation. None of it is required, and a bundle that adopts none of it is unaffected.

Scaffold a project with these enabled, `scaffold.py <target> --trust-signals`, and the
bundle declares `okf_version: "0.4"`, with `verified` renamed to `verified_on` in the
required set (freeing `verified` for the new shape; see `spec/SPEC.md`'s "Trust and
provenance" section for the full field contract and the reasoning behind the rename).
`Attested Computation` is likewise a `0.4`-only type. Without the flag, scaffolding is
unchanged from before this vocabulary existed.

## Session hooks

A scaffolded project ships a `.claude/` with two hooks so any Claude session opened in it
starts from the bundle, not from memory:

- **`okf-anchor.py`** (SessionStart) prints the bundle's root index into the session context.
- **`okf-orient.py`** (PreToolUse, no matcher) blocks the first action of the session once,
  until Claude confirms it read the index, then unblocks for the rest of the session. It is
  inert outside an OKF bundle and fails open on any error, so it never wedges a session.

Both are one cross-platform python3 script. The scripts are identical on every OS; only the
interpreter in `.claude/settings.json` changes: `python3` on macOS/Linux, `python` on
Windows. `scaffold.py` auto-detects the OS; `--hooks-os posix|windows` forces it.

Claude Code treats a checked-in `.claude/settings.json` as untrusted, so the first time the
project is opened it asks the user to approve the hooks; they run automatically after that.
To turn them off, scaffold with `--no-hooks`, or delete `.claude/` (or set `disableAllHooks`)
in an existing project.

### Client boundary

The portable OKF surface is `SPEC.md`, `README.md`, `VALIDATION.md`, and the
`bundle/` tree. Client-specific hooks and lifecycle integrations are outside
this companion. Do not claim they run in Arinova or any other chat client.

## Optional: publish into a GitHub wiki

OKF lives best as in-repo files (the validator and relative links work directly). A repo's
GitHub wiki is an optional reading surface, and wiring it up is an advanced, manual step,
most users should skip it and keep the bundle in-repo.

A wiki with zero pages has no Git repository to push to, so an authorized user
must create the first page through GitHub's web UI. After that, use the normal
reviewed Git workflow only when the user explicitly authorizes publication. Do
not collect browser-session state or automate the logged-in UI from this skill.

Note the impedance: GitHub wikis are flatter than an OKF tree and use `[[WikiLinks]]`, so
OKF's nested directories and relative links need adapting for the wiki surface. Treat the
wiki as a published view, not the source of truth. (v0.1 ships the bootstrap step; an
automatic bundle-to-wiki sync is not built yet.)

## Before finishing

- Run the validator and confirm it exits 0.
- Confirm the visibility you set during onboarding still fits what got authored: a bundle that
  ended up documenting real infrastructure is usually internal. OKF takes no position; you must.
