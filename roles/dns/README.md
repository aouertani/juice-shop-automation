Role Name: dns
=========

This role creates DNS record set in a managed zone. The zone needs to be created before running the role.

Requirements
------------

This role needs a [managed zone in GCP](https://cloud.google.com/dns/docs/zones) on which the records set for Juice Shop and Grafana will be created. It does not have any dependencies and can be run separately.

Role Variables
--------------

```yaml
---
zone_name: 
  name: mambudemo-com
  dnsName: mambudemo.com.

juice_shop_dns: juiceshop.mambudemo.com
grafana_dns: grafana.juiceshop.mambudemo.com

ingress_nginx_controller: ingress-nginx-controller
cert_manager_name_space: cert-manager

gcp_project_id: curious-furnace-316611
gcp_cred_kind: serviceaccount
gcp_cred_file: "{{ lookup('env', 'GOOGLE_APPLICATION_CREDENTIALS') }}"
```

Dependencies
------------

This role needs community.kubernetes and google.cloud collection. It can be set in roles/cert-manager/meta/main.yml
```yaml
---
  dependencies:
    "community.kubernetes": "*"
    "google.cloud": "*"
```

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - role: dna
      tags: role-dns
