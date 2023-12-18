KubeStack
=========

This ansible role can install basic components for kubernetes cluster:

  - MetalLB
  - CertManager
  - Nginx Ingress Controller
  - Longhorn CSI
  - Loki
  - Rancher Manager
  - Vault

Requirements
------------
This role requires the following:

  - [kubernetes](https://pypi.org/project/kubernetes/)
  - [helm](https://github.com/helm/helm/releases)
  - [yaml](https://pypi.org/project/PyYAML/)
  - python >= 3.6
  - jsonpatch



Role Variables
--------------
```yaml
stack_metallb:
  pools: 
  - 127.0.0.1-127.0.0.1
  enabled: true
  namespace: metallb-system
stack_cert_manager:
  version: v1.13.2
  namespace: cert-manager
  enabled: true
stack_nginx_controllers:
  - version: 4.8.3
    ingress_class_by_name: true
    ssl_passthrough: true
    service_type: LoadBalancer
    loadBalancerIP: 127.0.0.1
    ingressClass: nginx
    namespace: ingress-nginx
stack_rancher: 
  hostname: rancher.example.com
  bootstrapPassword: admin
  replicas: 1
  namespace: cattle-system
  enabled: true
  ingressClass: nginx
stack_longhorn:
  enabled: true
  replicas: 2
  ui: Rancher-Proxy
  namespace: longhorn-system
stack_loki:
  enabled: true
  namespace: loki
  persistence_enabled: true
  persistence_size: 10Gi
stack_argocd:
  enabled: true
  namespace: argocd
  hosts:
  - argocd.example.com
  vault_addr: http://vault.example.com:8200
  vault_token: hvs.token
  ingressClass: nginx
  refresh_interval: 180s
stack_vault:
  enabled: true
  namespace: vault
  externalVaultAddr: http://vault.example.com:8200
  server:
    enabled: true
    ingress:
      enabled: true
      ingressClassName: nginx
      host: test.example.com
  injector:
    enabled: false
use_manifests: true
manifests_path: /var/lib/rancher/rke2/server/manifests
kubeconfig: ~/.kube/config
```
Dependencies
------------

This role depends only on:
  - [kubernetes.core](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/index.html)


Example Playbook
----------------

Example playbook to install everything except vault (its already integrated into argocd):
```yaml
    - hosts: localhost
      vars:
        manifests_path: /tmp
        use_manifests: false
        kubeconfig: ~/.kube/config
        stack_metallb:
          pools: 
          - 192.168.0.200-192.168.0.250
          enabled: true
          namespace: metallb-system
        stack_cert_manager:
          version: v1.13.2
          namespace: cert-manager
          enabled: true
        stack_nginx_controllers:
          - version: 4.8.3
            ingress_class_by_name: true
            ssl_passthrough: true
            service_type: LoadBalancer
            loadBalancerIP: 192.168.0.200
            ingressClass: nginx
            namespace: ingress-nginx
        stack_rancher: 
          hostname: rancher.example.com
          bootstrapPassword: admin
          replicas: 1
          namespace: cattle-system
          enabled: true
          ingressClass: nginx
        stack_argocd:
          enabled: true
          namespace: argocd
          hosts:
          - argocd.example.com
          vault_addr: http://vault.example.com:8200
          vault_token: hvs.token
          ingressClass: nginx
          refresh_interval: 60s
        stack_vault:
          enabled: false
      roles:
        - role: kube-stack
```

License
-------
MIT License

Copyright (c) 2023 Tarek Srour

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
