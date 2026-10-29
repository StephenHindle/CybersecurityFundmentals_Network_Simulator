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
