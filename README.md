# Linux Tips and Tricks

## Command grep

Display only the match

```
# -o : option for displaying only the match
grep -P '[pattern A]|[pattern B]|[pattern C]|[pattern D]'
```

Multiple patterns

```
# Match multiple patterns
grep -P '[pattern A]|[pattern B]|[pattern C]|[pattern D]'
```

## Command sed

### Make permanent changes
```
sed -i 's/10.24.153.111/10.5.255.16/g'
```

### Preserve variable and white space
```
sed "s/10.24.153.111/$ip/g"
```

## Disk and file system storage

sudo vgs

https://www.christophe-casalegno.com/bash-how-to-simply-check-disk-space-on-linux-and-take-action-when-a-threshold-is-reached/

### Commande df

Note also that df outputs sizes for all filesystems, including virtual filesystems such as udev and tmpfs. You might consider filtering those out if you want the actual physical disks only. So if we consider only filesystems that have a device file represented in /dev/ filesystem, you could probably use something like this:

```
df -P | awk 'NR>2 && /^\/dev\//{sum+=$2}END{print sum}'
```

### Commande du

### Commande fdisk

```
sudo fdisk -l
```

## CPU server

```
cat /proc/cpuinfo
```

## RAM

```
free -h
```

## Volumetry
```
vgdisplay
```

## Archives and compression

```
# Archive and compress
tar -czvf directory.tar.gz /path/to/the/file/to/tar
```

```
# Unarchive and uncompress
tar -xzvf directory.tar.gz
```

## Network

### Netstat

```
netstat -tulpn | grep LISTEN
#UDP ports
netstat -lanu
```

### SS

```
ss -tulwn | grep LISTEN
```

### Netcat

```
# w2 -> wait 2 sec
nc -vw2 [Ip address or computer name] [port number]
# u -> check udp
nc -vw2 -u [Ip address or computer name] [port number]
# Write something and check on the server side with the command (sudo nc -v -u -l 892) set up beforehand
```

### LSOF

```
lsof -ni TCP:[port_number]
```

### Telnet

```
telnet [Ip address or computer name] [port number]
```

### Nmap

```
nmap [Ip address or computer name] -p [port number]
```
### Tar

```
tar -czvf file.tar.gz directory
```

### Netcat transfers

Always take a port number superior to 1024

```
# Host where to do the transfer
# Listen to the port 1111
nc -v -l -p 1111
# Convert in a file the result of the listening
nc -v -l -p 1111 | tar xvkf -
```
Next operation on the host transferring the file
```
# Host transferring the file
# Package the file without touching the original
tar cvf - localperl/
# Transfer the tar file via netcat
tar cvf - localperl/ | nc -v 10.5.253.97 1111
# In case the netcat communication doesn't stop
# we insert an error with a non existent file
tar cvf - localperl/ lol | nc -v 10.5.186.3 1111
```

### NFS

exported files system -> 
targeted files system -> mounted points

```
#version 3
#Open the config file
vi /etc/sysconfig/nfs
#Uncomment the following line
MOUNTD_PORT=892
service nfs stop
service nfs start
```

### OpenSSL commands

Check certificates without the browser

```
openssl s_client -connect localhost:443
```

With .csr file

```
openssl req -text -noout -verify -in *.csr
```

With .key file

```
# Generate .csr file
openssl req -new -newkey rsa:4096 -nodes -keyout file.key -out file.csr
```

With .key and .csr files

```
# Generate .pem file
openssl x509 -req -sha256 -days 365 -in file.csr -signkey file.key -out file.pem
```

With .pfx file

```
# Get the certificate info
openssl pkcs12 -in certificates/my-certificate.pfx -info
```
Convert from .pfx to .pem format (necessary to get the date)

```
# Convert to .pem file
openssl x509 -in certificat.crt -out certificat.pem
# out option : source file is not deleted 
openssl pkcs12 -in certificates/my-certificate.pfx -out temp.pem -passout pass:"password" -passin pass:"password"
```

With .pem file

```
# Get validity dates
openssl x509 -in temp.pem -noout -dates
```
Generate .p12 files

```
# Create a .p12 file
openssl pkcs12 -export -out $(hostname -s).p12 -inkey certificat.key -in certificat.pem -certfile certificat.pem
```

Generate pfx file

```
openssl x509 -in certificat.pem -inform PEM -out certificat.crt
```

Convert key file to pem file

```
openssl rsa -in C:\Certificates\localPrivateKeyfile.key -text > privateKeyFileInPemFormat.pem
```

openssl s_client -connect <hostname>:<port>

openssl x509 -in <(openssl s_client -connect osmose.mt.sncf.fr:443 -prexit 2>/dev/null) -out osmose.crt

Compare public key for csr, private key and pem

PEM : openssl x509 -in temp.pem -noout -text
KEY : openssl pkey -text_pub -in file.key -noout
CSR : openssl req -in *.csr -noout -text

openssl pkey -text_pub -in *.key -noout;openssl req -in *.csr -noout -text
https://www.ssldragon.com/blog/check-certificate-openssl-linux/
https://docs.pingidentity.com/r/en-us/solution-guides/htg_use_openssl_to_test_ssl_connectivity

Display intermediate certificates : -subject

echo | openssl s_client -connect x64exosmh01.hd.sncf.fr:443 | openssl x509 -noo
ut -subject

### Avoid connection when time out set up

```
while true
do
tput sc
sleep 99
done &
```

## TCPDUMP

```
nohup sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-fin) != 0 and (dst port 389 or dst port 636)' &
ps -ef | grep tcpdump
exit
```

## LDAPSEARCH

```

```

## Reboot

```
#
sudo reboot
#
sudo shutdown -rF
#
sudo init 6
```

## Command watch

```
# -n 5 -> check every 5s
# -c -> preserve the color
watch -c -n 10 cat path/to/a/file
```

## Rare issues

### Files with the same name issue

```
# Display quoted version of files (with GNU)
ls -lQ
```

#### Not in GNU system

```
# Display inode number
ls -li
```

```
# Look for the file
find . -inum [number] -user [user] -ls
```

```
# Look for the file (-delete for GNU system)
find . -inum [number] -user [user] -exec rm {} \
# For GNU system -> find . -inum [number] -user [user] -delete
```

## Boot

### nsrexecd issues

```
nsrrpcinfo -p localhost
```

[Ressource Windows](https://www.dell.com/community/en/conversations/networker/nsr-ports-conflict/647f927ff4ccf8a8de43e5cf)
[Ressource Linux](http://www.ipnom.com/Legato-NetWorker-Commands/nsrports.html)

## Services to start at boot

### chkconfig

```
chkconfig --level 2345 httpd on
```
References list:
[chkconfig command](https://phoenixnap.com/kb/chkconfig)
[runlevels](https://www.scaler.com/topics/run-level-in-linux/)
[runlevels](https://www.techtarget.com/searchdatacenter/definition/runlevel#:~:text=A%20runlevel%20is%20a%20preset,after%20the%20OS%20boots%20up.)

## Bash tips

### Convert lowercase string to uppercase

````
s="Hello World"
upperstr=$(echo $s | tr '[:lower:]' '[:upper:]')
echo "Original  String : $s"
echo "Uppercase String : $upperstr"
```

### Transfer a*bash script content in a file

```
# With this way no variable will be assigned
cat <<"EOF"> bash_script.sh
#!/bin/bash
value="hello"
echo $value
EOF
```

### Mini tips

#### Loops : filenames with spaces

```
# Before the loop assign the variable
IFS=$'\n'
```

### Conditions

```
for i in $(ls /path/with/file)
do
        if [[ "$i" == *.txt  ]]
        then
                echo $i
        fi
done
```

### Split a string

- Using sed

```
extract=$(echo "hello_world"| sed 's/_.*//')
echo $extract
# Output -> hello
```

- Using cut

```
extract=$(echo "hello_world_by"| cut -d'_' -f 2)
echo $extract
# Output -> world
```

- Expansion parameters

```
var="01234567890abcdefgh"

# Extract from the seventh first character of the string
echo ${var:7}
# Output -> 7890abcdefgh

# Extract from the seventh last character of the string
# The space at ": -7" is very important
echo ${var: -7}
# Output -> bcdefgh
```

```
var="01234567890abcdefgh"

# Start from a part of the string and take the 2 first
echo ${var:7:2}
# Output -> 78

# Start from a part of the string and remove the 2 last
echo ${var:7:-2}
# Output -> 7890abcdef
```

dialog

- 


###

<VirtualHost *:80>
        ServerName SERVER_NAME
        ErrorLog "/logs/apache/DOMAIN.error.log"
        CustomLog "/logs/apache/DOMAIN.access.log" combined
        Header add Set-Cookie "c8o-server-id=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED
        ProxyPass /convertigo balancer://convertigo-DOMAIN
        ProxyPassReverse /convertigo balancer://convertigo-DOMAIN
</VirtualHost>

<Proxy balancer://convertigo-DOMAIN>
        # Convertigo servers
        BALANCER_MEMBERS
        # Balancer config
        ProxySet stickysession=c8o-server-id
        ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:443>
        ServerName SERVER_NAME
        ErrorLog "/logs/apache/DOMAIN.ssl.error.log"
        CustomLog "/logs/apache/DOMAIN.ssl.access.log" combined
        Header add Set-Cookie "c8o-server-id=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED
        ProxyPass /convertigo balancer://convertigo-DOMAIN
        ProxyPassReverse /convertigo balancer://convertigo-DOMAIN
        Include conf/extra/httpd-ssl-vhost.conf
        SSLCertificateFile "/applis/apache/apache_unix.001/conf/extra/domains/DOMAIN/server.crt"
        SSLCertificateKeyFile "/applis/apache/apache_unix.001/conf/extra/domains/DOMAIN/server.key"
</VirtualHost>

<VirtualHost *:443>
        ServerName SERVER_NAME
        ErrorLog "/logs/apache/DOMAIN.ssl.error.log"
        CustomLog "/logs/apache/DOMAIN.ssl.access.log" combined
        Header add Set-Cookie "c8o-server-id=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED
        <Proxy balancer://convertigo>
                BalancerMember C8O_URL_1 route=1
                BalancerMember C8O_URL_2 route=2
                BalancerMember C8O_URL_3 route=3
                BalancerMember C8O_URL_4 route=4
                BalancerMember C8O_URL_5 route=5
                BalancerMember C8O_URL_6 route=6
                ProxySet stickysession=c8o-server-id
                ProxySet lbmethod=bybusyness
        </Proxy>
        ProxyPass /convertigo balancer://convertigo
        ProxyPassReverse /convertigo balancer://convertigo
        Include conf/extra/httpd-ssl-vhost.conf
        SSLCertificateFile "/applis/apache/apache_unix.001/conf/extra/domains/DOMAIN/server.crt"
        SSLCertificateKeyFile "/applis/apache/apache_unix.001/conf/extra/domains/DOMAIN/server.key"
</VirtualHost>

httpd.conf ->

<Location "/balancer-manager">
    SetHandler balancer-manager
    Require host example.com
</Location>

app1.conf ->

BalanceMrember http://hostname:port
...

https://www.youtube.com/watch?v=6gDW56dS8nU

cat /home/pmmhd_etude/installApache/vhost.conf
cat /home/pmmhd_etude/vhost-template.conf
cat /applis/ogm/source/apache.apache_2416_unix.001/docs/conf/extra/proxy-html.conf

https://httpd.apache.org/docs/2.4/fr/mod/mod_proxy_balancer.html

