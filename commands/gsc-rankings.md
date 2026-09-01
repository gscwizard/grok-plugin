---
description: Analyze ranking winners and losers for a property via GSC Wizard
---

Run the **ranking-changes** skill for the property given as `$ARGUMENTS`
(if empty, call `list_sites` and ask which property to analyze).

Default comparison: last 28 settled days vs the prior 28 days, unless the
user specified a window. Prioritize by traffic impact and follow the
skill's output rules.
