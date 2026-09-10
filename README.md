# pCloud Sync

An Obsidian plugin that mirrors the whole vault into a pCloud folder as **plain,
unencrypted files**, optimized for mobile too — they can be opened straight from
pCloud's own app or web interface, and cloned back down onto any other device (desktop
or mobile) with this same plugin.

**Requires a [pCloud](https://www.pcloud.com/) account.** This plugin's only network use
is talking directly to pCloud's own API to read and write your vault's files — no other
server is involved, and nothing is sent anywhere except to the pCloud account you log
into.

- One **sync button** (ribbon icon, spins while syncing) — no separate upload/download,
  both directions run in a single pass.
- Opening or leaving a note runs a quick, single-file sync **quietly in the
  background** — nothing visible happens by default. A full sync also runs
  automatically at startup and every 10 minutes, just as quietly.
- Up to **3 files** transfer at once on desktop (1 on mobile, and large files always
  get a lane of their own) — small notes still sync at full speed either way.
- Downloads always arrive in small pieces rather than all at once, and uploads or notes
  over 25 MB are skipped on mobile with a clear message rather than risking a crash —
  sync those from desktop instead.
- If a Markdown note was edited on both sides since the last sync, the plugin
  **merges it paragraph by paragraph (line by line)** — a three-way (base/local/remote)
  merge algorithm. No separate "conflict" file is ever created: the losing (older)
  paragraph stays right in the same note, struck through and flagged with a warning
  line; the winner is left untouched and unmarked.
- Binary files (images, attachments) have no meaningful way to merge their bytes —
  both versions survive there: the newer one at the original path, the older one next
  to it as a timestamped copy.
- Logging in uses pCloud's own OAuth sign-in page (opened in your browser) — the plugin
  never sees your password, only the access token pCloud hands back afterward. The pCloud
  app behind that sign-in page is registered once by this plugin's maintainer; installing
  or using the plugin never requires registering your own app.

## Installing

- **Community Plugins** (once listed): Settings → Community plugins → Browse → search
  for "pCloud Sync".
- **[BRAT](https://github.com/TfTHacker/obsidian42-brat)**: add this repository
  (`laszlorepassy/pcloud-sync`) in BRAT's "Add a beta plugin" dialog.
- **Manual**: download `main.js`, `manifest.json`, and `styles.css` from the
  [latest release](https://github.com/laszlorepassy/pcloud-sync/releases/latest) into
  `<vault>/.obsidian/plugins/pcloud-vault-clone/`, then enable it under Obsidian
  Settings → Community plugins.

## Good to know

- `.obsidian/plugins/pcloud-vault-clone/` — where this plugin keeps its own settings,
  local cache, and the last-agreed text snapshot Markdown merging needs — is always
  excluded from the sync.
- Also excluded by default: `.obsidian/workspace.json`, `.obsidian/workspace-mobile.json`,
  `.trash/**`, `.git/**` — these are per-device state, not content. The list can be
  extended or narrowed in the settings.
- A `.vault-clone-manifest.json` file is created at the root of the remote folder to
  track what's out there and its hashes — don't edit it by hand.
- A deletion never beats an edit: if you deleted a file on one side while it was
  changed on the other in the meantime, the edit wins and the file comes back where
  it was deleted.
- A large vault's first full sync is naturally slower (every file has to be read and
  hashed once) — after that, only files that actually changed get transferred.

## Source code

This repository hosts the plugin's public releases. The source code is developed in a
private repository and is available to the Obsidian team for review through the
Community Directory's private-source-repository verification.
