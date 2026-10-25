# Contributing to the Flutter Security Cookbook

Thank you for your interest in contributing to the **Flutter Security Cookbook**! 🛡️

This project aims to be a production-grade security reference, threat-modeling guide, and implementation handbook for Flutter applications on Android and iOS. Mobile security changes constantly as platform rules, OS releases, and attack techniques evolve. We welcome contributions from mobile engineers, security researchers, and DevSecOps practitioners.

---

## 📑 Quick Navigation

- [Guiding Philosophy](#-guiding-philosophy)
- [Ways to Contribute](#-ways-to-contribute)
- [Mandatory Module Structure](#-mandatory-module-structure-blueprint)
- [Code & Documentation Standards](#-code--documentation-standards)
- [Diagrams](#-diagrams)
- [Security Testing Ethics & Content Boundaries](#%EF%B8%8F-security-testing-ethics--content-boundaries)
- [Pull Request Workflow](#-pull-request-pr-workflow)
- [Responsible Vulnerability Disclosure](#%EF%B8%8F-responsible-vulnerability-disclosure)
- [Code of Conduct](#-code-of-conduct) · [License](#-license)

---

## 🧭 Guiding Philosophy

Every contribution must align with the core principle of this cookbook:

> **A Flutter app is a public, untrusted client.**  
> Client-side hardening (obfuscation, root/jailbreak detection, biometrics, certificate pinning) raises the attacker's work factor, but **server-side authorization, schema validation, and access control are your true security boundaries.**

When authoring recipes or advice:

- **Never present client controls as absolute security boundaries.** Explain what the client mitigates and where the backend must enforce the real perimeter.
- **Treat client-computed verdicts as advisory.** Root detection, integrity checks, and similar signals can be hooked by a Tier 4 attacker. The authoritative decision belongs on the server (for example, by verifying Play Integrity / App Attest tokens).
- **Fail restricted, never fail open.** Defensive patterns must fail closed or degrade safely on errors, timeouts, or exceptions.
- **Provide verifiable code.** Samples must be idiomatic Dart 3 / Flutter, pass `flutter analyze` with `flutter_lints`, and reflect production constraints, not toy prototypes.
- **Be honest about limits.** State what a control does _not_ protect against, and name the attacker tiers it does and does not stop.
- **Cite sources.** Link to official Android, Apple, Flutter, or OWASP documentation for claims about platform behavior.

---

## 📋 Ways to Contribute

1. **Authoring or expanding modules (`docs/`)**: add missing chapters from the Table of Contents, or deepen existing ones.
2. **Domain-specific modules (`docs/33-domain-specific-modules/`)**: real-world guidance for high-risk verticals (fintech, healthcare, e-commerce, IoT, and so on).
3. **Platform updates**: keep Android and iOS specifics current (for example `network_security_config.xml`, `RECEIVER_NOT_EXPORTED`, Apple Privacy Manifests, Secure Enclave access flags).
4. **Tooling & pentest recipes**: reproducible verification steps using Frida, Burp Suite, mitmproxy, jadx, OSV-Scanner, and similar tools.
5. **Corrections**: fix typos, broken links, outdated plugin options, or inaccurate claims. Accuracy fixes are highly valued.

Not sure where to start? Look for issues labeled `good first issue` or `help wanted`, or open a discussion before starting a large module.

---

## 📐 Mandatory Module Structure (Blueprint)

Every guide in [`docs/`](./docs) must follow this 6-section blueprint. Section headings should match so modules stay consistent and easy to navigate:

```markdown
# [Module Number]. [Module Title]

> **Priority:** `[P0]` / `[P1]` / `[P2]`  
> **Target Platforms:** Android & iOS  
> **Relevant Standards:** OWASP MASVS v2 (tags), OWASP Mobile Top 10 (2024), OWASP MASTG  
> **Last Verified:** YYYY-MM against Flutter x.y / Android API nn / iOS nn

---

## 1. Threat: Who You Are Defending Against

- Attacker profile (map to Attacker Tiers 1–7)
- STRIDE categorization for this specific surface
- Concrete mobile attack vector and real-world impact

## 2. Why It Matters (Theory & Standards)

- Low-level OS mechanics (Android Keystore, iOS Keychain, Binder, sandbox, ATS)
- Flutter engine / Dart runtime internals (AOT compilation, isolates, memory lifecycle)
- Trust boundaries, trade-offs, and explicit out-of-scope threats

## 3. Flutter & Platform Implementation

- Production-ready Dart / Flutter code recipes
- Native platform channel code (Kotlin / Swift) where native APIs are required
- Clear error handling, lifecycle management, and null safety

## 4. How to Test and Verify

- Concrete CLI commands and reproducible steps
- Testing tools (mitmproxy, Burp Suite, Frida, jadx, Ghidra)
- Positive and negative controls, and Flutter-specific testing gotchas

## 5. Common Pitfalls & Anti-Patterns

- Vulnerable vs. hardened side-by-side code (❌ Vulnerable / ✅ Hardened)
- Why common developer shortcuts fail

## 6. OWASP MASVS & Mobile Top 10 (2024) Mapping Reference

- Table mapping controls to MASVS v2 categories and OWASP Mobile Top 10 (2024)
```

Notes:

- Priority tags (`P0` / `P1` / `P2`) are defined in the [README](./README.md#2-priority-matrix). Tag each control, not just the module.
- Domain modules under `docs/33-domain-specific-modules/` follow the same blueprint, with a short "Regulatory Context" subsection in Section 2.
- Use the data classes (Class A–D) and attacker tiers (Tier 1–7) exactly as defined in [Module 00](./docs/00-threat-model-and-attacker-tiers.md). Do not introduce new "Tier" or "Class" terminology.

---

## 💻 Code & Documentation Standards

### Dart & Flutter Code

- **Dart 3 & sound null safety**: code must compile cleanly against the current Flutter stable release. State the Flutter/Dart version you tested against.
- **Static analysis**: run `flutter analyze` and `dart format` before submitting.
- **Strict typing**: avoid `dynamic` except when handling unstructured platform channel responses, and validate types immediately at that boundary.
- **Never hardcode secrets**: no hardcoded cryptographic keys, IVs, passwords, tokens, or real endpoints in samples. Demonstrate secure runtime generation (for example `Random.secure()` or a vetted crypto package) or hardware-backed retrieval. Use obvious placeholders such as `example.com`.
- **Dependency hygiene**: name the package and version a snippet targets, and call out known breaking changes across major versions (for example `flutter_secure_storage` option changes).
- **Release-mode behavior**: do not rely on `assert()`, `kDebugMode`-gated checks, or debug-only behavior for security. Test on a **release** build.

### Cross-Platform Parity

Flutter ships to both Android and iOS. Document behavior and configuration for **both**, for example Android Keystore vs. iOS Keychain/Secure Enclave, and Android Manifest / network security config vs. iOS `Info.plist` / Privacy Manifest. If a control exists on only one platform, say so explicitly.

### Markdown Formatting

- Use GitHub Flavored Markdown (GFM).
- Put language identifiers on code blocks (`dart`, `swift`, `kotlin`, `bash`, `javascript`, `xml`, `text`).
- Use relative links for repository references (for example `[Module 01](./docs/01-secure-data-storage.md)`), and verify they work from both `README.md` and `docs/`.
- When showing a Markdown example that itself contains code fences, wrap it in a longer outer fence (four or five backticks).
- Keep table cells concise. Move long explanations out of tables.
- Update the README Table of Contents and OWASP mapping table whenever you add or rename a module.

### Writing Style

- Be precise and direct. Prefer concrete examples over generalities.
- Avoid absolute claims such as "unbreakable" or "100% secure". Describe what a control raises the cost of, and for which attacker tier.
- Mark uncertain or platform-version-dependent claims and link a source.

---

## 🖼️ Diagrams

- Prefer Mermaid fenced as ` ```mermaid ` (the opening fence at column 0, with a blank line before and after).
- Use `flowchart` rather than the older `graph` keyword, and declare each node once.
- Keep labels short; very long labels render poorly on narrow screens.
- Mermaid does not render in every Markdown viewer (it works on GitHub and GitLab, but not in some local previews). For critical diagrams, also provide a plain-text fallback or an exported SVG in `assets/images/`.
- Use box-drawing ASCII art only inside a ` ```text ` block, and check it in a monospace font.

---

## ⚖️ Security Testing Ethics & Content Boundaries

This cookbook is defensive. To keep it that way:

- **Authorized testing only.** Every test recipe must state that it applies only to apps and infrastructure you own or are explicitly authorized to assess.
- **No weaponized content.** Do not submit working malware, ready-to-use exploit chains against third-party apps or services, credential dumps, or step-by-step guidance for attacking specific named products. Describing an attack class at the level needed to build and verify a defense is fine.
- **No real secrets or personal data.** Redact tokens, keys, device identifiers, and personal information from logs, screenshots, and traces.
- **Test positive controls.** When a test can pass vacuously (for example a proxy test where the app ignores system proxy settings), include a positive control that proves the setup works.

---

## 🔄 Pull Request (PR) Workflow

1. **Fork & branch**
   - Fork the repository on GitHub.
   - Create a descriptively named branch:
     ```bash
     git checkout -b docs/15-platform-specific-security
     # or
     git checkout -b fix/typo-module-00
     ```

2. **Commit conventions**: use [Conventional Commits](https://www.conventionalcommits.org/):
   - `docs(00): enhance threat modeling STRIDE matrix`
   - `feat(02): add SPKI pinning implementation snippet`
   - `fix(readme): correct table of contents link to module 33`

3. **Pre-submission checklist**
   - [ ] The module follows the **Mandatory Module Structure** and uses the standard headings.
   - [ ] Dart snippets compile, pass `flutter analyze`, and are formatted with `dart format`.
   - [ ] The target Flutter, plugin, and OS versions are stated, and the **Last Verified** date is set.
   - [ ] Test recipes were run against a **release** build (with a positive control where needed).
   - [ ] Controls are mapped to OWASP MASVS v2 and OWASP Mobile Top 10 (2024) and tagged `P0` / `P1` / `P2`.
   - [ ] Android **and** iOS behavior is covered, or the single-platform scope is stated.
   - [ ] Relative links work from `README.md` and from `docs/`.
   - [ ] README Table of Contents and mapping tables are updated if modules were added or renamed.
   - [ ] Diagrams render (or have a text fallback), and code blocks have language identifiers.
   - [ ] No proprietary, confidential, or live production secrets or personal data are included.

4. **Submit the PR**
   - Open a pull request against the `main` branch.
   - Describe what changed, why, and link related issues. Include before/after notes for corrections.
   - Be ready to iterate: maintainers may ask for sources, tests, or scope changes.

---

## 🛡️ Responsible Vulnerability Disclosure

If you find a security flaw in a sample, recipe, or configuration recommended in this cookbook that could put implementing applications at risk:

1. **Do NOT open a public GitHub issue or pull request.**
2. Report it privately using GitHub's **[private vulnerability reporting](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)**: open the repository's **Security** tab and choose **Report a vulnerability**.
3. Include the affected file, a description of the issue, reproduction steps, and suggested remediation.
4. We will acknowledge your report, coordinate a fix, and credit you in the changelog if you wish.

> **Maintainer note:** enable _Private vulnerability reporting_ under **Settings → Code security**, and add a `SECURITY.md` that repeats these steps. A GitHub `noreply` address cannot receive email, so do not list one as a contact.

For ordinary inaccuracies (for example an outdated plugin option) that carry no meaningful risk, a normal issue or PR is fine.

---

## 🤝 Code of Conduct

Be respectful and constructive. Assume good faith, critique ideas rather than people, and welcome newcomers. Harassment or discrimination of any kind is not tolerated. Maintainers may edit, reject, or remove contributions and moderate discussions to uphold this. Consider adopting the [Contributor Covenant](https://www.contributor-covenant.org/) as a standalone `CODE_OF_CONDUCT.md`.

---

## 📜 License

By contributing to the Flutter Security Cookbook, you agree that your contributions will be licensed under the project's [MIT License](LICENSE), and that you have the right to submit them.
