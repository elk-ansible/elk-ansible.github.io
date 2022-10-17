### Kibnana Ansible Role

We will use the local roles folder instead of 
```shell
cd roles
git clone https://github.com/fedelemantuano/ansible-kibana.git

```

```shell
ansible-playbook -i inventory-elk/ playbook-kibana.yml
```
#### Notes on the Kibana playbook
For connecting to Elasticsearch we will use  `elasticsearch.ssl.certificate:` and `elasticsearch.ssl.key:`
which are already generated from the  certbot `acme.sh` script in the previous step.

Kibana server certificate will use the same pfx file that we created previously since it contains the DNS names for Kibana and our 3 es nodes.

The playbook that we will use
```yaml
- name: Install Kibana
  hosts: kibana-node
  roles:
  - role: ansible-kibana
#  - role: fedelemantuano.kibana
    es_version: "{{hostvars[inventory_hostname].es_version}}"
    es_enable_oidc: "{{hostvars[inventory_hostname].es_enable_oidc}}"
    kibana_api_host: "{{ ansible_default_ipv4.address }}"
#   HTTP SSL conection to Elasticsearch
    es_enable_http_ssl: true
    es_ssl_verification_mode: "certificate"
    es_ssl_cert: "files/certs/{{hostvars[inventory_hostname].kibana_es_ssl_cert}}"
    es_ssl_key: "files/certs/{{hostvars[inventory_hostname].kibana_es_ssl_key}}"
#    es_ssl_key_passphrase: elastic
    #Alternatively use the PKCS12
#    es_ssl_keystore: "files/certs/{{hostvars[inventory_hostname].cert_file}}"
#    es_ssl_keystore_password:  "{{hostvars[inventory_hostname].cert_pass}}"
    enable_server_ssl: true
#    server_ssl_cert: "files/certs/server/server.crt"
#    server_ssl_key: "files/certs/server/server.key"
#    server_ssl_key_passphrase: elastic
    secure_settings: true
    es_pass:  "{{hostvars[inventory_hostname].kibana_pass}}"
    server_ssl_keystore: "files/certs/{{hostvars[inventory_hostname].cert_file}}"
    server_ssl_keystore_password:  "{{hostvars[inventory_hostname].cert_pass}}"
    kibana_config:
        server.name: "{{ inventory_hostname }}"
        server.port: 5601
        server.host: "{{ ansible_default_ipv4.address }}"
        server.publicBaseUrl: "{{hostvars[inventory_hostname].kibana_srv_public_url}}"
        elasticsearch.hosts:  "[\"https://{{hostvars[inventory_hostname].kibana_es_host}}:9200\"]"
#        elasticsearch.hosts:  "https://{{ ansible_default_ipv4.address }}:9200"
#        elasticsearch.hosts:  "[\"https://10.127.44.11:9200\",\"https://10.127.44.22:9200\",\"https://10.127.44.33:9200\"]"
#        xpack.security.audit.enabled: true
#        Add this when deploying behind ALB TG "/kibana" route
#        server.basePath: "/kibana"
#        server.rewriteBasePath: true
```