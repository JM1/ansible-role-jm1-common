# Ansible Role: jm1.common

This role defines additional facts, e.g. to identify distributions.

*Details*

* New `distribution_id` fact: A [list](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)
  that uniquely identifies a distribution release. For example:
  - `distribution_id: [ 'CentOS', '8' ]` for `CentOS 8 (Core)`
  - `distribution_id: [ 'Debian', '10' ]` for `Debian 10 (Buster)`
  - `distribution_id: [ 'Red Hat Enterprise Linux', '8' ]` for `Red Hat Enterprise Linux (RHEL) 8`
  - `distribution_id: [ 'Ubuntu', '20.04' ]` for `Ubuntu 20.04 LTS (Focal Fossa)`

* New `distribution_codename` fact: A [list](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)
  that stores the distribution codename. For example:
  - `distribution_codename: [ 'Debian', 'Buster' ]` for `Debian 10 (Buster)`
  - `distribution_codename: [ 'Ubuntu', 'Focal' ]` for `Ubuntu 20.04 LTS (Focal Fossa)`

  **NOTE:**
  Not all distributions provide distinct codenames for each release. For example,
  `ansible_facts['distribution_release']` is set to `Core` for both `CentOS 7`
  and `CentOS 8`. Hence `distribution_codename` is not suitable to distinguish
  between releases, but `distribution_id` is an unique identifier!

* Variables are assigned using [`set_fact`](https://docs.ansible.com/ansible/latest/modules/set_fact_module.html)!
  Mind [Ansible's variable precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable):
  Variables defined with `set_fact` override variables from most other places such as `host_vars` and `group_vars`.


**Tested OS images**

- Cloud image of [`Debian 10 (Buster)` \[`amd64`\]](https://cdimage.debian.org/cdimage/openstack/current/)
- Generic cloud image of [`CentOS 7 (Core)` \[`amd64`\]](https://cloud.centos.org/centos/7/images/)
- Generic cloud image of [`CentOS 8 (Core)` \[`amd64`\]](https://cloud.centos.org/centos/8/x86_64/images/)
- Ubuntu cloud image of [`Ubuntu 20.04 LTS (Focal Fossa)` \[`amd64`\]](https://cloud-images.ubuntu.com/focal/)

Available on Ansible Galaxy as [jm1.common](https://galaxy.ansible.com/jm1/common).

## Frequently Asked Questions

* Why is `distribution_id` defined as `ansible_facts['distribution_major_version']`
  on `Debian` and `CentOS` but `ansible_facts['distribution_version']` on `Ubuntu`?
* How is `distribution_id` better than `ansible_facts['distribution_major_version']`
  or `ansible_facts['distribution_version']`?

  The intention of `distribution_id` is to provide a variable that changes whenever
  a new distribution release might bring breaking changes which demand for updates
  to Ansible roles, their tasks or default variables. On `Debian` and `CentOS`, the
  `ansible_facts['distribution_major_version']` fact is a proper change identifier.
  However, Ubuntu might introduce breaking changes biannually in April and October,
  hence `ansible_facts['distribution_version']` fits better.

  Role `jm1.common` picks the variable which qualifies best as a change identifier
  and then assigns its value to `distribution_id`.

## Requirements

None.

## Variables

None.

## Dependencies

None.

## Example Playbook

```yml
- hosts: all
  tasks:
    - import_role:
        name: jm1.common
    - debug:
        var: distribution_id
```

For instructions on how to run Ansible playbooks have look at Ansible's
[Getting Started Guide](https://docs.ansible.com/ansible/latest/network/getting_started/first_playbook.html).

## Example Role

Facts from role `jm1.common` might help to develop [Ansible roles with OS-specific defaults](
https://gist.github.com/JM1/9363beeb9fb5055e054b5f64aea0a598#approach-using-os-agnostic-dictionaries-in-defaultsmainyml):
First off, create OS-agnostic dictionaries in `defaults/main.yml`. Then use `distribution_id`
as a `dict` key to assign values for the host OS from dictionaries to default variables.
For example:

`defaults/main.yml`:
```yml
image_uri: "{{
        {
            'CentOS 8': 'https://...',
            'Ubuntu 20.04': 'https://...'
        }[' '.join(distribution_id)]
    }}"
```

## License

GPL3

## Author

Jakob Meng
@jm1 ([github](https://github.com/jm1), [galaxy](https://galaxy.ansible.com/jm1), [web](http://www.jakobmeng.de))
