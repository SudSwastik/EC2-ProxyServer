## Proxy Server Creation in AWS EC2 Instance

- Create an ubuntu EC2 instance in AWS
- SSH into the EC2 Server

```
$ chmod 400 <PemFileNAme>
$ ssh -i <PemfileName> ubuntu@<Public-IP of EC2 insatnce>
```

### Installation of Squid Proxy in EC2 Instance

```
$ apt-get update  && apt-get install  squid apache2-utils -y
```

> By Default All sites are blocked 
> Add the public Ip of EC2 instance, in browser proxy
setting to allow all request through proxy server

- To Allow all request to pass through proxy without blocker

```
$ sudo su
$ vi /etc/squid/squid.conf

```

- Search for http_proxy deny all
- Change it to http_proxy allow all

```
$ service squid restart
```


> Make a request in browser and check if it is allowed or not

- access.log conatins all the request made to Proxy server
```
$ tail -f /var/log/squid/access.log 
```

- Let's block some sites in Proxy

> Create a new acl files "blocked_sites.acl" and add
sites to be blocked inside it
let add ".google.com"


```
$ vi /etc/squid/blocked_sites.acl
$ .google.com
```
- add blocked sites to squid config
```
$ vi /etc/squid/squid.conf
$ acl blocked_url  dstdomain "/etc/squid/blocked_sites.acl"
$ http_access deny blocked_url
$ service squid restart
```
> Hit google.com and it will be blocked but others are not

- Let add authentication to login into proxy server


```
$ touch /etc/squid/passwd
$ htpasswd /etc/squid/passwd user
set password
$ vi /etc/squid/squid.conf
$ auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
$ acl squid_users proxy_auth REQUIRED
$ http_access allow squid_users 
$ service squid restart
```