## Welcome to ^(ida|arsi)$ collective!

> [!IMPORTANT]
> Everything here is under development and is not yet considered production-ready. Maturity levels and RC Readiness scores are engineering assessments, not production approval or certification.

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

With iac_blueprint, those implementation details can remain inside reusable roles. Role selection and execution order still need to be defined where services depend on one another.

This provides several advantages:

* **Infrastructure is described as architecture**, with the implementation handled by reusable roles.
* **A common data model can span multiple technologies**, such as PostgreSQL, Nginx, Podman, Kubernetes, storage, networking and local identities.
* **Complex services can be represented with relatively small inventories** while still allowing detailed configuration when required.
* **Implementation knowledge stays inside the roles**, reducing duplication between environments.
* **Validation can happen before changes are applied**, making invalid infrastructure definitions easier to detect.
* **The same blueprint can support different operations**, such as installation, removal, start, stop or validation, according to each role's supported states.
* **Policies and compliance requirements can be implemented in the automation layer** instead of being manually reproduced in every environment. This is an extension mechanism, not a claim of compliance certification.

The result is a separation between two concerns:

**iac_blueprint defines WHAT the infrastructure should look like.
Ansible defines HOW that state is achieved.**

This makes Ansible automation easier to reuse, infrastructure definitions easier to understand, and larger environments easier to manage consistently.

## Capabilities

The repositories cover both individual services and more complex deployments. Examples include:

* **PostgreSQL:** multiple versions and instances, databases, roles, extensions, physical replication, and optional Patroni clusters with explicitly enabled role-managed etcd.
* **Backup management:** a separate Barman role for backup-host configuration and PostgreSQL server definitions. End-to-end backup and restore verification remains an outstanding test area.
* **Containers:** Podman container lifecycle management and optional rootful Quadlet integration for systemd-managed workloads.
* **Kubernetes:** containerd or CRI-O, control-plane bootstrap, worker joining, Flannel, and declared applications using YAML manifests or Helm charts.
* **Storage:** experimental Ceph bootstrap, explicitly declared OSD provisioning, pools, CephFS and client mounts.
* **Web and traffic management:** Nginx sites and reverse proxies, and HAProxy frontends and backends.
* **Mail and access:** Postfix, Dovecot, firewalld, and local users, groups, SSH authorized keys and sudo rules.

Feature scope, prerequisites and operational limitations are documented in each repository. Support for a capability does not imply that every deployment combination has been verified.

## Validation and operational safety

Safety controls are implemented within individual roles and vary by service. Examples include:

* Blueprint validation before state-specific changes.
* Nginx and HAProxy configuration syntax validation before accepting configuration changes.
* Dovecot candidate configuration validation and guarded configuration restoration after a failed reload.
* TLS certificate verification enabled by default for Nginx HTTPS proxy upstreams and HAProxy TLS backends.
* Explicit opt-ins, ownership markers and path checks for selected destructive operations.
* Protection of sensitive configuration and private keys through restrictive permissions and suppressed output where implemented.

These controls do not make deployments transactional. A failed run can leave some changes applied, and rollback support is limited to the resources documented by each role.

## Testing and development

The project includes a shared [test environment](https://github.com/idarsi/idarsi-ansible-test-environments) with two complementary backends:

* **Podman** for fast container-based scenarios.
* **KVM + Kickstart** for full virtual machines, including SELinux and firewall behavior.

Reusable virtual-machine base images and disposable copy-on-write overlays provide repeatable test environments. Separate, pinned Ansible environments support testing across multiple Ansible Core versions.

Role-specific Molecule scenarios remain in each role repository. Depending on the role, they exercise input validation, convergence, idempotence, lifecycle operations and safety controls.

**Declared platform support and verified test coverage are separate.** Coverage varies by role, operating-system version, backend and operation. Container or UBI results do not establish full RHEL virtual-machine coverage. Each repository's `TESTING.md` describes the available evidence and remaining gaps.

The [development and testing guides](https://github.com/idarsi/idarsi-ansible-role-development-guide) document common expectations for role contracts, validation, maintainability, automated tests and CI. The [shared task library](https://github.com/idarsi/ansible-iac-shared-tasks) provides reusable filesystem, bind-mount, cron and Git operations for roles that consume it.

## Project

The repositories under the **idarsi** organization contain the reusable Ansible roles and supporting components that implement the iac_blueprint model.

The project is being developed toward a common infrastructure abstraction where increasingly complex environments can be designed, validated, deployed and maintained from the same declarative blueprint.

### Repositories

The following table summarizes the public repositories reviewed for this overview. Maturity and readiness values are snapshots; each repository's own documentation is authoritative.

| Repository | Maturity | RC Readiness |
|------------|----------|--------------|
| [ansible-iac-role-barman](https://github.com/idarsi/ansible-iac-role-barman) | Beta | 84% |
| [ansible-iac-role-ceph](https://github.com/idarsi/ansible-iac-role-ceph) | Beta | 81% |
| [ansible-iac-role-dovecot](https://github.com/idarsi/ansible-iac-role-dovecot) | Beta | 78% |
| [ansible-iac-role-firewalld](https://github.com/idarsi/ansible-iac-role-firewalld) | Alpha | 63% |
| [ansible-iac-role-haproxy](https://github.com/idarsi/ansible-iac-role-haproxy) | Beta | 77% |
| [ansible-iac-role-kubernetes](https://github.com/idarsi/ansible-iac-role-kubernetes) | Beta | 80% |
| [ansible-iac-role-local-identity](https://github.com/idarsi/ansible-iac-role-local-identity) | Release Candidate | 100% |
| [ansible-iac-role-nginx](https://github.com/idarsi/ansible-iac-role-nginx) | Beta | 86% |
| [ansible-iac-role-podman](https://github.com/idarsi/ansible-iac-role-podman) | Beta | 81% |
| [ansible-iac-role-postfix](https://github.com/idarsi/ansible-iac-role-postfix) | Beta | 80% |
| [ansible-iac-role-postgresql](https://github.com/idarsi/ansible-iac-role-postgresql) | Beta | 80% |
| [ansible-iac-shared-tasks](https://github.com/idarsi/ansible-iac-shared-tasks) | Alpha | 43% |

Supporting repositories:

| Repository | Purpose |
|------------|---------|
| [idarsi-ansible-role-development-guide](https://github.com/idarsi/idarsi-ansible-role-development-guide) | Shared role development and testing guidance |
| [idarsi-ansible-test-environments](https://github.com/idarsi/idarsi-ansible-test-environments) | Reusable Podman and KVM test infrastructure |

### Maturity

Maturity describes the assessed scope and evidence for a repository. Individual features may have stricter limitations than the overall repository label.

| Maturity Level | Description |
|----------------|-------------|
| **Experimental** | Early prototype. Interfaces and behavior may change freely. |
| **Alpha** | Core functionality exists, but important features or verification remain incomplete. |
| **Beta** | Substantial functionality is implemented and tested within documented limits. Important verification gaps or interface changes may remain. |
| **Release Candidate** | Meets the documented release-candidate criteria for its declared scope. Awaiting stable-release approval. |
| **Stable** | Approved for production use within its documented scope, with established testing and compatibility expectations. |
| **Mature** | Proven stable through long-term real-world use and sustained maintenance. |

### RC Readiness

RC Readiness is an approximate engineering assessment of progress toward release-candidate criteria. It considers areas such as functionality, validation and safety, convergence and recovery, testing, CI and documentation.

**A score of 100% means that the assessed release-candidate criteria have been met. It does not mean production approval, complete platform coverage, absence of vulnerabilities or compliance certification.**

Scores should be read together with the repository's assessment reference, test evidence and outstanding limitations. Some assessments include local working-tree results that have not yet been confirmed by CI.
