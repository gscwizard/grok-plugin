---
description: Audit the Google Ads accounts linked to a property via GSC Wizard
---

Run the **google-ads-audit** skill for the property given as `$ARGUMENTS`
(if empty, call `list_sites` and ask which property to analyze).

Return the health score and a fix list ranked by the spend each fix affects.
GSC Wizard only reads Google Ads: every fix is one the user makes there.
