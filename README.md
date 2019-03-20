Ansible Role: Grafana for Kubernetes
===================================

Ansible role to install [Grafana](https://grafana.com/) on Kubernetes.

Role Variables
--------------

```yaml
# Image used
kubernetes_grafana_image: "grafana/grafana:latest"

# Namespace
kubernetes_grafana_namespace: "default"
# App name (used as selector)
kubernetes_grafana_app: "grafana"
# Deployment name
kubernetes_grafana_deployment: "grafana-deployment"
# Service name
kubernetes_grafana_service: "grafana"

# Number of replicas
kubernetes_grafana_replicas: 1
kubernetes_grafana_revision_history: 1

# Node selector
kubernetes_grafana_node_selector: {}

# Add custom labels in the deployment metadata section
kubernetes_grafana_deployment_labels: {}
# Add custom annotations in the deployment metadata section
kubernetes_grafana_deployment_annotations: {}

kubernetes_grafana_resources:
  limits:
    memory: "512Mi"
  requests:
    memory: "64Mi"

# Setup ingress for grafana
kubernetes_grafana_setup_ingress: false
kubernetes_grafana_ingress:
  name: "grafana-ingress"
  host: "grafana.example.com"
  annotations:
  tls:

# config written in /etc/grafana/grafana.ini
# Dictionary composed of sections as keys, themselves having a dict as value
# with `option_name: value`
kubernetes_grafana_config: {}

### Volumes ###

# For each volume, `definition` and `subPath` can be used. Their content is
# exactly what would be in a Kkubernetes pod manifest.

# Data volume. Mounted in /var/lib/grafana/ (see examples for more details)
kubernetes_grafana_data_volume:
  definition:
```

Dependencies
------------

Kubectl needs to be installed on the host targeted by the role.


Example Playbook
----------------

```yaml

- hosts: kube-master
  run_once: true
  vars:
    kubernetes_grafana_data_volume:
      # This volume will be mounted as /var/lib/grafana
      subPath: "data"
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: grafana
          readOnly: false

    kubernetes_grafana_setup_ingress: true
    kubernetes_grafana_ingress:
      name: "grafana-ingress"
      host: "grafana.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "grafana-ingress-tls"
          hosts:
            - "grafana.example.com"
    kubernetes_grafana_config:
      default:
        instance_name: grafana.tld
      database:
        url: mysql://user:secret@host:port/database
  roles:
    - role: Anthony25.kubernetes_grafana
```

Use `run_once` to run the role on only one available master in the cluster.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
