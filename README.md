# Caliper

**Local-first CNC Quoting Workstation for Precision Machine Shops**

[![Release](https://img.shields.io/github/v/release/muicci/caliper-hq)](https://github.com/muicci/caliper-hq/releases)
[![License](https://img.shields.io/github/license/muicci/caliper-hq)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Windows-blue)](https://github.com/muicci/caliper-hq/releases)

---

## 🎯 What is Caliper?

Caliper is a **local-first desktop application** that helps precision machine shops generate manufacturing quotes from engineering drawings. Built for high-security environments (Aerospace, Defense, Medical) where **no drawing data leaves the machine**.

### Key Features

- **📄 PDF Analysis** — Drop engineering drawings, extract text and dimensions automatically
- **🤖 AI-Powered** — Claude/Gemini integration for intelligent feature extraction
- **💰 Instant Quotes** — Generate manufacturing quotes with editable line items
- **🔒 Local-First** — All data stays on your machine, SQLite persistence
- **📤 Export Ready** — Professional PDF quotes, ERP CSV exports
- **🔍 History Search** — Full-text search through past quotes instantly

---

## 🚀 Quick Start

### Download

**Latest Release:** [v2.2.1](https://github.com/muicci/caliper-hq/releases/latest)

| Platform | Download | Requirements |
|----------|----------|--------------|
| **macOS** (Apple Silicon) | [Caliper_2.2.1_aarch64.dmg](https://github.com/muicci/caliper-hq/releases/download/v2.2.1/Caliper_2.2.1_aarch64.dmg) | macOS 11.0+ |
| **macOS** (Intel) | [Caliper_2.2.1_x64.dmg](https://github.com/muicci/caliper-hq/releases/download/v2.2.1/Caliper_2.2.1_x64.dmg) | macOS 11.0+ |
| **Windows** | Coming in v2.4.0 | Windows 10/11 |

### Installation (macOS)

1. Download the `.dmg` file
2. Open and drag Caliper.app to Applications folder
3. First launch: Right-click → Open (if Gatekeeper blocks)
4. Complete onboarding:
   - Enter Polar.sh license key
   - Add your Anthropic/Gemini API key (BYOK)
   - Configure shop settings

### First Quote

1. **Drop a PDF** drawing into the workspace
2. **AI extracts** material, dimensions, operations
3. **Review & edit** values in the quote grid
4. **Export** quote PDF or save to history

---

## 📋 Requirements

### System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **OS** | macOS 11.0 / Windows 10 | macOS 14.0 / Windows 11 |
| **CPU** | Apple Silicon / Intel i5 | Apple M2+ / Intel i7+ |
| **RAM** | 8 GB | 16 GB |
| **Storage** | 500 MB | 1 GB |
| **Internet** | Required for AI features | — |

### API Requirements

- **Anthropic Claude** API key (BYOK) — [Get key](https://console.anthropic.com/)
- **OR** **Google Gemini** API key (BYOK) — [Get key](https://makersuite.google.com/app/apikey)
- **Polar.sh** license key — [Get license](https://polar.sh/muicci)

---

## 📖 Documentation

| Guide | Description |
|-------|-------------|
| [Installation Guide](docs/INSTALLATION.md) | Detailed setup instructions |
| [User Guide](docs/USER_GUIDE.md) | How to use Caliper features |
| [AI Configuration](docs/AI_SETUP.md) | Setting up API keys |
| [FAQ](docs/FAQ.md) | Common questions |

---

## 🗺️ Roadmap

Caliper development is tracked in our [Public Roadmap](https://github.com/muicci/caliper-hq/projects).

### Current Sprint (v2.3.0)

| Feature | Status | ETA |
|---------|--------|-----|
| AI Retry Logic | 🟡 In Progress | Feb 2026 |
| Zod Validation | 🟡 In Progress | Feb 2026 |
| SQL Security Hardening | 🟡 In Progress | Feb 2026 |

### Upcoming (v2.4.0)

| Feature | Status | ETA |
|---------|--------|-----|
| Vision Pipeline Optimization | 🔴 Planned | Mar 2026 |
| Windows Vision Support | 🔴 Planned | Mar 2026 |
| Filesystem Caching | 🔴 Planned | Mar 2026 |

### Future (v2.5.0+)

- Multi-page PDF triage
- Assembly quoting UI polish
- Material cost override table
- Professional branded PDFs

---

## 🐛 Reporting Issues

### Bug Reports

Found a bug? [Open an issue](https://github.com/muicci/caliper-hq/issues/new?template=bug-report.yml)

**Include:**
- Caliper version
- OS version
- Steps to reproduce
- Expected vs actual behavior
- Screenshot (if UI issue)

### Feature Requests

Have an idea? [Request a feature](https://github.com/muicci/caliper-hq/issues/new?template=feature-request.yml)

**Include:**
- Problem you're solving
- Proposed solution
- Use case example

### Security Issues

**Do NOT open public issues for security vulnerabilities.**

Report security issues privately to: **security@caliper.app**

See [SECURITY.md](SECURITY.md) for details.

---

## 🏗️ Architecture

Caliper uses a **two-repo architecture**:

| Repository | Purpose | Access |
|------------|---------|--------|
| **caliper-web-app** | Source code, CI/CD, internal docs | Private |
| **caliper-hq** | Releases, issues, roadmap, distribution | Public |

### Tech Stack

- **Frontend:** SvelteKit + Svelte 5 Runes + Tailwind CSS
- **Desktop:** Tauri v2 (Rust)
- **Database:** SQLite + FTS5 (full-text search)
- **AI:** Anthropic Claude / Google Gemini (BYOK)
- **License:** Polar.sh

---

## 🤝 Contributing

We welcome contributions! See our [Contributing Guide](CONTRIBUTING.md) for details.

### Ways to Contribute

- 🐛 Report bugs
- ✨ Suggest features
- 📚 Improve documentation
- 💬 Help other users in discussions
- 🔒 Security research (responsible disclosure)

### Code Contributions

For external contributors:

1. Fork the public repo
2. Create issue describing change
3. Wait for maintainer review
4. Submit PR if approved

**Note:** Source code is in private `caliper-web-app` repo. Contact maintainers for access if you're a confirmed contributor.

---

## 📄 License

Caliper is **proprietary software** licensed under the Caliper Commercial License.

- **Free trial:** 14 days
- **Paid license:** Via [Polar.sh](https://polar.sh/muicci)
- **Source available:** Yes (private repo)
- **Open source:** No

See [LICENSE](LICENSE) for full terms.

---

## 🔐 Security

- **Local-first:** All drawing data stays on your machine
- **BYOK:** You own your API keys, we never see them
- **No cloud:** No data sent to Caliper servers
- **Auditable:** Full SQLite history for compliance

See [SECURITY.md](SECURITY.md) for our security policy.

---

## 📞 Support

| Channel | Response Time |
|---------|---------------|
| **GitHub Issues** | < 24 hours |
| **Email Support** | < 48 hours |
| **Discord** | Community support |

**Email:** support@caliper.app  
**Discord:** [Join server](https://discord.gg/caliper)

---

## 🏢 About

Caliper is built for precision machine shops in aerospace, defense, and medical industries. Founded 2025.

**Headquarters:** [Your Location]  
**Contact:** hello@caliper.app

---

## 📊 Stats

![GitHub Issues](https://img.shields.io/github/issues/muicci/caliper-hq)
![GitHub Pull Requests](https://img.shields.io/github/issues-pr/muicci/caliper-hq)
![GitHub Downloads](https://img.shields.io/github/downloads/muicci/caliper-hq/total)

---

**Last Updated:** 2026-02-23
