# GitLab Runner Ansible role

This Ansible role installs and configures GitLab runner.

**Please note:** Only the **docker** and **shell** executor is supported. Other executors are
untested and may not work out of the box with this role.

Currently supported operating systems:
- Debian 12
- Ubuntu Server 24.04

## Requirements
This role has been tested with the following dependencies:

`ansible_core` >= `2.17.1`
Ansible Collections
  - `community.general` >= `9.2.0`
Python >= `3.11.2`

### Testing
- `molecule` >= `24.7.0`
- `molecule-plugins[docker]` >= `23.5.3`

## Role Variables
### Required
- `gitlab_access_token`: **Required.** GitLab Personal Access Token with `admin` scope.

### Optional
- `os_pkg_gitlab_runner`: Optional. Installation package name.
- `gitlab_gpg_key_url`: Optional. URL for GitLab's official gpg key.
- `gitlab_url`:Optional. URL of GitLab server.
- `gitlab_apt_repository_url`: Optional. GitLab Runner apt repository URL.
- `apt_os_dependencies`: Optional. OS dependencies that should be installed.

- `runner_count`: Optional. The number of runner instances.
- `runner_name`: Optional. The runner's name.
- `runner_executor`: Optional. The runner's executor that is used to run your
builds.
- `runner_concurrent`: Optional. Limits how many jobs globally can be run
concurrently.
- `runner_builds_limit`: Optional. Maximum number of builds processed by a
runner.
- `runner_is_locked`: Optional. If true, runner cannot be assigned to other
projects.
- `runner_is_paused`: Optional. If true, runner will not receive any new jobs.
- `runner_run_untagged_builds`: Optional. Register to run untagged builds.
- `runner_tags`: Optional. List of tags.
- `runner_metrics_enabled`: Optional. If true, runner will expose Prometheus
metrics via embedded HTTP server.
- `runner_metrics_listen_address`. Optional. `<host>:<port>` address on which
the Prometheus metrics HTTP server should be listening (default: `':9252'`).

- `runner_docker_image`: Optional. Docker image to be used.
- `runner_docker_privileged`: Optional. Give extended privileges to docker
container.
- `runner_docker_services_timeout`: Optional. How long to wait for service
startup.
- `runner_stop_timeout`: Optional. Timeout in seconds for the service stop
command after which systemd will just `SIGKILL` the process.

- `runner_user`: Optional. User that will be used to run the runner. Only used
if `runner_executor` is set to `shell`.
- `runner_group`: Optional. Group that will be used to run the runner. Only used
if `runner_executor` is set to `shell`.

- `force_unregistration`: Optional. Force unregistration of all existing runners,
- `force_reregistration`: Optional. Force unregistration of all existing
runners  before registration.

- `runner_cleanup_is_enabled`: Optional. If true, GitLab Runner cleanup tool
will be used.
- `runner_cleanup_container_name`: Optional. GitLab Runner cleanup container
name.
- `runner_cleanup_container_image`: Optional. GitLab Runner cleanup container
image.
- `runner_cleanup_volumes`: Optional. GitLab Runner cleanup volumes.
- `runner_cleanup_env`: Optional. GitLab Runner cleanup environment variables.

- `runner_config_path`: Optional. Path to GitLab Runner's configuration file
(TOML format).

- `env_compose_http_timeout`: Optional. Value for `COMPOSE_HTTP_TIMEOUT`
environment variable injected to build environment.
- `env_docker_driver`: Optional. Value for `DOCKER_DRIVER` environment variable
injected to build environment.
- `env_docker_tls_certdir`: Optional. Value for `DOCKER_TLS_CERTDIR`
environment variable injected to build environment.

## Dependencies

- [geerlingguy.docker](https://github.com/geerlingguy/ansible-role-docker) to use the `docker` executor

## Example Playbook

```yaml
- hosts: servers
  become: true
  vars:
    gitlab_access_token: "xxxxx-oemL-tu-cvi"
  tasks:
    - name: "Include netresearch.gitlab_runner"
      ansible.builtin.include_role:
        name: "netresearch.gitlab_runner"
```

## Testing

For testing we use `Molecule` in combination with `Docker`.
For more information see:
- [Test requirements](molecule/default/INSTALL.rst)
- [Molecule](https://molecule.readthedocs.io/en/latest/)

Additionally we have two scenarios:
- Debian 12

1. Get a GitLab PAT with the `admin` scope.
2. For starting the tests, please run:
    ```bash
    molecule test -- --extra-vars "gitlab_access_token=<TOKEN>"
    ```
    For development you can also run the test step-by-step for Debian scenario:
    ```bash
    # create testing environment
    molecule create

    # rollout Ansible config
    molecule converge -- --extra-vars "gitlab_access_token=TOKEN_HERE"

    # start tests
    molecule verify

    # login into the testing environment
    molecule login --host <hostname_in_molecule.yml>
    ```

## License

AGPL-3.0-or-later
