## [LIVE: TeamHDP Network Exposed — Interactive Investigation Suite](https://ringmast4r.github.io/vecert-exposed/TEAMHDP_NETWORK_EXPOSED.html)

> **20+ individuals exposed | 5 countries | 633K+ stolen records | $5M+ in ransoms**
>
> Full interactive network map, profiles, timeline, and evidence linking the entire TeamHDP cybercriminal organization — from their founder in Doral, FL to Hezbollah operatives on Margarita Island.

---

![Visitors](https://api.visitorbadge.io/api/visitors?path=https%3A%2F%2Fgithub.com%2FRingmast4r%2Fvecert-exposed&label=VIEWS&countColor=%23ff0000&style=flat-square)

# VECERT Exposed

An OSINT investigation into **VECERT Security Labs USA LLC** — a one-man Florida LLC scraping cybercrime forums and selling the data through an unauthenticated API, while distributing hundreds of thousands of records of criminal marketplace content on public GitHub.

## Key Findings

- **Hardcoded stolen credentials** found in source code (ProtonMail sessions, DarkEye cookies)
- **Unauthenticated API** at `vecertapi.com` exposes 403,927+ indexed posts
- **200K+ records** of scraped dark forum data shipped on public GitHub repos + 403K+ via open API
- **Pickle data** reveals monitoring of Venezuela's National Electoral Council (`cne.gob.ve`)
- **Operator:** Gerardo J. Gil (sole manager, Doral, FL)
- **LLC filed:** October 15, 2025 — no EIN, no annual reports
- Two addresses — a Miami Beach residential condo and a Doral commercial mail suite
- Name mimics a government CERT but has **zero government affiliation**
- **Additional email discovered:** `intelligencevecert@gmail.com` (used on VECERTINC repos)

---

## Evidence: Hardcoded Stolen Credentials

### ProtonMail Session Tokens
Found in [`VECERTUSA/DarkForumCTI/search.py`](https://github.com/VECERTUSA/DarkForumCTI) — lines 30, 39-42:

```python
# Hardcoded ProtonMail API authentication
"x-pm-uid": "upyulffn33faq6mnwkuzz7q2qjqekalz",

PROTON_COOKIES = {
    "AUTH-upyulffn33faq6mnwkuzz7q2qjqekalz": "y6iuhnkaogtvhzjrfa4jf7xo2mykln6e",
    "Session-Id": "aIYuoSom-KqULYafC086vAAAAQ8"
}
```

These are used to enumerate email accounts across 19 privacy-focused providers:

```python
EXTENDED_DOMAINS = [
    "protonmail.com", "protonmail.ch", "tutanota.com", "tuta.io", "tutanota.de",
    "outlook.com", "outlook.es", "hotmail.com", "hotmail.co.uk", "yahoo.com",
    "yandex.com", "riseup.net", "mailfence.com", "pm.me", "cock.li", "safe-mail.net",
    "guerrillamail.com", "disroot.org", "secmail.pro", "proton.me"
]
```

### DarkEye.org Session Cookies
Found in [`VECERTINC/DarkEye/darkeye.py`](https://github.com/VECERTINC/DarkEye) — line 36:

```python
"Cookie": '__jsluid_s=b1ab69c55f648ec8067416c032051898; _ga=GA1.1.589433742.1735065866; ASSETS="gASVQAAAAAAAAAB9lIwEdXVpZJSMBFVVSUSUk5QpgZR9lIwDaW50lIoQT+6tIzu0UbmwSQVbq/nFfXNijApjbmUuZ29iLnZllHMu"; _ga_4W9GPBYZ2H=GS1.1.1735065866.1.1.1735066247.0.0.0'
```

The Google Analytics cookie timestamps decode to **December 24, 2024, 18:44 UTC** (Christmas Eve). The session lasted **6 minutes 21 seconds** (381 seconds). One visit, cookies grabbed, hardcoded into source code, published to GitHub.

### The Pickle — Venezuela's Electoral Council
The `ASSETS` cookie above is base64-encoded Python pickle data. Safe disassembly with `pickletools.dis()` reveals:

```
Structure: {uuid.UUID("7dc5f9ab-5b05-49b0-b951-b43b23adee4f"): "cne.gob.ve"}
```

**`cne.gob.ve` = Consejo Nacional Electoral de Venezuela** — the government body that runs elections. The operator was scanning Venezuela's election infrastructure for credential leaks, compromised systems, and attack surface exposure.

### XSS.is Forum Authentication Tokens
The scraped data in [`VECERTUSA/XSS.isCTI/xss_threads.csv`](https://github.com/VECERTUSA/XSS.isCTI) contains preview URLs with embedded XenForo CSRF tokens:

```
https://xss.is/threads/29391/preview?_xfToken=1734628049%2Cdfcb57ea95441cd401997637f9cfa5b2
```

Timestamp `1734628049` = **December 19, 2024, 17:07 UTC**. This proves VECERT maintained an **authenticated account on the XSS.is cybercrime forum** to scrape content.

---

## Evidence: Scraped Criminal Marketplace Data

VECERT ships pre-scraped data from criminal forums directly in their GitHub repos. Here is what's inside:

### Dark Forum User Directory — 15,851 users
From `DarkForumCTI/darkforums_users.json`:
```json
[
    {"URL": "https://darkforums.st/User-Lucifer", "user": "Lucifer"},
    {"URL": "https://darkforums.st/User-mrrobot", "user": "mrrobot"},
    {"URL": "https://darkforums.st/User-ASTRA", "user": "ASTRA"}
]
```
Complete membership roster of a criminal marketplace with profile links.

### Exploit & Stolen Data Sales — 1,227 listings
From `DarkForumCTI/sellers_output.csv`:
```
Title,Author,Posted_Date
[SELLING] WINRAR RCE 0DAY,AnonOne,"11-07-25"
SELLING 10M bkash.com CUSTOMER PHONE NUMBER,ARXU,"18-08-24"
INDIAN CITIZENS AND MILITARY DATA BREACHED,Team_CRO,"07-07-25"
Korean drivers license + resident registration card with full SSN (1000+),miandropacio,"11-07-25"
400K Israel Whatsapp Phone Numbers For Sale,BlackH0le,"12-07-25"
Usa Driver License Available with ssn,Khanagha122,"19-07-25"
POLAND 700K FOREX DATABASE,Thor,"19-07-25"
```

### Credential Stealer Logs — 3,895 threads
From `LeakBaseCTI/stealer.csv`:
```
Title,Author,Created
7M+ URL:LOG:PASS 18/07 FRESH LOGS TODAY,Askyn09Sell,2025-07-18
BONUS HELL CLOUD 07 32GB JUNE/JULY,picij99064,2025-07-18
Xavier Log 38K JULY,picij99064,2025-07-17
Universe Logs 30K JULY,picij99064,2025-07-17
```
Active stealer malware output — credential dumps in URL:Login:Password format.

### Credential Combo Sales — 668 threads
From `DarkForumCTI/backup_posts/combolist.csv`:
```
Title,Replies
ALIEN TXTBASES - FREE URL:LOG:PASS 5-15-2025,258
[Collection] 667496 line URL-LOGIN-PASS_07.19.25,37
[MA]-MOROCCO-URL-LOGIN-PASS,124
PSOE.ES LOGIN CREDENTIALS,86
20M | FRESH & PRIVATE LINES | URL:LOGIN:PASS,0
LinkedIn DB 15GB,38
```

### LeakBase Forum Membership — 125,260 user profiles
From `LeakBaseCTI/leakbase_users.csv`:
```
username,profile_url,user_id,messages,reaction_score
#learnhack,https://leakbase.la/members/learnhack.80511/,80511,0,0
$cup$,https://leakbase.la/members/cup.77383/,77383,0,0
$DbizTr4der$,,,,1,0
```
Complete membership database of a breach trading forum — usernames, IDs, profile URLs, activity metrics.

### XSS.is Russian Hacking Forum — 50,374 CSV rows (33 MB)
From `XSS.isCTI/xss_threads.csv` — 20 years of content (2004-2024), 7,097 unique authors, 70 million cumulative views. Full post body text included. Content spans ransomware operations, zero-day exploit trading, banking trojans, stealer malware, and credential sales.

---

## The Open API

VECERT's backend API requires **zero authentication**:

```
GET https://vecertapi.com/api_analyzer?title=cashea&page=1&per_page=100
```

No API key. No auth headers. No rate limiting. **403,927+ posts** from BreachForums, XSS.is, Cracked, and more — dumpable by anyone with `curl`.

They sell paid "API Plans" for access to data that is already completely open.

---

## Data Inventory

### In GitHub Repos (verified line counts)

| Repo | Records | Content |
|------|---------|---------|
| DarkForumCTI | ~28,600 | 15,851 forum users, 1,922 breach posts, 1,227 seller listings, 9,599 archived threads |
| LeakBaseCTI | ~134,700 | 125,260 user profiles, 2,738 big leaks, 3,895 stealer logs, 1,783 private sales, 969 admin posts |
| XSS.isCTI | 50,374 rows | 20 years of Russian cybercrime forum (33 MB), full post content |
| **Repo Total** | **~213,000+** | |

### Via Unauthenticated API

| Source | Records | Content |
|--------|---------|---------|
| vecertapi.com | 403,927+ | BreachForums, XSS.is, Cracked, XForums — no auth required |

---

## Read the Full Report

**[VECERT-Expose-Public-Release.md](VECERT-Expose-Public-Release.md)** — complete investigation with full technical analysis, timeline, code review, and sourcing.

---

## Archived Evidence (Forks)

All 7 VECERT repositories have been forked as evidence preservation in case the originals are deleted:

| Original Repo | Archived Fork |
|---------------|---------------|
| [VECERTUSA/DarkForumCTI](https://github.com/VECERTUSA/DarkForumCTI) | [Ringmast4r/DarkForumCTI](https://github.com/Ringmast4r/DarkForumCTI) |
| [VECERTUSA/LeakBaseCTI](https://github.com/VECERTUSA/LeakBaseCTI) | [Ringmast4r/LeakBaseCTI](https://github.com/Ringmast4r/LeakBaseCTI) |
| [VECERTUSA/XSS.isCTI](https://github.com/VECERTUSA/XSS.isCTI) | [Ringmast4r/XSS.isCTI](https://github.com/Ringmast4r/XSS.isCTI) |
| [VECERTUSA/Analyzer_forums](https://github.com/VECERTUSA/Analyzer_forums) | [Ringmast4r/Analyzer_forums](https://github.com/Ringmast4r/Analyzer_forums) |
| [VECERTINC/Intelx_CLI_Free_Version](https://github.com/VECERTINC/Intelx_CLI_Free_Version) | [Ringmast4r/Intelx_CLI_Free_Version](https://github.com/Ringmast4r/Intelx_CLI_Free_Version) |
| [VECERTINC/leakix](https://github.com/VECERTINC/leakix) | [Ringmast4r/leakix](https://github.com/Ringmast4r/leakix) |
| [VECERTINC/DarkEye](https://github.com/VECERTINC/DarkEye) | [Ringmast4r/DarkEye](https://github.com/Ringmast4r/DarkEye) |

---

## The Operator: Florida LLC Filing

| Field | Value |
|-------|-------|
| **Entity** | VECERT SECURITY LABS USA LLC |
| **Document #** | L25000472380 |
| **Filed** | October 15, 2025 |
| **Status** | ACTIVE |
| **EIN** | NONE |
| **Manager** | GIL, GERARDO J (sole) |
| **Address** | 4711 NW 79TH AVE, SUITE 2B, DORAL, FL 33166 |

- **Doral, FL** is nicknamed "Doralzuela" — largest Venezuelan diaspora in the US
- The address is a multi-suite commercial building housing a **paralegal/LLC formation service** (MyFlorida Corp Paralegal Services in Suite 5E)
- A **second address** (1621 Bay Rd #601, Miami Beach) on GitHub is a **residential condo** at Bayview Plaza
- The LLC has no federal tax ID and no annual reports — filed 4 months ago

### Git Attribution (Verified)

Commits across repos use **three different author identities**, all pointing to the same operation:

| Repos | Git Author | Email |
|-------|-----------|-------|
| DarkForumCTI | `VECERT` / `VECERTUSA` | `investigations@vecert.io` |
| LeakBaseCTI, XSS.isCTI, Analyzer_forums | `VECERT` | `investigations@vecert.io` |
| Intelx_CLI, leakix, DarkEye | `VECERT Digital Intelligence and Security` | `intelligencevecert@gmail.com` |

- VECERTUSA repos committed in **UTC+8** timezone
- VECERTINC repos committed in **UTC+9** timezone
- Neither timezone matches Florida (UTC-5)

Source: [Florida Division of Corporations — Sunbiz](https://search.sunbiz.org)

---

## Methodology

This investigation was conducted entirely using open source intelligence (OSINT). All data referenced is from publicly accessible sources including Florida state records, public GitHub repositories, and publicly available websites. No systems were accessed without authorization.

---

**NO ONE PAYWALLS VENEZUELAN DATA** —Ringmast4r
