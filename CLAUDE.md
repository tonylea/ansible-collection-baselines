# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Ansible collection `tonylea.baselines` — three roles providing baseline configuration for Linux (Debian Trixie), macOS, and Raspberry Pi systems. Public repo, personal use only. Not published to Ansible Galaxy.

Source roles to reference (read-only, do not modify):
- `/Users/tonylea/Developer/homelab/ansible/roles/linux_baseline`
- `/Users/tonylea/Developer/homelab/ansible/roles/macos_baseline`
- `/Users/tonylea/Developer/homelab/ansible/roles/raspberry_pi_baseline`

Structure and tooling reference: `/Users/tonylea/Developer/ansible-roles/ansible-role-ssh-hardening`

## Build Order — CRITICAL

Work in strict phases. Complete one phase, stop, wait for user feedback before starting the next:

1. Collection scaffolding
2. `linux_baseline` role
3. `macos_baseline` role
4. `raspberry_pi_baseline` role

Do not research, implement, or think ahead into later phases.

## Commands

```bash
# Install all dependencies
npm ci
pip install -r requirements-lint.txt
pip install -r requirements-test.txt

# Lint (ansible-lint + cspell + markdownlint)
npm run lint

# Individual linters
npm run lint:ansible
npm run lint:spell
npm run lint:markdown

# Molecule — run all scenarios for a role
molecule test                        # default scenario
molecule test -s custom-vars         # custom-vars scenario

# Molecule — individual steps
molecule converge                    # apply role
molecule verify                      # run assertions
molecule destroy                     # tear down containers
```

## Architecture

### Collection structure

```
galaxy.yml                     # collection metadata (namespace: tonylea, name: baselines)
meta/runtime.yml               # min ansible version
roles/
  linux_baseline/              # Debian Trixie base config (NTP, locale, packages, updates)
  macos_baseline/              # macOS base config (Homebrew, firewall, hostname, updates)
  raspberry_pi_baseline/       # RPi optimisation (services, zram, logrotate)
extensions/
  molecule/                    # molecule scenarios live here for collection testing
    <role>-default/
    <role>-custom-vars/
```

### Molecule — collection pattern

Molecule scenarios are under `extensions/molecule/`. The `molecule.yml` provisioner sets `ANSIBLE_COLLECTIONS_PATH` so Ansible resolves FQCNs (`tonylea.baselines.<role>`) against the checked-out repo without a symlink or separate install.

Linux and RPi scenarios use Docker (`jrei/systemd-debian:trixie`, privileged, with cgroup mounts). macOS role is tested directly on a `macos-latest` GitHub Actions runner via `ansible-playbook` — no Docker container.

### CI/CD

- `ci.yml` — triggers on PRs to `main`: lint job → molecule test matrix
- `release.yml` — triggers on push to `main` (skips `chore(release)` commits): bumps version in `galaxy.yml`, commits, tags, pushes

### Key constraints

- **Debian Trixie only** — never add bullseye or bookworm targets
- **No Galaxy publish** — release workflow does not push to Ansible Galaxy
- **No mocks in molecule** — all tests run against real containers or real runners
- **Two molecule scenarios per role** — `default` (role defaults) and `custom-vars` (variable overrides)
- **Atomic commits** — each commit is a single complete unit of work

### Toolchain versions (requirements files are authoritative)

- `ansible-core`, `ansible-lint`, `molecule`, `molecule-plugins[docker]` — pin to latest stable in requirements files
- Node tooling: `markdownlint-cli2`, `cspell`, `commit-and-tag-version`, `husky`, `commitlint`

### Commit format

Conventional commits enforced by commitlint + husky pre-commit hook. Types: `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, `test`.
