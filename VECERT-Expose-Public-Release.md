# VECERT Exposed: A One-Man Miami LLC Selling Scraped Cybercrime Data Through an Unauthenticated API

**Published:** February 22, 2026
**Investigation Type:** Open Source Intelligence (OSINT)
**Classification:** PUBLIC RELEASE

---

## Executive Summary

**VECERT Security Labs USA LLC** presents itself as a professional cyber threat intelligence (CTI) firm. In reality, it is a **sole proprietorship run by Gerardo J. Gil** out of a commercial mail suite in Doral, Florida. The LLC was filed **four months ago**, has **no federal tax ID**, and operates from two separate addresses — neither of which appears to be a real office.

VECERT scrapes criminal marketplace forums (BreachForums, XSS.is, LeakBase, DarkForums.st, Cracked) and repackages the data through a flashy dashboard at `analyzer.vecert.io`. They sell API access to this data while simultaneously distributing **583,000+ records** of scraped criminal forum content — including stealer logs, breach catalogs, and user databases — through **seven public GitHub repositories**.

Their source code contains **hardcoded stolen session credentials** for ProtonMail and DarkEye.org, embedded authentication tokens from the XSS.is cybercrime forum, and a Python pickle object revealing they monitored **Venezuela's National Electoral Council** (`cne.gob.ve`) for credential leaks.

Their backend API at `vecertapi.com` requires **zero authentication** — anyone can dump their entire 403,927+ post database without an API key.

This report documents the full findings.

---

## Table of Contents

1. [The Entity](#1-the-entity)
2. [The Operator](#2-the-operator)
3. [The Addresses](#3-the-addresses)
4. [The Platform](#4-the-platform)
5. [The Code — Hardcoded Stolen Credentials](#5-the-code--hardcoded-stolen-credentials)
6. [The Data — What VECERT Distributes on GitHub](#6-the-data--what-vecert-distributes-on-github)
7. [The Open API](#7-the-open-api)
8. [The Pickle — Venezuela's Electoral Council](#8-the-pickle--venezuelas-electoral-council)
9. [Timeline](#9-timeline)
10. [Indicators & Red Flags](#10-indicators--red-flags)
11. [Recommendations](#11-recommendations)
12. [Sources & Evidence](#12-sources--evidence)

---

## 1. The Entity

| Field | Value |
|-------|-------|
| **Registered Name** | VECERT SECURITY LABS USA LLC |
| **FL Document Number** | L25000472380 |
| **Date Filed** | October 15, 2025 |
| **Status** | ACTIVE |
| **State** | Florida |
| **FEI/EIN** | **NONE** — no federal tax ID has been registered |
| **Annual Reports** | None filed |

The entity name on the Florida filing ("VECERT SECURITY LABS USA LLC") does not match the branding used on GitHub ("VECERTUSA", "VECERTINC") or the website ("VECERT Digital Intelligence and Security"). The name is constructed as **VE** (Venezuela) + **CERT** (Computer Emergency Response Team), designed to mimic an official government cybersecurity body. **VECERT has no affiliation with any government.**

Source: [Florida Division of Corporations — Sunbiz](https://search.sunbiz.org)

---

## 2. The Operator

| Field | Value |
|-------|-------|
| **Registered Agent** | GIL, GERARDO J |
| **Title** | MGR (Manager — sole) |
| **Address** | 4711 NW 79TH AVE, SUITE 2B, DORAL, FL 33166 |

Gerardo J. Gil is the **sole manager, sole registered agent, and sole authorized person** listed on the LLC filing. Despite VECERT's website claiming "a successful staff in investigations and cybersecurity," all evidence points to a one-person operation:

- All seven GitHub repositories have commits attributed to a single author: `VECERT <investigations@vecert.io>`
- No individual team members are named anywhere on the website, social media, or GitHub
- The LLC has a single authorized person
- Commit volume is minimal (2-6 commits per repo)

---

## 3. The Addresses

VECERT uses **two different addresses** across its online presence, neither of which appears to be a genuine operational office.

### Address 1: Florida LLC Filing
```
4711 NW 79TH AVE, SUITE 2B
DORAL, FL 33166
```

This is a multi-suite commercial building in Doral, Florida — a city nicknamed **"Doralzuela"** for its large Venezuelan diaspora population. The same building houses **MyFlorida Corp Paralegal Services** (Suite 5E), a company specializing in LLC formations and corporate filings. At least seven other businesses are registered at various suites in the same building, including Flash Cargo, Miami Print Shop, and School of ABA LLC. The address pattern is consistent with a **registered agent service or virtual office** rather than a physical workspace.

### Address 2: GitHub Profile
```
1621 BAY RD #601
MIAMI BEACH, FL 33139
```

This address, listed on the VECERTINC GitHub organization profile, is a unit in **Bayview Plaza** — a **residential condominium** overlooking Biscayne Bay in South Beach, Miami Beach. This is not a commercial office.

The use of two separate addresses — one residential condo and one commercial mail suite — with no evidence of a physical office at either location, is consistent with a virtual business presence.

---

## 4. The Platform

VECERT operates three web properties:

| URL | Purpose |
|-----|---------|
| `vecert.io` | Company landing page |
| `analyzer.vecert.io` | Threat intelligence dashboard with 3D globe visualization |
| `analyzer.vecert.io/forums` | Index of scraped breach forum posts |

### Tech Stack
- **Frontend:** HTML5/CSS3, Globe.gl (Three.js), Leaflet.js, Chart.js
- **Backend:** Python scraping tools, Cloudflare protection
- **API:** `vecertapi.com/api_analyzer` (unauthenticated — see Section 7)

### Tracking & Analytics
No Google Analytics, Google Tag Manager, Facebook Pixel, Hotjar, Mixpanel, or any other tracking/analytics scripts were detected on the site. No cookies are explicitly set. The only third-party service with visibility into visitor traffic is Cloudflare (bot protection).

### Code Quality Indicators
- CSS comments are written in Spanish (e.g., `/* CONTROLES DE VISTA */`, `/* PANELES LATERALES */`)
- No HTML meta tags, Open Graph tags, or SEO implementation
- Breach data is embedded as inline JavaScript objects rather than fetched from the API
- HTTP headers in the Intelx CLI tool include `Accept-Language: es-ES,es;q=0.9` (Spanish language preference)
- Git commit timestamps across all repositories use **UTC+8 timezone** (Asia-Pacific), inconsistent with Florida (UTC-5)

---

## 5. The Code — Hardcoded Stolen Credentials

Analysis of VECERT's seven public GitHub repositories revealed **hardcoded authentication credentials** in two separate tools. These appear to be stolen or reverse-engineered session tokens from third-party services.

### 5.1 ProtonMail Session Tokens (DarkForumCTI)

The file `search.py` in the [DarkForumCTI](https://github.com/VECERTUSA/DarkForumCTI) repository contains hardcoded ProtonMail API authentication headers:

| Credential | Value |
|------------|-------|
| ProtonMail UID (`x-pm-uid`) | `upyulffn33faq6mnwkuzz7q2qjqekalz` |
| Session Cookie (`AUTH-<uid>`) | `y6iuhnkaogtvhzjrfa4jf7xo2mykln6e` |
| Session ID | `aIYuoSom-KqULYafC086vAAAAQ8` |

These credentials are used to query ProtonMail's user availability API (`https://account.proton.me/api/core/v4/users/available`) to enumerate whether usernames have registered ProtonMail accounts. The tool tests usernames against **19 privacy-focused email providers** including ProtonMail, Tutanota, Riseup, cock.li, and secmail.pro.

The presence of hardcoded session tokens for ProtonMail's authenticated API raises questions about how these credentials were obtained.

### 5.2 DarkEye.org Session Cookies (DarkEye)

The file `darkeye.py` in the [DarkEye](https://github.com/VECERTINC/DarkEye) repository contains a complete set of hardcoded session cookies:

```
__jsluid_s=b1ab69c55f648ec8067416c032051898
_ga=GA1.1.589433742.1735065866
ASSETS="gASVQAAAAAAAAAB9lIwEdXVpZJSMBFVVSUSUk5QpgZR9lIwDaW50lIoQT+6tIzu0UbmwSQVbq/nFfXNijApjbmUuZ29iLnZllHMu"
_ga_4W9GPBYZ2H=GS1.1.1735065866.1.1.1735066247.0.0.0
```

**Cookie analysis:**

| Cookie | Finding |
|--------|---------|
| `_ga` (Google Analytics) | First visit timestamp: **December 24, 2024, 18:44:26 UTC** (Christmas Eve) |
| `_ga_4W9GPBYZ2H` | Session lasted **6 minutes and 21 seconds** (18:44:26 to 18:50:47 UTC). Visit count: **1** (single session). |
| `__jsluid_s` | Jiasule CDN session ID — Jiasule is a **Chinese CDN/WAF provider**, confirming the DarkEye README note that the tool is "of Asian origin" |
| `ASSETS` | Base64-encoded **Python pickle data** — decoded and analyzed in Section 8 |

The DarkEye tool contains **no login logic**. It relies entirely on these hardcoded cookies for authentication, suggesting they were captured from a browser session and pasted directly into the source code.

### 5.3 XSS.is Forum Authentication Tokens (XSS.isCTI)

The scraped data file `xss_threads.csv` in the [XSS.isCTI](https://github.com/VECERTUSA/XSS.isCTI) repository contains **16,183 preview URLs** with embedded XenForo CSRF tokens in the format:

```
https://xss.is/threads/[id]/preview?_xfRequestUri=...&_xfToken=[timestamp],[hash]
```

These tokens indicate the scraping was performed from an **authenticated session on the XSS.is cybercrime forum** — meaning VECERT maintained an active account on a criminal marketplace to collect this data.

---

## 6. The Data — What VECERT Distributes on GitHub

VECERT's seven public GitHub repositories collectively contain **583,000+ records** of scraped cybercrime forum data, distributed freely while the company simultaneously sells API access to the same material.

### 6.1 Repository Inventory

| Repository | Org | Stars | Data Records | Content |
|------------|-----|-------|-------------|---------|
| [DarkForumCTI](https://github.com/VECERTUSA/DarkForumCTI) | VECERTUSA | 73 | 28,657 | DarkForums.st user directory (15,280 users), breach listings, exploit sales, combolists, cracked accounts |
| [LeakBaseCTI](https://github.com/VECERTUSA/LeakBaseCTI) | VECERTUSA | 67 | 134,652 | LeakBase.la member database (125,260 users), breach catalogs, stealer log indexes, private data sales |
| [XSS.isCTI](https://github.com/VECERTUSA/XSS.isCTI) | VECERTUSA | 38 | 16,183 | XSS.is Russian hacking forum — 20 years of posts (2004-2024), 33 MB with full post content, 70M cumulative views |
| [Analyzer_forums](https://github.com/VECERTUSA/Analyzer_forums) | VECERTUSA | 4 | 403,927+ (API) | CLI client for the unauthenticated VECERT API |
| [Intelx_CLI_Free_Version](https://github.com/VECERTINC/Intelx_CLI_Free_Version) | VECERTINC | 2 | — | IntelX API wrapper for searching leak databases |
| [leakix](https://github.com/VECERTINC/leakix) | VECERTINC | 2 | — | LeakIX vulnerability scanner wrapper (75+ detection plugins) |
| [DarkEye](https://github.com/VECERTINC/DarkEye) | VECERTINC | — | — | DarkEye dark web monitoring wrapper with hardcoded stolen cookies |

### 6.2 Data Breakdown by Category

#### DarkForumCTI — 10 data files, 3.3 MB total

| File | Records | Content |
|------|---------|---------|
| `darkforums_users.json` | 15,280 | Complete user directory of DarkForums.st with profile URLs |
| `leaks_output.csv` | 1,922 | Breach announcements — government databases, corporate breaches, identity documents |
| `sellers_output.csv` | 1,227 | Active sales listings — zero-day exploits, stolen databases, hacking services |
| `forum53_threads.csv` | 5,807 | Archived threads — combolists sorted by country, stealer logs |
| `darkforums_market.csv` | 1,450 | Marketplace listings — exploits, DDoS services, government data |
| `combolist.csv` | 668 | Credential combo sales — URL:Login:Pass stealer output, police credentials |
| `cracked accounts.csv` | 636 | Cracked service accounts — Netflix, VPN, PayPal, CashApp |
| `other leaks.csv` | 653 | Identity documents, CCTV access, ransomware, defense contractor leaks |
| `leads market.csv` | 184 | Lead databases — crypto depositors, casino players, IBAN data |
| `buyerplace.csv` | 201 | Purchase requests for stolen data and hacking services |

**Notable entities referenced in post titles:** US House of Representatives, FBI, CIA, DOD, French Naval Group (~1TB internal data), Central Bank of Venezuela, Italian Government Banking Network, Indian military, Taiwan military, Rheinmetall Defence, Boeing, Iranian banks, UAE/Dubai police, Shanghai Police (1 billion citizens), and government entities across 50+ countries.

#### LeakBaseCTI — 5 data files, 9.1 MB total

| File | Records | Content |
|------|---------|---------|
| `leakbase_users.csv` | 125,260 | Complete LeakBase.la membership database — usernames, IDs, profile URLs, message counts, reputation scores |
| `big_leaks.csv` | 2,738 | Major breach announcements — 5.2M views across threads |
| `stealer.csv` | 3,818 | Credential stealer log indexes — LummaC2 output, URL:Login:Pass collections. Individual posts advertise volumes of 94M lines, 276M lines, and "5.3 Billions" of entries |
| `private.csv` | 1,783 | High-value data requests — NATO/Turkish military data, government email access, national ID databases |
| `chuki.csv` | 969 | Archive of forum founder "Chucky" (User ID 1) — 948 threads of breach posts including Dell CRM (3.5GB), Samsung (435K records), Nintendo (311K), Facebook (1.7M) |

**Key actor documented:** "Chucky" (User ID 1), the LeakBase forum founder, with 1,286+ threads across the dataset — representing the single most prolific distributor of breach data on the platform.

#### XSS.isCTI — 1 data file, 33 MB

| File | Records | Content |
|------|---------|---------|
| `xss_threads.csv` | 16,183 | Complete archive of the XSS.is Russian cybercrime forum spanning 2004-2024. **7,097 unique authors**. **70 million cumulative views**. **248,155 total replies**. Contains full post body text including exploit code, tool configurations, and breach data previews. |

**Language breakdown:** 36.5% Russian, 28.8% English, 34.5% mixed.

**Content categories:** Malware/RAT distribution (849 threads), exploit trading (815), credential sales (1,384), database breaches (445), DDoS/botnets (259), ransomware operations (130), carding/financial fraud (229).

**Malware families referenced:** Cobalt Strike (72 mentions), REvil (44), Cerberus (42), Redline Stealer (27), Zeus (24), LockBit (12), ALPHV/BlackCat (14), Conti (18), Emotet (6), and 30+ others.

**Top poster:** "INC." with 737 threads (4.6% of all content).

### 6.3 Doxing Capabilities

Multiple VECERT tools automatically generate doxing links for searched usernames:
- **IDCrawl:** `https://www.idcrawl.com/u/{username}` — people search/identity lookup
- **WhatsMyName:** Username enumeration across platforms
- **Social-Searcher:** Social media search
- **BreachForums:** Direct profile lookup
- **Telegram:** Profile linking

The XSS.isCTI tool generates interactive D3.js network graphs visualizing relationships between forum users — effectively mapping criminal networks with one-click doxing integration.

### 6.4 Email Enumeration

The DarkForumCTI and LeakBaseCTI tools include email existence validators that test usernames against **19 privacy-focused email providers:**

ProtonMail (protonmail.com, protonmail.ch, pm.me, proton.me), Tutanota (tutanota.com, tuta.io, tutanota.de), Riseup (riseup.net), Mailfence, cock.li, safe-mail.net, guerrillamail.com, disroot.org, secmail.pro, plus standard providers (Outlook, Hotmail, Yahoo, Yandex).

This uses the X/Twitter email availability API (`https://api.x.com/i/users/email_available.json`) and ProtonMail's user check API to verify which email addresses are active.

---

## 7. The Open API

VECERT's analyzer backend at `https://vecertapi.com/api_analyzer` requires **no authentication whatsoever**. The Analyzer_forums CLI client confirms:

| Parameter | Description |
|-----------|-------------|
| `title` | Keyword search across post titles |
| `author` | Filter by threat actor name |
| `posted_date` | Filter by date (YYYY-MM-DD or YYYY-MM) |
| `page` | Pagination |
| `per_page` | Results per page (max 100) |

- **No API key required**
- **No rate limiting observed**
- **No authentication headers**
- **User-Agent:** `VECERT-CLI/2.1`
- **Total indexed posts:** 403,927+
- **Dominant source:** Cracked forum (263,016 posts / 65%)

The CLI tool includes an export function that **automatically paginates through the entire database** and dumps everything to CSV. Anyone with `curl` or `requests` can replicate this.

VECERT offers paid "API Plans" through their website for access to this data — while the API itself is completely open.

---

## 8. The Pickle — Venezuela's Electoral Council

The `ASSETS` cookie in the DarkEye tool contains a base64-encoded **Python pickle object**. Safe disassembly (using `pickletools.dis()`, without executing the pickle) reveals:

```
Pickle Protocol: 4
Structure: Python dict
Content: {uuid.UUID("7dc5f9ab-5b05-49b0-b951-b43b23adee4f"): "cne.gob.ve"}
```

**`cne.gob.ve` is the website of Venezuela's Consejo Nacional Electoral (National Electoral Council)** — the government body responsible for administering elections in Venezuela.

The DarkEye `ASSETS` cookie stores registered monitoring targets. This pickle proves the VECERT operator used DarkEye to scan Venezuela's electoral authority for:
- Employee credential leaks
- Compromised/controlled systems
- Dark web mentions
- Attack surface exposure
- User credential leaks

Combined with the Google Analytics timestamps showing this session occurred on **December 24, 2024** (Christmas Eve) and lasted only **6 minutes**, the picture is clear: the operator briefly visited DarkEye.org, ran a query against Venezuela's election infrastructure, then hardcoded the entire browser session — cookies and all — into a Python script and published it to GitHub.

---

## 9. Timeline

| Date | Event |
|------|-------|
| **December 23, 2024** | VECERTINC GitHub repos created (Intelx_CLI, leakix) |
| **December 24, 2024** | DarkEye.org session (6 minutes) — queried `cne.gob.ve`. Cookies hardcoded into source code |
| **July 28-29, 2025** | DarkForumCTI and XSS.isCTI repos published (6 commits each, all same day) |
| **August 8, 2025** | LeakBaseCTI published (2 commits) |
| **October 14, 2025** | Analyzer_forums published (4 commits within 4 minutes) |
| **October 15, 2025** | **VECERT SECURITY LABS USA LLC** filed with Florida Division of Corporations |
| **February 22, 2026** | This investigation published |

Notable: The GitHub repositories existed for **months** before the LLC was even filed. The operation was running on public GitHub with no legal entity behind it until October 2025.

---

## 10. Indicators & Red Flags

| Indicator | Detail |
|-----------|--------|
| LLC filed 4 months ago | October 2025, no annual reports, no EIN |
| Two different addresses | Miami Beach condo (GitHub) vs. Doral mail suite (LLC) |
| Sole operator | Gerardo J. Gil is the only person on the filing |
| Git timestamps in UTC+8 | Asia-Pacific timezone, not US Eastern |
| Spanish code comments | CSS comments in Spanish throughout the frontend |
| `es-ES` Accept-Language | Spanish language headers in API wrapper tools |
| Hardcoded ProtonMail tokens | Stolen or reverse-engineered session credentials in source code |
| Hardcoded DarkEye cookies | Complete browser session pasted into Python script |
| XSS.is auth tokens in data | Scraping was done from an authenticated crime forum account |
| `cne.gob.ve` in pickle | Monitored Venezuela's election authority for credential leaks |
| Unauthenticated API | 403K+ posts accessible without any API key |
| Sells what's free | Offers paid API plans while the API requires zero authentication |
| Ships criminal data | 583K+ records of dark forum content on public GitHub |
| No tracking/analytics | Zero analytics on their own site |
| Mimics government CERT | Name designed to look like an official Computer Emergency Response Team |

---

## 11. Recommendations

### For GitHub
All seven VECERT repositories should be reviewed for violations of GitHub's Terms of Service and Acceptable Use Policies, specifically:
- Distribution of scraped PII (125,260 user profiles with identifiers)
- Hosting indexes of stolen credentials and stealer log references
- Hardcoded stolen authentication tokens for third-party services
- Distribution of data scraped from criminal forums using authenticated sessions

### For ProtonMail / Proton AG
The hardcoded session credentials in DarkForumCTI should be investigated:
- UID: `upyulffn33faq6mnwkuzz7q2qjqekalz`
- Session: `y6iuhnkaogtvhzjrfa4jf7xo2mykln6e`
- These are being used for automated email enumeration against ProtonMail's API

### For DarkEye.org
The hardcoded session cookies in the DarkEye repository should be revoked and the associated account investigated.

### For Security Researchers
- The unauthenticated API at `vecertapi.com/api_analyzer` exposes 403,927+ indexed posts from criminal forums
- The XSS.isCTI dataset contains embedded XSS.is authentication tokens that may still be valid
- The DarkEye ASSETS pickle contains a UUID that may map to an active DarkEye account

### For Journalists
- Florida LLC records for VECERT SECURITY LABS USA LLC are publicly searchable at [sunbiz.org](https://search.sunbiz.org)
- The sole operator Gerardo J. Gil has no other publicly visible cybersecurity presence
- The operation has been running on GitHub since December 2024 with no legal entity until October 2025

---

## 12. Sources & Evidence

### Florida State Records
- Florida Division of Corporations — VECERT SECURITY LABS USA LLC (Document# L25000472380)
- Searchable at: https://search.sunbiz.org

### VECERT Web Properties
- Main site: https://vecert.io
- Analyzer dashboard: https://analyzer.vecert.io
- Intelligence forums: https://analyzer.vecert.io/forums
- API endpoint: https://vecertapi.com/api_analyzer

### GitHub Repositories
- https://github.com/VECERTUSA (4 repos, GitHub User ID 223159119)
- https://github.com/VECERTINC (3 repos)
- https://github.com/VECERTUSA/DarkForumCTI
- https://github.com/VECERTUSA/LeakBaseCTI
- https://github.com/VECERTUSA/XSS.isCTI
- https://github.com/VECERTUSA/Analyzer_forums
- https://github.com/VECERTINC/Intelx_CLI_Free_Version
- https://github.com/VECERTINC/leakix
- https://github.com/VECERTINC/DarkEye

### Social Media
- X/Twitter: https://x.com/VECERTAmerica
- X/Twitter (Analyzer): https://x.com/VECERTRadar
- Medium: https://medium.com/@vecert
- YouTube: @VECERTlabs

### Contact Emails (from vecert.io)
- general@vecert.io, labs@vecert.io, help@vecert.io, marketing@vecert.io, reports@vecert.io, info@vecert.io, contact@vecert.io, investigations@vecert.io

### Address Verification
- Bayview Plaza Condos (1621 Bay Road, Miami Beach): https://www.miamicondolifestyle.com/bayview-plaza.php
- MyFlorida Corp Paralegal Services (4711 NW 79th Ave, Doral): https://www.yelp.com/biz/myflorida-corp-paralegal-services-miami

### Cashea Breach References (Post #3466 on VECERT analyzer)
- VECERT alert — Gordon Freeman / Cashea: https://x.com/VECERTRadar/status/2017417296357020129
- VECERT alert — BlackHex Brotherhood / Cashea: https://x.com/VECERTRadar/status/2004194602119491892
- Bancrecer Venezuela breach: https://x.com/VECERTRadar/status/2021794473026400522
- Cashea Bloomberg profile: https://www.bloomberg.com/news/articles/2025-08-07/buy-now-pay-later-app-cashea-is-unlikely-player-on-caracas-exchange

### Threat Actor References
- malconguerra2 — Peru Court leak: https://x.com/Alph4_Sec/status/1975446894412206153
- malconguerra2 — UNMSM leak: https://x.com/Alph4_Sec/status/2015599248738255312

---

*This investigation was conducted entirely using open source intelligence (OSINT) methods. All data referenced is from publicly accessible sources including Florida state records, public GitHub repositories, and publicly available websites. No systems were accessed without authorization.*
