# Safe Network Change via GitOps (Demo)

Goal: idempotent change with pre-checks, config backup, atomic apply, post-checks, and auto-rollback.

Workflow
1) Pre-checks: BGP sessions healthy, routing OK.
2) Backup: save pre-change running-config per node and change-id.
3) Change: merge/replace config (FRR demo uses vtysh; real gear uses napalm/vendor modules).
4) Post-checks: verify sessions/routes stable.
5) Rollback: auto if post-check asserts fail.

CI idea: add GitHub Actions to lint YAML/Ansible and run small validators before merge.
