### Ansible prerequisites


For Windows OS use the Docker images from [WillHallOnline](https://www.willhallonline.co.uk/project/docker/docker-ansible/)

Ansible for Windows
```shell
PS C:\Users\user\Ansible> 

docker run --rm -it -v C:\Users\mnm\Ansible:/ansible  -v C:\Users\mnm\.ssh:/root/.ssh willhallonline/ansible:2.9-centos-7 /bin/sh
sh-4.2# 
```

### SSH Hostfile for Ansible Inventory
We already launched the EC2 nodes with Terraform.
In order to generate the `$HOME/.ssh/config` file entries for our infrastructure we can use the `aws cli` utiliy

View current active profile
```
aws configure list
```

```shell
aws ec2 describe-instances \
--filters Name=tag:Name,Values=tf*  "Name=instance-state-name, Values=running"  \
--query 'Reservations[*].Instances[*].{ PublicIpAddress:PublicIpAddress,Name:Tags[?Key==`Name`]|[0].Value }'  \
--region us-west-2 \
--output text \
--profile awsprofile |awk '{print "Host "tolower($1)"\nHostName "$2}'
```            
This would output the 3 node entries for our `.ssh/config`  fille
```shell
Host tf-elk-es02
HostName 35.160.70.231
Host tf-elk-es03
HostName 52.43.33.179
Host tf-elk-es01
HostName 34.216.82.135
```
Additionally add to your `.ssh/config`
```shell
Host tf-*
IdentityFile ~/.ssh/<your_aws_ssh_key>

```

#### Ansible Inventory
Consists of hosts and varialbes file
Hosts file looks like this
```yaml
[es-nodes]
tf-elk-es01 node_name=node-1
tf-elk-es02 node_name=node-2
tf-elk-es03 node_name=node-3

[kibana-node]
tf-elk-kibana node_name=kibana

[ent-search-node]
tf-elk-kibana node_name=enterprise-search

[apm-server]
tf-elk-kibana node_name=apm-server
```

And our variables file 
```yaml
[all:vars]
es_version=7.15.1
heap_size="1g"
cluster_name=demo-cluster
es_enable_oidc=false
elastic_pass=changeme
remote_monitoring_user_pass=changeme
es_package_dir="/opt/logs"
cert_file=kibana.sciviz.co.pfx
cert_pass=changeme
logstash_system_pass=changeme
apm_system_pass=changeme
beats_system_pass=changeme
beats_user_pass=changeme
logstash_internal_user=logstash_internal
logstash_internal_pass=changeme
...

```

#### Disable SSH Host key checking
```shell
export ANSIBLE_HOST_KEY_CHECKING=False
```

Test Ansible Inventory 
```shell
 ansible -i inventory-elk/hosts.conf all  -m ping
```
returns 
```shell
...
tf-elk-es01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
...


