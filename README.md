# Document Pi (Paperless)

[![CI](https://github.com/DudeCalledBro/document-pi/actions/workflows/ci.yml/badge.svg)](https://github.com/DudeCalledBro/document-pi/actions/workflows/ci.yml)

This repository contains the Ansible code for deploying Paperless-ngx using Docker.

## Prerequisites

- Ensure you have Ansible installed (e.g. `pip3 install ansible`)
- Ensure Docker is installed on the proxy server (you may want to checkout my [ansible-docker-role](https://github.com/DudeCalledBro/ansible-role-docker))
- **Optional**: Set up a reverse proxy for your Paperless-ngx instance (you may want to checkout my [traefik-deployment](https://github.com/DudeCalledBro/traefik-ansible))

## Installation

1. Copy the example.hosts.yml file to hosts.yml. You also have to setup a variables file for your configuration. Therefore you have to copy example.config.yml to config.yml.

2. Configure the setup (config.yml), such as the Traefik reverse proxy.

    ```yaml
    # Define the paperless compose override
    paperless_docker_override:
      services:
        broker:
          networks:
            - paperless
    
        db:
          networks:
            - paperless
    
        webserver:
          labels:
            - traefik.enable=true
            - traefik.http.routers.paperless.rule=Host(`documents.local`)
            - traefik.http.routers.paperless.entrypoints=websecure
            - traefik.http.routers.paperless.tls=true
            - traefik.http.services.paperless.loadBalancer.server.port=8000
            - traefik.docker.network=traefik
          networks:
            - paperless
            - traefik
    
      # Custom networking in your docker compose override
      networks:
        paperless:
          name: paperless
        traefik:
          name: traefik
    ```

3. Run the Ansible playbook to deploy Jellyfin

    ```bash
    ansible-playbook main.yml
    ```

> You may have to enter a password to SSH into the target system, so you need to add `-k` after the `ansible-playbook` command.

## License

Copyright © 2025 Niclas Spreng

Licensed under the [MIT license](LICENSE).
