# Copilot Instructions for n3tuk Ansible Repository

## Repository Overview

**Purpose**: Ansible automation for Arch/Debian Linux systems on Proxmox
infrastructure  
**Scale**: 467 files, 30 roles, 9 playbooks  
**Tools**: Ansible 2.20+, Task 3.x, Python 3.12+, Node.js/npm

### Required Tools

**Install before running commands:**

- `task` (go-task/task v3.x), `ansible`/`ansible-playbook` (v2.15+),
  `ansible-lint` (v25.x)
- `prettier` (npm), `yamllint` (pip), `markdownlint-cli` (npm),
  `check-jsonschema` (pip), `pre-commit` (pip)

### Ansible Collections & Vault Override

**Install collections**: `ansible-galaxy collection install -r requirements.yaml`

**CRITICAL - Vault Password Override**: This repo uses encrypted secrets.
**ALWAYS set before running ansible commands**:

```bash
export ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE="test-password"
```

Without this, all ansible-lint and playbook checks fail with vault errors.

## Build and Validation Commands

**Use Task commands** - they handle dependencies and order correctly.

### Linting (Run BEFORE changes)

```bash
# Full suite (prettier, yamllint, markdownlint, jsonschema) - 30-90s
ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE="test-password" task lint

# Individual: task prettier / yamllint / markdownlint / jsonschema
# Ansible linting (needs collections):
ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE="test-password" task ansible-lint
```

### Testing Playbooks

```bash
# Dry-run test
ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE="test-password" task test:baseline

# Limit to hosts
task test:baseline limit=proxmox-01.services.n3t.uk

# Syntax only
ansible-playbook --syntax-check plays/baseline.yaml
```

**Playbooks**: `bootstrap`, `baseline`, `users`, `update`, `upgrade`,
`authentik`, `ca`, `dns`, `proxmox`

### Pre-commit

```bash
pre-commit install          # Setup hooks
pre-commit run --all-files  # Run all checks
```

**Hooks**: gitleaks, shellcheck, yamllint, ansible-lint, markdownlint

## Project Layout

### Directory Structure

```text
├── .github/workflows/linting.yaml  # CI: ansible-lint on PRs
├── .taskfiles/                     # Modular Task definitions
├── plays/                          # Playbooks + group_vars/ + host_vars/
├── roles/[role-name]/              # 30 roles (Ansible Galaxy structure)
│   ├── meta/main.yaml              # Metadata, dependencies
│   ├── defaults/main.yaml          # Default variables
│   ├── tasks/main.yaml             # Main tasks
│   ├── tasks/{arch,debian}.yaml    # OS-specific tasks
│   ├── handlers/main.yaml          # Event handlers
│   └── templates/*.jinja           # Config templates
├── scripts/bin/vault-password-decrypt  # Vault handler
├── Taskfile.yaml                   # Main Task config
├── ansible.cfg                     # Ansible config
├── inventory.yaml                  # Hosts inventory
└── requirements.yaml               # Ansible collections
```

### Key Config Files

- `.ansible-lint.yaml` - Production profile, custom rules
- `.yamllint.yaml` - 80 char lines, consistent spacing
- `.markdownlint.yaml` - MD010/MD013 rules
- `.prettier.yaml` - No semicolons, double quotes
- `.pre-commit-config.yaml` - Pre-commit hooks

**Role patterns**: All tasks need names and tags. Follow production profile in
`.ansible-lint.yaml`.

## CI/CD Pipeline

### GitHub Actions

**Workflow**: `.github/workflows/linting.yaml`  
**Trigger**: PRs to `main`  
**Steps**: Checkout → ansible-lint@v25 → install collections → lint (production
profile) → upload SARIF

**Environment**: Uses `ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE` GitHub secret.

### Pre-commit Checks

Runs: gitleaks, shellcheck, shfmt, yamllint, check-jsonschema, ansible-lint,
markdownlint

**Run `task lint` before committing** to catch issues early.

## Common Issues

### Vault Decryption Failures

**Symptom**: `FATAL: Unable to find the vault key file`  
**Fix**: Set `ANSIBLE_SCRIPT_VAULT_DECRYPT_OVERRIDE="test-password"`

### ansible-lint Unknown Module

**Symptom**: `couldn't resolve module/action 'community.general.pacman'`  
**Fix**: `ansible-galaxy collection install -r requirements.yaml`  
**Note**: CI handles automatically. Warnings are normal without collections
locally.

### Missing Tools

**task**: `curl -sL https://taskfile.dev/install.sh | sh && sudo mv task
/usr/local/bin/`  
**npm tools**: `npm install -g prettier markdownlint-cli`

### Expected Behaviors

- yamllint skips `ansible.cfg` (INI format, not YAML)
- `plays/encrypted_vars/` excluded from linting (`.ansiblelintignore`)

## Conventions and Best Practices

### File Standards

- **Use `.yaml` not `.yml`**
- Start all YAML with `---` (document start)
- 2-space indentation, 80 char lines (soft), no trailing whitespace
- `snake_case` for roles/files, hyphens for hostnames
  (`proxmox-01.services.n3t.uk`)

### Ansible Rules

- **All tasks need `name:` attributes**
- Use tags for organization
- Variable naming: `^[a-z][a-z0-9]*(_[a-z0-9]+)*[a-z0-9]$` (snake_case)
- Use `become: true` (not `sudo`)
- Test with `--check --diff` before applying

### Workflow

1. **Run `task lint` BEFORE changes** (baseline)
2. Make minimal changes
3. **Run `task lint` AFTER** (verify)
4. Test: `task test:playbook-name limit=test-host`
5. Apply: `task play:playbook-name limit=test-host`

### Trust These Instructions

Commands are tested and work with proper setup (vault override, tools
installed). Only search if info is missing/incorrect.

**Checklist when stuck**:

1. Vault override set?
2. Tools installed? (`task --version`, `ansible --version`)
3. Run `task lint` to validate
4. Review existing roles/playbooks for patterns

**No temporary scripts** - use Task commands as documented.
