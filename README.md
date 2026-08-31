# Claude Skills Journalism — skills-only companion

This public companion redistributes the 63 skill directories selected from
[`jamditis/claude-skills-journalism`](https://github.com/jamditis/claude-skills-journalism)
for Arinova's skill-catalog review funnel. It intentionally omits the upstream
site, repository control plane, CI, evals, and unrelated top-level scripts.

- Upstream author: Joe Amditis (`jamditis`)
- Reviewed upstream commit: `86715e40aef0fe8608b3aaee20a86929b748ad3d`
- Upstream license: MIT; the complete text is preserved in [`LICENSE`](LICENSE)
- Additional attribution: [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md)
  and [`THIRD_PARTY_LICENSES/superjawn-LICENSE`](THIRD_PARTY_LICENSES/superjawn-LICENSE)
- Language: primarily English, with Portuguese templates in the Brazil public
  records skill

All 63 upstream skill identities remain present. Fifty `SKILL.md` files are
byte-for-byte copies; thirteen have narrowly scoped, reviewable adaptations for
the catalog's prompt-only runtime and deterministic security policy. The full
upstream `secure-auth` text is also preserved byte-for-byte as an audit
reference, deliberately outside the import artifact.
See [`ADAPTATIONS.md`](ADAPTATIONS.md) for every changed path and
[`CLEANUP.md`](CLEANUP.md) for the extraction boundary and scan evidence.

This repository is a redistribution and review input, not an endorsement by
the upstream author and not a claim that every upstream client integration is
available in Arinova.
