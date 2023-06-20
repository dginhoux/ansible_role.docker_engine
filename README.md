# ROLE dginhoux.docker_engine



## DESCRIPTION

This ansible role install, configure and uninstall docker-engine.<br />



## REQUIREMENTS

#### SUPPORTED PLATFORMS

This role is tested on the following platforms.<br />

| Platform | Versions |
|----------|----------|
| Debian | buster, bullseye, bookworm |
| Fedora | 33, 34, 35, 36, 37, 38 |
| EL | 7, 8 |

You can set this variable `check_compatibility` to let the role skip nodes with unsupporteds platforms to avoid any compatibility problems.<br />


#### ANSIBLE VERSION

Ansible >= 2.13

#### DEPENDENCIES

None.



## INSTALLATION

#### ANSIBLE GALAXY

```shell
ansible-galaxy install dginhoux.docker_engine
```
#### GIT

```shell
git clone https://github.com/dginhoux/ansible_role.docker_engine dginhoux.docker_engine
```


## USAGE

#### EXAMPLE PLAYBOOK

```yaml
- hosts: all
  roles:
    - name: start role dginhoux.docker_engine
      ansible.builtin.include_role:
        name: dginhoux.docker_engine
```


## VARIABLES

#### DEFAULT VARIABLES

Defaults variables defined in `defaults/main.yml` : 

```yaml
docker_deploy_action: install
# docker_deploy_action: uninstall

##
# Role Switches
##
# Switches disabling the docker-engine, docker group and swarm-mode setup.
# if true, skips the setup of the docker repository
docker_engine_skip_repo: false
# if true, skips the setup of containerd
docker_engine_skip_containerd: false
# if true, skips the docker engine installation
docker_engine_skip_engine: false
# if true, skips the docker cli installation
docker_engine_skip_cli: false
# if true, do not add the docker_admin_users to the docker_group_name
docker_engine_skip_group: false
# if true, skips the installation of docker-py
docker_engine_skip_docker_py: false

##
# Docker
##
# Name of the package providing Docker
docker_package_name: "docker-ce"

# Version of the Docker package to be installed.
# By default, the latest available version will be installed.
docker_package_version: ""

# Installation state of the Docker package.
# Set it to 'latest' to upgrade Docker to the latest version.
docker_package_state: present

# Extra packages that have to be installed together with Docker
# e.g. linux-image-extra-virtual on some Debian systems
# docker_dependencies: "{{ default_docker_dependencies }}"

# Contect written to the systemd unit drop-in overriding
# the default Docker service definition.
# docker_service_override: ""
# docker_service_override: |
# [Service]
# ExecStart=
# ExecStart=/usr/bin/dockerd
docker_service_override: |
  [Unit]
  Description=Docker Application Container Engine
  Documentation=https://docs.docker.com
  After=network-online.target firewalld.service containerd.service
  Wants=network-online.target
  Requires=docker.socket containerd.service
  [Service]
  Type=notify
  # the default is not to use systemd for cgroups because the delegate issues still
  # exists and systemd currently does not support the cgroup feature set required
  # for containers run by docker
  # ExecStart=/usr/bin/dockerd -H fd:// -H tcp://127.0.0.1:2375 --containerd=/run/containerd/containerd.sock
  ExecStart=
  ExecStart=/usr/bin/dockerd --containerd=/run/containerd/containerd.sock
  ExecReload=/bin/kill -s HUP $MAINPID
  TimeoutSec=0
  RestartSec=2
  Restart=always
  # Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
  # Both the old, and new location are accepted by systemd 229 and up, so using the old location
  # to make them work for either version of systemd.
  StartLimitBurst=3
  # Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
  # Both the old, and new name are accepted by systemd 230 and up, so using the old name to make
  # this option work for either version of systemd.
  StartLimitInterval=60s
  # Having non-zero Limit*s causes performance problems due to accounting overhead
  # in the kernel. We recommend using cgroups to do container-local accounting.
  LimitNOFILE=infinity
  LimitNPROC=infinity
  LimitCORE=infinity
  # Comment TasksMax if your systemd version does not support it.
  # Only systemd 226 and above support this option.
  TasksMax=infinity
  # set delegate yes so that systemd does not reset the cgroups of docker containers
  Delegate=yes
  # kill only the docker process, not all processes in the cgroup
  KillMode=process
  OOMScoreAdjust=-500
  [Install]
  WantedBy=multi-user.target

# State of the Docker deamon service
docker_service_state: "started"

# Whether the Docker service should start on boot
docker_service_enabled: "yes"

# Docker Deamon configuration
# docker_daemon_config: {}
docker_daemon_config:
  default-shm-size: 64M
  experimental: true
  hosts:
    # - tcp://0.0.0.0:2375
    - unix:///var/run/docker.sock
  icc: false
  iptables: true
  ipv6: false
  # live-restore: true
  # log-driver: json-file
  # log-opts:
  #   cache-disabled: "false"
  #   max-size: 10m
  #   max-file: 10
  log-level: warn
  metrics-addr: "127.0.0.1:9323"
  selinux-enabled: false
  userland-proxy: false
  tls: false
  shutdown-timeout: 90

##
# Docker CLI
##
# Name of the package providing the Docker CLI
docker_cli_package_name: "docker-ce-cli"

# Version of the Docker CLI package to be installed.
docker_cli_package_version: ""

# Installation state of the Docker CLI package.
# Set it to 'latest' to upgrade the Docker CLI to the latest version.
docker_cli_package_state: present

##
# Containerd
##
# Name of the package providing containerd
containerd_package_name: "containerd.io"

# Version of the containerd package to be installed.
# By default, the latest available version will be installed.
containerd_package_version: ""

# Installation state of the containerd package.
# Set it to 'latest' to upgrade containerd to the latest version.
containerd_package_state: present

# Contect written to the systemd unit drop-in overriding
# the default containerd service definition.
containerd_service_override: |
  [Service]
  ExecStartPre=

# State of the containerd service
containerd_service_state: "started"

# Whether the containerd service should start on boot
containerd_service_enabled: "yes"

##
# Docker-py
##
# Name of the python-pip package providing docker-py
docker_py_package_name: "docker"

# Version of the docker-py package to be installed.
docker_py_package_version: ""

# Installation state of the docker-py package.
# Set it to 'latest' to upgrade the Docker CLI to the latest version.
docker_py_package_state: present

##
# Docker Users
##
# Name of the Docker group
docker_group_name: "docker"

# List of the users in the Docker group
# if ansible_user is not defined, we default to the ansible ansible_user_id fact
docker_group_users:
  - "{{ ansible_user | default(ansible_user_id) }}" 
docker_architecture:
  x86_64: "amd64"

##
# Docker CE stable release repository
##
docker_repo_ce_stable:
  name: "docker_ce_stable"
  yum_repo: "https://download.docker.com/linux/centos/7/$basearch/stable"
  yum_gpg_key: "https://download.docker.com/linux/centos/gpg"
  yum_gpg_check: "yes"
  apt_repo: "deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} stable"
  apt_gpg_key: "https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg"
  apt_gpg_key_id: "9DC858229FC7DD38854AE2D88D81803C0EBFCD88"
# Docker CE edge release repository
docker_repo_ce_edge:
  name: "docker_ce_edge"
  yum_repo: "https://download.docker.com/linux/centos/7/$basearch/edge"
  yum_gpg_key: "https://download.docker.com/linux/centos/gpg"
  yum_gpg_check: "yes"
  apt_repo: "deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} edge"
  apt_gpg_key: "https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg"
  apt_gpg_key_id: "9DC858229FC7DD38854AE2D88D81803C0EBFCD88"
# Docker CE test release repository
docker_repo_ce_test:
  name: "docker_ce_test"
  yum_repo: "https://download.docker.com/linux/centos/7/$basearch/test"
  yum_gpg_key: "https://download.docker.com/linux/centos/gpg"
  yum_gpg_check: "yes"
  apt_repo: "deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} test"
  apt_gpg_key: "https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg"
  apt_gpg_key_id: "9DC858229FC7DD38854AE2D88D81803C0EBFCD88"
# Docker CE nightly release repository
docker_repo_ce_nightly:
  name: "docker_ce_nightly"
  yum_repo: "https://download.docker.com/linux/centos/7/$basearch/nightly"
  yum_gpg_key: "https://download.docker.com/linux/centos/gpg"
  yum_gpg_check: "yes"
  apt_repo: "deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} nightly"
  apt_gpg_key: "https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg"
  apt_gpg_key_id: "9DC858229FC7DD38854AE2D88D81803C0EBFCD88"

# Repository to be used for the installation of the packages
docker_repo: "{{ docker_repo_ce_stable }}"

# Name of the SSH Deamon
docker_ssh_daemon: sshd

docker_python_sni_pip_dependencies:
  - pyopenssl
  - ndg-httpsclient
  - pyasn1
```

#### DEFAULT OS SPECIFIC VARIABLES

Those variables files are located in `vars/*.yml` are used to handle OS differences.<br />
One of theses is loaded dynamically during role runtime using the `include_vars` module and set OS specifics variable's.

* RedHat family
```yaml
docker_python_pip_packages:
  - "{% if ansible_python_version is version_compare('3.0.0', '<') %}python-pip{% else %}python3-pip{% endif %}"

docker_dependencies:
  # See https://github.com/moby/moby/issues/26054
  - xfsprogs
```

* Debian family
```yaml
docker_python_pip_packages:
  - python-pip

docker_dependencies: []
```



## AUTHOR

Dany GINHOUX - https://github.com/dginhoux



## LICENSE

MIT


## INSPIRED FROM

This role is inspired and forked from this role https://github.com/atosatto/ansible-dockerswarm created by atosatto

