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
- **An interrupted sync picks up where it stopped.** Progress is written back to disk as
  the sync runs, not only once it finishes, and a large download that was cut short —
  a dropped connection, the app closed, the phone reclaiming memory — resumes from the
  byte it reached instead of starting the file over. On a phone with a big vault, that's
  the difference between getting there eventually and never finishing at all.
- Individual transfers retry a few times before giving up, and a pCloud download link
  that expired or stopped working after a network change is quietly replaced with a
  fresh one mid-transfer.
- If a Markdown note was edited on both sides since the last sync, the plugin
  **merges it paragraph by paragraph (line by line)** — a three-way (base/local/remote)
  merge algorithm. No separate "conflict" file is ever created: the losing (older)
  paragraph stays right in the same note, struck through and flagged with a warning
  line; the winner is left untouched and unmarked.
- Binary files (images, attachments) have no meaningful way to merge their bytes —
  both versions survive there: the newer one at the original path, the older one next
  to it as a timestamped copy.
- Obsidian's own settings JSON (`app.json`, `community-plugins.json`, `hotkeys.json`
  and the rest of the config folder) is the exception to that: it is merged **key by
  key** against the last-agreed version, so a plugin enabled on the phone and a font
  changed on the laptop both survive, and no conflict copy is left behind — a copy of
  `app.json` is litter nothing ever reads. Only a setting each side moved in a
  different direction needs a winner (the newer file), and those keys are named in the
  sync report.
- A sync that would delete most of the vault on one side stops and asks first — and
  offers to **copy the surviving side back instead** of deleting, which is what an
  unexpectedly empty remote folder (a switched pCloud account, a re-created folder, a
  reset manifest) almost always calls for.
- On mobile, signing in leads with pCloud's copy-a-code method rather than the
  browser hand-off — the `obsidian://` link that hand-off relies on often doesn't make
  it back to the app on a phone. Desktop still uses the hand-off, where it is reliable.
- The settings tab shows how full the connected pCloud account is. A full account
  refuses every upload with an error that says nothing about space, so it's worth
  seeing coming.
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
  track what's out there and its hashes — don't edit it by hand. Once per device the
  plugin also checks the folder itself against that index: a file the index lists but
  pCloud doesn't actually hold is uploaded again from this device's copy, rather than
  quietly staying unreachable from every device.
- A deletion never beats an edit: if you deleted a file on one side while it was
  changed on the other in the meantime, the edit wins and the file comes back where
  it was deleted.
- A large vault's first full sync is naturally slower (every file has to be read and
  hashed once) — after that, only files that actually changed get transferred. On a
  phone it may well take several syncs to get through that first one; each picks up
  where the last left off, so every attempt makes real progress even if it's cut short.
- Files past the mobile size limit are listed separately from failures, as "too large
  for this device" — they aren't broken, they just need a desktop.
- A sync skips opening a file whose modification time and size haven't moved since it
  was last read — that's what keeps a large vault fast. **Re-check every file** (in the
  settings, or the command of the same name) throws that away and reads the whole vault
  again. It's the way to settle any doubt that a device's record of a file has drifted
  out of step with the file itself, since an ordinary sync would never open it again to
  find out. Nothing is deleted; it simply takes considerably longer than a normal sync.

## Source code

This repository hosts the plugin's public releases. The source code is developed in a
private repository and is available to the Obsidian team for review through the
Community Directory's private-source-repository verification.
