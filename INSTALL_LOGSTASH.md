#### Get the Role
```shell
git clone https://github.com/elk-ansible/ansible-role-logstash.git
git checkout feature-ls-keystore
ln -s ansible-role-logstash/ ansible-logstash
```
 #### Run the playbook
```shell
ansible-playbook -i inventory-elk/ playbook-logstash.yml
```

##### Test the pipeline
This role installs by default a pipeline that takas for input an apache log file from the server `/tmp` dir
and ingest that data into elasticsearch

Download the file
```shell
ansible-playbook -i inventory-elk/ playbook-download-sample-apache-logs.yml 
```

Check if the index exists via the `_cat` API

````shell
GET _cat/indices/logstash-apache-type?v
```
will output 
````shell
health status index                uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   logstash-apache-type Uo5hcDueRrS-aO-vzMCzVw   1   1      10000            0      2.9mb          1.4mb

````

