# Ansible RoboShop Roles

Role-based Ansible automation for deploying the **RoboShop** microservices e-commerce application. Unlike a flat playbook setup, this repo organizes each service into its own reusable **Ansible role**, following standard Ansible best practices.

## What This Does

RoboShop is a microservices-based e-commerce demo application made up of several backend services (catalogue, cart, shipping, payment, user, frontend) and datastores (MySQL, MongoDB, Redis, RabbitMQ). This repo structures the deployment of each component as an independent, reusable role, orchestrated by a single top-level playbook.

## Project Structure

```
.
├── group_vars/
│   └── mysql/
│       └── all.yaml          # Variables scoped to the mysql host group
├── include-import/
│   ├── tasks/                # Reusable task files pulled in via include/import
│   └── vars/                 # Variables used by the include-import tasks
└── roles/
    ├── cart/                 # Cart service role
    ├── catalogue/            # Catalogue service role
    ├── common/               # Shared/baseline tasks applied across all hosts
    ├── demo/
    │   └── tasks/
    ├── frontend/             # Frontend (Nginx) role
    ├── mongodb/              # MongoDB role
    ├── mysql/                # MySQL role
    ├── payment/              # Payment service role
    ├── rabbitmq/             # RabbitMQ role
    ├── redis/
    │   └── tasks/
    ├── shipping/             # Shipping service role
    └── user/                 # User service role
```

Each role under `roles/` follows the standard Ansible role layout (`tasks/`, and optionally `handlers/`, `templates/`, `vars/`, `defaults/`) and is responsible for installing, configuring, and starting its corresponding service or datastore. The `common` role holds baseline setup shared across all hosts (e.g. package repos, users), while `include-import` holds task/variable files that are explicitly included or imported into other roles rather than run as a standalone role.

## Prerequisites

- Ansible installed on the control node (`pip install ansible` or `dnf install ansible`)
- SSH access to all target EC2 instances, with keys configured
- An inventory file listing target hosts under groups matching each role (e.g. `mysql`, `mongodb`, `cart`, `catalogue`, etc.)
- Python installed on all managed nodes (required by Ansible)

## Usage

1. Review and update `group_vars/mysql/all.yaml` (and any other group-specific variable files you add) for your environment.
2. Update your inventory file with target hosts, matching the host groups expected by each role.
3. Verify connectivity to all hosts:

   ```bash
   ansible all -m ping
   ```

4. Run the full deployment using your top-level playbook:

   ```bash
   ansible-playbook site.yaml
   ```

5. To apply a single role only, use tags or `--limit`:

   ```bash
   ansible-playbook site.yaml --tags catalogue
   ```

## Why Roles Instead of Flat Playbooks

This repo is a refactor of the simpler flat-playbook approach (see the companion [`ansible-roboshop`](https://github.com/Naga-Sai-Prasanna/ansible-roboshop) repo) into Ansible's role-based structure. Roles make each service:

- **Reusable** — a role can be applied to any host or reused across projects
- **Testable in isolation** — run or debug a single role without touching others
- **Organized** — tasks, variables, and templates for a service live together instead of in one large file

## Status

This is a personal learning/practice project for DevOps automation (Ansible roles + AWS EC2), part of a broader RoboShop deployment exercise also covering plain Ansible playbooks, Shell scripting, Terraform, Docker, and EKS in separate repos.
