# :computer: ROLE dginhoux.docker_engine

## :scroll: DESCRIPTION

This ansible role install, configure and uninstall docker engine



## :nut_and_bolt: REQUIREMENTS

#### SUPPORTED PLATFORMS

This role require a supported platform. 
It will skip node with unsupported platform to avoid any compatibility problem.
This behaviour can be bypassed by settings this variable `asserts_bypass=True`.

| Platform | Versions |
|----------|----------|
| Debian | buster, bullseye |
| Fedora | 33, 34, 35, 36 |
| EL | 7, 8 |


#### ANSIBLE VERSION

Ansible >= 2.12


#### DEPENDENCIES

None.


## :inbox_tray: INSTALLATION

#### ANSIBLE GALAXY

```shell
ansible-galaxy install dginhoux.docker_engine
```

#### GIT

```shell
git clone https://github.com/dginhoux/ansible_role.docker_engine dginhoux.docker_engine
```


## :rocket: USAGE

#### EXAMPLE PLAYBOOK

```yaml
- hosts: all
  roles:
    - name: start role dginhoux.docker_engine
      ansible.builtin.include_role:
        name: dginhoux.docker_engine
      vars:
        containerd_package_name: containerd.io
        containerd_package_state: present
        containerd_package_version: ''
        containerd_service_enabled: 'yes'
        containerd_service_override: '[Service]
        
          ExecStartPre=
        
          '
        containerd_service_state: started
        docker_architecture:
          x86_64: amd64
        docker_cli_package_name: docker-ce-cli
        docker_cli_package_state: present
        docker_cli_package_version: ''
        docker_daemon_config:
          default-shm-size: 64M
          experimental: true
          hosts:
            - unix:///var/run/docker.sock
          icc: false
          iptables: true
          ipv6: false
          log-level: warn
          metrics-addr: 127.0.0.1:9323
          selinux-enabled: false
          shutdown-timeout: 90
          tls: false
          userland-proxy: false
        docker_deploy_action: install
        docker_engine_skip_cli: false
        docker_engine_skip_containerd: false
        docker_engine_skip_docker_py: false
        docker_engine_skip_engine: false
        docker_engine_skip_group: false
        docker_engine_skip_repo: false
        docker_group_name: docker
        docker_group_users:
          - '{{ ansible_user | default(ansible_user_id) }}'
        docker_package_name: docker-ce
        docker_package_state: present
        docker_package_version: ''
        docker_py_package_name: docker
        docker_py_package_state: present
        docker_py_package_version: ''
        docker_python_sni_pip_dependencies:
          - pyopenssl
          - ndg-httpsclient
          - pyasn1
        docker_repo: '{{ docker_repo_ce_stable }}'
        docker_repo_ce_edge:
          apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg
          apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88
          apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{
            ansible_distribution | lower }} {{ ansible_distribution_release }} edge
          name: docker_ce_edge
          yum_gpg_check: 'yes'
          yum_gpg_key: https://download.docker.com/linux/centos/gpg
          yum_repo: https://download.docker.com/linux/centos/7/$basearch/edge
        docker_repo_ce_nightly:
          apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg
          apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88
          apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{
            ansible_distribution | lower }} {{ ansible_distribution_release }} nightly
          name: docker_ce_nightly
          yum_gpg_check: 'yes'
          yum_gpg_key: https://download.docker.com/linux/centos/gpg
          yum_repo: https://download.docker.com/linux/centos/7/$basearch/nightly
        docker_repo_ce_stable:
          apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg
          apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88
          apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{
            ansible_distribution | lower }} {{ ansible_distribution_release }} stable
          name: docker_ce_stable
          yum_gpg_check: 'yes'
          yum_gpg_key: https://download.docker.com/linux/centos/gpg
          yum_repo: https://download.docker.com/linux/centos/7/$basearch/stable
        docker_repo_ce_test:
          apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution | lower }}/gpg
          apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88
          apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{
            ansible_distribution | lower }} {{ ansible_distribution_release }} test
          name: docker_ce_test
          yum_gpg_check: 'yes'
          yum_gpg_key: https://download.docker.com/linux/centos/gpg
          yum_repo: https://download.docker.com/linux/centos/7/$basearch/test
        docker_service_enabled: 'yes'
        docker_service_override: '[Unit]
        
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
        
          # Both the old, and new location are accepted by systemd 229 and up, so using the
          old location
        
          # to make them work for either version of systemd.
        
          StartLimitBurst=3
        
          # Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
        
          # Both the old, and new name are accepted by systemd 230 and up, so using the old
          name to make
        
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
        
          '
        docker_service_state: started
        docker_ssh_daemon: sshd
        
```


## :factory: VARIABLES
#### DEFAULT VARIABLES
Role default variables from `defaults/main.yml` : 

| Variable Name | Value |
|---------------|-------|
|docker_deploy_action | <pre> install </pre> |
|docker_engine_skip_repo | <pre> False </pre> |
|docker_engine_skip_containerd | <pre> False </pre> |
|docker_engine_skip_engine | <pre> False </pre> |
|docker_engine_skip_cli | <pre> False </pre> |
|docker_engine_skip_group | <pre> False </pre> |
|docker_engine_skip_docker_py | <pre> False </pre> |
|docker_package_name | <pre> docker-ce </pre> |
|docker_package_version | <pre>  </pre> |
|docker_package_state | <pre> present </pre> |
|docker_service_override | <pre> [Unit]<br>Description=Docker Application Container Engine<br>Documentation=https://docs.docker.com<br>After=network-online.target firewalld.service containerd.service<br>Wants=network-online.target<br>Requires=docker.socket containerd.service<br>[Service]<br>Type=notify<br># the default is not to use systemd for cgroups because the delegate issues still<br># exists and systemd currently does not support the cgroup feature set required<br># for containers run by docker<br># ExecStart=/usr/bin/dockerd -H fd:// -H tcp://127.0.0.1:2375 --containerd=/run/containerd/containerd.sock<br>ExecStart=<br>ExecStart=/usr/bin/dockerd --containerd=/run/containerd/containerd.sock<br>ExecReload=/bin/kill -s HUP $MAINPID<br>TimeoutSec=0<br>RestartSec=2<br>Restart=always<br># Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.<br># Both the old, and new location are accepted by systemd 229 and up, so using the old location<br># to make them work for either version of systemd.<br>StartLimitBurst=3<br># Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.<br># Both the old, and new name are accepted by systemd 230 and up, so using the old name to make<br># this option work for either version of systemd.<br>StartLimitInterval=60s<br># Having non-zero Limit*s causes performance problems due to accounting overhead<br># in the kernel. We recommend using cgroups to do container-local accounting.<br>LimitNOFILE=infinity<br>LimitNPROC=infinity<br>LimitCORE=infinity<br># Comment TasksMax if your systemd version does not support it.<br># Only systemd 226 and above support this option.<br>TasksMax=infinity<br># set delegate yes so that systemd does not reset the cgroups of docker containers<br>Delegate=yes<br># kill only the docker process, not all processes in the cgroup<br>KillMode=process<br>OOMScoreAdjust=-500<br>[Install]<br>WantedBy=multi-user.target<br> </pre> |
|docker_service_state | <pre> started </pre> |
|docker_service_enabled | <pre> yes </pre> |
|docker_daemon_config | <pre> default-shm-size: 64M<br>experimental: true<br>hosts:<br>- unix:///var/run/docker.sock<br>icc: false<br>iptables: true<br>ipv6: false<br>log-level: warn<br>metrics-addr: 127.0.0.1:9323<br>selinux-enabled: false<br>shutdown-timeout: 90<br>tls: false<br>userland-proxy: false<br> </pre> |
|docker_cli_package_name | <pre> docker-ce-cli </pre> |
|docker_cli_package_version | <pre>  </pre> |
|docker_cli_package_state | <pre> present </pre> |
|containerd_package_name | <pre> containerd.io </pre> |
|containerd_package_version | <pre>  </pre> |
|containerd_package_state | <pre> present </pre> |
|containerd_service_override | <pre> [Service]<br>ExecStartPre=<br> </pre> |
|containerd_service_state | <pre> started </pre> |
|containerd_service_enabled | <pre> yes </pre> |
|docker_py_package_name | <pre> docker </pre> |
|docker_py_package_version | <pre>  </pre> |
|docker_py_package_state | <pre> present </pre> |
|docker_group_name | <pre> docker </pre> |
|docker_group_users | <pre> - '{{ ansible_user  \| default(ansible_user_id) }}'<br> </pre> |
|docker_architecture | <pre> x86_64: amd64<br> </pre> |
|docker_repo_ce_stable | <pre> apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution  \| lower }}/gpg<br>apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88<br>apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{<br>  ansible_distribution  \| lower }} {{ ansible_distribution_release }} stable<br>name: docker_ce_stable<br>yum_gpg_check: 'yes'<br>yum_gpg_key: https://download.docker.com/linux/centos/gpg<br>yum_repo: https://download.docker.com/linux/centos/7/$basearch/stable<br> </pre> |
|docker_repo_ce_edge | <pre> apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution  \| lower }}/gpg<br>apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88<br>apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{<br>  ansible_distribution  \| lower }} {{ ansible_distribution_release }} edge<br>name: docker_ce_edge<br>yum_gpg_check: 'yes'<br>yum_gpg_key: https://download.docker.com/linux/centos/gpg<br>yum_repo: https://download.docker.com/linux/centos/7/$basearch/edge<br> </pre> |
|docker_repo_ce_test | <pre> apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution  \| lower }}/gpg<br>apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88<br>apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{<br>  ansible_distribution  \| lower }} {{ ansible_distribution_release }} test<br>name: docker_ce_test<br>yum_gpg_check: 'yes'<br>yum_gpg_key: https://download.docker.com/linux/centos/gpg<br>yum_repo: https://download.docker.com/linux/centos/7/$basearch/test<br> </pre> |
|docker_repo_ce_nightly | <pre> apt_gpg_key: https://download.docker.com/linux/{{ ansible_distribution  \| lower }}/gpg<br>apt_gpg_key_id: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88<br>apt_repo: deb [arch={{ docker_architecture[ansible_architecture] }}] https://download.docker.com/linux/{{<br>  ansible_distribution  \| lower }} {{ ansible_distribution_release }} nightly<br>name: docker_ce_nightly<br>yum_gpg_check: 'yes'<br>yum_gpg_key: https://download.docker.com/linux/centos/gpg<br>yum_repo: https://download.docker.com/linux/centos/7/$basearch/nightly<br> </pre> |
|docker_repo | <pre> {{ docker_repo_ce_stable }} </pre> |
|docker_ssh_daemon | <pre> sshd </pre> |
|docker_python_sni_pip_dependencies | <pre> - pyopenssl<br>- ndg-httpsclient<br>- pyasn1<br> </pre> |


#### CONTEXT VARIABLES

Those variables are located in `vars/*.yml` are used to handle OS differences ; One of theses is loaded dynamically during role
runtime using the `include_vars` module and set OS specifics variable's.


Variables loaded from `vars/debian.yml` : 

| Variable Name | Value |
|---------------|-------|
| docker_python_pip_packages | - python-pip<br> |
| docker_dependencies | []<br> |

Variables loaded from `vars/redhat.yml` : 

| Variable Name | Value |
|---------------|-------|
| docker_python_pip_packages | - '{% if ansible_python_version is version_compare(''3.0.0'', ''<'') %}python-pip{%<br>  else %}python3-pip{% endif %}'<br> |
| docker_dependencies | - xfsprogs<br> |




## :man: AUTHOR

[![Author](https://img.shields.io/badge/maintained%20by-dginhoux-e00000?style=flat-square)](https://github.com/dginhoux)


## :bookmark_tabs: LICENSE

[![License](https://img.shields.io/github/license/dginhoux/ansible_role.docker_engine?style=flat-square)](https://github.com/dginhoux/ansible_role.docker_engine/blob/master/LICENSE)