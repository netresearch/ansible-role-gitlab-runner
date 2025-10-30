# GitHub Copilot Instructions for ansible-role-gitlab-runner

This repository contains an Ansible role that installs and configures GitLab Runner on Debian and Ubuntu systems.

## Repository Structure

This is an Ansible Galaxy role with the following structure:

- `tasks/` - Ansible task files for installation, registration, configuration, cleanup, and unregistration
- `defaults/main.yml` - Default variables for the role
- `vars/` - OS-specific variable files
- `templates/` - Jinja2 templates for configuration files
- `handlers/` - Ansible handlers for service management
- `meta/main.yml` - Role metadata and dependencies
- `molecule/` - Molecule test scenarios for testing the role
- `.github/workflows/` - GitHub Actions workflows for CI/CD

## Development Requirements

### Dependencies

- **Ansible Core**: >= 2.17.1
- **Python**: >= 3.11.2
- **Ansible Collections**:
  - `community.general` >= 9.2.0

### Testing Tools

- **Molecule**: >= 24.7.0
- **molecule-plugins[docker]**: >= 23.5.3

### Linting Tools

- `ansible-lint` - For Ansible best practices and syntax checking
- `yamllint` - For YAML syntax and style checking

## Building and Testing

### Linting

Always run linting before committing changes:

```bash
# Install linting dependencies
python3 -m pip install ansible ansible-lint yamllint

# Run ansible-lint
ansible-lint .

# Run yamllint (configuration in .yamllint)
yamllint .
```

### Testing with Molecule

The role uses Molecule with Docker for testing. A GitLab Personal Access Token (PAT) with `admin` scope is required for testing.

```bash
# Run complete test suite
molecule test -- --extra-vars "gitlab_access_token=<TOKEN>"

# Step-by-step testing for development:
molecule create                                              # Create test environment
molecule converge -- --extra-vars "gitlab_access_token=..."  # Apply role
molecule verify                                              # Run tests
molecule login --host <hostname>                             # Login to test environment
molecule destroy                                             # Cleanup
```

Test scenarios are located in `molecule/default/` directory.

## Code Style and Conventions

### YAML Style

- Maximum line length: 120 characters (enforced by yamllint)
- Use 2-space indentation
- Follow the style defined in `.yamllint` configuration
- Quote strings when necessary for clarity

### Ansible Conventions

- All task files should start with `---`
- Use fully qualified collection names (e.g., `ansible.builtin.template`)
- Tag tasks appropriately (see existing tasks for examples)
- Use descriptive task names in the format: `"Component | Action description"`
- Follow the existing variable naming convention (snake_case with prefixes)

### Variable Naming

- Role variables use the `runner_`, `gitlab_`, `os_`, or `env_` prefixes
- Boolean variables use `is_` or `_enabled` suffixes where appropriate
- Required variables should be documented in README.md

## GitLab Runner Specifics

### Supported Executors

This role primarily supports:
- **Docker executor** (default, recommended)
- **Shell executor**

Other executors are untested and may require additional configuration.

### Supported Operating Systems

- Debian 12 (bookworm)
- Ubuntu Server 24.04 (noble)

### Key Role Variables

Required:
- `gitlab_access_token` - GitLab Personal Access Token with admin scope

Important optional variables:
- `runner_executor` - Executor type (docker, shell)
- `runner_count` - Number of runner instances
- `runner_tags` - Tags for the runner
- `runner_docker_image` - Docker image for builds
- `force_reregistration` - Force re-registration of runners

See `defaults/main.yml` for all available variables and README.md for detailed documentation.

## Making Changes

### When modifying tasks

1. Ensure task names follow the convention: `"Component | Action description"`
2. Use appropriate tags
3. Test with Molecule before committing
4. Update documentation in README.md if adding/changing variables

### When adding new variables

1. Add defaults to `defaults/main.yml`
2. Document in the README.md under "Role Variables"
3. Update example playbooks if necessary

### When modifying templates

1. Templates are in `templates/` directory
2. Ensure Jinja2 syntax is correct
3. Test the rendered output with Molecule

## CI/CD

The repository uses GitHub Actions for CI:

- `.github/workflows/molecule.yml` - Currently only runs ansible-lint (Molecule tests are commented out)
- `.github/workflows/release.yml` - Handles releases to Ansible Galaxy

Always ensure that `ansible-lint .` passes before submitting changes.

## Pre-existing Issues

Note: There is a known pre-existing line-length violation in `tasks/registration.yml:56` that is not related to new changes. You are not responsible for fixing unrelated linting issues.

## Additional Resources

- [Molecule Documentation](https://molecule.readthedocs.io/)
- [Ansible Best Practices](https://docs.ansible.com/ansible/latest/tips_tricks/ansible_tips_tricks.html)
- [GitLab Runner Documentation](https://docs.gitlab.com/runner/)

## License

This project is licensed under AGPL-3.0-or-later.
