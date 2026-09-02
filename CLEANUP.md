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
- `okf-wiki/scripts/validate.py` and its example copy, whose detector regexes
  contain private-key sentinel text even though the reviewed prompt-only OKF
  entry does not execute either helper;
- `security-toolkit/skills/secure-auth/references/upstream-secure-auth-details.md`,
  whose exact upstream audit snapshot contains a credential-shaped historical
  example and is recoverable from parent commit `53aa6e4f323e12a5544d3e025f35545a70951fec`;
- visual-explainer command wrappers, which are Claude plugin UI wrappers rather
  than skill resources.
- the PDF design template's embedded bitmap payload; the source HTML SHA-256 was
  `c93292f1ffee60144590c0736c371b4219b5025842f48abeb208c32f54974c05`,
  and the prompt now asks for a user/project-supplied reviewed template.

Adapted after the first production screen:

- blocked local-file, loopback, script-URL, and embedded-data URL literals were
  changed to explicit placeholders, relative assets, or platform-provided URLs;
- one prose field that looked like a literal credential assignment was changed
  to an unambiguous parameter description;
- Open Knowledge Format instructions were made prompt-only and no longer claim
  unavailable client hooks, browser-session automation, or helper execution;
- `secure-auth` was reduced below the 50,000-character prompt ceiling; the exact
  upstream body is recorded by checksum and Git history but omitted from this
  source tree because full-archive scanning includes unselected files;
- the two selected visual-explainer HTML templates were copied byte-for-byte to
  `.html.txt` resource aliases, the supported non-executable text form used by
  the catalog resource index;
- unsafe executable/vendor resources remain outside the import selection.

Every content change is itemized in [`ADAPTATIONS.md`](ADAPTATIONS.md).

Verification for this companion:

- exactly 63 `SKILL.md` files;
- 50 `SKILL.md` files match the reviewed upstream commit byte for byte and 13
  preserve their identity with documented prompt-only adaptations;
- the removed secure-auth audit snapshot is pinned in parent commit
  `53aa6e4f323e12a5544d3e025f35545a70951fec` at SHA-256
  `2c61e8a4e32ab9c2e04fdde5f0adcaa9c9644c7770d83561cc84c1a5fc9b9966`;
- both selected `.html.txt` aliases are byte-identical to their reviewed HTML
  source templates, as recorded in [`IMPORT_CLOSURE.tsv`](IMPORT_CLOSURE.tsv);
- no symlinks;
- gitleaks v8.21.2 `detect --no-git --redact`: zero findings.
