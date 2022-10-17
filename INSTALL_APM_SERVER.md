### APM Server

Download the role

```shell
cd roles/
git clone ttps://github.com/elk-ansible/ansible-role-elastic-apm-server.git
ln -s ansible-role-elastic-apm-server/ apm-server
```

To install the APM server run these commands
```shell
 ansible-playbook -i inventory-elk/ playbook-apm-server.yml 
```

To verify the server installed successfully visit the endpoint in your browser
[https://apm-server.sciviz.co:8200/](https://apm-server.sciviz.co:8200/)
Also  you will notice the entry in the monitoring console
Management->Stack Monitoring-> 
and to remove APM server run
```shell
ansible-playbook -i inventory-elk/ playbook-remove-apm-server.yml
```
