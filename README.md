# AnyReplay — Google Tag Manager template

A Community Template Gallery tag that loads the AnyReplay recorder. It is the
script tag from the dashboard's setup page, rebuilt in GTM's sandboxed
JavaScript so a site owner fills in fields instead of pasting code:

| Field | Becomes |
| --- | --- |
| Project key | `projectKey` (validated: `ar_pk_live_` / `ar_pk_test_` + 24 hex) and `?k=` on the loader URL |
| Mask everything typed into form fields | `maskAllInputs: true` |
| Also mask rich-text editors | `maskAllTyping: true` |
| Record only after the visitor grants consent | `requireConsent: true`, then `anyreplay('consent', true/false)` from Consent Mode (`isConsentGranted` + `addConsentListener`) |
| Sample rate | `sampleRate` (0–1) |
| Monthly session cap | `maxSessionsPerMonth` (1–1,000,000) |
| Ingest URL | `ingestUrl` (default `https://in.anyreplay.com`) |
| Debug | `debug: true` |

What it does on the page: `createArgumentsQueue('anyreplay', 'anyreplay.q')`
defines the same queueing stub the snippet does (skipped if `window.anyreplay`
is already a function), `callInWindow('anyreplay', 'init', options)` queues
`init`, and `injectScript` loads `https://cdn.anyreplay.com/v1/ar.min.js?k=<key>`.
The recorder replays the queue when it arrives (`packages/recorder/src/cdn.ts`).

Permissions it asks for: read/write/execute `window.anyreplay`, read/write
`window.anyreplay.q`, inject scripts from `https://cdn.anyreplay.com/v1/*`,
read consent state for `analytics_storage`, `functionality_storage` and
`personalization_storage`, and console logging in debug mode only.

A self-hosted install that serves the recorder from its own CDN has to edit
the template's "Injects scripts" permission and `CDN_URL`; the gallery
version only loads from `cdn.anyreplay.com`.

## Files

- `template.tpl` — the template, including its `___TESTS___` (run them in
  GTM: Templates → AnyReplay → Tests → Run tests).
- `metadata.yaml` — what the gallery reads: homepage, documentation and the
  list of versions, each pinned to a commit sha in the public repo.
- `LICENSE` — Apache 2.0, which the gallery requires.

## Submitting to the gallery

The gallery does not take uploads. It reads a public GitHub repository that
has `template.tpl`, `metadata.yaml` and `LICENSE` at its root.

1. Create the public repo `baemtech/anyreplay-gtm-template` and copy these
   three files (and this README, optional) to its root. Nothing else from the
   monorepo goes there.
2. Test the template in a real container first: GTM → Templates → Tag
   Templates → New → ⋮ → Import → `template.tpl`. Run the Tests tab, then add a
   tag with a test project key, open Preview on a site and check that
   `ar.min.js` loads and the setup page in AnyReplay turns **Connected**. Also
   check a Consent Mode setup with consent denied, then granted.
3. Add a brand icon in the template editor (Info → Icon, 48×48 PNG, under
   50 KB) and export again — the editor writes it into `___INFO___` as
   `brand.thumbnail`. Replace `template.tpl` in the repo with the export.
4. Commit and push. Put that commit's sha in `metadata.yaml` under
   `versions[0].sha`, commit and push again.
5. Submit the repository URL with the submission form linked from Google's
   Community Template Gallery docs
   (https://developers.google.com/tag-platform/tag-manager/templates/gallery). Google reviews it; a template shows up in the gallery
   once accepted, usually within a few days to two weeks.
6. Later versions: change `template.tpl`, push, then add a new entry at the
   **top** of `versions` with the new sha and change notes. The gallery picks
   it up on its own; containers using the template are offered the update.

After the gallery lists it, flip `gtm-template` to `available` in
`packages/shared/src/install-targets.ts` (see `integrations/README.md`).
