# Web Recon & Bug Bounty Notes

Daily progress notes on web hacking reconnaissance techniques, tools, and a real-world XSS report breakdown.

---

## 1. Scope Discovery

| Tool/Technique | Purpose |
|---|---|
| `whois <ip>` | Looks up the owner of an IP/domain; the **NetRange** field gives the IP range. |
| [whois.cymru.com](https://whois.cymru.com/) | Identifies the ASN of a target, revealing who owns a given IP range. |
| `nslookup` | Queries internet name servers for IP info about a host. |
| [ViewDNS Reverse WHOIS](https://viewdns.info/reversewhois/) | Searches WHOIS records by keyword to find related domains. |
| [ViewDNS Reverse IP](https://viewdns.info/reverseip/) | Finds other domains hosted on the same server/IP. |

---

## 2. Certificate Parsing

SSL certificates contain a **Subject Alternative Name (SAN)** field that often lists multiple hostnames for a site — a great source of valid subdomains/domains.

**Tools:**
- [crt.sh](https://crt.sh/)
- [Censys](https://censys.io/)
- [Cert Spotter](https://sslmate.com/certspotter/)

---

## 3. Subdomain Enumeration

**Tools:** Sublist3r, SubBrute, Amass, Shodan, Gobuster
> 💡 Idea: build a custom tool combining all of the above.

- [Sublist3r](https://github.com/aboul3la/Sublist3r/)
- [SubBrute](https://github.com/TheRook/subbrute/)
- [Amass](https://github.com/OWASP/Amass/)
- [Gobuster](https://github.com/OJ/gobuster/)
- [Altdns](https://github.com/infosec-au/altdns/) — brute-forces subdomains via permutations of common names
- [SecLists](https://github.com/danielmiessler/SecLists/) — wordlists for subdomain/filepath brute-forcing
- [Commonspeak2](https://github.com/assetnote/commonspeak2/) — generates wordlists from public data

**Tip:** Recursively enumerate subdomains of already-discovered subdomains.

---

## 4. Active & Passive Scanning

- **Active:** Nmap, Naabu, Masscan — scan for open ports.
- **Passive:** Shodan, Censys, [Project Sonar](https://www.rapid7.com/research/project-sonar/) — find exposed services without directly touching the target.

---

## 5. Directory Brute Forcing

Reveals admin panels, database interfaces, and other hidden paths.

**Tools:** [Dirsearch](https://github.com/maurosoria/dirsearch/), [Gobuster](https://github.com/OJ/gobuster/)

**Bonus:** EyeWitness / Snapper — screenshot a list of URLs to quickly triage discovered paths.

---

## 6. Crawling

- [OWASP ZAP](https://owasp.org/www-project-zap/) — scanner, proxy, and web spider for content discovery.
- Also worth checking individual **cloud storage buckets** for exposed access points:
  - [GrayhatWarfare](https://buckets.grayhatwarfare.com/) — search engine for public S3 buckets
  - [Lazys3](https://github.com/nahamsec/lazys3/) / [Bucket Stream](https://github.com/eth0izzle/bucket-stream/) — brute-force buckets by keyword

---

## 7. GitHub Recon

Reviewing source code reveals low-level functionality and often exposes secrets.

- Look for hardcoded **API keys, encryption keys, database passwords**.
- **Tools:**
  - [Gitrob](https://github.com/michenriksen/gitrob/) — finds sensitive files pushed to public repos
  - [TruffleHog](https://github.com/trufflesecurity/truffleHog/) — finds secrets via string patterns/high-entropy strings
- **Wayback Machine** ([archive.org/web](https://archive.org/web/)) — find old endpoints, URLs, subdomains.
  - [Waybackurls](https://github.com/tomnomnom/waybackurls/) — fetch URLs from the Wayback Machine

---

## 8. Fingerprinting

Identify the tech stack to target known vulnerabilities.

- [Wappalyzer](https://www.wappalyzer.com/) — identifies CMS, frameworks, languages
- [BuiltWith](https://builtwith.com/)
- [StackShare](https://stackshare.io/)
- [Retire.js](https://retirejs.github.io/retire.js/) — detects outdated JS libraries/Node packages
- [CVE Database](https://cve.mitre.org/cve/search_cve_list.html) — search for known vulnerabilities affecting the target's stack

---

## 9. OSINT

- [Google Hacking Database](https://www.exploit-db.com/google-hacking-database/) — useful dorks for finding vulnerabilities/sensitive files
- [KeyHacks](https://github.com/streaak/keyhacks/) — validate leaked credentials/API keys and learn how to use them
- [PasteHunter](https://github.com/kevthehermit/PasteHunter/) — scans paste sites for leaked sensitive info

---

## 10. Automation

- [Git](https://git-scm.com/) — use `git diff` to track file changes over time (useful for monitoring target changes).

---

## 11. XSS Case Study — HackerOne Reports

### [Report #2078490](https://hackerone.com/reports/2078490)
- **Type:** Stored XSS
- **Location:** An IP address input field
- **Payload:** `<svg onload=(alert)(document.domain)>`
- **Bounty:** $1337 (Severity 7–8)

**Key lesson:**
> If the response `Content-Type` is `text/html` instead of `application/json`, the browser renders the input as a document (executing scripts) rather than treating it as inert data/an object.

**Checkpoint Analysis:**

| Checkpoint | What it protects against | Was it missing here? |
|---|---|---|
| Output encoding before rendering user data as HTML | Stored/reflected XSS | ✅ Yes — root cause |
| Input validation on write endpoints | Garbage/malicious data entering the system | ✅ Yes |
| Field-level access control (which properties a user can modify) | Mass assignment / privilege escalation | ✅ Yes |
| Verifying actual state, not just HTTP status code | False negatives during testing | Researcher caught this correctly |
| ID/resource predictability | IDOR at scale | ❌ Not exploitable here (32-char random hash) |

---

*Notes compiled during ongoing web application security / bug bounty recon study.*
