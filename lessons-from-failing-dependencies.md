# Lessons from Famous Dependency Failures — Log4Shell and xz-utils

## Executive Summary

Two high-profile dependency failures illustrate the stakes of unpatched vulnerable libraries:

**Log4Shell** (CVE-2021-44228, Dec 2021): a critical RCE in Apache Log4j 2. Discovered by Chen Zhaojun of Alibaba Cloud Security Team on 24 Nov 2021; publicly disclosed 10 Dec 2021. Severity: CVSS 10 (Critical). Official GitHub advisory GHSA-jfh8-c2jp-5v3q; affected package `org.apache.logging.log4j:log4j-core`, version range `>= 2.13.0, < 2.15.0`, first fixed version 2.15.0. Follow-up CVE-2021-45046 (GHSA-7rjr-3q55-vv33, Critical) shows patching one CVE was insufficient.

**xz-utils/liblzma backdoor** (CVE-2024-3094, Mar 2024): malicious code inserted into xz tarballs starting at version 5.6.0 onward; embedded-code vulnerability, severity Critical (CVSS 10), classified "malware" in the GitHub Advisory Database (type: unreviewed).

Both show that patching alone isn't enough — misconfiguration and supply-chain integrity matter. Includes disclosure sources, GitHub advisory records, upstream issues, and a Java/Linux checklist.

---

## Incident 1: Log4Shell

- **Discoverer:** Chen Zhaojun, Alibaba Cloud Security Team. Reported privately to Apache SF on 24 Nov 2021; public disclosure 10 Dec 2021. Name "Log4Shell" coined by Free Wortley (LunaSec).
- **Severity:** CVSS 3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H/E:H = 10.0.
- **GitHub advisory:** GHSA-jfh8-c2jp-5v3q — "Remote code injection in Log4j", severity Critical. Package: `org.apache.logging.log4j:log4j-core`, affected range `>= 2.13.0, < 2.15.0`, first fixed version 2.15.0. Type: reviewed (2021-12-10).
- **Follow-ups:** CVE-2021-45046 (GHSA-7rjr-3q55-vv33, Critical, incompletely fixed patch); CVE-2021-45105 (GHSA-8489-44mv-ggj8, Medium).
- **Source:** https://en.wikipedia.org/wiki/Log4Shell ; https://www.huntress.com/threat-library/vulnerabilities/cve-2021-44228 ; https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a ; https://nvd.nist.gov/vuln/detail/cve-2021-44228

## Incident 2: xz-utils Backdoor (CVE-2024-3094)

- **Discovery:** Malicious code in upstream tarballs starting v5.6.0; added .m4 automake instructions. Severity: Critical. CWE-506 "Embedded Malicious Code".
- **GitHub advisory:** GHSA-rxwq-x6h5-x525 — severity Critical, CVSS 10.0, type "unreviewed", no package listed, affected patches unknown.
- **Source:** https://github.com/advisories/GHSA-rxwq-x6h5-x525 ; https://nvd.nist.gov/vuln/detail/CVE-2024-3094

## Table of Related Log4j Advisories

| GHSA | CVE | Severity | CVSS | Type |
|---|---|---|---|---|
| GHSA-jfh8-c2jp-5v3q | CVE-2021-44228 | Critical | 10.0 | reviewed |
| GHSA-7rjr-3q55-vv33 | CVE-2021-45046 | Critical | 10.0 | reviewed |
| GHSA-8489-44mv-ggj8 | CVE-2021-45105 | Medium | 6.6 | reviewed |

*Note: GHSA-rxwq-x6h5-x525 (CVE-2024-3094) is classified "unreviewed" and in the malware section.*

---

## What This Means for Our Java/Linux Stack

1. Upgrade Log4j to 2.17.1+ or backport v2.15.0+ and disable JNDI lookups; disable `log4j2.formatMsgNoLookups=true`.
2. Patch to the exact fixed version in the database and verify build requirements (JndiLookup removal).
3. Verify Linux package integrity (md5/sha256) and use dependency scanners (semgrep, SCA).
4. Ensure upstream xz tarballs are from trusted sources; pin patched xz.
5. Subscribe to CISA NVD / GitHub Advisory Database alerts.

## Sources

- Wikipedia, Log4Shell: https://en.wikipedia.org/wiki/Log4Shell
- Huntress: https://www.huntress.com/threat-library/vulnerabilities/cve-2021-44228
- CISA aa21-356A: https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a
- NVD CVE-2021-44228: https://nvd.nist.gov/vuln/detail/cve-2021-44228
- GitHub Advisory GHSA-jfh8-c2jp-5v3q: https://github.com/advisories/GHSA-jfh8-c2jp-5v3q
- GitHub Advisory GHSA-7rjr-3q55-vv33: https://github.com/advisories/GHSA-7rjr-3q55-vv33
- GitHub Advisory GHSA-rxwq-x6h5-x525: https://github.com/advisories/GHSA-rxwq-x6h5-x525
- NVD CVE-2024-3094: https://nvd.nist.gov/vuln/detail/CVE-2024-3094
- Apache Log4j repo: https://github.com/apache/logging-log4j2
- xz-utils upstream: https://github.com/escapes/xz
