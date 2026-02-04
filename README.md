# Learning Vagrant & Ansible By Provisioning VM + Podman

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Vagrant](https://img.shields.io/badge/Vagrant-2.4+-1868F2?logo=vagrant)](https://www.vagrantup.com/)
[![Ansible](https://img.shields.io/badge/Ansible-2.9+-EE0000?logo=ansible)](https://www.ansible.com/)
[![VirtualBox](https://img.shields.io/badge/VirtualBox-7.0+-183A61?logo=virtualbox)](https://www.virtualbox.org/)

This repository contains a Vagrant configuration to set up a development environment for Podman with REST API enabled. The virtual machine (VM) is provisioned using VirtualBox and is pre-configured with necessary specifications and networking settings.

---

## Table of Contents

- [Infrastructure Information](#infrastructure-information)
- [Architecture Overview](#architecture-overview)
- [File Structure](#file-structure)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Getting Started](#getting-started)
- [Ansible Playbook Details](#ansible-playbook-details)
- [Networking](#networking)
- [REST API Usage](#rest-api-usage)
- [Troubleshooting](#troubleshooting)
- [Additional Notes](#additional-notes)
- [Contributing](#contributing)
- [References](#references)
- [License](#license)

---

## Infrastructure Information

- **Operating System**: Ubuntu Jammy 64-bit (`ubuntu/jammy64`)
- **CPU and Memory Allocation**: 8 CPUs and 8GB RAM
- **Networking**:
  - Private network with static IP: `192.168.1.10`
  - Forwarded ports for HTTP (80), HTTPS (443), custom services (8080, 10001), and Podman REST API (8888)
- **Shared Folder**: Synced folder between host and guest (`/vagrant`)
- **Provisioning**: Uses Ansible for provisioning tasks via `ansible-playbook.yml`
- **Custom VM Settings**:
  - VM Name: `DEVPODMAN-01-ENV01`
  - Cluster Name: `Development Container Engine Podman REST API 01`

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        Host Machine                         │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                    vagrant up                         │  │
│  └───────────────────────┬───────────────────────────────┘  │
│                          ▼                                  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              VirtualBox VM (Ubuntu Jammy)             │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │              Ansible Local Provisioner          │  │  │
│  │  │  ┌───────────────────────────────────────────┐  │  │  │
│  │  │  │  Stage 1: Install Podman + Dependencies  │  │  │  │
│  │  │  │  Stage 2: Configure Rootless Podman      │  │  │  │
│  │  │  │  Stage 3: Enable REST API Socket         │  │  │  │
│  │  │  │  Stage 4: Enable REST API over TCP       │  │  │  │
│  │  │  │  Stage 5: Test with hello-world          │  │  │  │
│  │  │  │  Stage 6: Verify REST API                │  │  │  │
│  │  │  └───────────────────────────────────────────┘  │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  │                          ▼                            │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │    Podman Engine + REST API (Ready to Use)     │  │  │
│  │  │    Socket: /run/user/1000/podman/podman.sock   │  │  │
│  │  │    TCP: http://192.168.1.10:8888               │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

---

## File Structure

```
├── Vagrantfile              # VM configuration (VirtualBox provider)
├── ansible-playbook.yml     # Ansible playbook for Podman setup
├── ansible-requirements.yml # Ansible Galaxy dependencies
├── README.md                # Project documentation
├── LICENSE                  # GPLv3 license
└── .gitignore               # Git ignore rules
```

---

## Prerequisites

1. **Vagrant**: [Install Vagrant](https://www.vagrantup.com/downloads)
2. **VirtualBox**: [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads)
3. **Ansible**: Required on the guest VM for provisioning (automatically installed by Vagrant).

---

## Quick Start

For experienced users, get up and running in 3 commands:

```bash
git clone https://github.com/Adhito/learning-devops-vagrant-ansible-env-podman
cd learning-devops-vagrant-ansible-env-podman
vagrant up
```

Then access the VM:
```bash
vagrant ssh devpodman-01
```

---

## Getting Started

#### Stage 1. Clone the Repository
```bash
git clone https://github.com/Adhito/learning-devops-vagrant-ansible-env-podman
cd learning-devops-vagrant-ansible-env-podman
```

#### Stage 2. Customize Configuration (Optional)

You can modify VM settings by editing the following variables in the `Vagrantfile`:

| Variable                 | Description                                | Default Value         |
|--------------------------|--------------------------------------------|-----------------------|
| `VM_TOTAL_CPU`           | Number of CPUs allocated to the VM         | `8`                   |
| `VM_TOTAL_MEMORY`        | RAM allocated to the VM (in MB)            | `8192`                |
| `VM_IP_ADDRESS`          | Private network IP address                 | `192.168.1.10`        |
| `VM_NAME`                | Name of the VM in VirtualBox               | `DEVPODMAN-01-ENV01` |
| `VAGRANT_CLUSTER_NAME`   | Group name for the VM in VirtualBox         | `Development Container Engine Podman REST API 01` |

---

#### Stage 3. Start the VM

Run the following command to spin up the environment:

```bash
vagrant up
```

Run the following command to check the environment status:

```bash
vagrant status
```

#### Stage 4. Access the VM

To SSH into the VM, use the following command:

```bash
vagrant ssh devpodman-01
```

```powershell
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Nov 18 02:53:16 UTC 2024

  System load:  1.33               Processes:               172
  Usage of /:   30.1% of 38.70GB   Users logged in:         0
  Memory usage: 3%                 IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%


vagrant@ubuntu-jammy:~$ podman --version
podman version 3.4.4
vagrant@ubuntu-jammy:~$ podman ps
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
```


#### Stage 5. Provisioning with Ansible

The VM uses Ansible for provisioning. Ensure the following files are present in the repository:

- **`ansible-playbook.yml`**: Main playbook for provisioning tasks.
- **`ansible-requirements.yml`**: Contains collection requirements for Galaxy.

To setup the Podman Engine and other VM settings Ansible are choosen for configuring the environment. Vagrant supports two types of provisioners: the traditional [ansible], which requires a Linux-based host system, and [ansible_local], which is ideal when the host system is not Linux. When using the ansible_local provisioner, Vagrant will automatically install Ansible on the guest VM and execute the playbooks there. For more information, refer to the official documentation.

To use ansible_local, the minimum configuration requires specifying a [playbook.yml] file, which Vagrant will look for in the current project directory.

Provisioning runs automatically when the VM is created with `vagrant up`. To manually re-provision the VM, use:

```bash
vagrant provision
```

---

## Ansible Playbook Details

The `ansible-playbook.yml` executes six stages:

| Stage | Task | Description |
|-------|------|-------------|
| 1 | Install Podman & Prerequisites | Installs podman, dbus-user-session, uidmap, slirp4netns, fuse-overlayfs |
| 2 | Configure Rootless Podman | Enables lingering, configures subuid/subgid for vagrant user |
| 3 | Enable REST API Socket | Starts podman.socket as systemd user service |
| 4 | Enable REST API over TCP | Creates and starts podman-tcp.service on port 8888 |
| 5 | Test with Hello-World | Pulls and runs hello-world container using containers.podman modules |
| 6 | Verify REST API | Tests both socket and TCP API endpoints |

**Key Features:**
- Uses `become: true` for privilege escalation during package installation
- Configures rootless Podman with proper subuid/subgid mappings
- Enables user lingering so systemd user services persist after logout
- Exposes REST API via both Unix socket and TCP port 8888
- Automatically tests Podman with hello-world container and verifies API access

---

## Networking

The VM is accessible via the following ports:

| Service            | Guest Port | Host Port | IP Address      |
|--------------------|------------|-----------|-----------------|
| HTTP               | 80         | 80        | 192.168.1.10    |
| HTTPS              | 443        | 443       | 192.168.1.10    |
| Custom Service 1   | 8080       | 8080      | 192.168.1.10    |
| Podman REST API    | 8888       | 8888      | 192.168.1.10    |
| Custom Service 2   | 10001      | 10001     | 192.168.1.10    |

---

## REST API Usage

The Podman REST API is accessible via two methods:

### Socket Access (Inside VM)

```bash
# Get Podman info
curl --unix-socket /run/user/1000/podman/podman.sock http://localhost/v4.0.0/libpod/info

# List containers
curl --unix-socket /run/user/1000/podman/podman.sock http://localhost/v4.0.0/libpod/containers/json

# List images
curl --unix-socket /run/user/1000/podman/podman.sock http://localhost/v4.0.0/libpod/images/json
```

### TCP Access (From Host Machine)

```bash
# Get Podman info
curl http://192.168.1.10:8888/v4.0.0/libpod/info

# List containers
curl http://192.168.1.10:8888/v4.0.0/libpod/containers/json

# List images
curl http://192.168.1.10:8888/v4.0.0/libpod/images/json

# Docker-compatible endpoint
curl http://192.168.1.10:8888/v1.40/info
```

### Using with Docker CLI

You can use the Docker CLI with Podman's REST API:

```bash
# Set the Docker host to Podman's TCP endpoint
export DOCKER_HOST=tcp://192.168.1.10:8888

# Now use docker commands
docker ps
docker images
```

---

## Troubleshooting

### Port Already in Use
If you get port conflict errors, modify the port mappings in `Vagrantfile` or stop the conflicting service.

### Provisioning Failed
Re-run provisioning:
```bash
vagrant provision
```

### VM Won't Start
Check VirtualBox is running and has enough resources:
```bash
vagrant status
VBoxManage list runningvms
```

### Podman Socket Not Available
SSH into the VM and verify socket status:
```bash
vagrant ssh devpodman-01
systemctl --user status podman.socket
```

### REST API TCP Service Not Running
Check the TCP service status:
```bash
vagrant ssh devpodman-01
systemctl --user status podman-tcp.service
```

### Rootless Podman Permission Issues
Verify subuid/subgid configuration:
```bash
vagrant ssh devpodman-01
cat /etc/subuid | grep vagrant
cat /etc/subgid | grep vagrant
```

### Reset Everything
```bash
vagrant destroy -f
vagrant up
```

---

## Additional Notes

- **To stop the VM**:
  Use the following command to gracefully stop the VM:
  ```bash
  vagrant halt
  ```
- **To destroy the VM**:
  Use the following command to destroy the VM:
  ```bash
  vagrant destroy
  ```

---

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## References

- [Vagrant Documentation](https://developer.hashicorp.com/vagrant/docs)
- [Ansible Documentation](https://docs.ansible.com/)
- [Ansible Local Provisioner](https://developer.hashicorp.com/vagrant/docs/provisioning/ansible_local)
- [Podman Documentation](https://docs.podman.io/)
- [Podman REST API](https://docs.podman.io/en/latest/markdown/podman-system-service.1.html)
- [Containers.Podman Ansible Collection](https://docs.ansible.com/ansible/latest/collections/containers/podman/)
- [VirtualBox Documentation](https://www.virtualbox.org/manual/)

---

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.
