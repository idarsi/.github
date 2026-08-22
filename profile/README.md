## Welcome to ^(ida|arsi)$ collective!

> [!IMPORTANT]
> Everything here is under development and not considered as production quality

# iac_blueprint

**iac_blueprint is a declarative infrastructure model built on top of Ansible.**

Traditional Ansible automation is typically organized around playbooks, roles, tasks and variables. This works well, but as environments grow, infrastructure intent can become distributed across many roles, playbooks and implementation-specific variables.

iac_blueprint takes a different approach: **describe the infrastructure you want, rather than the sequence of automation required to build it.**

```yaml
iac_blueprint:
  postgresql:
    - version: 17
      instances:
        - name: main
          roles:
            - name: application
          databases:
            - name: application
              owner: application
```

The blueprint becomes the common description of the desired infrastructure state. Ansible roles interpret that model and handle the operating-system, application and configuration details required to reach it.

## Why use iac_blueprint instead of traditional Ansible?

iac_blueprint does not replace Ansible. It provides a higher-level abstraction on top of it.

With traditional Ansible, the infrastructure design and the implementation are often closely coupled. Users need to understand which roles to run, which states or tasks are required, which variables belong to which role, and in which order operations should happen.

With iac_blueprint, those implementation details can remain inside reusable roles.

This provides several advantages:

* **Infrastructure is described as architecture, not as an execution procedure.**
* **A common data model can span multiple technologies**, such as PostgreSQL, Nginx, Podman, networking, identities and certificates.
* **Complex services can be represented with relatively small inventories** while still allowing detailed configuration when required.
* **Implementation knowledge stays inside the roles**, reducing duplication between environments.
* **Validation can happen before changes are applied**, making invalid infrastructure definitions easier to detect.
* **The same blueprint can support different operations** such as installation, removal, start, stop, backup, restore or validation without duplicating the infrastructure definition.
* **Policies and compliance requirements can be implemented in the automation layer** instead of being manually reproduced in every environment.

The result is a separation between two concerns:

**iac_blueprint defines WHAT the infrastructure should look like.
Ansible defines HOW that state is achieved.**

This makes Ansible automation easier to reuse, infrastructure definitions easier to understand, and larger environments easier to manage consistently.

## Project

The repositories under the **idarsi** organization contain the reusable Ansible roles and supporting components that implement the iac_blueprint model.

The project is being developed toward a common infrastructure abstraction where increasingly complex environments can be designed, validated, deployed and maintained from the same declarative blueprint.

### Repositories

| Name                          | Maturity Level     |
|-------------------------------|--------------------|
| ansible-iac-role-barman       | Experimental       |
| ansible-iac-role-dovecot      | Alpha              |
| ansible-iac-role-firewalld    | Alpha              |
| ansible-iac-role-nginx        | Alpha              |
| ansible-iac-role-podman       | Beta               |
| ansible-iac-role-postfix      | Beta               |
| ansible-iac-role-postgresql   | Beta               |

| Maturity Level   | Description                                                                     |
| ---------------- | ------------------------------------------------------------------------------- |
| **Experimental** | Early prototype. Interfaces and behavior may change freely.                     |
| **Alpha**        | Core functionality works, but important features or tests are still incomplete. |
| **Beta**         | Main functionality is complete and tested, but changes may still occur.         |
| **Stable**       | Production-ready, well tested, and backward compatibility is expected.          |
| **Mature**       | Proven stable in long-term real-world use with broad test coverage.             |


