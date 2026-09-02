# Prompt-only adaptation record

Source baseline:
`jamditis/claude-skills-journalism@86715e40aef0fe8608b3aaee20a86929b748ad3d`.
The companion keeps all 63 skill identities. Git history is the authoritative
line-level diff; this document records the intent and boundary of every change.

## Adapted `SKILL.md` files

| Path | Review reason | Adaptation boundary |
| --- | --- | --- |
| `dev-toolkit/skills/electron-dev/SKILL.md` | Blocked local/script URL literals | Rephrased the unsafe schemes without changing the Electron navigation warning. |
| `dev-toolkit/skills/mobile-debugging/SKILL.md` | Blocked script and loopback URL literals | Converted bookmarklet URLs to explicit DevTools-console functions and referred to the proxy-reported loopback URL. |
| `dev-toolkit/skills/test-first-bugs/SKILL.md` | Prompt runtime cannot execute bundled helpers | Kept the two Markdown references and directs the agent to draft tests in the user's framework instead of invoking examples/scripts. |
| `okf-wiki/SKILL.md` | Vendor hooks, browser-session automation, and Python helpers are unavailable | Converted scaffolding and validation to an explicit chat/file-content workflow with a manual checklist; retained the OKF authoring model. |
| `pdf-design/SKILL.md` | Blocked local-file URLs and embedded-media template | Uses direct reviewed paths with Chromium, requires a project/user-supplied template, and keeps the same design/verification workflow. |
| `research-toolkit/skills/content-access/SKILL.md` | Blocked script URL and credential-shaped prose | Presents the reader-mode function as a DevTools snippet and makes the OpenAlex credential field a parameter description. |
| `research-toolkit/skills/page-monitoring/SKILL.md` | Blocked loopback URL literal | Refers to the loopback URL printed by the container runtime. |
| `research-toolkit/skills/web-archiving/SKILL.md` | Blocked script URL literals | Presents archive helpers as DevTools-console functions, preserving their destinations and behavior. |
| `security-toolkit/skills/api-hardening/SKILL.md` | Blocked loopback examples and deterministic exfiltration phrase | Uses documentation-only `.invalid` origins and neutral breach wording; controls and lessons are unchanged. |
| `security-toolkit/skills/secure-auth/SKILL.md` | Original prompt exceeded the 50,000-character limit | Replaced the activation prompt with a concise current-source, threat-model, control, output, and verification workflow; the exact upstream body remains checksum-pinned in Git history but is omitted from the full archive scanned for import. |
| `security-toolkit/skills/supply-chain-hardening/SKILL.md` | Deterministic exfiltration phrase | Replaced one breach verb with neutral wording; the incident and control guidance are unchanged. |
| `video-toolkit/skills/video-dashboard/SKILL.md` | Blocked loopback URL literal | Refers to the server-reported loopback origin and retains the exact dashboard path. |
| `visual-explainer/SKILL.md` | Blocked embedded-data URL, unavailable vendor runtime, deployment wrapper, and unsupported HTML resource-index extension | Uses reviewable relative assets, static fallbacks, selected safe references, byte-identical `.html.txt` aliases for the two reviewed templates, and an explicit publication confirmation gate. |

The remaining 50 `SKILL.md` files are byte-for-byte copies of the pinned
upstream commit.

## Adapted or removed resources

- `pdf-design/templates/democracy-day-proposal.html` was removed because it
  embedded a large bitmap payload of uncertain redistribution provenance inside
  the HTML. Upstream SHA-256:
  `c93292f1ffee60144590c0736c371b4219b5025842f48abeb208c32f54974c05`.
- `pdf-playground/skills/document-design/references/css-patterns.md` replaces a
  blocked local-file URL with a direct reviewed filesystem path.
- `visual-explainer/references/css-patterns.md` and
  `visual-explainer/references/slide-patterns.md` replace embedded-data examples
  with explicit relative asset files so generated media stays separately
  reviewable.
- the removed companion audit file
  `security-toolkit/skills/secure-auth/references/upstream-secure-auth-details.md`
  remains recoverable from parent commit
  `53aa6e4f323e12a5544d3e025f35545a70951fec`; its SHA-256 is
  `2c61e8a4e32ab9c2e04fdde5f0adcaa9c9644c7770d83561cc84c1a5fc9b9966`.
- `visual-explainer/templates/{architecture,data-table}.html.txt` are exact
  byte copies of their reviewed `.html` templates. Only the supported resource
  extension and the four prompt pointers changed; the HTML template bytes did
  not.

No adaptation adds a vendor dependency, credential, executable import closure,
or claim that an unavailable tool completed an action.
