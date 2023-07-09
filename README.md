
# Ansible Role - potos\_hibernate

This role works for me, but does not satisfy the potos acceptance rules yet:

* the automated checks are currently failing
* meta is not up to date

This role tweaks several settings so that hibernate (and suspend) work
reliably on my Notebook with an AMD GPU. The core finding on my hardware:

* suspend / hibernate does not work when called from Gnome or
  e.g. with `systemctl suspend`: The first wake up can succeed,
  but the second wake up always ends in power cycling.

* however, all works perfectly when called as root as follows: `/lib/systemd/systemd-sleep suspend`

Furthermore, I prefer power savings over fast close/open cycles. Therefore, I want to be sure
that closing the lid causes the system to hibernate, irrelevant whether the system is on
battery power, externaly powered or docked.

* a `sudo` rule to enable calling `systemd-sleep` as non privileged user.
* a `logind` configuration that causes a hibernation on any lid close.
* a `polkit` file prohibiting Gnome to call suspend (as this would be unwakable again).

Please note: Hibernation needs to know where to store the memory contents
on disk (and where to wake up from). This works "automagically" when

* you have a swap partition that is at least as big as your memory size.
* `update-initrd -u -k all` is called when that swap partition is active. This
  should be added to the `potos_firstboot_postcommands` in your specs
  repo, e.g. like [this](https://github.com/nis65/ansible-specs-potos-mlc/blob/292fd283b899bc65898faaadee071a5e6db3119f/vars/potos_firstboot.yml#L3).

The easiest way to make use of this implementation is via
an [argos shell extension](https://github.com/nis65/ansible-role-potos_argos/blob/01b6961db64b4b97b54390cd344f9044c344e100/files/usr/local/src/potos_argos/mgmt#L6).

## Example Playbook

As this role is tested via Molecule one can use [that
playbook](./molecule/default/converge.yml) as a starting point:

```yaml
---

- name: Converge
  hosts: all
  gather_facts: yes
  tasks:
    - name: run role
      ansible.builtin.include_role:
        name: 'ansible-role-potos_template'
```

## Role Variables

The default variables would be defined in [defaults/main.yml](./defaults/main.yml),
but there currently no variables for this role.

## Requirements

N/A

## License

See [LICENSE](./LICENSE)

## Author Information

[Project Potos](https://github.com/projectpotos)

