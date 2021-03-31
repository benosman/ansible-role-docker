# Ansible Role: Docker

[![Build Status](https://travis-ci.org/geerlingguy/ansible-role-docker.svg?branch=master)](https://travis-ci.org/geerlingguy/ansible-role-docker)

An Ansible Role that installs [Docker](https://www.docker.com) on Linux.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    # Edition can be one of: 'ce' (Community Edition) or 'ee' (Enterprise Edition).
    docker_edition: 'ce'
    docker_package: "docker-{{ docker_edition }}"
    docker_package_state: present

The `docker_edition` should be either `ce` (Community Edition) or `ee` (Enterprise Edition). You can also specify a specific version of Docker to install using the distribution-specific format: Red Hat/CentOS: `docker-{{ docker_edition }}-<VERSION>`; Debian/Ubuntu: `docker-{{ docker_edition }}=<VERSION>`.

You can control whether the package is installed, uninstalled, or at the latest version by setting `docker_package_state` to `present`, `absent`, or `latest`, respectively. Note that the Docker daemon will be automatically restarted if the Docker package is updated. This is a side effect of flushing all handlers (running any of the handlers that have been notified by this and any other role up to this point in the play).

    docker_service_state: started
    docker_service_enabled: true
    docker_restart_handler_state: restarted

Variables to control the state of the `docker` service, and whether it should start on boot. If you're installing Docker inside a Docker container without systemd or sysvinit, you should set these to `stopped` and set the enabled variable to `no`.

    docker_install_compose: true
    docker_compose_version: "1.22.0"
    docker_compose_path: /usr/local/bin/docker-compose

Docker Compose installation options.

    docker_apt_release_channel: stable
    docker_apt_arch: amd64
    docker_apt_repository: "deb [arch={{ docker_apt_arch }}] https://download.docker.com/linux/{{ ansible_distribution|lower }} {{ ansible_distribution_release }} {{ docker_apt_release_channel }}"
    docker_apt_ignore_key_error: True

(Used only for Debian/Ubuntu.) You can switch the channel to `edge` if you want to use the Edge release.

    docker_yum_repo_url: https://download.docker.com/linux/centos/docker-{{ docker_edition }}.repo
    docker_yum_repo_enable_edge: '0'
    docker_yum_repo_enable_test: '0'

(Used only for RedHat/CentOS.) You can enable the Edge or Test repo by setting the respective vars to `1`.

    docker_users:
      - user1
      - user2

A list of system users to be added to the `docker` group (so they can use Docker on the server).

    # docker client configuration --------------------------------------------------
    ## enable authentication for docker registry
    docker_client_config_enabled: false
    ## the location we should push client configuration
    docker_client_config_location: "/root/.docker/config.json"
    # for auth (docker login) use something like:
    #docker_client_config:
    #  auths:
    #    "https://test.tld:1234":
    #      auth: "SOME_STRING"
    #      email: "SOME_EMAIL"
    
    # default dockerd configuration options ----------------------------------------
    ## https://docs.docker.com/engine/reference/commandline/dockerd/#/linux-configuration-file
    docker_config_graph: "/var/lib/docker"
    docker_config_log_driver: ""
    docker_config_log_opts: {}
    docker_config_max_concurrent_downloads: 3
    docker_config_max_concurrent_uploads: 5
    docker_config_debug: false
    docker_config_log_level: ""
    docker_config_bridge: ~
    docker_config_bip: "172.16.0.1/24"
    docker_config_fixed_cidr: "172.16.0.0/24"
    docker_config_fixed_cidr_v6: ""
    docker_config_default_gateway: ""
    docker_config_default_gateway_v6: ""
    docker_config_selinux_enabled: false
    docker_config_ip: "0.0.0.0"
    docker_config_group: "{{ docker_group }}"
    docker_config_insecure_registries: []
    
    # additional custom docker settings can be added to this dict:
    docker_config_custom: {}

## Use with Ansible (and `docker` Python library)

Many users of this role wish to also use Ansible to then _build_ Docker images and manage Docker containers on the server where Docker is installed. In this case, you can easily add in the `docker` Python library using the `geerlingguy.pip` role:

```yaml
- hosts: all

  vars:
    pip_install_packages:
      - name: docker

  roles:
    - geerlingguy.pip
    - geerlingguy.docker
```

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  roles:
    - geerlingguy.docker
```

Or use the following playbook with Proxy

```yaml
- hosts: all

  vars:
    docker_http_proxy: http://user:password@proxy:port
    docker_https_proxy: http://user:password@proxy:port
    docker_no_proxy: localhost,127.0.0.0/8,192.168.0.0/16

  roles:
    - geerlingguy.docker

  environment:
    docker_http_proxy: "{{ docker_http_proxy }}"
    docker_https_proxy: "{{ docker_https_proxy }}"
    docker_no_proxy: "{{ docker_no_proxy }}"
```

## License

MIT / BSD

## Author Information

This role was created in 2017 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
