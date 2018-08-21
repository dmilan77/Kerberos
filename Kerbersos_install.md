On server:
# Install the KDC Server
```bash

yum install krb5-server krb5-libs krb5-workstation -y
```
# On Client:

```bash

yum install krb5-workstation -y
```
# Edit krb5 
`vi /etc/krb5.conf`

```properties

includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 max_life = 1d  
 max_renewable_life = 7d
 kdc_tcp_ports = 88
 default_realm = EXAMPLE.COM
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
 EXAMPLE.COM = {
  kdc = ip-172-50-0-75.ec2.internal
  admin_server = ip-172-50-0-75.ec2.internal
 }

[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM

```
# Create the Kerberos Database
```bash

kdb5_util create -s
```
# Create a Kerberos Admin
```bash
kadmin.local -q "addprinc admin/admin"
```

# Edit ACL file
`vi /var/kerberos/krb5kdc/kadm5.acl`

```
*/admin@EXAMPLE.COM     *
```
# Restart and enable on reboot

```bash
systemctl start krb5kdc
systemctl start kadmin
systemctl enable krb5kdc
systemctl enable kadmin
```


# Verify
```bash
kadmin -p admin/admin@EXAMPLE.COM

On Hadoop enter username: admin/admin
Domain name:EXAMPLE.COM
```
