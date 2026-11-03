# report-to-detection

A Claude Code skill that turns a threat intelligence report into reviewable Sigma YAML detection rules, with ATT&CK mapping, test events, false-positive notes and a telemetry-gap list. The skill deploys nothing. Its output is files for a pull request.

Skill authored 2026-10-08; published 2026-11-02.

## Install

Copy `SKILL.md` to `~/.claude/skills/report-to-detection/SKILL.md`.

## Use

```
/report-to-detection <report file or URL> [log sources]
```

The report may be a PDF, a web page or pasted text. The log sources name the telemetry the team collects, for example `okta, gcp-audit, macos-endpoint-security`.

## Output

```
detections/<log-source>/<short-behavior-name>.yml
detections/indicators/<report-slug>-iocs.yml
detections/<report-slug>-SUMMARY.md
```

Every rule is marked `experimental` and carries a positive and a negative test event. Unverified technique IDs and field names are marked `# VERIFY`, so a reviewer knows what to check before trusting a rule.

## Write-up

A worked example with the rules in full: [From Threat Report to Detection Rules: A Claude Code Skill for Sigma YAML](https://seattlecodestudio.com/#post/report-to-detection-threat-reports-to-sigma-rules).

## License

MIT
