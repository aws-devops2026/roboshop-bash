roboshop-bash
In this repo, we will write bas script to do the configuration management for roboshop on linux redhat 9.

We also need to make sure that we are going to write the code keeping the multi-environment strategy in to consideration.

This is exepcted to run on the top of the component servers, to execute the script frontend.sh, we need to be on frontend server.

Login to frontend server
Do a git clone of your repo that has bash for roboshop
and then switch to the repo and then run $ bash frontend.sh
Whenever we deal things that scale, we need an NFR that has to be followed across the board!
NFR: Non-Functional Requirement

1) When we automate something, the re-run of that should work without any disruption.
2) When you restart the compoments, apps should come up automatically.
3) Code should always support multi-environment. ( dev , prod ) 
            shipping-dev.roboshop.internal 
            shipping-prod.roboshop.internal
4) When you see some value is repeated multiple times, make sure it's parameterized
5) All the backend components should be accessed privately with private dns records
Basics are important

If I say run the script test.sh
git clone or git pull the repo
Make sure that script is in the folder that you're running
ls -ltr
bash test.sh
We can buy domain in aws as well, but if we already have a domain, we can move it to AWS Route53 or buy a domain on Hostinger or GoDaddy and can be managed by AWS Route53 using Domain Transer
Going forward 1) We will create A Type DNS Record with Public IP Address for frontend and for rest of the components, we will create A Type records using the private ip address of the component.

How can we manage a domain that we bought on HOSTINGER or GODADDY to be managed from Cloud ? 1) To do that, create a Public Hosted zone with the domain you bought like sample.com on AWS Route53 2) Then AWS offers you the nameservers, whch are the windows servers managed by the AWS. 3) Then copy these nameservers on AWS and replace them with what you see on hostiner domain.

This way, requests related to your domain will be forwated to the hosted zone created by you on AWS Route53. Once you update the Nameservers of AWS Route53 Hosted zone on your domain provider, it's going to take 1 to 24 hrs of time for them to work on AWS (for the first time )

When you write a script or code, ensure the code is not duplicated. Always make sure the code is DRY!!!!
DRY vs WET Code ?

DRY: Don't Repeat Yourself Core Principle: Every piece of knowledge or logic must have a single, unambiguous, authoritative representation in a system.

Advantages: Makes code easier to maintain, test, and scale. Changes only need to be applied in one place. Best Used For: Complex logic, database queries, API calls, and validation logic.

WET Code is a code that's written multiple times.

Executive Summary — Configuration Management Tools
To overcome the limitations of traditional Bash scripting and ensure automation works consistently across multiple Linux distributions such as Red Hat Enterprise Linux, Ubuntu, Debian, and Kali Linux, organizations adopt Configuration Management tools.

Platform-Independent Automation Configuration Management tools abstract OS-specific differences, allowing administrators to define infrastructure and application states once and execute them consistently across multiple Linux flavors.

Centralized and Scalable Execution Unlike Bash scripting, where administrators often need to log in to individual servers and execute scripts manually, Configuration Management tools enable centralized execution across hundreds or thousands of servers from a single control node without manually accessing each system.

Declarative and Maintainable Infrastructure These tools simplify infrastructure management through declarative automation, reducing imperative step-by-step scripting and improving readability, consistency, scalability, and maintainability.

Popular Industry Tools Widely adopted Configuration Management solutions include:

Chef
Puppet
Ansible — an open-source automation platform later backed by Red Hat and now part of IBM.
#...............................................................................................
................................................................................................

### Executive Summary — roboshop-ansible

The `roboshop-ansible` repository is intended to serve as a centralized platform for learning Ansible basics and implementing Configuration Management for all Roboshop components within the `roboshop/` directory.

1. **Modern Configuration Management with Ansible**
   Ansible enables infrastructure and application automation in a scalable, declarative, and platform-independent manner, reducing the operational complexity associated with traditional Bash scripting.

2. **Supports Both Push and Pull Automation Models**
   Ansible is one of the few Configuration Management tools that supports both:

   * **Push Mechanism** — where a centralized Ansible control node connects to target servers over SSH (port 22) and pushes configurations remotely.
   * **Pull Mechanism** — where managed nodes independently pull configurations from a Git repository and apply them locally.

3. **Push Mechanism Characteristics**
   Push-based automation requires:

   * Ansible installed on the central control node.
   * Network connectivity between the control node and managed servers.
   * Proper SSH authentication and credentials management, commonly through a dedicated `ansible-user`.
   * Stable or known server IPs and network accessibility.

4. **Pull Mechanism Characteristics**
   Pull-based automation requires:

   * Ansible installed directly on the managed node.
   * Connectivity from the node to the Git repository containing automation code.
   * Distributed execution capability without depending on centralized inbound connectivity.

5. **Declarative Automation Using YAML Playbooks**
   Unlike Bash, where automation logic is written as imperative scripts, Ansible uses **Playbooks** written in YAML (Yet Another Markup Language). YAML-based automation is declarative, human-readable, easier to maintain, and better suited for large-scale infrastructure management.


# Executive Summary — Introduction to Ansible

## What is Ansible?

Ansible is an open-source automation and configuration management tool used for:

* Server provisioning
* Application deployment
* Configuration management
* Infrastructure automation

It is agentless, meaning no software needs to be installed on target servers. It primarily uses SSH for communication.

---

# Installing Ansible

## Core Concept

Ansible is a Python-based application and is commonly installed using Python package managers like `pip`.

### Default Installation via DNF

```bash
dnf install ansible -y
```

* This usually installs **ansible-core** from the OS repository.
* On RHEL/Rocky/AlmaLinux 9, this often provides:

  * `ansible-core 2.14`
  * Equivalent to Red Hat Ansible Automation Platform version 7-era tooling.

---

## Installing Latest Ansible via PyPI

[PyPI Ansible Package](https://pypi.org/project/ansible/?utm_source=chatgpt.com)

### Why PyPI?

PyPI provides the latest upstream Ansible versions faster than OS repositories.

### Python Requirement

Latest Ansible releases require newer Python versions (Python 3.12+ recommended).

### Suggested Installation Steps

```bash
dnf install python3.12 -y

pip3.12 install ansible
```

### Important Correction

The original note mentions:

```bash
dnf remove python -y
```

This is risky and not recommended because:

* System Python is required by the operating system.
* Removing it can break package management and core OS tools.

Instead:

* Install Python 3.12 alongside the existing version.
* Use `pip3.12`.

---

 ansible -i 172.31.23.125,172.31.24.238, all -e ansible_user=ec2-user -e ansible_password=DevOps321 -m ansible.builtin.ping
 ..
  ansible -i inventory prod -e ansible_user=ec2-user -e ansible_password=DevOps321 -m ansible.builtin.ping

# Inventory in Ansible

Ansible manages servers using an **Inventory** file.

The inventory contains:

* IP addresses
* Hostnames
* Groups of servers

Example:

```ini
[dev]
172.31.22.44

[prod]
172.31.22.26
```

---

# How Ansible Works

Ansible operates mainly using:

1. **Ad-hoc Commands**
2. **Playbooks**

---

# 1) Ad-hoc Commands

Ad-hoc commands are:

* One-line commands
* Used for quick operations
* Best for testing or small tasks

## Example: Ping Servers

```bash
ansible -i inventory all \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.ping
```

### Key Components

| Component      | Meaning                |
| -------------- | ---------------------- |
| `-i inventory` | Inventory file         |
| `all`          | All hosts in inventory |
| `-e`           | Extra variables        |
| `-m`           | Module name            |

---

## Using Groups

```bash
ansible -i inventory prod \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.ping
```

Here:

* `prod` refers to a server group in inventory.

---

## Installing Packages via Ad-hoc Command

```bash
ansible -i inventory dev \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.package \
-a "name=nginx state=present" \
--become
```

### What this does

* Targets all servers in the `dev` group
* Installs `nginx`
* `--become` executes commands with elevated privileges (sudo/root)

---

# 2) Playbooks

Playbooks are YAML-based automation files that allow:

* Multiple tasks
* Sequential execution
* Reusability
* Infrastructure as Code (IaC)

Example capabilities:

* Install packages
* Configure services
* Deploy applications
* Restart services

---

# Key Takeaways

* Ansible is an agentless automation tool built on Python.
* Latest versions are best installed via PyPI using modern Python versions.
* Inventory files define managed servers and groups.
* Ad-hoc commands are suitable for quick operations.
* Playbooks provide scalable and reusable automation workflows.
* Modules are the building blocks of Ansible automation.