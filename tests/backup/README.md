# Feature 8 recovery contract

These six integration tests require Docker and Python 3.11+. They create a unique
PostGIS 16 / PostGIS 3.4 container with synthetic data, no published ports and no
mounted host data. Cleanup removes only that test container. No AWS account or
credentials are used.

```bash
DWMS_RUN_DR_INTEGRATION=1 python -m unittest discover -s tests/backup -p 'test_*.py' -v
```

In PowerShell, first set `$env:DWMS_RUN_DR_INTEGRATION = '1'`, then run the Python
command. `DWMS_BACKUP_TOOL` optionally selects an explicit implementation path.
Without opt-in, discovery reports skipped tests, which is not a recovery pass.

The suite verifies spatial geometry and SRID via a constrained column, a GiST
index, both application schemas, restored sequence state, source preservation,
existing-target refusal, corrupt-archive refusal before database creation,
application-database refusal, missing-source failure and backup overwrite refusal.

The TEST branch is independent of the IMP branch. Until IMP is merged, its CI
resolves the fork's prescribed IMP branch, records its SHA in the run log, and
checks out that exact commit as a temporary dependency. Once implementation
exists on the tested branch, CI uses that local
implementation instead. Both paths run the same tests; no tests silently pass
because implementation is absent.

Real S3/KMS behavior, production database size, concurrent application traffic,
recovery time objectives and operator cutover remain separate acceptance checks.
