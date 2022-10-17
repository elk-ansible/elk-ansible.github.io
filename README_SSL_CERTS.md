## Ansible Deployment 

We will be using Ansible playbooks to install and configure the ELK stack components.
Ansible is available in the package repositories fpr Liux and Mac.

### SSL Cert with acme.sh
We already set up our DNS server to point to the deployd EC2s. We will also need a valid SSL certificates for our ELK stack deployment.
In this example here we're using the Acme.sh API utility and the GoDaddy provider.
[acme.sh](https://github.com/acmesh-official/acme.sh)
[Use GoDaddy.com domain API to automatically issue cert](https://github.com/acmesh-official/acme.sh/wiki/dnsapi#4-use-godaddycom-domain-api-to-automatically-issue-cert)

```shell
export GD_Key="xxx"
export GD_Secret="PPPX123"
```
```shell
acme.sh --issue --dns dns_gd -d kibana.sciviz.co -d es01.sciviz.co -d es02.sciviz.co -d es03.sciviz.co

[Tue Feb  2 07:42:03 PM CST 2021] Your cert is in  /home/user/.acme.sh/kibana.sciviz.co/kibana.sciviz.co.cer 
[Tue Feb  2 07:42:03 PM CST 2021] Your cert key is in  /home/user/.acme.sh/kibana.sciviz.co/kibana.sciviz.co.key 
[Tue Feb  2 07:42:03 PM CST 2021] The intermediate CA cert is in  /home/user/.acme.sh/kibana.sciviz.co/ca.cer 
[Tue Feb  2 07:42:03 PM CST 2021] And the full chain certs is there:  /home/user/.acme.sh/kibana.sciviz.co/fullchain.cer 
 
```
Similarly if your DNS is managed by AWS 
```shell

export  AWS_ACCESS_KEY_ID=AKxxxx
export  AWS_SECRET_ACCESS_KEY=gKyyyy
```
```shell
acme.sh --issue --dns dns_aws -d kibana.sciviz.co -d es01.sciviz.co -d es02.sciviz.co -d es03.sciviz.co
```
[letsencrypt + route53](https://gist.github.com/nelsonenzo/35a95107cee1a57e7ac4178c526b1b00)
### Generate the PKCS12 (p12, aka pfx) file 
Go to the folder where the certificates were generated with `acme.sh`
```shell
openssl pkcs12 -export  -inkey kibana.sciviz.co.key  -in kibana.sciviz.co.cer -certfile fullchain.cer -out kibana.sciviz.co.p12 
```

```shell
export  SSL_DOMAIN=kibana.sciviz.co
export SSL_PASS=changeme
openssl pkcs12 -export \
-inkey ${SSL_DOMAIN}.key \
-in ${SSL_DOMAIN}.cer \
 -certfile fullchain.cer  \
-out ${SSL_DOMAIN}.pfx   \
-password env:SSL_PASS
 
```
[https://www.openssl.org/docs/man1.1.0/man1/openssl.html#Pass-Phrase-Options](https://www.openssl.org/docs/man1.1.0/man1/openssl.html#Pass-Phrase-Options)
            
