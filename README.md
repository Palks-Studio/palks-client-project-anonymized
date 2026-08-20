<p align="center">
  <img src="docs/images/sereona.png"
       alt="Sereona homepage — natural wellness program interface and structured weekly guidance"
       width="1200">
</p>

> 🇬🇧 English | [🇫🇷 Français](./README_FR.md)

![License](https://img.shields.io/badge/License-LICENSE.md-lightgreen.svg)
![Static Site](https://img.shields.io/badge/Type-Static%20Site-0a5645?style=flat)
![Architecture](https://img.shields.io/badge/Architecture-Showcase-151b1c?style=flat)
![Stack](https://img.shields.io/badge/Stack-HTML%2FCSS%2FJS-0095b1?style=flat)

<p align="center">
  <a href="https://sereona.fr">
    <img src="https://img.shields.io/badge/Sereona-Website-0a5645?style=for-the-badge&logoColor=white" />
  </a>
</p>

# Project Overview

> This repository is a technical presentation and documentation repository.  
> It does not contain downloadable source code or production files.

This repository presents the complete architecture of a website focused on natural wellness,  
designed without CMS, SaaS, cookies, or an exposed application backend.

The entire system runs exclusively on shared hosting,  
without dedicated infrastructure or managed services,  
except for the payment provider.

The project follows a deliberately minimalist and autonomous approach:  
no critical external dependencies, no data collection, and an infrastructure  
designed to operate sustainably on shared hosting.

Due to shared hosting constraints,  
server entry points may be physically located  
within the site layer while remaining  
strictly protected by server-level access rules.

The separation presented in this repository is logical and functional.  
It does not necessarily reflect the exact physical deployment,  
which may vary depending on hosting constraints.

The website is currently deployed in production at [Sereona](https://sereona.fr)

---

## Principles and Goals

The project was designed around clear principles:  

- total autonomy of the infrastructure  
- no unnecessary third-party services  
- no reliance on CMS or server frameworks  
- no user data collection  
- long-term stability and maintainability  
- attack surface minimized to the bare minimum

The goal was not to maximize technical complexity, but to build a robust, readable,  
and predictable system capable of functioning reliably without constant supervision.

---

## General Architecture

The project is structured around three distinct subsystems, deliberately separated by role and exposure level.
This organization helps minimize the attack surface, clarify responsibilities, and ensure simple long-term maintenance.

The architecture is based on the following blocks:  

- `sereona.fr/`: public static site, including protected server entry points  
- `core/`: minimal exposed technical layer (server entry point, access rules)  
- `assistant-node/`: asynchronous internal processing (bot, automation, maintenance)

Each subsystem is logically independent but interacts in a controlled manner with the others.

---

## Project Structure

```
sereona/
├── core/
│    │
│    ├── data_counter.json             → Invoice numbering counter
│    ├── data_tokens.json              → Temporary download tokens
│    ├── data_config.json              → Configuration file (anonymized)
│    ├── logs_reviews.json             → Review submission logs
│    ├── engine_logs_cleaner.py        → Log cleanup script
│    ├── engine_reviews_cleaner.py     → Review cleanup script
│    ├── engine_emails_cleaner.py      → Email cleanup script
│    ├── data_senders.json             → Email auto-reply tracker
│    ├── data_stripe.json              → Stripe duplicate prevention
│    ├── engine_downloads_cleaner.php  → Download log cleanup
│    ├── engine_tokens_cleaner.php     → Expired token cleanup
│    ├── core_pdf.php                  → PDF generation functions
│    ├── core_html.php                 → HTML utility functions
│    ├── core_mail.php                 → Automatic invoice email delivery
│    ├── core_engine.php               → Counter-related functions
│    │
│    ├── renderer/                     → PDF generation library
│    ├── invoices/                     → Generated invoices
│    ├── revenues/                     → Revenue data
│    ├── logs/                         → Error logs
│    └── docs/
│        ├── GUIDE_CORE.md             → Project and architecture overview
│        ├── SYSTEM.md                 → General overview of the project and its architecture
│        ├── OPERATIONS.md             → Operations and usage guide
│        └── OVERVIEW.md               → System overview
│
├── assistant-node/
│    ├── engine_main.py                → Worker entry point (cron / PHP trigger)
│    ├── engine_core.py                → Main worker logic
│    ├── engine_reply.py               → Automated processing
│    ├── engine_purge.py               → Log maintenance
│    ├── bridge.php                    → PHP → Python bridge
│    ├── data/                         → Worker data source
│    └── tmp/                          → Control / state file
│
└── web/
     ├── pdf/
     │    ├── template_document.html   → Invoice HTML template
     │    ├── success.html             → Page displayed after successful payment
     │    └── cancel.html              → Page displayed after canceled payment
     │
     ├── assets/                       → Stylesheets (optional external)
     ├── pages/                        → Website pages (articles and content)
     ├── images/                       → Website images (including logos and favicons)
     ├── tmp/                          → Control / state file
     ├── dl/                           → Store
     │
     ├── LICENCE.md                    → Terms of use and legal framework
     │
     ├── site.webmanifest              → Website PWA manifest
     ├── index.html                    → Homepage
     ├── gateway.php                   → Stripe payment webhook
     ├── reviews.php                   → Review submission handler
     ├── download.php                  → Download entry point
     ├── checkout.php                  → Stripe payment initialization
     ├── robots.txt                    → Search engine indexing rules
     ├── sitemap.xml                   → Sitemap for indexing
     ├── hero_loader.js                → Weekly content initialization script
     ├── messages-2025.js              → Weekly data – year 2025
     └── messages-2026.js              → Weekly data – year 2026
```


---

### `sereona.fr/` — Public Layer

This folder contains exclusively the public site: [Sereona](https://sereona.fr)

It is a static website composed of independent HTML files, accompanied by lightweight CSS stylesheets and JavaScript scripts.  
No critical server-side logic is exposed from this layer.

The public site is the only point of contact with the browser.  
It does not store any sensitive data and does not depend on any external services.

Although certain server-side scripts are physically located within this directory,  
they are never directly accessible and are strictly protected by server-level rules.

---

### `core/` — Minimal Exposed Technical Layer

This folder contains the entire private server logic:  
processing scripts, PHP libraries, JSON data,  
generated invoices and internal logs.

It is never publicly exposed and is only accessible  
internally, from scripts and scheduled tasks.

---

### `assistant-node/` — Internal Automation

The `assistant-node/` folder contains internal processes that run in the background, with no public exposure.  
These scripts are triggered solely via scheduled tasks or internal server calls.

They handle, among other things:  

- automation of specific operations  
- log maintenance  
- temporary file cleanup  
- management of locally stored internal data

This choice eliminates the need for an exposed backend and maintains a silent, controlled architecture.

---

## 1. Static Public Site

The public site is built on a deliberately simple and lightweight architecture,  
entirely composed of independent HTML files.  
No CMS, no frameworks, no builders, and no CDNs are used.  
Each page is designed as an autonomous, stable, and reusable unit.

### Key Features

- Fully static site (HTML + standalone CSS)  
- No critical external dependencies  
- Fast and smooth navigation  
- Design focused on visual comfort  
- Simple and predictable structure  
- Exportable and reusable files without adaptation

This choice ensures a fast, robust, and easy-to-maintain site,  
with an extremely reduced risk of failure.

The site also includes lightweight dynamic display scripts,  
allowing certain content to evolve periodically without any backend or client-side storage.

Stylesheets can be integrated autonomously or optionally externalized,  
with no critical dependency on external loading.

---

## 2. Internal Automation (Worker)

The project incorporates an internal automation system, without exposing any application backend to the public.
No Python server is accessible from the outside (no web framework,  
no public APIs, no persistent runtime). All processes are executed exclusively internally.

### How It Works

- Python scripts executed via scheduled tasks (Cron)  
- Controlled triggers from the server  
- Data stored locally in JSON format  
- No unnecessary outgoing communication  
- No direct network exposure

This choice allows for a silent, controlled, and compliant architecture,  
while ensuring the project’s automation and maintenance needs are met.  
The absence of an exposed backend significantly reduces the attack surface and simplifies long-term monitoring.

---

## 2 bis. Internal Assistant and Response Engine

The project integrates an internal assistant designed to guide users and answer targeted questions,  
without exposing complex application logic on the public side.

This assistant relies on an autonomous response engine, implemented in Python and powered by a locally structured JSON database.  
It analyzes incoming queries, identifies keyword and category matches, and then returns tailored responses.

### How It Works

- Response engine executed server-side  
- Deterministic and controlled logic  
- No reliance on external AI services  
- No collection or storage of personal data  
- Local logging of unrecognized queries  
- Traceability of technical errors for maintenance purposes

The assistant does not provide medical advice and is strictly limited to informational and guiding content,  
in line with the project’s scope.  
This choice offers contextual assistance while maintaining a sober,  
predictable architecture that respects security and compliance constraints.

---

## 3. Payment, Invoicing, and Distribution

The project integrates a payment and distribution system fully managed server-side,  
without relying on any external automation intermediaries.
Payments are initiated via a dedicated provider and then processed by internal scripts triggered by events.  
No sensitive data is stored on the public site.

### General Pipeline

- Payment triggered via a dedicated page  
- Reception and processing of server events  
- Automatic generation of invoices in PDF format  
- Assignment of a unique, sequential invoice number  
- Automatic filing of documents by year and month  
- Preparation of secure download access

The entire process is automated and does not depend on any third-party orchestration platform.

### Secure File Distribution

The distribution of digital files relies on a dedicated internal engine,  
designed to avoid any direct exposure of resources.  
Files are never accessible via public URLs. Access is conditioned on temporary one-time use links,  
generated dynamically after server-side validation.

The implemented controls include, notably:  

- Automatic expiration of access  
- Validation of the download context  
- Access integrity checks  
- Immediate invalidation after use  
- Timestamped logging of actual accesses

The system also distinguishes between a simple link consultation and an actual download,  
with notifications sent to the administration side.
The entire mechanism works without third-party services and without exposing any sensitive logic on the public site.

---

## 4. Security and Structural Protection

The project’s security relies primarily on simple and strict structural choices rather than the accumulation of external solutions.  
The architecture has been deliberately designed to minimize the attack surface and reduce exploitable entry points.

### Implemented Measures

- Strict separation between the public site and server-side logic  
- Enhanced access rules at the server level  
- Complete deactivation of directory listing  
- Protection of sensitive files (data, scripts, logs)  
- Critical zones made inaccessible by default  
- No direct URLs to private resources

File names, endpoints, and sensitive data have been deliberately abstracted to limit opportunistic attacks and automated scanning.  
This approach prioritizes simplicity, readability, and long-term passive security.

---

## 5. GDPR Compliance and Data Minimization

The project was designed from the outset with a maximal data minimization approach and regulatory compliance in mind.  
No personal data is collected for tracking, analysis, or profiling purposes.  
The site does not rely on any tracking mechanisms.

### Applied Principles

- No cookies used  
- No third-party trackers or pixels  
- No external analytics tools  
- No local storage on the browser side  
- No user accounts  
- No data collection for marketing purposes

The only data handled by the system is strictly functional, time-limited, and stored locally on the server side.  
This approach ensures native GDPR compliance, without intrusive banners or consent management,  
while respecting the principle of data minimization.

---

## 6. Technical Choices and Sustainability

The technical choices made in this project were guided by the goal of sustainability rather than the pursuit of complexity or novelty.

The architecture does not rely on any server frameworks, persistent application runtimes, or heavy dependencies.  
The components used are deliberately simple, stable, and proven.

### Chosen Principles

- No CMS or server frameworks  
- No dependencies that need continuous maintenance  
- Use of simple, sustainable formats (HTML, JSON, Python)  
- Readable and auditable application logic  
- Compatibility with standard shared hosting environments

This choice drastically reduces maintenance needs, avoids disruptions from updates, and ensures maximum stability in the long term.  
The goal is not technical sophistication, but reliability, predictability, and full control over the system.

---

## Security Notes and Disclosure

This repository provides an accurate view of the project’s logical architecture while respecting responsible disclosure principles.  
Certain file names, endpoints, and structures have been deliberately abstracted or modified to limit any direct exploitation.  
No keys, secrets, real data, or sensitive production paths are present in this repository.  
The exposed structure aims to document technical choices and system organization, without reproducing the exact production environment.

---

## Communication and Automated Maintenance

The project integrates communication mechanisms that are intentionally limited and controlled.

Email notifications are used solely to confirm the receipt of messages or to notify of important technical events.  
User responses are handled manually, by choice, to preserve human interaction.

Additionally, internal scripts ensure automated maintenance:  

- Regular cleanup of temporary files  
- Purging of expired logs and data  
- Maintaining a clean and stable environment over time

---

## Conclusion

This project demonstrates the design of a complete, autonomous, and secure website,  
without relying on external platforms and without unnecessary backend exposure.

It shows that a simple, well-thought-out, and controlled architecture  
can meet real-world needs while remaining sustainable, performant, and compliant.

The system was designed to be reliable in the long term,  
requiring minimal maintenance and reducing the attack surface.

---

© Palks Studio — see LICENSE.md  
- https://palks-studio.com
