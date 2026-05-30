# ◊ fallvault · sovereign emergency backup

**One HTML file. AES-256-GCM in your browser. Your passphrase never leaves your machine.**

Stop paying SaaS rent on a file viewer.

[**Live landing**](https://sjgant80-hub.github.io/fallvault/) · [Source](./fallvault.mjs) · MIT · Part of [AI Native Solutions](https://www.ai-nativesolutions.com) · ◊·κ=1

---

## The pitch

You back up your laptop. It dies. Cloud-X reads every file you ever wrote, charges you $10/month forever, and you hope they're trustworthy.

fallvault replaces all of that with a single HTML file.

```
Dropbox 2TB     $12/mo  · reads your files
iCloud 2TB      $10/mo  · reads your files
Backblaze       $9/mo   · reads your files
─────────────────────────────────────────────
fallvault       $0      · WebCrypto AES-256 · only you decrypt · MIT
```

---

## What it does

1. **Walks** any folder on your machine.
2. **Skips** the noise — git repos (already on GitHub), node_modules, AppData, media, binaries.
3. **Encrypts** the remaining text/configs/source files with AES-256-GCM. Key derived via PBKDF2-SHA256 (200k iterations). Random salt + IV per chunk.
4. **Wraps** the result in a self-contained HTML file. Browseable tree. WebCrypto decryptor baked in.
5. **Chunks** at 90MB for GitHub's per-file limit.
6. **Restores** anywhere — open the HTML in any browser, type your passphrase, click "restore" on any file.

---

## Install · 60 seconds

```bash
gh repo clone sjgant80-hub/fallvault
cd fallvault
```

### PowerShell

```powershell
$env:FALLVAULT_PASS = "your-strong-passphrase-write-it-down"
node fallvault.mjs C:\Users\you\Downloads
```

### Bash

```bash
export FALLVAULT_PASS="your-strong-passphrase-write-it-down"
node fallvault.mjs ~/Documents
```

Output lands in `./fallvault-out/vault-NNN.html`.

---

## Push to a private GitHub repo (the sovereign storage)

```bash
cd fallvault-out
gh repo create your-user/fallvault-private --private --source=. --push
```

Now your encrypted backup lives on GitHub's servers but **only your passphrase opens it**. GitHub holds the bytes. You hold the key.

---

## Restore on any machine

1. `gh repo clone your-user/fallvault-private`
2. Open `vault-001.html` in any modern browser (Chrome 113+, Firefox 113+, Safari 16+)
3. Enter your passphrase
4. Browse the tree
5. Click "restore" on any file → it downloads back

---

## What gets backed up · what doesn't

**Backed up by default:**
- Plain source · `.js .ts .py .ps1 .sh .rs .go .java .cs`
- Configs · `.json .yaml .toml .env.example .ini`
- Notes · `.md .txt .org`
- Web · `.html .css .scss .svg` (small SVG only)
- Any text file under 5MB

**Skipped by default:**
- ⊘ Any folder containing `.git` (already on GitHub — re-clone)
- ⊘ `node_modules`, `AppData`, `Library`, `.cache`, `dist`, `build`
- ⊘ Images — `.png .jpg .gif .webp .heic .raw .psd`
- ⊘ Video / audio — `.mp4 .mov .mp3 .wav`
- ⊘ Binaries — `.zip .exe .dll .iso`
- ⊘ Files over 5MB

**Customize via env vars:**

| Var | Default | Use |
|---|---|---|
| `FALLVAULT_PASS` | (required) | Your passphrase |
| `FALLVAULT_OUT` | `./fallvault-out` | Output directory |
| `FALLVAULT_MAX_FILE` | `5242880` (5MB) | Skip files larger |
| `FALLVAULT_MAX_CHUNK` | `94371840` (90MB) | Chunk size before splitting |
| `FALLVAULT_SKIP` | (see source) | Comma list of skip patterns |
| `FALLVAULT_SKIP_GIT` | `1` | Set `0` to backup git repos too |

---

## Crypto details

- **KDF:** PBKDF2 with SHA-256 · 200,000 iterations (OWASP 2023 compliant)
- **Salt:** 16 bytes random, unique per chunk
- **IV:** 12 bytes random, unique per chunk
- **Cipher:** AES-256-GCM
- **Auth tag:** 16 bytes appended to ciphertext
- **Compression:** gzip (manifest JSON gzipped before encrypt)
- **Browser side:** WebCrypto SubtleCrypto (zero deps, audited by every browser vendor)

The encryption is symmetric — the same passphrase encrypts and decrypts. The Node script and the HTML decoder use identical KDF and cipher parameters.

---

## The sovereign pattern

> Code public · data private · key in your head.

**fallvault** (this repo, public, MIT) is the **tool**. Anyone can fork it, audit the encryption, run it on their own files, modify it freely.

**Your-username/fallvault-private** (your repo, private) holds your **encrypted vaults**. Only your passphrase decrypts them.

If GitHub closes your account: the local HTML still works from a USB stick.
If you forget your passphrase: the vault is unrecoverable. That's the trade. Pick a strong one. Write it on paper.

---

## Threat model

| Adversary | Can they decrypt? |
|---|---|
| GitHub staff | No · they hold ciphertext only |
| Anthropic / Claude | No · passphrase never sent over network |
| The CIA / NSA | Not within polynomial time · with a strong passphrase |
| Your future self with the passphrase written down | Yes ✓ |
| Your future self who forgot the passphrase | No · this is by design |

---

## Roadmap

- [x] v1.0 · single-file encrypted HTML · CLI · MIT
- [ ] v1.1 · in-browser zip download (restore-all in one click)
- [ ] v1.2 · scheduled diff backups (only changed files since last vault)
- [ ] v1.3 · Ed25519-signed manifest (Konomi pattern · prove authenticity)
- [ ] v2.0 · browser-native vault creation (drag folder into HTML, no CLI needed)

---

## License

MIT. Fork it, sell it, white-label it.

Built by [AI Native Solutions](https://www.ai-nativesolutions.com). Part of the sovereign estate of 60+ tools. Same philosophy: single HTML files, you own them outright, no SaaS rent.

◊·κ=1 · prime 379 · v20.1 · φ=1.618 · κ=0.618 · phi is home
