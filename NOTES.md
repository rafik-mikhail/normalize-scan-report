# Notes: Normalize scan report

## Gotchas

- **SARIF `level` is not security severity.** `error`, `warning`, and `note` describe how serious the rule's author considers a problem, for any kind of rule. GitHub shows `security-severity` in preference to `level` when both exist. Mapping `error` to `high` makes quality rules look like security issues.
- **Boundary values.** GitHub's docs describe critical as "over 9.0"; CVSS v3 and v4 rate 9.0 as critical. This skill follows CVSS. If your platform disagrees, supply a mapping.
- **Base scores are severity, not risk.** NVD and most tools publish CVSS-B. Label it so step 4 does not treat it as an environmental score.
- **Tool brand is not category.** Trivy, Snyk, Checkov, and GitHub all emit several categories.
- **Path drift breaks identity later.** If one run reports `./src/a.py` and another `/build/src/a.py`, fingerprints and deduplication fail. Record the original and normalized path and the rule you used.
- **Export caps.** GitHub keeps only the top 5,000 of up to 25,000 results per SARIF run, and 100 of up to 1,000 locations per result. An export from a platform may already be truncated.

## Areas for improvement

- Add per-tool mapping tables for common scanners (Semgrep, CodeQL, Snyk, Trivy, Checkov, ZAP, Gitleaks).
- Add an optional OCSF output (Vulnerability Finding and Compliance Finding classes) for teams that use a security data lake.
- Add a deterministic parser script for SARIF and CycloneDX, so the agent reviews the mapping rather than doing it by hand.
- Large reports: define a chunking rule for reports that exceed the agent's context.

## Sources

- [GitHub: SARIF support for code scanning](https://docs.github.com/en/code-security/reference/code-scanning/sarif-files/sarif-support): fingerprints, `security-severity` ranges, limits, run categories.
- [OASIS SARIF 2.1.0](https://docs.oasis-open.org/sarif/sarif/v2.1.0/errata01/os/sarif-v2.1.0-errata01-os-complete.html): `kind`, `level` defaults, `baselineState`, suppressions.
- [FIRST CVSS v4.0 User Guide](https://www.first.org/cvss/v4.0/user-guide): score nomenclature, base score is not risk.
- [DefectDojo Findings](https://docs.defectdojo.com/asset_modelling/engagements_tests/os__findings/): finding data model across SAST, DAST, and SCA.
- [OCSF Vulnerability Finding](https://schema.ocsf.io/classes/security_finding): an alternative normalized schema.
