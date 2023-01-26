kube-prometheus-stack-scrape-role
=========

This role creates Kubernetes Secrets with a TLS certificate, which is mounted in kube-prometheus-stack as a volume.

* `values.yaml` helm chart kube-prometheus-stack 
```yaml
# Additional volumes on the output StatefulSet definition.
volumes:
  - name: certs
    secret:
      secretName: prom01-certs
      optional: false

# Additional VolumeMounts on the output StatefulSet definition.
volumeMounts:
  - name: certs
    mountPath: "/etc/prometheus/certs/node"
    readOnly: true
```
It also creates a Kubernetes Secret that contains host addresses to configure scrape.
* `values.yaml` helm chart kube-prometheus-stack 
```yaml
additionalScrapeConfigsSecret:
  enabled: true
  name: additional-scrape-configs
  key: prometheus-additional.yaml
```

Requirements
------------

Ansible > 2.9

Role Variables
--------------

  variable | Description | Default
 --- | --- | ---
ne_port|Port node_exporter| 9100
ne_win_port| Port windows_exporter in hostgroup "windows"| 9182
k8s_namespace|Kubernetes namespace|monitoring
k8s_secret_path|The kubernetes secrets storage directory| ~/kubernetes
tls_secret_name|The kubernetes TLS secret name | kps-node-tls-secret
tls_secret_name_file|TLS secret name file| tls-secret.yaml
scrape_secret_name|Scrape secret name|additional-scrape-configs
scrape_secret_name_file|The configuration file loaded in the prometheus container|prometheus-additional.yaml
tls_secret_mount_path|The path to the certificate specified in the chart helm manifest|/etc/prometheus/certs/node

Example Hosts
----------------
```yaml
all:
  children:
    nodes:
      hosts:
        host1
        host2
    # These hosts will be added to job=windows with connection port 9182 without TLS and authentication. No windows_exporter installation is performed.
    windows:
      hosts: 
        host1
        host2
    kubernetes:
      hosts:
        host_with_access_to_kubernetes
  vars:
    ansible_connection: ssh
    ansible_user: 
```

Example Playbook
----------------

    - hosts: kubernetes
      roles:
         - kube-prometheus-stack-scrape-role

License
-------

BSD

Author Information
------------------

Emil Temerbulatov
