# Ansible Collection: baselines

An Ansible collection providing baseline configuration roles for Linux, macOS, and Raspberry Pi systems.

## Description

Three roles that apply consistent baseline configuration across a personal homelab. Each role is independently usable.

### Supported Platforms

| Role | Platform |
| --- | --- |
| `linux_baseline` | Debian Trixie (13) |
| `macos_baseline` | macOS (all versions) |
| `raspberry_pi_baseline` | Raspberry Pi OS (Trixie) |

### Requirements

- Ansible >= 2.16
- `community.general` collection
- `ansible.posix` collection

## Roles

### `linux_baseline`

Applies common baseline configuration to Debian Trixie systems: package management, timezone, locale, and NTP via Chrony.

See [roles/linux_baseline](roles/linux_baseline/README.md) for full variable reference.

---

### `macos_baseline`

Applies baseline configuration to macOS systems: Homebrew management, software updates, hostname, timezone, and firewall.

See [roles/macos_baseline](roles/macos_baseline/README.md) for full variable reference.

---

### `raspberry_pi_baseline`

Optimises Raspberry Pi systems: disables unnecessary services, configures zram swap, and sets up log rotation.

See [roles/raspberry_pi_baseline](roles/raspberry_pi_baseline/README.md) for full variable reference.

---

## Tests

Linux and Raspberry Pi roles are tested with [Molecule](https://ansible.readthedocs.io/projects/molecule/) using Docker. macOS is tested by running the role directly on a macOS GitHub Actions runner.

For local Molecule runs, clone the repo into a path matching the collection namespace structure so Ansible can resolve the collection FQCN:

```bash
mkdir -p ansible_collections/baselines/baselines
git clone https://github.com/tonylea/ansible-collection-baselines.git ansible_collections/baselines/baselines
cd ansible_collections/baselines/baselines
pip install -r requirements-test.txt
molecule test -s linux_baseline-default
```

## License

[MIT](LICENSE)
