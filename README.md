# Mythic Ansible Role (MEhrn00.mythic)

Ansible role for installing and configuring [Mythic](https://github.com/its-a-feature/Mythic).

## Install
```bash
ansible-galaxy role install MEhrn00.mythic
```

requirements.yaml
```yaml
- name: MEhrn00.mythic

# To install a specific version
- name: MEhrn00.mythic
  version: "1.0.0"
```


## Role Variables

| Variable                       | Required | Default                                     | Type       | Description                        |
|--------------------------------|----------|---------------------------------------------|------------|------------------------------------|
| mythic_admin_username          | no       | mythic_admin                                | str        | Username for the Mythic admin user |
| mythic_admin_password          | no       | Randomly generated                          | str        | Password for the Mythic admin user |
| mythic_repo                    | no       | https://github.com/its-a-feature/Mythic.git | str        | URL of the Mythic git repository   |
| mythic_version                 | no       | master                                      | str        | Mythic version to checkout         |
| mythic_shallow_clone           | no       | true                                        | bool       | Do a shallow git clone of Mythic   |
| mythic_operation_name          | no       | Operation Chimera                           | str        | Name of the default operation      |
| mythic_install_path            | no       | /opt/Mythic                                 | str        | Path to install Mythic             |
| mythic_config                  | no       | {}                                          | dict       | Extra configuration values to set  |
| mythic_services                | no       | []                                          | list       | Mythic services to install         |
| mythic_services[].repo         | yes      |                                             | str        | Git repository of the service      |
| mythic_services[].name         | no       | Extracted from `mythic_services[].repo`     | str        | Name of the service                |
| mythic_services[].version      | no       |                                             | str        | Version of the service             |
| mythic_services[].force        | no       | false                                       | bool       | Force install the service          |
| mythic_services[].state        | no       | present                                     | str        | Service state                      |

Extended role variable information can be found in [meta/argument_specs.yml](meta/argument_specs.yml).

## Returned Variables

- mythic_admin_password
  - description: The Mythic administrator's password

## Supported Platforms

- Debian
  - 12 (Bookworm)
  - 13 (Trixie)

- Ubuntu
  - 20.04 (Focal)
  - 22.04 (Jammy)
  - 24.04 (Noble)

## Dependencies

- [geerlingguy.docker](https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/docker/)

## Examples

Set up Mythic with the default configuration
```yaml
- hosts: mythic
  become: true # Needed if not connecting as root
  tasks:
    - ansible.builtin.import_role:
        name: MEhrn00.mythic

    - name: Print the admin password
      ansible.builtin.debug:
        var: mythic_admin_password
```

Configure the default operation and admin user
```yaml
- hosts: mythic
  become: true # Needed if not connecting as root
  roles:
    - role: MEhrn00.mythic
      mythic_admin_username: admin
      mythic_admin_password: password123
      mythic_operation_name: Operation Chimera
```

Install services
```yaml
- hosts: mythic
  become: true # Needed if not connecting as root
  roles:
    - role: MEhrn00.mythic
      mythic_services:
        # Installs a service
        - repo: https://github.com/MythicAgents/Apollo

        # Force install a service
        - repo: https://github.com/MythicAgents/Apollo
          force: true
          state: present

        # Specify a version
        - repo: https://github.com/MythicC2Profiles/http
          version: v0.0.2.12

        # Uninstall a service by name
        - name: apollo
          state: absent
```

Set additional Mythic configuration options
```yaml
- hosts: mythic
  become: true # Needed if not connecting as root
  roles:
    - role: MEhrn00.mythic
      mythic_admin_username: admin
      mythic_admin_password: password123
      mythic_config:
        # Keys are case insensitive and will be converted to uppercase in the Mythic config
        debug_level: info
        INSTALLED_SERVICE_CPUS: 2
        JUPYTER_USE_VOLUME: true
        webhook_default_url: http://webhook
        # Lists will be joined with commas
        allowed_ip_blocks:
          - 10.0.0.0/8
          - 172.16.0.0/12
          - 192.168.0.0/16
          - 100.64.0.0/10
          - 127.0.0.0/8
        mythic_server_dynamic_ports:
          - 7000-7010
          - 9050-9100
```

## License
MIT
