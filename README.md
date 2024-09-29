# Server_prepare

## Description

Repository contains Ansible playbook and role for server preconfiguring according to the task:

#### Goal: Write an Ansible role and playbook to prepare the server for operation
#### Task: Prepare the OS using Ansible
##### Using Ansible, implement the ability to perform the following actions (and repeat them on other servers in the future):
- Implement the procedure of encrypting the second disk in the system (where there is no root partition) (partition name should be specified in the inventory).
- Implement a procedure to encrypt the partition that is present on the disk next to the root partition.
- Disabling C-state for all available CPUs.
- Switching CPU operation from power-saving mode to more productive mode.
- Rename the active network interface to "net0". Display information about the renamed interface during the playbook. At the end of the playbook execution, a list of CPUs should be displayed, as well as information about Intel Hyper-Threading (AMD multithreading).
##### General Requirements:
- When designing playbooks and roles, attention should be paid to error handling and correct task execution messages.
- Each playbook or role should be described in a separate README file along with startup and validation instructions.
All required files (playbooks, roles, scripts) must be submitted along with the result of the assignment.
##### Evaluation: The candidate will be evaluated on the following criteria:
- Quality of writing Ansible playbooks and roles.
- Overall code organization.
- Understanding of basic Ansible concepts and system administration.

## Running playbooks guide

### WARNING! Playbook encrypts target partition/disk without user confirmation. All the data on these partition and disk will be lost!

- Clone the repo into /opt folder
- `cd server_prepare`
- Create python3 virtual environment in /opt/server_prepare - `python3 -m venv .`
- Install python3 modules accordingly to description of environment below
- Update variable `reboot_approved` (`true` - automatic reboot, `false` - manual reboot) in the playbook
- Add needed host into the inventory
- Update variable `encrypted_disk` in the inventory (this disk/partition will be encrypted by the playbook along side with next to the root partition if that will be found)
- Run `ansible-playboot -e "target=NAME_OF_HOST/GROUP" playbooks/server_prepare.yml`

## Environment

```
root@w-ansible:/opt/ansible# ansible --version
ansible [core 2.17.4]
  config file = /opt/ansible/ansible.cfg
  ...
  python version = 3.11.2 (main, Aug 26 2024, 07:20:54) [GCC 12.2.0] (/opt/ansible/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
#### Python3 modules in virtual environment
```
root@w-ansible# pip freeze
ansible==10.4.0
ansible-core==2.17.4
certifi==2024.8.30
cffi==1.17.1
charset-normalizer==3.3.2
cryptography==43.0.1
idna==3.10
Jinja2==3.1.4
MarkupSafe==2.1.5
netaddr==1.3.0
packaging==24.1
pycparser==2.22
pynetbox==7.4.0
pytz==2024.2
PyYAML==6.0.2
requests==2.32.3
resolvelib==1.0.1
urllib3==2.2.3
```

## Test environment
Two hosts with Ubuntu 24.04

## Repository structure

```
root@w-ansible:/opt/ansible# tree playbooks
playbooks
├── roles
│   └── server_prep
│       ├── defaults
│       │   └── main.yml
│       ├── files
│       │   └── passphrase
│       ├── handlers
│       │   └── main.yml
│       ├── meta
│       │   └── main.yml
│       ├── README.md
│       ├── tasks
│       │   ├── cpu_config.yml
│       │   ├── defined_partition.yml
│       │   ├── encrypt_disk.yml
│       │   ├── main.yml
│       │   ├── network_config.yml
│       │   └── next_partition.yml
│       ├── templates
│       │   └── 70-persistent-net.rules.j2
│       ├── tests
│       │   ├── inventory
│       │   └── test.yml
│       └── vars
│           └── main.yml
└── server_prepare.yml
```
#### hosts.yml - simple YAML inventory
#### playbooks/server_prepare.yml - main playbook
#### playbooks/roles/server_prep - main role:
- templates/70-persistent-net.rules.j2 - template for udev-rule used for renaming of network interface
- tasks/cpu_config.yml - tasks for CPU configuration
- tasks/defined_partition.yml - tasks for encrypting partition/device defined in the inventory
- tasks/encrypt_disk.yml - used in both next_partition and defined_partition as an include, does encrypting of the provided target
- tasks/main.yml - main tasks of roles, includes other files
- tasks/network_config.yml - task for updating of networking configuration
- tasks/next_partition.yml - tasks for encrypting next to the root partition if found and suitable
- files/passphrase - passphrase used in encryption of disks/partitions
##### playbook-run-result - contains output after running of the playbook on host provided by the employer



