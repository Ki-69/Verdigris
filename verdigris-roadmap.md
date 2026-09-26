# Verdigris — Roadmap

*An operating system that gives old machines a second life.*

**Target:** v1.0 by May 2027 · **Base:** Alpine Linux (OpenRC, musl, BusyBox, apk) · **Languages:** C, C++, Bash

---

## Naming and conventions (settled)

| Item | Convention |
|---|---|
| Project name | **Verdigris** — the green patina that forms on copper, brass, and bronze as they age; from Old French *vert-de-gris*, "green of grey". Age that adds character and value instead of wear, with a green nod to the project's eco origins |
| Product name | "Verdigris" or "Verdigris OS". Avoid "Verdigris Linux" as the formal product name; the Linux trademark is administered under a sublicense program (check current terms) |
| Pronunciation | Pick one and use it in the docs (VUR-dih-gree or VUR-dih-gris) |
| Command prefix | `verd` |
| Primary interface | `verd <subcommand>` — one dispatcher, like `git` or `docker` |
| Internal binaries | `/usr/libexec/verd/<name>` |
| Optional short aliases | `verd-mon`, `verd-bench` via argv[0] dispatch (BusyBox-style), only if wanted later |
| Check before shipping | Confirm `verd` is free in the Alpine and Debian package indexes and with `apk search verd` / `which verd`. A near-name exists: **`verda`**, the Verda Cloud CLI. Never ship a binary called `verda`, and watch for mistyping between the two |
| Disclaimer for the README | "Verdigris OS is an independent project and is not affiliated with Verdigris Technologies." (Verdigris Technologies is a separate building-energy-monitoring company) |
| Hostname / mDNS | `verd-nas.local`, `verd-display.local` |
| Packages | `verd-base`, `verd-tools`, `verd-nas`, `verd-display` |
| Config | `/etc/verd/` |
| Repo name | `verdigris` |
| Repo layout | `build/ kernel/ tools/ bench/ installer/ docs/ tests/` |
| Editions | **Verdigris** (standard) and **Verdigris Lite** (CD-sized) |

### Subcommand map

| Subcommand | What it does |
|---|---|
| `verd bench` | Benchmark harness |
| `verd mon` | Live resource monitor |
| `verd doctor` | Pre-install hardware health check |
| `verd detect` / `verd tune` | Hardware discovery and profile selection |
| `verd govern` | Focus-aware resource governor (includes the OOM daemon) |
| `verd nas` | NAS role setup and management |
| `verd display` | Second-monitor receiver |
| `verd role` | Switch between Desktop / Display / NAS |
| `verd backup` | Backups and restore |
| `verd install` | Installer |
| `verd report` | Collect a local bug report |
| `verd settings` | Display, network, power, keyboard, time (SHOULD) |
| `verd prefetch` | Record file access and preload in disk order (SHOULD) |
| `verd bootgraph` | Boot timeline from `initcall_debug` and OpenRC timings (SHOULD) |
| `verd store` | App catalog with real RAM cost per app (LATER) |
| `verd rip` | Disc ingestion to NAS (LATER) |
| `verd migrate` | Import data from a Windows install (LATER) |
| `verd fleet` | Network-boot and manage a lab of machines (LATER) |
| `verd help` | Single discoverable entry point |

**First build task:** the dispatcher itself. About 100 lines of C that resolves the subcommand and `execvp`s the real binary. Good practice with `exec`, `PATH`, and `argv`.

---

## How to use this document

Every feature carries three things:

- **v1.0 tag:** `MUST` (not shippable without it), `SHOULD` (aim for it, cut if behind), `LATER` (v1.x, v2, or a learning experiment).
- **Concept it teaches:** the specific OS idea you learn by building it.
- **Status:** `☐` not started, `◐` in progress, `☑` done.

**Effort:** *Config* = integrate an existing component. *Original* = substantial code you write. Your resume value lives mostly in the *Original* rows and in the benchmarks.

**The tags are a first draft.** Retag after your baseline benchmark (end of Phase 2) and again after a week of daily use. Your own measurements outrank this list.

---

## Key decisions to record

| Decision | Choice | Date |
|---|---|---|
| Project name | **Verdigris** (chosen after Eco Linux, Patina, Cairn, and Ambrosia were ruled out or set aside) | Sep 2026 |
| Command convention | `verd <subcommand>` | Sep 2026 |
| `verd` prefix free in Alpine/Debian package indexes | *(check: `apk search verd`, `which verd`)* | |
| Domain / GitHub org | *(check availability)* | |
| Tier A hardware (64-bit, ~2 GB RAM, HDD) | | |
| Tier B hardware (32-bit, ~512 MB RAM), stretch | | |
| Physical test machine(s) | | |
| Base distro | Alpine (verify 32-bit x86 support before committing) | |
| Display stack | Xorg + lightweight WM | |
| Comparison distros | Lubuntu / Debian+XFCE / Tiny Core / Puppy / wattOS | |

---

## A. Core system

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Reproducible ISO build (`make iso`, Alpine `mkimage`) | MUST | Config | Boot chain, what a distro is made of | ☐ |
| `verd` dispatcher binary | MUST | Original (C) | `execvp`, `PATH`, argv dispatch | ☐ |
| Trimmed kernel config + zram/zswap | MUST | Config | Kernel config, virtual memory, swap, compression | ☐ |
| OpenRC service audit (justify every running service) | MUST | Config | Init systems, service dependencies | ☐ |
| Networking: Ethernet, Wi-Fi, firmware loading | MUST | Config | Drivers, firmware loading, netlink | ☐ |
| `apk` packaging for all Verdigris components | MUST | Config | Package building, dependency metadata | ☐ |
| Xorg + lightweight WM (Openbox/IceWM/JWM) + panel | MUST | Config | X11 architecture, window management | ☐ |
| Framebuffer / `modesetting` fallback for weak GPUs | MUST | Config | DRM/KMS, fbdev, X drivers | ☐ |
| Basic apps: file manager, terminal, editor, image and PDF viewers, archive manager | MUST | Config | Choosing software by measured cost | ☐ |
| Browser choice with documented RAM rationale | MUST | Config | Memory cost of real workloads, PSS | ☐ |
| Audio: ALSA baseline, PipeWire measured against it | MUST | Config | Sound stack, latency vs RAM | ☐ |
| Locale, keyboard layouts, fonts for major scripts | MUST | Config | Locales, input methods, Unicode | ☐ |
| Network UI with warnings for weak Wi-Fi security (WEP) | MUST | Config | Wi-Fi security modes, wpa_supplicant | ☐ |
| Lightweight office suite | LATER | Config | Cost/benefit of heavy apps | ☐ |
| Bluetooth, printing (CUPS), scanning | LATER | Config | Device stacks vs RAM budget | ☐ |

---

## B. Verdigris's own tools

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| **`verd bench`** — idle RAM, boot time, disk footprint, process count, app launch latency; runs identically on comparison distros | MUST | Original (C++/Bash) | Measurement methodology, `/proc`, `/sys`, statistics | ☐ |
| **`verd mon`** — TUI monitor reading `/proc` and `/sys` directly, with **PSS** from `smaps_rollup` for honest per-app memory | MUST | Original (C++) | `/proc` internals, shared pages, copy-on-write, PSI | ☐ |
| **`verd govern --oom`** — userspace low-memory daemon on PSI + available memory (stage 1 of the governor) | MUST | Original (C) | Virtual memory, PSI, signals | ☐ |
| **`verd detect` / `verd tune`** — CPU flags, RAM, disk type, GPU → profile (zram size, swappiness, I/O scheduler, governor, services, codec, PAE check) | MUST | Original (C++/Bash) | Hardware discovery, sysfs, VM and scheduler tunables | ☐ |
| **`verd install`** — TUI installer | MUST | Original (C++/Bash) | Partitioning, bootloader (BIOS + UEFI), chroot | ☐ |
| **`verd govern`** — focus-aware resource control via cgroup v2 (`cpu.weight`, `memory.high`), driven by PSI. *Signature feature.* | SHOULD | Original (C++) | cgroups v2, scheduling, memory reclaim | ☐ |
| App freezer using `cgroup.freeze` for idle background apps | SHOULD | Original | cgroup freezer, reclaim with zram | ☐ |
| **`verd doctor`** — pre-install health check (SMART, RAM test, thermals, battery, CPU flags) with a verdict | SHOULD | Original (Bash + C++) | SMART, diagnostics, thermal sensors | ☐ |
| `verd bootgraph` — parse `initcall_debug` + OpenRC timings into a timeline | SHOULD | Original | Kernel boot sequence, initcalls, service ordering | ☐ |
| `verd prefetch` — record file access (`fanotify`), preload in disk order | SHOULD | Original (C) | Page cache, readahead, `posix_fadvise`, `mincore` | ☐ |
| Hardware compatibility database, contributable format | SHOULD | Original (data + tooling) | PCI/USB IDs, udev, firmware mapping | ☐ |
| Battery health tracker (capacity vs design capacity) | SHOULD | Original (C++) | ACPI, `/sys/class/power_supply` | ☐ |
| Thermal / fan management daemon | LATER | Original (C) | Thermal zones, cooling devices, throttling | ☐ |
| Suspend/resume tester + per-model quirk database | LATER | Original | ACPI sleep states, driver resume paths | ☐ |
| `verd mon --explore` — live page cache, process tree, cgroups, interrupts, with explanations | LATER | Original | Teaching the OS by exposing its internals | ☐ |
| Own minimal WM/panel in C (XCB) + dmenu-style launcher | LATER | Original (C) | X11 protocol, event loops | ☐ |

---

## C. Roles (boot profiles)

Each role starts only the services it needs. `verd detect` suggests one; `verd role set <name>` switches.

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Role framework (`verd role`): Desktop / Display / NAS | SHOULD | Config + small original | Service sets, OpenRC profiles | ☐ |

### C1. NAS role

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| MVP: Samba + Avahi (`verd-nas.local`) + SFTP, single ext4 disk, LAN-only, no default passwords | MUST | Config | Network filesystems, mDNS, permissions | ☐ |
| **`verd nas setup`** — detect disks (`/sys/block`, udev), safe format/mount, users and shares, generate `smb.conf` | MUST | Original (C++) | Block devices, udev, filesystems, config generation | ☐ |
| SMART disk-health warnings | SHOULD | Config + small original | SMART attributes, failure prediction | ☐ |
| Disk spin-down (`hdparm`), wake-on-LAN, measured idle watts | SHOULD | Config | Power management, ATA power states | ☐ |
| mdadm mirroring | LATER | Config | Software RAID (RAID is not backup) | ☐ |
| btrfs snapshots | LATER | Config | Copy-on-write filesystems | ☐ |
| Syncthing / NFS options | LATER | Config | Sync protocols | ☐ |
| Integration with your private file-sharing system | LATER | Original | Protocol and storage design | ☐ |
| WireGuard remote access | LATER | Config | VPNs, key exchange | ☐ |

### C2. Display role (second monitor)

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Milestone 1: Linux host → Verdigris over wired Ethernet, mirror only, ffmpeg/GStreamer scripts; receiver renders via DRM/KMS (no Xorg) | MUST | Config + scripts | DRM/KMS, video pipelines, link-local addressing | ☐ |
| Glass-to-glass latency measurement (240 fps camera + on-screen timer) | MUST | Method | End-to-end latency measurement | ☐ |
| **`verd display`** daemon — mDNS discovery, pairing PIN, sessions, protocol framing | SHOULD | Original (C++) | Sockets, protocol design, mDNS | ☐ |
| Extended desktop on X11 host via virtual monitor (`xrandr`) | SHOULD | Config | RandR, virtual outputs | ☐ |
| Codec selection by `verd detect` (MJPEG vs H.264), benchmarked | SHOULD | Original (small) | Codec CPU/bandwidth trade-offs | ☐ |
| Wireless mode with adaptive bitrate | LATER | Original | Congestion, jitter, rate adaptation | ☐ |
| Wayland host (PipeWire / portal capture) | LATER | Config | Wayland screencasting | ☐ |
| Windows host agent (Indirect Display Driver) | LATER | Original | Windows display driver model | ☐ |
| macOS host (mirror only) | LATER | Original | Platform restrictions | ☐ |
| Miracast receiver experiment (MiracleCast) | LATER | Experiment | Wi-Fi Direct, Miracast | ☐ |
| Keyboard/mouse sharing (evdev + uinput) | LATER | Original (C) | Input subsystem | ☐ |
| Web offload: heavy browser runs elsewhere, streamed to the old machine | LATER | Original | Remote rendering | ☐ |

### C3. Other roles (pick at most one)

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Router / firewall (two NICs) | LATER | Config | nftables, routing, NAT | ☐ |
| DNS ad-blocker (dnsmasq / Unbound) | LATER | Config | DNS, resolver design | ☐ |
| Kiosk / digital signage | LATER | Config | Minimal session design | ☐ |
| Thin client (boots into RDP/SSH) | LATER | Config | Remote sessions | ☐ |

---

## D. Boot, storage, and installation

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Read-only squashfs root + overlayfs; run-from-RAM mode | SHOULD | Config | Layered filesystems, initramfs handoff | ☐ |
| Custom initramfs `/init` in C (mount, overlayfs, `switch_root`) | SHOULD | Original (C) | The full boot handoff | ☐ |
| Disk-ordered squashfs layout (sort file by boot access order) | SHOULD | Config | HDD seek behavior, sequential vs random reads | ☐ |
| **Compression study:** squashfs lz4/zstd/xz + zram algorithms on real hardware | SHOULD | Method | CPU vs IO trade-off, where the crossover falls | ☐ |
| **Mitigations study:** Spectre/Meltdown on vs off | SHOULD | Method | CPU vulnerabilities, security vs performance | ☐ |
| **Memory strategy study:** KSM + zram tuning | SHOULD | Method | Page deduplication, reclaim | ☐ |
| **Verdigris Lite** — CD-bootable edition (target < 200 MB, copy-to-RAM, El Torito via `xorriso`) | SHOULD | Config | ISO 9660, El Torito, isolinux | ☐ |
| Multisession CD persistence | LATER | Experiment | Optical filesystems | ☐ |
| Boot helpers for machines that can't boot USB (Plop, etc.) | LATER | Config | BIOS boot limitations | ☐ |
| PXE network boot | LATER | Config | DHCP/TFTP boot | ☐ |
| Host-only initramfs generated per detected machine | LATER | Original | Module selection, minimal boot | ☐ |
| Minimal PID 1 with dependency graph (learning experiment) | LATER | Original (C) | What init actually does | ☐ |
| Tiny kernel module exposing stats via `/proc` or a char device | LATER | Original (C) | Kernel space, module API | ☐ |
| FUSE filesystem (compression or dedup) | LATER | Original (C) | Filesystem internals | ☐ |

### D1. Optical drive features

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| `verd rip` — disc ingestion to NAS, own `SG_IO` MMC reads (`READ CD`), data ISOs and audio to FLAC | LATER | Original (C/C++) | SCSI layer, MMC commands, raw sectors, secure ripping | ☐ |
| Disc health scanner (speed + error graph; extends `verd doctor`) | LATER | Original | Error rates, drive health, Reed-Solomon | ☐ |
| Disc server (auto-mount inserted disc, share over Samba) | LATER | Config | udev, autofs | ☐ |
| CD jukebox role | LATER | Config | Audio streaming | ☐ |
| Offline update discs (signed local `apk` repo across disc swaps) | LATER | Original | Package signing, offline distribution | ☐ |

---

## E. Desktop polish and everyday use

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| **i18n groundwork** — translatable strings separate from code in every tool and the installer | MUST | Method | gettext, locale handling | ☐ |
| `verd settings` (display, network, power, keyboard, time) | SHOULD | Original | Config UIs over system APIs | ☐ |
| Screen lock, notifications, clipboard, screenshots | SHOULD | Config | Desktop session services | ☐ |
| First-boot wizard (language, Wi-Fi) + offline help | SHOULD | Original | First-run design, user flow | ☐ |
| Video playback: VA-API where available, software decode otherwise | SHOULD | Config | GPU acceleration, codecs | ☐ |
| Per-model firmware notes for old Wi-Fi / Ethernet chips | SHOULD | Docs | Firmware, blob licensing | ☐ |
| Accessibility: large text, high contrast, screen reader | LATER | Config | Assistive technology stack | ☐ |
| `verd store` — app catalog showing real RAM cost (PSS) per app | LATER | Original | Package metadata, measurement | ☐ |
| Factory reset via read-only root | LATER | Config | Immutable system design | ☐ |
| Atomic updates with rollback | LATER | Original | Update design, A/B partitions | ☐ |
| Legacy peripherals: serial, parallel, PS/2, floppy | LATER | Config | Legacy hardware interfaces | ☐ |

---

## F. Data safety and security

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Default-deny firewall (nftables), per-role rules | MUST | Config | Packet filtering | ☐ |
| Written support and patch policy (CVE response time, support length) | MUST | Docs | Security lifecycle | ☐ |
| Signed ISOs and published checksums | MUST | Config | Signing, key management | ☐ |
| Reproducible build verification (build twice, compare hashes) | SHOULD | Method | Determinism in builds | ☐ |
| Own hosted, signed `apk` repository | SHOULD | Config | Repo metadata, signing keys | ☐ |
| `verd backup` — scheduled incremental backups + restore test | SHOULD | Original | Hardlink rotation, snapshots | ☐ |
| Rescue / safe-mode boot entry (minimal shell, fsck, rollback) | SHOULD | Config | Recovery paths | ☐ |
| `verd report` — collects hardware and logs locally, sends nothing | SHOULD | Original (Bash) | Diagnostics, log handling | ☐ |
| Energy measurements (plug meter, idle watts per role) | SHOULD | Method | Power measurement | ☐ |
| Optional LUKS encryption; measure cost on CPUs without AES-NI | LATER | Config | dm-crypt, cipher performance | ☐ |
| Landlock + seccomp sandboxing for browser and network services | LATER | Original | Linux security model | ☐ |
| Software bill of materials per release | LATER | Config | Supply-chain transparency | ☐ |
| Delta / low-bandwidth updates | LATER | Original | Binary diffing, update design | ☐ |
| `verd migrate` — import documents and bookmarks from a Windows NTFS install | LATER | Original | NTFS access, partition layouts | ☐ |
| Opt-in anonymized hardware reports | LATER | Original | Privacy-preserving telemetry design | ☐ |
| `verd fleet` — network-boot and configure a lab of machines | LATER | Original | PXE, config management | ☐ |
| Energy dashboard | LATER | Original | Power accounting | ☐ |

---

## G. Project infrastructure and engineering practice

| Feature | v1.0 | Effort | Concept it teaches | Status |
|---|---|---|---|---|
| Name check | — | Task | Done — Verdigris chosen Sep 2026 | ☑ |
| Verify the `verd` prefix is free (Alpine and Debian package indexes, `which verd`) | MUST | Task | Do before writing the dispatcher | ☐ |
| Domain + GitHub org for Verdigris | MUST | Task | Do before publishing | ☐ |
| Non-affiliation line in the README (Verdigris Technologies) | MUST | Docs | Avoiding confusion with similarly named projects | ☐ |
| Public repo with README and commit history from day one | MUST | Task | Visibility for internship applications | ☐ |
| Dev log documenting problems and fixes | MUST | Docs | Communicating engineering decisions | ☐ |
| One-page design doc per tool (problem, alternatives, choice) | MUST | Docs | Design reasoning | ☐ |
| Docs: install guide, hardware list, troubleshooting, how it works | MUST | Docs | Technical writing | ☐ |
| Release process: versioning, changelog, upgrade notes | MUST | Process | Release engineering | ☐ |
| Licensing compliance (GPL source availability, license tracking) | MUST | Process | Open-source licensing | ☐ |
| CI: build ISO and boot it headless in QEMU | MUST | Config | CI pipelines, automated testing | ☐ |
| CI performance budgets (fail build on RAM / boot-time regression) | SHOULD | Original | Performance regression testing | ☐ |
| Sanitizers and static analysis (`-fsanitize`, `cppcheck`, `clang-tidy`) | SHOULD | Config | Memory-safety bugs | ☐ |
| Testing matrix: architectures, RAM sizes, BIOS vs UEFI | SHOULD | Config | Test design | ☐ |
| Contribution guide | SHOULD | Docs | Open-source practice | ☐ |
| Upstream a fix (Alpine, a tool, or docs) when the chance arises | SHOULD | Opportunistic | Working with upstream | ☐ |
| Fuzzing for parsers (config and protocol) | LATER | Original | Input validation, robustness | ☐ |
| Public hardware reports table | LATER | Docs | Community data | ☐ |

---

## Merged overlaps

Features teaching the same thing were combined so effort isn't duplicated:

- The OOM daemon is stage 1 of `verd govern`. Build it first, then add cgroup control on the same PSI code.
- The squashfs and zram compression comparisons are one **compression study**.
- Run-from-RAM, Verdigris Lite's copy-to-RAM, and the custom initramfs `/init` all rest on the same overlayfs + `switch_root` work. Build it once.
- `verd store`'s RAM column and `verd mon`'s PSS code share one measurement library.
- Factory reset and rollback are the same read-only-root design. Plan them together.

---

## Scope check

Most `MUST` rows are integration and documentation, not new code. Only **eight** need substantial original code: the dispatcher, `bench`, `mon`, `govern --oom`, `detect`, `install`, `nas setup`, and the wired display MVP.

**Cut in this order** if you fall behind — never cut quality to keep scope:

1. Everything already marked `LATER`
2. Display extend mode, pairing daemon, codec selection (keep the wired mirror and its latency number)
3. App freezer, then full `verd govern` (keep the OOM daemon)
4. Verdigris Lite, `verd prefetch`, read-only root
5. `verd doctor`, battery tracker, `verd bootgraph`

**Never cut:** `verd bench`, the reproducible build, the CI boot test, and the documentation. They are what make the project credible.

---

## Timeline

| Phase | Window | Goal |
|---|---|---|
| 0. Foundations | Late Sep – Oct 2026 | Repo, domain, Alpine in QEMU, custom ISO via `mkimage`, hardware chosen, dispatcher written |
| 1. Reproducible build | Oct – Nov 2026 | `make iso` boots to a shell with networking and `apk`; CI boots it in QEMU. **Internship milestone:** public repo, bootable ISO, `verd mon` in progress |
| 2. Desktop + baseline | Dec 2026 – Jan 2027 | Xorg, WM, basic apps, browser choice. **Record the baseline before optimizing anything.** Retag this roadmap. |
| 3. Tooling | Feb – Mar 2027 | `bench`, `mon`, `govern --oom`, `detect`; NAS role MVP; wired display prototype; real-hardware testing |
| 4. Installer + release | Apr – May 2027 | `verd install`, docs, signed ISO, full benchmark report, **v1.0** |
| v1.x | After May 2027 | Own repo, `verd store`, rollback, encryption, migration, more roles |
| v2 | Later | Fleet management, hardware reports, community |

---

## Metrics to publish with v1.0

Set numeric targets only after measuring the comparison distros on the same machine. The *form* to aim for:

- Idle RAM at least X% below Lubuntu / Debian+XFCE on Tier A
- Boot to desktop under N seconds on a Tier A HDD
- Installed disk footprint under N MB; Verdigris Lite under 200 MB
- Process count at idle
- Display role: glass-to-glass latency in ms over wired Ethernet
- NAS role: idle watts with the disk spun down

For every number, record the hardware, the number of runs, and the method in `bench/methodology.md`.

---

## Weekly habits

- Update the Status column every week.
- Once you have a bootable image, use Verdigris for real work for a week and log every annoyance. Promote the worst ones into this list.
- Before adding any feature, write down which OS concept it teaches. If an existing item already teaches it, drop one.
