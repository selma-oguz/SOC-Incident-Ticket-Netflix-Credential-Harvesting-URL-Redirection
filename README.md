<img width="487" height="480" alt="Ekran görüntüsü 2026-09-19 094107" src="https://github.com/user-attachments/assets/e8341058-7162-425b-9e8c-09d9aa015c0d" />

# SOC Incident Ticket: Netflix Credential Harvesting & URL Redirection

**Ticket ID:** INC-PHISH-003
**Date:** 19 September 2026
**Analyst:** Selma Oguz

SECTION 1: Incident Summary


**Header Analysis Link:** [MXToolBox Analysis](https://mxtoolbox.com/Public/Tools/EmailHeaders.aspx?huid=f8531d12-3960-4606-896d-db2139a5f5fe)

On July 7, 2021, an internal user (`REDACTED_USER@yahoo.com`) received a highly deceptive phishing email impersonating Netflix. The email's core objective is credential harvesting, specifically targeting payment card details under the guise of an "account on hold" notification. 

**Analyst Observations & Red Flags:**
* **Typosquatting & Visual Spoofing:** The sender name `N𝅹e𝅴t𝅴f𝅷I𝅶i𝅶x𝅶` and the subject line `Y⁪o𝅸u⁮r⁫ N⁫e⁫t⁫f⁫I⁫i⁫x A⁫c⁫c⁫o⁫u⁫n⁫t⁫ i⁫s⁫ o⁫n⁫ H⁫o⁫l⁫d⁫` utilize special Unicode characters and capitalization tricks (e.g., using a capital 'I' instead of a lowercase 'l' in Netflix) to bypass basic spam filters and visually deceive the recipient.
* **Generic Greeting:** As visible in the provided email screenshot, the email does not use the victim's name, instead addressing them generically as `Hi redacted@yahoo.com`. This is a standard indicator of a mass-mailing campaign.
* **Complex URL Redirection Chain:** The "UPDATE ACCOUNT NOW" button initiates a multi-stage redirection sequence to evade detection. The traffic first routes through a shortened Twitter link (`t.co`), then to a LinkedIn tracking link (`linkedin.com/slink`), before finally landing on the malicious payload hosted at a compromised third-party domain (`prioritysurveyors.com`). 
* **Sender Infrastructure Anomaly:** The sending domain `JOg7ODDQwWdR-yVkCaBkTNp.gogolecloud.com` attempts to look like legitimate Google infrastructure (note the misspelling "gogolecloud"). The `Return-Path` points to an entirely different domain (`etekno.xyz`).

**Impact Assessment:** 
No user interaction (clicks) occurred. The incident is contained.

### A) Email Artifacts (Observables)
=================================
* **Sending Address:** `N𝅹e𝅴t𝅴f𝅷I𝅶i𝅶x𝅶 <JGQ47wazXe1xYVBrkeDg-JOg7ODDQwWdR[@]JOg7ODDQwWdR-yVkCaBkTNp[.]gogolecloud[.]com>`
* **Subject Line:** `Y⁪o𝅸u⁮r⁫ N⁫e⁫t⁫f⁫I⁫i⁫x A⁫c⁫c⁫o⁫u⁫n⁫t⁫ i⁫s⁫ o⁫n⁫ H⁫o⁫l⁫d⁫`
* **Recipients:** `[REDACTED_USER]@yahoo.com`
* **Sending Server IP:** `209[.]85[.]167[.]226`
* **Reverse DNS:** IP belongs to Google infrastructure.
* **Return Path:** `postmaster[@]etekno[.]xyz`
* **Date and Time:** Wed, 7 Jul 2021 04:14:40 +0200
* **Message ID:** `<60e50e16.1c69fb81.186da.9717SMTPIN_ADDED_MISSING@mx.google.com>`

### B) Web Artifacts (Observables)
==============================
* **Full URL Links (Defang-sanitized):** 
  * `hxxps[://]t[.]co/yuxfZm8KPg?amp=1` (Initial Redirect)
  * `hxxps[://]www[.]linkedin[.]com/slink?code=enmd-V3` (Secondary Redirect)
  * `hxxp[://]prioritysurveyors[.]com/public/metronic/global/plugins/jquery-file-upload/server/restore[.]php?css=webapps` (Final Malicious Destination)
* **Root Domains (Defanged):** 
  * `etekno[.]xyz`
  * `prioritysurveyors[.]com`
  * `JOg7ODDQwWdR-yVkCaBkTNp[.]gogolecloud[.]com`

### C) File (Attachment) Artifacts
=============================================
* **File Name:** N/A
* **File Hash (SHA256):** N/A


## SECTION 2: Artifact Analysis
##############################
* **URL Redirection Analysis (VirusTotal & ANY.RUN):** Analysis confirms that the legitimate LinkedIn shortlink (`linkedin.com/slink?code=enmd-V3`) acts as a proxy, hiding the final malicious destination. This technique exploits the high reputation of domains like Twitter and LinkedIn to bypass corporate web proxies.
* **Final Payload Domain:** `prioritysurveyors.com` appears to be a legitimate but compromised website. The attacker has uploaded a malicious PHP script (`restore.php`) deep within a legitimate plugin directory (`/plugins/jquery-file-upload/`), utilizing it to host the fake Netflix login portal.
* **IP Reputation (AbuseIPDB):** The sending IP `209.85.167.226` has been flagged for spam activity, despite belonging to the broader Google network. 


## SECTION 3: Suggested Defensive Measures
#######################################

* **Declaration:** **True Positive – No Impact (Non-Issue)**

* **Block the Root Domains:** 
  * `etekno[.]xyz`
  * `prioritysurveyors[.]com`
  * `JOg7ODDQwWdR-yVkCaBkTNp[.]gogolecloud[.]com`
* **Block the specific URL paths:** 
  * `hxxp[://]prioritysurveyors[.]com/public/metronic/global/plugins/jquery-file-upload/server/*`
* **Block The IP:** None. *(Blocking `209.85.167.226` is not recommended as it is part of Google's shared infrastructure; doing so would cause massive false positives for legitimate Google services).*

***
**Disclaimer:** *This ticket is based on a real-world phishing sample analyzed within a controlled lab environment for educational and portfolio demonstration purposes.*
