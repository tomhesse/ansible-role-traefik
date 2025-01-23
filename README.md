Ansible Role: Traefik
=========

Installs [Traefik](https://traefik.io/traefik/) on Debian/Ubuntu servers.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see defaults/main.yml):

    traefik_version: v3.0.0

The version that will be installed.

    traefik_binary_local_dir: ""

Local binary that will be installed instead of the remote one.

    traefik_binary_url: "https://github.com/{{ _traefik_repo }}/releases/download/{{ traefik_version }}/traefik_{{ traefik_version }}_linux_{{ go_arch }}.tar.gz"

Remote location of the binary.

    traefik_skip_install: false

Skip installtion and only manage the configuration.

    traefik_binary_install_dir: "/usr/local/bin"
    traefik_system_group: "traefik"
    traefik_system_user: "{{ traefik_system_group }}"
    traefik_config_dir: /etc/traefik

Traefik configuration options.

    traefik_archive_path: "/tmp"

Download location for the binary.

The following variables are undefined by default but required for the configuration of Traefik. Most variables follow the same format as the Traefik YAML configuration.

    traefik_environment_variables:
      - API_TOKEN: some-api-token

Set systemd service environment variables. Could be used for DNS challenge providers.

    traefik_acme_storage:
      - /etc/traefik/acme.json

Set systemd ReadWritePath and create the acme storage.

    traefik_api_enabled: true
    traefik_dashboard_enabled: true

Enable the API and the Traefik dashboard.

    traefik_entrypoints:
      - name: web
        address: ":80"
      - name: websecure
        address: ":443"

Entrypoints used by your Traefik configuration.

    traefik_providers:
      docker:
        enabled: true
      file:
        enabled: false

The provider config tries to follow the traefik config. See molecule/converge.yml for an example.

    traefik_certificate_resolvers:
      - name: letsencrypt_staging
        acme:
          email: webmaster@example.com
          storage: /etc/traefik/acme.json
          dnsChallenge:
            provider: some-provider

Certificate resolvers for automatic HTTPS. API Tokens for DNS providers can be defined with `traefik_environment_variables`. The storage file needs to be specified in `traefik_acme_storage`.

    traefik_services:
      - name: example-service
        loadBalancer:
          servers:
            - url: https://example.com/

Services used by your Traefik configuration.

    traefik_middlewares:
      - name: example-basic-auth
        basicAuth:
          users:
            - traefik:$apr1$cBjT1kfY$bqHz991pR0jT.vxChyYPf/

Middlewares used by your Traefik configuration.

    traefik_routers:
      - name: traefik-dashboard
        entrypoints:
          - traefik
        middlewares:
          - example-basic-auth
        rule: Host(`traefik.example.com`) && (PathPrefix(`/api`) || PathPrefix(`/dashboard`))
        service: api@internal

Routers used by your Traefik configuration.

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: all
  roles:
    - role: tomhesse.traefik
```

License
-------

MIT / BSD

Author Information
------------------

This role was created in 2024 by [tomhesse](https://www.tomhesse.xyz/).
