# report-to-detection

A Claude Code skill that turns a threat intelligence report into reviewable Sigma YAML detection rules, with ATT&CK mapping, test events, false-positive notes and a telemetry-gap list. The skill writes rule files and never commits, pushes or loads them into a SIEM, so nothing it produces takes effect until a reviewer merges it in a pull request, as part of a detection-as-code workflow.

The write-up, with a worked example and the generated rules in full, is at [From Threat Report to Detection Rules: A Claude Code Skill for Sigma YAML](https://www.seattlecodestudio.com/#post/report-to-detection-threat-reports-to-sigma-rules).

Skill authored 2026-10-08; published 2026-11-02.

## Install

Copy `SKILL.md` to `~/.claude/skills/report-to-detection/SKILL.md`.

## Use

```
/report-to-detection <report file or URL> [log sources]
```

The report may be a PDF, a web page or pasted text. The log sources name the telemetry the team collects, for example `okta, gcp-audit, macos-endpoint-security`. If none are given, the skill asks once.

## Constraints

Four constraints keep the output auditable.

- The report is untrusted input. Threat reports quote attacker-controlled strings that can carry prompt-injection text, so anything addressed to an AI or to the analyst is recorded as a finding and never acted on.
- Behaviors come before indicators. Rules are written from what the adversary did, in intrusion order. IPs, domains and hashes go into separate indicator files with an expiry date (90 days after publication by default), since indicators go stale quickly.
- The output claims no more than the report supports. Ambiguity is marked `[UNCLEAR IN REPORT]`. ATT&CK IDs the report does not state, and field names the skill cannot confirm, are marked `# VERIFY`, so the reviewer knows what to check before trusting the rule.
- Every rule is testable. Each carries a `test_event` that must fire and a `test_negative` that must not, together with a known-good filter, named false positives and the exact telemetry required, so a reviewer can confirm the rule does what it claims. New rules are always `status: experimental` until tuned.

## Output

```
detections/<log-source>/<short-behavior-name>.yml
detections/indicators/<report-slug>-iocs.yml
detections/<report-slug>-SUMMARY.md
```

The summary holds a coverage table, the telemetry gaps, a verify list, the report content that did not become rules with the reason for each, and a reviewer checklist.

## Worked example

The write-up runs the skill against Google Threat Intelligence Group's August 2025 report on UNC6395 and the Salesloft Drift integration, using Salesforce and Google Workspace log sources. The run produced five behavior rules and two indicator files. It also produced a matrix of which log sources could observe each behavior, and a sketch of the report's hardening advice as SaaS posture checks. The skill does not yet emit posture checks. That part of the write-up was assembled from the run's summary.

## Scope of verification

The rules in the write-up were parsed as YAML and applied to their own test events by a short script. No rule has passed through a Sigma backend, a SIEM or a real tenant, and Salesforce field names are unconfirmed. Treat every generated rule as a draft for review.

## License

MIT
