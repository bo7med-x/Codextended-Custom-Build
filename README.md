# CoDExtended — Old Linux & Locked Host Compatibility

A custom **compiled & packaged build** of [CoDExtended](https://github.com/xtnded/codextended), built specifically for **older Linux server environments** and to bypass the locked/unchangeable startup commands found on certain free or budget hosting panels (such as **OptikLink**).

This build is based on the original, unmodified CoDExtended source, compiled against older toolchains/libraries (`debian_source`-style base images) and packaged as `iw1x.so` so it drops in as a direct replacement for hosts that hardcode `LD_PRELOAD="./iw1x.so"` in their startup string.

The **only source change** made in this build is a single cosmetic edit to the `SERVERINFO` version string, so the server browser / `serverinfo` output shows who packaged the build:

```c
Cvar_Get("codextended", va("CoDExtended v%d (%s) Build By BO7MEDX", CURRENTBUILD, __DATE__), CVAR_SERVERINFO | CVAR_ROM | CVAR_NORESTART);
```

No gameplay logic, mod features, or functionality were changed — this is purely a build/version-label change, made transparently in line with GPLv3's requirement to mark modified files.

| | |
|---|---|
| **Compiled & Packaged by** | [BO7MEDX](https://github.com/bo7med-x) |
| **Original Project / Source Code** | [xtnded/codextended](https://github.com/xtnded/codextended) |
| **License** | [GNU General Public License v3.0 (GPLv3)](https://www.gnu.org/licenses/gpl-3.0.html) |
| **Target** | Call of Duty 1 Dedicated Server (`cod_lnxded`) — Linux |

> **This repository does not claim authorship of CoDExtended.** All credit for the mod itself goes to the original developers at [xtnded/codextended](https://github.com/xtnded/codextended). This repo only provides a pre-compiled binary for a specific compatibility use case, in compliance with the GPLv3 license.

---

## The Problem This Solves

Many free or budget CoD1 hosting providers (e.g. the **OptikLink** panel) lock the server's **startup command** and **Docker image**, and don't allow you to edit them. A typical locked startup string looks like this:

```bash
LD_LIBRARY_PATH="." LD_PRELOAD="./iw1x.so" ./cod_lnxded +exec ServerConf.cfg +set dedicated "2" +set ttycon 0 ...
```

The Docker image is often something like `quay.io/parkervcp/pterodactyl-images:debian_source`, which is an **older Linux base** — meaning a modern build of CoDExtended (normally named `codextended.so`) will either:

- **Not load at all**, because the startup command is hardcoded to preload `iw1x.so`, not `codextended.so` — and you have no permission to edit the startup line, **or**
- **Fail to run / crash on launch**, because the host's base system libraries are too old for a build compiled against a modern toolchain.

### This build fixes both issues:
1. It is **compiled for older Linux base images** (`debian_source`-style), so it runs without missing-symbol or `GLIBC` version errors on outdated hosts.
2. It is **packaged/renamed as `iw1x.so`**, matching the filename the locked startup command already expects — no panel/startup edits needed.

> Internally, the version string shown to players/server browsers (`serverinfo`) was edited to read `Build By BO7MEDX` (see [License & Attribution](#license--attribution) below) — this is the only source-level change in this build. The shared object is then renamed to `iw1x.so` on disk so it matches what locked startup commands expect.

---

## Download & Installation

> This repo distributes **compiled binaries only** (via [Releases](../../releases)). No source code is included or modified here — for the source, see the [original repository](https://github.com/xtnded/codextended).

### Step 1 — Download
Go to the **[Releases](../../releases)** page of this repo and download the latest release archive. It contains **two files** — both are required:

| File | Purpose |
|---|---|
| `iw1x.so` | The compiled CoDExtended build itself |
| `libmariadb.so.3` | MariaDB client library required by CoDExtended's SQL features — must sit next to `iw1x.so` |

### Step 2 — Upload
Upload **both files** to your CoD1 dedicated server's **main directory** — the same folder where `cod_lnxded` lives (your `fs_basepath`/`fs_homepath` root). On OptikLink this is usually the root shown in the **Files** tab.

### Step 3 — Verify your startup command
Make sure your startup command (Startup tab on OptikLink, or your launch script elsewhere) references `iw1x.so`, for example:

```bash
LD_LIBRARY_PATH="." LD_PRELOAD="./iw1x.so" ./cod_lnxded +exec ServerConf.cfg +set dedicated "2" +set ttycon 0 +set sv_maxclients "50" +set rconpassword "yourpassword" +set net_ip 0.0.0.0 +set net_port "5243" +set fs_basepath . +set fs_homepath . +map_rotate
```

If your panel already locks the startup command to `./iw1x.so` (like OptikLink does by default), you **don't need to change anything** — just drop the file in and restart the server.

### Step 4 — Restart the server
Restart your CoD1 server from your panel/console. If it loaded correctly, you'll see CoDExtended's normal startup messages in the console log.

---

## Tested Environment

- **Docker Image:** `quay.io/parkervcp/pterodactyl-images:debian_source`
- **Panel:** [OptikLink](https://control.optiklink.net/)
- **Game Server:** `cod_lnxded` (Call of Duty 1)

If you're running a different/older Linux distro and getting `GLIBC_2.XX not found` or similar errors with the official CoDExtended build, this build is likely the fix you need.

---

## License & Attribution

This project redistributes a compiled build of **[CoDExtended](https://github.com/xtnded/codextended)**, licensed under **GPLv3**. In line with the license:

- Full credit and all rights to the original mod belong to the **CoDExtended / xtnded** team.
- This build contains **one (1) modified line** in the source — the `SERVERINFO` version-string `Cvar_Get` call — changed solely to append `Build By BO7MEDX` to the version string shown in `serverinfo`/server browsers. This is disclosed here as required by GPLv3 §5(a) for marking modified files.
- No other source files, gameplay logic, or mod functionality were changed.
- The compiled output is packaged/renamed as `iw1x.so` purely for hosting compatibility (see [above](#the-problem-this-solves)) — this is a filename change on disk only, not a source modification.
- The original license text applies in full — see [LICENSE](LICENSE) or the [GPLv3 license text](https://www.gnu.org/licenses/gpl-3.0.html).
- For the unmodified source code, build instructions, and original documentation, please refer to the **[upstream repository](https://github.com/xtnded/codextended)**.

---

## Support / Issues

This repo only handles **packaging/compatibility for older hosts**. For bugs related to CoDExtended's actual functionality, please check the [upstream repository](https://github.com/xtnded/codextended) first. For issues specific to this build (e.g. it not loading on your host), feel free to open an [Issue](../../issues) here.
