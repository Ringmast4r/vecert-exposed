# VECERT — Complete Intelligence Report

**Date:** 2026-02-22
**Subject:** Full OSINT workup on VECERT platform, operators, code, and the Cashea breach post (#3466)

---

## TL;DR

VECERT is a **one-man operation** run by **Gerardo J. Gil**, a Venezuelan national in Doral, FL ("Doralzuela"). The LLC was filed **October 2025** — 4 months old, no EIN, no annual reports. The registered address is a multi-suite commercial building in Doral that houses a paralegal/LLC formation service. Their GitHub repos contain **hardcoded stolen session tokens** (ProtonMail, DarkEye), ship **pre-scraped criminal marketplace data** (200K+ forum posts, 125K user profiles, stealer logs), and have an **unauthenticated API** anyone can dump. The site has zero tracking/analytics. The Cashea breach involves 3 separate actors and the "79 million records" claim is likely inflated.

---

## 1. Site Architecture

| URL | Purpose |
|-----|---------|
| **vecert.io** | Company landing page — about, contact, services |
| **analyzer.vecert.io** | Threat intel dashboard — live globe map, breach tracker |
| **analyzer.vecert.io/forums** | Breach forum post index (where post #3466 lives) |
| **vecertapi.com** | Backend API — `https://vecertapi.com/api_analyzer` |

All same organization. `analyzer` is a subdomain. Nav bar: Live Map > Forums > Archive Leaks > Defacement > API Plan.

---

## 2. Who Runs It — CONFIRMED IDENTITY

### Florida Division of Corporations Filing

| Field | Value |
|-------|-------|
| **Entity Name** | VECERT SECURITY LABS USA LLC |
| **Document Number** | L25000472380 |
| **Date Filed** | October 15, 2025 |
| **Effective Date** | October 14, 2025 |
| **State** | FL |
| **Status** | ACTIVE |
| **FEI/EIN** | **NONE** — no federal tax ID registered |
| **Annual Reports** | None filed |

### Principal & Mailing Address
```
4711 NW 79TH AVE
SUITE 2B
DORAL, FL 33166
```

### Registered Agent & Manager (Sole)
```
GIL, GERARDO J
4711 NW 79TH AVE
SUITE 2B
DORAL, FL 33166
Title: MGR (Manager)
```

### Address Analysis
- **4711 NW 79TH AVE, Doral** is a multi-suite commercial building
- Houses **MyFlorida Corp Paralegal Services** (Suite 5E) — a company that does LLC formations, corporate filings, immigration services
- Also houses: Flash Cargo (Suite 1A), Miami Print Shop (Suite 8H-9I), School of ABA LLC (Suite 20T), and 7+ other registered entities in Suite 3C alone
- **Strong indicator this is a virtual office or paralegal-provided address** — not a real operational office
- **Different from the GitHub address** (1621 Bay Rd #601, Miami Beach / Bayview Plaza condo)
- Two addresses used: Bayview Plaza condo (Miami Beach) on GitHub, Doral commercial suite on LLC filing

### Assessment
- **Gerardo J. Gil** is the sole person behind VECERT
- **Venezuelan national in Doral, FL** — Doral is nicknamed "Doralzuela" for its massive Venezuelan diaspora
- **VE** (Venezuela) + **CERT** (Computer Emergency Response Team) = VECERT
- Name designed to sound like an official government CERT — **not affiliated with any government**
- LLC is **4 months old**, no tax ID, no annual reports — barely a real business
- Entity name "VECERT SECURITY LABS USA LLC" differs from GitHub branding ("VECERTINC", "VECERTUSA")
- Self-describes as having "a successful staff in investigations and cybersecurity" — it's one guy

### Potential LinkedIn Match (UNCONFIRMED)
- Search results returned **Gerardo Xavier Yanes Gil** on LinkedIn — works at Bluecore Solutions, based in Venezuela
- In Hispanic naming: Gerardo (first) Xavier (middle) Yanes (paternal) Gil (maternal)
- Could be same person using maternal surname "Gil" as primary in the US (common pattern)
- **NOT confirmed** — the "J" middle initial doesn't cleanly match "Xavier"
- LinkedIn: https://www.linkedin.com/in/gerardo-xavier-yanes-gil-0a7141151/

### Contact Emails (from vecert.io)
| Email | Purpose |
|-------|---------|
| general@vecert.io | General inquiries |
| labs@vecert.io | Research/labs |
| help@vecert.io | Support |
| marketing@vecert.io | Marketing |
| reports@vecert.io | Threat reports |
| info@vecert.io | Info (from Analyzer_forums repo) |
| contact@vecert.io | Contact (from Analyzer_forums README) |
| investigations@vecert.io | Git commit author email |

### Social Media
| Platform | Handle | URL |
|----------|--------|-----|
| X (Company) | @VECERTAmerica | https://x.com/VECERTAmerica |
| X (Analyzer) | @VECERTRadar | https://x.com/VECERTRadar |
| YouTube | @VECERTlabs | — |
| Medium | @vecert | https://medium.com/@vecert |
| LinkedIn | Listed on site | No URL provided |

---

## 3. Tech Stack

### Frontend
| Tech | Usage |
|------|-------|
| HTML5/CSS3 | Custom dark theme, cyan (#22d3ee) accent, CSS variables |
| Globe.gl / Three.js | 3D globe visualization with cyber-pulse markers |
| Leaflet.js | 2D map fallback with CartoDB dark basemap tiles |
| Chart.js | Timeline and author analytics charts |
| Inline JavaScript | Heavy — breach data embedded as JS objects, not API-fetched |

### Backend
| Tech | Usage |
|------|-------|
| Python | All scraping/collection tools |
| Cloudflare | Bot protection, challenge script |
| API Server | `https://vecertapi.com/api_analyzer` — **NO AUTHENTICATION REQUIRED** |
| User-Agent | `VECERT-CLI/2.1` |

### Verdict: Semi-Vibe-Coded
- Heavy inline JS with data embedded in page rather than proper API calls
- CSS comments written in Spanish (`/* CONTROLES DE VISTA */`, `/* PANELES LATERALES */`)
- No meta tags, no Open Graph, no SEO
- No analytics or tracking whatsoever
- Backend Python scrapers are more polished than the frontend
- Works, but built fast without frontend discipline

---

## 4. Tracking & Analytics

| Tracker | Detected? |
|---------|-----------|
| Google Analytics (GA4/UA) | **NO** |
| Google Tag Manager | **NO** |
| Facebook Pixel | **NO** |
| Hotjar | **NO** |
| Mixpanel | **NO** |
| Any ad tracker | **NO** |
| Cookies | **NONE explicitly set** |

### What IS Present
- **Cloudflare challenge script** (`/cdn-cgi/challenge-platform/scripts/jsd/main.js`) — standard bot protection
- **CartoDB tile requests** — map tiles from cartocdn.com
- **GitHub raw content** — GeoJSON from github.com/nvkelso/natural-earth-vector

**Bottom line:** Zero tracking. Cloudflare is the only third-party with visibility.

---

## 5. GitHub Repos — Full Code Analysis

### Git Attribution
- **All commits signed as:** `VECERT <investigations@vecert.io>`
- **Commit timestamps:** UTC+8 timezone (Asia-Pacific) — interesting for a "Miami" company
- **No individual contributor names exposed in any repo**

### Organization: [VECERTUSA](https://github.com/VECERTUSA)
- **GitHub User ID:** 223159119
- **42 followers, 0 following**

### Organization: [VECERTINC](https://github.com/VECERTINC)
- **Address listed:** 1621 Bay Rd #601, Miami Beach
- **Website:** vecert.io

---

### 5.1 DarkForumCTI (VECERTUSA)
**Repo:** https://github.com/VECERTUSA/DarkForumCTI
**Stars:** 73 | **Forks:** 15 | **Commits:** 6 | **Last activity:** July 28-29, 2025

**What it does:** Interactive Python CLI for investigating DarkForums.st users. Searches users, checks email existence, generates OSINT doxing links.

**HARDCODED STOLEN CREDENTIALS:**
| Secret | Value |
|--------|-------|
| ProtonMail Session UID | `upyulffn33faq6mnwkuzz7q2qjqekalz` |
| ProtonMail Session Cookie | `y6iuhnkaogtvhzjrfa4jf7xo2mykln6e` |
| ProtonMail Session ID | `aIYuoSom-KqULYafC086vAAAAQ8` |

**Hardcoded API Endpoints:**
- ProtonMail check: `https://account.proton.me/api/core/v4/users/available`
- X/Twitter API: `https://api.x.com/i/users/email_available.json`
- DarkForums API: `https://darkforums.st/xmlhttp.php?action=get_users`
- DarkForums RSS: `https://darkforums.st/syndication.php`

**Email Enumeration:** Tests usernames against 19 privacy-focused email domains (ProtonMail, Tutanota, Riseup, cock.li, secmail.pro, etc.)

**OSINT Doxing Links Generated:**
- WhatsMyName, IDCrawl, Social-Searcher, BreachForums, Telegram profiles

**Shipped Data Files:**

| File | Records | Content |
|------|---------|---------|
| `darkforums_users.json` (1.6 MB) | ~31,700 users | Complete DarkForums user directory with profile URLs |
| `leaks_output.csv` (332 KB) | ~1,922 | Breach announcements catalog |
| `sellers_output.csv` (252 KB) | ~1,227 | Stolen data marketplace listings |
| `darkforums_market.csv` (174 KB) | ~1,450 | General marketplace posts |
| `forum53_threads.csv` (649 KB) | ~5,807 | Archived forum threads |
| `cracked accounts.csv` (73 KB) | ~636 | Cracked account listings |
| `combolist.csv` (74 KB) | ~668 | Credential combo sales |
| `buyerplace.csv` (22 KB) | ~201 | Buyer requests for stolen data |
| `leads market.csv` (22 KB) | ~184 | Leads for sale |
| `other leaks.csv` (72 KB) | ~653 | Miscellaneous leaks |
| **TOTAL** | **~44,448** | **3.3 MB of dark web marketplace data** |

**Notable listings in the data:**
- Naval Group (French defense contractor) — ~1TB internal data
- WinRAR RCE 0-day exploit for sale
- 10M bKash customer phone numbers
- Indian Citizens + Military data
- 400K Israel WhatsApp numbers
- USA Driver Licenses with SSN
- Binance/Coinbase databases
- Italian Government Banking Network access

---

### 5.2 LeakBaseCTI (VECERTUSA)
**Repo:** https://github.com/VECERTUSA/LeakBaseCTI
**Stars:** 67 | **Forks:** 17 | **Commits:** 2 | **Last activity:** Aug 8, 2025

**What it does:** Python CLI for searching LeakBase.la forum data. Same pattern — email enumeration, OSINT doxing links, CSV search.

**Hardcoded URLs:**
- LeakBase search: `https://bf.based.re/search/{username}`
- LeakBase profiles: `https://leakbase.la/members/{username}.{id}/`
- Same X/Twitter email API, WhatsMyName, IDCrawl, Social-Searcher, Telegram

**Shipped Data Files:**

| File | Records | Content |
|------|---------|---------|
| `leakbase_users.csv` (6.8 MB) | **125,260** | Complete LeakBase forum member database with usernames, IDs, avatar URLs, message counts, reaction scores |
| `big_leaks.csv` (690 KB) | 2,739 | Major breach announcements (Badoo 200M, Shein 30M, Instagram 17M, HHS.gov 6.5M doctors, Collections 2-5) |
| `chuki.csv` (218 KB) | 969 | Breach posts by admin "Chucky" (Dell CRM 3.5GB, Samsung 435K, Facebook 1.7M, Nintendo 311K, Malwarebytes 20K) |
| `private.csv` (462 KB) | 1,789 | Private/high-value data sales (MyKAD Malaysia 17M, NATO/Turkish military, Uruguay govt email access, German IBAN data) |
| `stealer.csv` (895 KB) | **3,895** | **Credential stealer logs** — URL:LOGIN:PASS format, LummaC2 stealer output, 70B+ free records advertised, 116M private credential combos |
| **TOTAL** | **134,652** | **9.1 MB of breach forum data** |

**Critical content:** The `stealer.csv` contains references to active malware (LummaC2) credential harvesting operations with daily fresh stealer log releases.

---

### 5.3 XSS.isCTI (VECERTUSA)
**Repo:** https://github.com/VECERTUSA/XSS.isCTI
**Stars:** 38 | **Forks:** 7 | **Commits:** 6 | **Last activity:** July 28, 2025

**What it does:** Python tool for searching a complete backup of the XSS.is Russian hacking forum. Generates D3.js network visualization graphs of actor relationships.

**Shipped Data:**
- `xss_threads.zip` (9.3 MB) → `xss_threads.csv` (33 MB uncompressed)
- **50,374 forum posts** from **7,125 unique authors** spanning **2005-2024**
- Primarily Russian-language cybercrime content
- 16,183 direct links to xss.is preserved in data

**Notable content in database:**
- Ransomware: Paradise source code, Chaos builder
- Malware: LummaC2, Android botnets, banking trojans
- Exploits: WordPress/Exchange 0-days for sale
- Financial crime: Singapore bank IBAN databases, Ghana bank access, Brazilian bank RCE
- Infrastructure: SS7 SMS interception, WiFi cracking tools
- 110+ references to anonfiles.com for distribution

**IDCrawl doxing integration:** Auto-generates `https://www.idcrawl.com/u/{username}` links for one-click doxing of forum users

**XSS vulnerability in code:** Unescaped user input from forum data injected directly into generated HTML files (lines 114, 121) — could execute JavaScript payloads from forum posts

**Top posters in dataset:** INC. (574 posts), Winux (252), tabac (195), Rehub (188)

---

### 5.4 Analyzer_forums (VECERTUSA)
**Repo:** https://github.com/VECERTUSA/Analyzer_forums
**Stars:** 4 | **Forks:** 2 | **Commits:** 4 | **Last activity:** Oct 14, 2025

**What it does:** The CLI client for the VECERT analyzer API — this is the engine behind `analyzer.vecert.io/forums`.

**API Details:**
| Field | Value |
|-------|-------|
| Base URL | `https://vecertapi.com/api_analyzer` |
| Authentication | **NONE — completely open** |
| User-Agent | `VECERT-CLI/2.1` |
| Timeout | 20 seconds |
| Max per page | 100 results |
| Total indexed | **403,927+ posts** |

**API Parameters:**
- `title` — keyword search
- `author` — threat actor filter
- `posted_date` — date filter (YYYY-MM-DD or YYYY-MM)
- `page` — pagination
- `per_page` — results per page (max 100)

**Forum sources indexed:**
| Forum | Posts | Percentage |
|-------|-------|-----------|
| Cracked | 263,016 | 65% |
| BreachForums | — | — |
| XSS.is | — | — |
| XForums | — | — |

**Critical finding:** The API requires NO authentication. Anyone can dump the entire 403,927+ post database by paginating through it. No API key, no rate limiting visible in client code.

**Export capability:** Can export current page or ALL pages automatically — loops through every page and dumps to CSV.

---

### 5.5 Intelx_CLI_Free_Version (VECERTINC)
**Repo:** https://github.com/VECERTINC/Intelx_CLI_Free_Version
**Stars:** 2 | **Forks:** 1 | **Commits:** 4 | **Created:** Dec 23, 2024

**What it does:** CLI wrapper for IntelX API (intelligence search engine). Searches leak databases including WikiLeaks, general leaks, and SciHub.

**API Endpoints:**
- Search: `https://2.intelx.io/intelligent/search`
- Results: `https://2.intelx.io/intelligent/search/result`
- Preview: `https://2.intelx.io/file/preview`
- Download: `https://2.intelx.io/file/read`

**Leak buckets searched:**
- `leaks.public.wikileaks`
- `leaks.public.general`
- `dumpster`
- `documents.public.scihub`

**Hardcoded headers reveal:**
- `Accept-Language: es-ES,es;q=0.9` — **Spanish language preference** confirms Spanish-speaking developer
- Spoofed Chrome 130 User-Agent on Windows 10
- API key stored in plaintext `API.txt` file

---

### 5.6 leakix (VECERTINC)
**Repo:** https://github.com/VECERTINC/leakix
**Stars:** 2 | **Forks:** 0 | **Commits:** 4 | **Created:** Dec 23, 2024

**What it does:** CLI wrapper for LeakIX.net vulnerability scanner API. Queries domains, subdomains, IPs with dorks and plugins.

**75+ vulnerability detection plugins** organized by access level:
- **Public:** Git config exposure, Docker registry, Elasticsearch, MongoDB, Redis, open SMB shares
- **Pro:** Apache ActiveMQ, Citrix ADC, FortiGate, Palo Alto, Juniper, Exchange Server, Confluence, Jira, SharePoint, Log4J

**API key stored in plaintext** `api_key.txt`

**Setup includes matrix rain animation** — unnecessary but characteristic of the "hacker aesthetic" branding

---

### 5.7 DarkEye (VECERTINC)
**Repo:** https://github.com/VECERTINC/DarkEye
**Stars:** — | **Forks:** — | **Commits:** 4

**What it does:** CLI wrapper for DarkEye.org dark web monitoring APIs. Searches for employee credential leaks, compromised systems, darkweb mentions, and attack surface exposure.

**HARDCODED STOLEN/COMPROMISED SESSION COOKIES:**
```
__jsluid_s=b1ab69c55f648ec8067416c032051898
_ga=GA1.1.589433742.1735065866
ASSETS="gASVQAAAAAAAAAB9lIwEdXVpZJSMBFVVSUSUk5QpgZR9lIwDaW50lIoQT+6tIzu0UbmwSQVbq/nFfXNijApjbmUuZ29iLnZllHMu"
_ga_4W9GPBYZ2H=GS1.1.1735065866.1.1.1735066247.0.0.0
```

**Critical issues:**
- Session cookies hardcoded in source — likely stolen or reverse-engineered from a compromised DarkEye account
- `_ga` cookie timestamps to **January 2025** — indicates when the account was accessed
- `ASSETS` cookie contains **Python pickle-encoded data** — could execute arbitrary code if unpickled
- Spoofed User-Agent includes `CrKey/1.54.248666` (Chromecast identifier — unusual fingerprint)
- **No login logic** — entirely dependent on hardcoded stolen cookies
- The tool references `cne.gob.ve` (Venezuelan National Electoral Council) in the ASSETS cookie data

**API Endpoints:**
- Asset creation: `https://darkeye.org/api/assets/assets/`
- Employee credentials: `https://darkeye.org/api/i/employee_credential_leak/`
- Compromised systems: `https://darkeye.org/api/i/controlled_system/`
- Dark web mentions: `https://darkeye.org/api/i/darkweb_mention/`
- Attack surface: `https://darkeye.org/api/i/attack_surface_exposure/`
- User credentials: `https://darkeye.org/api/i/user_credential_leak/`

---

## 6. Critical Security Findings Summary

### Hardcoded Secrets Found in Code

| Repo | Secret Type | Value/Detail |
|------|-------------|-------------|
| DarkForumCTI | ProtonMail UID | `upyulffn33faq6mnwkuzz7q2qjqekalz` |
| DarkForumCTI | ProtonMail Cookie | `y6iuhnkaogtvhzjrfa4jf7xo2mykln6e` |
| DarkForumCTI | ProtonMail Session | `aIYuoSom-KqULYafC086vAAAAQ8` |
| DarkEye | DarkEye Session | `b1ab69c55f648ec8067416c032051898` |
| DarkEye | Google Analytics ID | `GA1.1.589433742.1735065866` |
| DarkEye | Pickle-encoded ASSETS | Contains `cne.gob.ve` reference |

### Shipped Criminal Data Inventory

| Repo | Records | Size | Content |
|------|---------|------|---------|
| DarkForumCTI | ~44,448 | 3.3 MB | Dark forum users, breach listings, exploit sales, combolists, cracked accounts |
| LeakBaseCTI | ~134,652 | 9.1 MB | 125K user profiles, major breach catalogs, stealer logs, private data sales |
| XSS.isCTI | ~50,374 | 33 MB | 19 years of Russian hacking forum posts from 7,125 authors |
| Analyzer_forums | 403,927+ (API) | Remote | BreachForums, XSS, Cracked, XForums aggregated posts |
| **TOTAL** | **~633,401+** | **45+ MB** | — |

### Open/Unauthenticated API
- `https://vecertapi.com/api_analyzer` — **no auth required**, anyone can dump 403K+ posts

### Code Vulnerabilities
- XSS in XSS.isCTI HTML generation (unescaped forum content)
- Path traversal risk in filename generation
- No input validation across all tools
- No rate limiting on any API calls
- Plaintext API key storage in all tools

---

## 7. The Cashea Breach (Post #3466)

### Forum Post Details
| Field | Value |
|-------|-------|
| Post ID | #3466 |
| Title | CASHEA APP VENEZUELA 79 MILLION CONFIDENTIAL DATA |
| Size | 46.5 GB |
| Classification | UNCLASSIFIED |
| Actor | malconguerra2 |
| Country | Venezuela |
| Sector | Finance (BNPL / Buy-Now-Pay-Later) |

### What Is Cashea?
- Venezuelan fintech / Buy-Now-Pay-Later app
- Listed on Caracas stock exchange
- [Bloomberg coverage](https://www.bloomberg.com/news/articles/2025-08-07/buy-now-pay-later-app-cashea-is-unlikely-player-on-caracas-exchange)
- [Google Play listing](https://play.google.com/store/apps/details?id=com.cashea.app)

### Breach Timeline
| Date | Actor | Details |
|------|-------|---------|
| **2025-12-25** | HvckMvSoneria33 (BlackHex Brotherhood) | First disclosure. JSON data suggesting insecure API endpoints, payment metadata, mobile transaction data |
| **2026-01-30** | Gordon Freeman | Massive leak. 30,000+ records: bank accounts, phone numbers, emails, business names, tax IDs. Distributed via Telegram & BreachForums |
| **2026-02-??** | malconguerra2 | Post #3466. Claims 79M records, 46.5 GB. Likely repackaging or escalation of prior leaks |

### Threat Actor: malconguerra2
- Active LATAM-focused threat actor
- Known leaks: Peru courts (1.9 GB), UNMSM Peru (290K records)
- Primarily targets LATAM government and financial institutions
- The "79 million" claim is suspicious — likely inflated

### Threat Actor: HvckMvSoneria33 / BlackHex Brotherhood
- Original Cashea discloser (Dec 2025)
- Shared JSON data suggesting exposed API endpoints

### Threat Actor: Gordon Freeman
- Also responsible for Bancrecer Venezuela breach — 65,000 banking records (Feb 2026)
- Distributed Cashea data via Telegram and BreachForums

### Data Accessibility
**Not from VECERT.** The analyzer indexes breach posts but does NOT host leaked data. Actual dumps live on BreachForums, Telegram, and dark web paste sites.

---

## 8. Platform Stats
- **403,927+ posts** indexed in API database
- **3,372 threats** displayed on analyzer dashboard (281 pages)
- **Top targeted countries:** USA (525), France (390), Spain (191)
- **100+ new entries** in February 2026 alone
- **65% of indexed posts** come from Cracked forum (263,016 posts)

---

## 9. Key Indicators & Red Flags

| Indicator | Detail |
|-----------|--------|
| **LLC age** | 4 months (filed Oct 2025) |
| **Tax ID** | None |
| **Registered address** | Doral commercial suite at building with paralegal LLC services |
| **GitHub address** | Different — Miami Beach condo (Bayview Plaza) |
| **Commit timezone** | UTC+8 (Asia-Pacific, not US Eastern) |
| **Language indicators** | Spanish CSS comments, `es-ES` Accept-Language headers |
| **Hardcoded stolen tokens** | ProtonMail sessions, DarkEye sessions in source code |
| **Pickle data** | References `cne.gob.ve` (Venezuelan elections body) |
| **Criminal data shipped** | 633K+ records of dark forum posts, user databases, stealer logs |
| **Open API** | No authentication on 403K+ post database |
| **Sole operator** | Gerardo J. Gil — one person running everything |
| **GitHub branding** | "VECERTUSA" / "VECERTINC" — neither matches LLC name |

---

## 10. Sources

### Florida State Records
- Florida Division of Corporations — VECERT SECURITY LABS USA LLC (Doc# L25000472380)
- [Florida Sunbiz Search](https://search.sunbiz.org/Inquiry/CorporationSearch/ByName)

### VECERT Properties
- [VECERT Main Site](https://vecert.io)
- [VECERT Analyzer Dashboard](https://analyzer.vecert.io)
- [VECERT Intelligence Forums](https://analyzer.vecert.io/forums)
- [VECERT API](https://vecertapi.com/api_analyzer)
- [VECERTUSA GitHub](https://github.com/VECERTUSA)
- [VECERTINC GitHub](https://github.com/VECERTINC)
- [@VECERTAmerica on X](https://x.com/VECERTAmerica)
- [@VECERTRadar on X](https://x.com/VECERTRadar)
- [VECERT on Medium](https://medium.com/@vecert)

### GitHub Repos (Cloned Locally)
- [DarkForumCTI](https://github.com/VECERTUSA/DarkForumCTI) — 73 stars
- [LeakBaseCTI](https://github.com/VECERTUSA/LeakBaseCTI) — 67 stars
- [XSS.isCTI](https://github.com/VECERTUSA/XSS.isCTI) — 38 stars
- [Analyzer_forums](https://github.com/VECERTUSA/Analyzer_forums) — 4 stars
- [Intelx_CLI_Free_Version](https://github.com/VECERTINC/Intelx_CLI_Free_Version) — 2 stars
- [leakix](https://github.com/VECERTINC/leakix) — 2 stars
- [DarkEye](https://github.com/VECERTINC/DarkEye)

### Cashea Breach References
- [VECERT Cashea Alert (Gordon Freeman)](https://x.com/VECERTRadar/status/2017417296357020129)
- [VECERT Cashea Alert (BlackHex Brotherhood)](https://x.com/VECERTRadar/status/2004194602119491892)
- [Bancrecer Venezuela Breach](https://x.com/VECERTRadar/status/2021794473026400522/photo/1)
- [Cashea Bloomberg Profile](https://www.bloomberg.com/news/articles/2025-08-07/buy-now-pay-later-app-cashea-is-unlikely-player-on-caracas-exchange)
- [Venezuela Data Breach (IAPP)](https://iapp.org/news/a/venezuela-data-breach-highlights-scattered-privacy-regulation)

### Threat Actor References
- [malconguerra2 — Peru Court Leak](https://x.com/Alph4_Sec/status/1975446894412206153)
- [malconguerra2 — UNMSM Leak](https://x.com/Alph4_Sec/status/2015599248738255312)

### Address Verification
- [Bayview Plaza Condos — 1621 Bay Road, Miami Beach](https://www.miamicondolifestyle.com/bayview-plaza.php)
- [MyFlorida Corp Paralegal Services — 4711 NW 79th Ave, Doral (Yelp)](https://www.yelp.com/biz/myflorida-corp-paralegal-services-miami)

### Potential Identity Links
- [Gerardo Xavier Yanes Gil — LinkedIn (UNCONFIRMED)](https://www.linkedin.com/in/gerardo-xavier-yanes-gil-0a7141151/)
