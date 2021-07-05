# juice-shop-automation

A set of ansible roles and playbooks to automatically deploy juice-shop application and its ecosystem in Google Cloud Platform (GKE).
Each role manages a different componenet and can be used separately:

* common: Checks if kubectl, gcloud and helm are installed first and then authenticates to GKE using a service account key file and sets the current context.
* cert-manager: Deploys cert-manager in cert-manager namespace.
* ingress-controller: Deploys ingress-controller in cert-manager name space and creates let's encrypt staging and production issuers.
* dns: Creates DNS record set in a managed zone. The zone needs to be created before running the role.
* juice-shop: Deploys juice shop application in juice-shop namepace.
* prometheus: Deploys prometheus in monitoring namespace.
* grafana: Deploys grafana in monitoring namespace. Creates a prometheus data source and a user then loads Juice Shop Status Dashboard.

Each role is described with more details on its README.md file.

```bash
.
├── juice-shop.yml
├── LICENSE.md
├── README.md
└── roles
    ├── cert-manager
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    ├── common
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   ├── main.yml
    │   │   └── prerequisites.yml
    ├── dns
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    ├── grafana
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   │   └── demo-grafana-dashboard.json
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   │   └── values.yaml
    ├── ingress-controller
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    ├── juice-shop
    │   ├── README.md
    │   ├── defaults
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   │   └── values.yaml
    └── prometheus
        ├── README.md
        ├── defaults
        │   └── main.yml
        ├── files
        ├── handlers
        │   └── main.yml
        ├── meta
        │   └── main.yml
        ├── tasks
        │   └── main.yml
        ├── templates
        ├── tests
        │   ├── inventory
        │   └── test.yml
        └── vars
            └── main.yml
```

# Prerequisites
* ansible, helm, kubectl and gcloud should be installed in the machine that runs the playbooks
* A GCP project and a running GKE cluster 
* GOOGLE_APPLICATION_CREDENTIALS should contain the path to a service account key file: [instructions here](https://cloud.google.com/docs/authentication/production#automatically)
* A cloud DNS managed zone

# Deploy Juice Shop in GKE
To deploy the application and its ecosystem:

* roles/common/defaults/main.yml should look like this:
```yaml
---
# defaults file for common
service_account_key_file: "{{ lookup('env', 'GOOGLE_APPLICATION_CREDENTIALS') }}"

cluster_id: cluster-5
cluster_zone: europe-central2
gcp_project_id: curious-furnace-316611
```
* Juice Shop and Grafana URLs can be set here roles/dns/defaults/main.yml:
```yaml
---
# defaults file for common
...
juice_shop_dns: juiceshop.mambudemo.com
grafana_dns: grafana.juiceshop.mambudemo.com
...
```
* Once the required vars are configured, run the main playbook:
```bash
ansible-playbook juice-shop.yml
```
* Any role can be executed separately by specifying its tag:
```bash
ansible-playbook --tags=role-<role_name>  juice-shop.yml
```
* When the whole roles are successfully run the output must look like this:
```bash
TASK [Juice Shop application URL] ********************************************************************************************************************************
task path: ..../juice-shop-automation/juice-shop.yml:21
ok: [localhost] => {
    "msg": "Juice Shop application URL: juiceshop.mambudemo.com"
}

TASK [Grafana application URL] ***********************************************************************************************************************************
task path: ..../juice-shop-automation/juice-shop.yml:25
ok: [localhost] => {
    "msg": "Grafana application URL: grafana.juiceshop.mambudemo.com"
}
```
