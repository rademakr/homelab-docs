## Intro

Ansible is an open-source automation tool used for configuration management, application deployment, and task automation. It simplifies complex IT tasks by allowing administrators to automate repetitive tasks across multiple systems efficiently. Ansible operates by using SSH connections to communicate with remote servers, making it agentless and easy to set up. With its simple YAML syntax and powerful orchestration capabilities, Ansible enables organizations to streamline operations, increase productivity, and maintain consistency in their IT infrastructure.

The importance of roles in Ansible should not be underestimated. They help organize tasks and clean up the playbook by organizing multiple directories for specific tasks or targeted hosts, instead of having everything in a gigantic monolithic file.

The main playbook is responsible for calling the taskbooks organized in their respective directories. Directory structure is also important. Each role directory has a task directory with a main.yml file, and an optional files directory containing the files you would want to transfer to the remote hosts.

## Config files

The main configuration files required for Anaible are `ansible.cfg` and `inventory`.

* inventory  
  This file will organize your differnet hosts in groups. The same host can be in several groups. These groups are linked to the roles we will be using.
  For example:  
  ```shell
  [rpi5]
  clearwhite
  plexpi
  tallglass

  [rpi4]
  prismpi

  [rpi3]
  pihole3
  clearblue

  [plex_servers]
  plexpi

  [radius_servers]
  prismpi

  [ldap_certs]
  prismpi
  clearwhite

  [ldap_servers]
  prismpi
  clearwhite

  [web_servers]
  clearwhite
  prismpi
  tallglass

  [monitoring_servers]
  prismpi

  [postfix_client]
  clearwhite
  prismpi
  pihole3
  clearblue
  plexpi
  tallglass
  ```

* ansible.cfg
  This file contains the "default" variables that are set, so you don't have to pass them on the command line.  
  For example  
  ```shell
  [defaults]
  inventory = inventory
  private_key_file = ~/.ssh/id_automate
  ansible_python_interpreter = auto_silent
  remote_user = automate
  deprecation_warnings = False
  ```



```shell
rademakr@clearblue[~/ansible/ansible-homelab]$ ssha
Agent pid 503453
Enter passphrase for /home/rademakr/.ssh/id_ed25519:
Identity added: /home/rademakr/.ssh/id_ed25519 (clearblue rademakr)
rademakr@clearblue[~/ansible/ansible-homelab]$ ansible-playbook -t bootstrap homelab.yml
```

```shell
PLAY [all] *****************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************
ok: [clearwhite]
ok: [plexpi]
ok: [tallglass]
ok: [prismpi]
ok: [clearblue]
ok: [pihole3]

TASK [update repo cache (Debian)] *****************************************************************************************************************************************
ok: [tallglass]
ok: [plexpi]
ok: [prismpi]
ok: [clearwhite]
ok: [clearblue]
ok: [pihole3]

PLAY [all] *****************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************
ok: [clearwhite]
ok: [plexpi]
ok: [tallglass]
ok: [prismpi]
ok: [clearblue]
ok: [pihole3]

TASK [bootstrap : create automate user] *****************************************************************************************************************************************
ok: [clearwhite]
ok: [tallglass]
ok: [plexpi]
ok: [prismpi]
ok: [clearblue]
ok: [pihole3]

TASK [bootstrap : add ssh key for user automate] *****************************************************************************************************************************************
ok: [tallglass]
ok: [plexpi]
ok: [clearwhite]
ok: [prismpi]
ok: [clearblue]
ok: [pihole3]

TASK [bootstrap : add sudoers file for automate] *****************************************************************************************************************************************
ok: [tallglass]
ok: [plexpi]
ok: [clearwhite]
ok: [prismpi]
ok: [pihole3]
ok: [clearblue]

TASK [bootstrap : Copy bash files with owner and permissions] *****************************************************************************************************************************************
ok: [clearwhite] => (item=.bashrc)
changed: [plexpi] => (item=.bashrc)
changed: [tallglass] => (item=.bashrc)
ok: [clearwhite] => (item=.bash_profile)
changed: [prismpi] => (item=.bashrc)
ok: [tallglass] => (item=.bash_profile)
ok: [plexpi] => (item=.bash_profile)
ok: [prismpi] => (item=.bash_profile)
ok: [pihole3] => (item=.bashrc)
changed: [clearblue] => (item=.bashrc)
ok: [clearblue] => (item=.bash_profile)
ok: [pihole3] => (item=.bash_profile)

TASK [bootstrap : Copy script files with owner and permissions] ******************************************************************************************************************************************
ok: [clearwhite] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)
ok: [plexpi] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)
ok: [tallglass] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)
ok: [prismpi] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)
ok: [clearblue] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)
ok: [pihole3] => (item=/home/rademakr/ansible/ansible-homelab/roles/bootstrap/files/scripts/motd.sh)

TASK [bootstrap : Copy etc files] ******************************************************************************************************************************************
ok: [clearwhite] => (item=/etc/DIR_COLORS)
ok: [plexpi] => (item=/etc/DIR_COLORS)
ok: [tallglass] => (item=/etc/DIR_COLORS)
ok: [prismpi] => (item=/etc/DIR_COLORS)
ok: [clearblue] => (item=/etc/DIR_COLORS)
ok: [pihole3] => (item=/etc/DIR_COLORS)

PLAY [all] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [clearwhite]
ok: [plexpi]
ok: [tallglass]
ok: [prismpi]
ok: [clearblue]
ok: [pihole3]

PLAY [ldap_certs] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [clearwhite]
ok: [prismpi]

PLAY [plex_servers] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [plexpi]

PLAY [postfix_client] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [clearwhite]
ok: [plexpi]
ok: [prismpi]
ok: [clearblue]
ok: [tallglass]
ok: [pihole3]

PLAY [radius_servers] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [prismpi]

PLAY [ldap_servers] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [clearwhite]
ok: [prismpi]

PLAY [web_servers] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [clearwhite]
ok: [tallglass]
ok: [prismpi]

PLAY [monitoring_servers] ******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [prismpi]

PLAY RECAP ******************************************************************************************************************************************
clearblue                  : ok=11   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
clearwhite                 : ok=14   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
pihole3                    : ok=11   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
plexpi                     : ok=12   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
prismpi                    : ok=16   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
tallglass                  : ok=12   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

```yaml
- name: Add SSH key for user automate
  tags: always
  # Explanation: This task adds an SSH key for the user 'automate' to enable SSH access.
  authorized_key:
    user: automate
    key: "ssh-ed25519 AAAAC3NzaC1lZDI1MTE5AAAAIA7IUa5QX6XCbRS7Et1pb87QCLH+HlV6e+Cqd6d4A/xn automate"

- name: Set PS1 prompt color
  tags: always
  # Explanation: This task sets the PS1 prompt color in the .bashrc file.
  replace:
    path: /home/rademakr/.bashrc
    regexp: '^\s*PS1=\$YELLOW'
    replace: '\t    PS1={{ ps1_color }}'

- name: Install systemd-timesyncd to keep the time
  tags: always
  # Explanation: This task installs the systemd-timesyncd package to synchronize system time.
  package:
    name: systemd-timesyncd
    state: latest

- name: Setup systemd-timesyncd
  tags: always
  # Explanation: This task configures systemd-timesyncd by updating the NTP server.
  replace:
    path: /etc/systemd/timesyncd.conf
    regexp: '^#NTP='
    replace: 'NTP=10.5.5.1'

- name: Install list of utilities
  tags: always
  # Explanation: This task installs a list of utilities (highlight, btop) on the system.
  package:
    name:
      - highlight
      - btop
    state: latest

- name: Copy .vimrc file with owner and permissions
  tags: always
  # Explanation: This task copies .vimrc and .bashrc files with specified ownership and permissions.
  copy:
    src: "{{item}}"
    dest: "/home/rademakr/"
    owner: "rademakr"
    group: "rademakr"
    mode: 0644
  with_fileglob:
    - ".vimrc"
    - ".bashrc"

- name: Set defaults in /etc/vim/vimrc
  tags: always
  # Explanation: This task sets defaults in the /etc/vim/vimrc configuration file.
  replace:
    path: /etc/vim/vimrc
    regexp: '^"set background=dark'
    replace: 'set background=dark'

- name: Copy /usr/local/bin script files with owner and permissions
  tags: always
  # Explanation: This task copies script files to /usr/local/bin with specified ownership and permissions.
  copy:
    src: "{{item}}"
    dest: "/usr/local/bin/"
    owner: "root"
    group: "root"
    mode: 0744
  with_fileglob:
    - "/usr/local/bin/*"

- name: Copy timestamp.chk file with owner and permissions
  # Explanation: This task copies timestamp.chk file to /var/tmp with specified ownership and permissions.
  copy:
    src: "{{item}}"
    dest: "/var/tmp/"
    owner: "root"
    group: "root"
    mode: 0644
    force: no
  with_fileglob:
    - "/var/tmp/timestamp.chk"

- name: Copy /etc/backup/BCKP-self.conf config file
  tags: always
  # Explanation: This task copies a configuration file to /etc/backup with specified ownership and permissions.
  copy:
    src: "{{item}}"
    dest: "/etc/backup/"
    owner: "root"
    group: "root"
    mode: 0644
  with_fileglob:
    - "/etc/backup/*"

- name: Update /etc/crontab
  tags: always
  # Explanation: This task updates the system crontab file with scheduled tasks.
  ansible.builtin.blockinfile:
    path: /etc/crontab
    block: |
      # System backup on dao
      02 1 * * *   root    nice -n 10      /usr/local/bin/backup-self.rsync.sh >/dev/null 2>&1
      # What needs upgrading?
      15 0 * * *     root     nice -n 10   /usr/local/bin/upgradesync >/dev/null 2>&1
```
