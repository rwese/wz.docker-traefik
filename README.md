wz.docker-traefik
=========

Setup docker-compose traefik for routing docker-container and kubernetes trafik.
Let's Encrypt enabled.

It will create a external docker network which your docker-composes must use to be routed.

Example Service `docker-compose.yaml`:

```yaml
version: '3'
services:
  www:
    image: nginx
    restart: unless-stopped
    volumes:
    - ./data:/usr/share/nginx/html
    labels:
    - "traefik.enable=true"
    - "traefik.http.routers.my-service-secure.entrypoints=websecure"
    - "traefik.http.routers.my-service-secure.rule=Host(`my-service.com`)"
    - "traefik.http.routers.my-service-secure.service=my-service"
    - "traefik.http.services.my-service.loadbalancer.server.port=80"
    - "traefik.http.services.my-service.loadbalancer.passHostHeader=true"
    networks:
      - proxy
    logging:
      options:
        max-size: ${LOG_MAX_SIZE:-4m}
        max-file: ${LOG_MAX_FILE:-10}

networks:
  proxy:
    external: true
```

Requirements
------------

* docker
* docker-compose

Role Variables
--------------

| Variable Name | Type | Purpose | Default | Required |
|---|---|---|---|---|
| `docker_compose_reverse_proxy__image` | String | Docker image to use | `'traefik'` | Yes |
| `docker_compose_reverse_proxy__image_version` | String | Image Version | `'v2.6'` | Yes |
| `docker_compose_reverse_proxy__container_name` | String | Specify a container name if needed | `'traefik'` | No |
| `docker_compose_reverse_proxy__enable_image_updater` | String | | `true` | Yes |
| `docker_compose_reverse_proxy__project_name` | String | | `'docker-traefik'` | Yes |
| `docker_compose_reverse_proxy__network_name` | String | | `'proxy'` | Yes |
| `docker_compose_reverse_proxy__cert_admin_email` | String | | `""` | Yes |
| `docker_compose_reverse_proxy__pull_enabled` | Bool | | `false` | Yes |
| `docker_compose_reverse_proxy__build_enabled` | Bool | | `false` | Yes |
| `docker_compose_reverse_proxy__target_path` | String | | `/opt/docker_traefik` | Yes |
| `docker_compose_reverse_proxy__extra_files` | String | | `  []` | Yes |
| `docker_compose_reverse_proxy__state` | String | | `'present'` | Yes |
| `docker_compose_reverse_proxy__remove_volumes` | Bool | | `false` | Yes |
| `docker_compose_reverse_proxy__tls_default_min_version` | String | | `VersionTLS12` | Yes |
| `docker_compose_reverse_proxy__tls_mintls13_min_version` | String | | `VersionTLS13` | `` | Yes |
| `docker_compose_reverse_proxy__provider_docker_exposed_default` | Bool | true | false | Yes |
| `docker_compose_reverse_proxy__provider_kubernetes_ingress_enabled` | Bool | true | false | Yes |
| `docker_compose_reverse_proxy__provider_kubernetes_ingress_kubeconf` | String | `../files/kubeconf.yml` | '' | No |
| `docker_compose_reverse_proxy__admin_account` | String | htpassword for dasboard access | '' | Yes |


Dependencies
------------

none

Example Playbook
----------------

    ---
    - name: Setup reverse proxy
      hosts: linux
      become: true
      gather_facts: true
      roles:
        - role: wz.docker-traefik
          vars:
            vars:
              docker_compose_reverse_proxy__network_name: 'webproxy'


License
-------

MIT

Author Information
------------------

