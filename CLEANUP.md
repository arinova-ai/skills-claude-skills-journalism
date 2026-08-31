# Extraction and cleanup record

The upstream archive is small enough for transport, but a local gitleaks
v8.21.2 `--no-git` scan reported nine credential-shaped fixtures in repository
tests and non-skill guides. Because the production importer scans the complete
archive, the direct-repository route was rejected and this skills-only
companion was used instead.

Included:

- all 63 upstream `SKILL.md` files;
- adjacent references, templates, examples, and helper files that belong to
  those skill directories;
- the upstream MIT license and applicable third-party notices/licenses.

Excluded:

- upstream `.github`, site, plans, research, package manifests, catalogs,
  marketplace metadata, hooks, and other repository control-plane files;
- top-level `scripts/`, including credential-detector test fixtures;
- per-skill `agents/`, `.claude-plugin/`, and test-only directories;
- `okf-wiki/tests/test_okf_wiki.py`, which contained a credential-shaped test
  fixture, and other test-only metadata not needed by the skill;
- visual-explainer command wrappers, which are Claude plugin UI wrappers rather
  than skill resources.

Verification for this companion:

- exactly 63 `SKILL.md` files;
- every selected `SKILL.md` matches the reviewed upstream commit byte for byte;
- no symlinks;
- gitleaks v8.21.2 `detect --no-git --redact`: zero findings.

