<!-- # seeddms-hostinger-deployment-guide --> 
# 📁 SeedDMS — Hostinger Deployment Guide

> A complete deployment and maintenance guide for hosting **SeedDMS** (Open Source Document Management System) on **Hostinger Shared Hosting** with **Cloudflare** integration.

---

## 🧩 Project Overview

This project documents the end-to-end process of researching, deploying, customizing, and maintaining an open-source Document Management System (DMS) for internal office use.

The requirement was to set up a centralized document management solution for the organization. After evaluating multiple open-source options, **SeedDMS** was selected and successfully deployed on Hostinger Shared Hosting.

---

## 🔍 Why SeedDMS?

Before finalizing SeedDMS, multiple open-source DMS platforms were evaluated:

| Platform | Verdict |
|---|---|
| **OpenDocMan** | Outdated UI, limited features |
| **SeedDMS** ✅ | Clean UI, active development, feature-rich, PHP-based |

SeedDMS was chosen for its:
- Modern and clean user interface
- Role-based access control (ACL)
- Document versioning
- Full-text search support
- Active open-source community
- Easy deployment on shared hosting

---

## 🛠️ Tech Stack

| Component | Details |
|---|---|
| **Application** | SeedDMS 6.0.39 |
| **Language** | PHP 8.2 |
| **Database** | MySQL (via phpMyAdmin) |
| **Hosting** | Hostinger Shared Hosting |
| **DNS & Security** | Cloudflare |
| **Access** | SSH + hPanel File Manager |
| **Web Server** | Apache (with .htaccess) |

---

## 📌 What Was Done

- ✅ Researched and evaluated open-source DMS platforms
- ✅ Presented recommendation to management with justification
- ✅ Coordinated domain registration and hosting setup
- ✅ Deployed SeedDMS on Hostinger Shared Hosting via SSH
- ✅ Configured MySQL database via phpMyAdmin
- ✅ Set up Apache rewrite rules for clean URL routing
- ✅ Applied custom branding (logo, UI text customization)
- ✅ Integrated Cloudflare for DNS management and bot protection
- ✅ Created firewall rules to block malicious traffic
- ✅ Performed version upgrades (6.0.33 → 6.0.39)
- ✅ Documented complete installation and upgrade process

---

## 📂 Documentation

| Document | Description |
|---|---|
| [Installation Guide](docs/installation.md) | First-time setup on Hostinger |
| [Upgrade Guide](docs/upgrade.md) | How to upgrade to a newer version |
| [Troubleshooting](docs/troubleshooting.md) | Common errors and fixes |

---

## 🚀 Key Highlights

### 🔐 Cloudflare Integration
After noticing unusually high traffic (mostly bots), Cloudflare was integrated to:
- Manage DNS records
- Block malicious bot traffic using firewall rules
- Improve overall security

### 🎨 Custom Branding
- Custom logo added via `views/bootstrap/images/`
- UI text customized via `languages/en_GB/lang.inc`

### 🔄 Version Upgrades
The system has been upgraded multiple times since initial deployment.
A detailed upgrade guide is documented in [docs/upgrade.md](docs/upgrade.md).

---

## 📅 Timeline

| Milestone | Date |
|---|---|
| Initial Research & Evaluation | Early 2025 |
| First Deployment (v6.0.33) | April 2025 |
| Cloudflare Integration | April 2025 |
| Upgraded to v6.0.39 | May 2026 |
| Documentation Created | May 2026 |

---

## 👤 Author

**Parmeshwar** — [@MrParmeshwar](https://github.com/MrParmeshwar)

---

## 📜 License

This documentation is open for reference and learning purposes.
SeedDMS is licensed under GPLv2. Visit [seeddms.org](https://www.seeddms.org) for more information.
