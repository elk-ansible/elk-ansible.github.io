
### GoDaddy DNS Change

```shell
export GD_Key="xxx"
export GD_Secret="PPPX123"
``` 

##### DNS Update GoDaddy Nameservers
We set up with Terraform a Private and Public hosted zones. We would need to point our GoDaddy domain to the Custom Nameservers. We take these values
AWS Console>Route53>Public Zone>NS Type  Value/Route traffic to
These nameserver have the following format
```shell
ns-234.awsdns-33.com.
ns-999.awsdns-59.net.
ns-1234.awsdns-55.co.uk.
ns-123.awsdns-22.org.
```

You can obtain the nameservers via the AWS CLI by running
```shell
aws route53  list-hosted-zones
aws route53  get-hosted-zone --id Z0216218BVXFGLMIL118
```

We use the GoDaddy API for the custom DNS Nameserver update.
```shell

curl -X PATCH https://api.godaddy.com/api/v1/domains/sciviz.co \
-H "Authorization: sso-key ${GD_Key}:${GD_Secret}" \
-H "Content-Type: application/json" \
--data  '{	"nameServers": [
"ns-1143.awsdns-14.org",
"ns-154.awsdns-19.com",
"ns-1828.awsdns-36.co.uk",
"ns-576.awsdns-08.net"
]
  }'
  ```
##### Check if the change was propagated
```shell
curl -X GET  https://api.godaddy.com/api/v1/domains/sciviz.co \
-H "Authorization: sso-key ${GD_Key}:${GD_Secret}" \
  -H "Content-Type: application/json" 
```

##### DNS Set A record

For documentation purposes I am posting also how to change the DNS to the hostnames directly.
```shell
curl -X PATCH https://api.godaddy.com/v1/domains/sciviz.co/records \
-H "Authorization: sso-key ${GD_Key}:${GD_Secret}" \
  -H "Content-Type: application/json" \
  --data '[
  {"type": "A","name": "es1","data": "34.209.128.5","ttl": 3600},
   {"type": "A","name": "es2","data": "52.38.139.226","ttl": 3600},
   {"type": "A","name": "es3-sb","data": "34.209.124.156","ttl": 3600}
  ]'

```
