# Required "DATA" directory for OpenVPN

This container assumes you have a "/data" (or like) directory with your server config and certs like so:

```
/data/certs:
  ca.crt
  dh.pem
  server.crt
  server.key
  ta.key (technically "optional" - in reality a must for security)
  crl.pem (optional - revoke list)

/data:
  server.conf
```


## Server Config
```
# Example of a working EXTREMELY secure server.conf (customize as needed):

local 0.0.0.0
port 1194
# if tcp, change to "proto tcp-server"
proto udp
dev tun
ca certs/ca.crt
cert certs/server.crt
key certs/server.key  # This file should be kept secret
dh certs/dh.pem
crl-verify certs/crl.pem
server 192.168.123.0 255.255.255.0
push "dhcp-option DNS 1.1.1.1"
push "dhcp-option DNS 1.0.0.1"
push "redirect-gateway def1"
keepalive 10 120
cipher AES-256-GCM
ncp-ciphers AES-256-GCM:AES-256-CBC
auth SHA512
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-128-CBC-SHA256"
tls-version-min 1.2
tls-auth certs/ta.key 0 # This file is secret
compress
comp-lzo no
push 'comp-lzo no'
max-clients 5
user nobody
group nogroup
persist-key
persist-tun
status openvpn-status.log
log         openvpn.log
log-append  openvpn.log
verb 3

```


## Client Config
```
# Example of a working EXTREMELY secure client.conf (customize as needed):

client
dev tun
# if tcp, change to "proto tcp-client"
proto udp
ping-restart 15
remote vpn-server-host.tld 1194
resolv-retry infinite
nobind
persist-key
persist-tun
<ca>
# Insert "ca.crt"
</ca>
<dh>
# Insert "dh.pam"
</dh>
<cert>
# Insert "client.crt"
</cert>
<key>
# Insert "client.key"
</key>
compress
auth-nocache
cipher AES-256-GCM
auth SHA512
tls-client
tls-version-min 1.2
remote-cert-tls server
key-direction 1
<tls-auth>
# Insert "ta.key"
</tls-auth>
redirect-gateway def1
#verb 3
```

# How to run the OpenVPN Docker Container?
```
docker run -it -d --restart=always \
--name=openvpn \
-p 1194:1194/udp \
--cap-add=NET_ADMIN \
-v /data:/etc/openvpn \
ventz/openvpn
```
