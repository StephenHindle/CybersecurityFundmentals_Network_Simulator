# Cyber Fundamentals Network Simulator

A fork of the Network Design Tool, tailored for the **Cybersecurity Fundamentals** course. It adds a
**Lab Mode** that loads a starting network for Riverstone Community Health, shows the lab steps in a side
panel, and checks a student's configuration live.

It is the same single-file browser simulator as the networking-course tool, with three differences:

- a separate browser autosave key (`netSimAutosave_cyberfund_v1`), so it never collides with the
  Fundamentals of Networking tool;
- a course banner and lab title;
- an opt-in Lab Mode that is completely inert unless the page URL carries a `?lab=` parameter.

With no `?lab=` the tool behaves exactly like the base simulator.

## Using it in the course

Host this folder with GitHub Pages (Settings ▸ Pages ▸ Deploy from branch ▸ `main` / root). Each lab is
then embedded in the course with an iframe, for example:

```html
<div class="embed-app">
  <iframe src="https://stephenhindle.github.io/CybersecurityFundmentals_Network_Simulator/?lab=t5-segment"
          style="width:100%;height:900px" loading="lazy" allowfullscreen></iframe>
</div>
```

Open the base URL with no parameter to get a **Course labs** button (bottom-left) that lists every lab.

## Labs in this build

| id | Topic · Lab | Checks |
|----|-------------|--------|
| `t1-assets`        | 1 · Lab 1 | self-checked (asset tour) |
| `t2-recon-capture` | 2 · Lab 4 | self-checked (recon + clear-text capture) |
| `t3-ssh-users`     | 3 · Lab 5 | auto (per-user SSH) |
| `t4-wire-crypto`   | 4 · Lab 3 | mixed (encrypted vs clear) |
| `t5-segment`       | 5 · Lab 1 | auto (VLAN segmentation) |
| `t5-portsec`       | 5 · Lab 6 | auto (port security + BPDU guard) |
| `t6-harden`        | 6 · Lab 1 | auto (switch hardening baseline) |
| `t7-logging`       | 7 · Lab 1 | auto (syslog + NTP) |
| `t2-worm`          | 2 · Lab 6 | auto (watch a worm spread on a flat network) |
| `t5-worm-contain`  | 5 · Lab 8 | auto (contain the worm with segmentation) |
| `t7-ransomware`    | 7 · Lab 6 | auto (make the backup survive ransomware) |
| `t3-mfa`           | 3 · Lab 2 | auto (MFA rollout, kill shared logins) |
| `t3-lifecycle`     | 3 · Lab 4 | auto (joiner / mover / leaver) |
| `t4-padlock`       | 4 · Lab 4 | auto (certificates and the lookalike padlock) |
| `t7-siem`          | 7 · Lab 2 | auto (alert rules, catch incidents, avoid false alarms) |
| `t7-recovery`      | 7 · Lab 5 | auto (RPO, surviving copy, restore test) |
| `t5-dot1x`         | 5 · Lab 7 | auto (802.1X network access control) |

## Malware / ransomware engine

The last two labs use a self-contained threat engine (`window.CyberMalware`) that reasons over the
topology graph. It does not touch the base packet engine. A worm spreads to hosts that are **reachable**
(same VLAN and same switched fabric) and unpatched; ransomware encrypts reachable hosts whose backup is not
offline/immutable. The labs show a **Threat controls** panel (Infect / Run spread / Run ransomware / Reset,
plus per-host patch, isolate and backup-mode toggles), so students watch segmentation and offline backups
change the outcome. Checks are outcome-based (for example, "a worm from PC-Reception1 can no longer reach
any clinical host").

## Identity / MFA engine

The two Topic 3 labs use `window.CyberIdentity`, a self-contained staff-directory model stored on the
`Identity-Server` device. An **Identity directory** panel lets students toggle each account's enabled state
and MFA method, remove the shared login, add a starter, and run a credential-stuffing simulation that falls
to zero once every enabled account has a second factor. Checks are outcome-based (phishable count, shared
accounts, departed accounts). It does not touch the base packet engine.

## PKI / certificate engine

The Topic 4 padlock lab uses `window.CyberPKI`, a set of sites each with a certificate. A **Certificate
inspector** panel shows each site's padlock or warning, lets students install a CA-signed certificate on the
sites they own, and judge where it is safe to log in. It includes a lookalike phishing domain that holds a
perfectly valid certificate. Checks are outcome-based (broken certs fixed, genuine sites marked safe,
lookalike blocked). State is runtime, so this lab has no topology save. It does not touch the base packet
engine.

## SIEM engine

The Topic 7 alerting lab uses `window.CyberSIEM`, a log of normalised events (three real incidents hidden in
routine noise) and a rule builder. A **SIEM rules** panel shows the event counts, lets students add
threshold rules, and reports incidents caught and false alarms. Checks are outcome-based (all incidents
covered, no false-positive rule). State is runtime, so this lab has no topology save. It does not touch the
base packet engine.

## Backup / recovery engine

The Topic 7 recovery lab uses `window.CyberBackup`, a recovery model for the critical system: a schedule
(sets the RPO), backup copies (online/offline/immutable), and a restore test that passes only with a
schedule and a surviving copy (and reports the RTO). A **Backup & recovery** panel drives it. Checks are
outcome-based (RPO met, protected copy exists, restore proven). State is runtime, so this lab has no
topology save. It does not touch the base packet engine.

## 802.1X network access control engine

The Topic 5 802.1X lab uses `window.CyberDot1x`, which stores 802.1X on the real SW-Access staff ports and a
RADIUS flag on the switch (so it persists in saves). A **Network access control** panel enables 802.1X per
port, points it at the Identity-Server, and simulates admission: a known staff device is admitted, an
unknown laptop is denied. Checks are outcome-based (all staff ports on, RADIUS on, rogue denied while staff
admitted). It does not touch the base packet engine.

## Topology saves (`/topologies`)

- `Riverstone-Master-Starter.json` — the shared starting network (flat, insecure baseline).
- `*-solution.json` — the finished "answer key" network for each auto-checked lab. Load one with
  **Tools ▸ Load** to show the intended end state.

Lab Mode loads its own starting network automatically, so students never need these files; they are for
the instructor.

## How Lab Mode works internally

Three script blocks are appended to the base tool:

- `COURSE_TOPO` — the Riverstone starting network(s).
- `COURSE_LABS` — the lab catalogue: steps, "Done when" items, and check functions.
- the Lab Mode engine — reads `?lab=`, loads the starter, renders the panel, and runs the checks.

To add or edit a lab, edit `COURSE_LABS`. Each auto check is a small function that inspects device state
(for example, "VLAN 20 exists on SW-Access" or "the VTY lines accept SSH only").
