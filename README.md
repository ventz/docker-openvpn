# Required "DATA" directory for OpenVPN

This container assumes you have a "/data" (or like) directory with your server config and certs like so:

```
/data/certs:
  ca.crt
  dh.pem
  server.crt
  server.key
  ta.key

/data:
  server.conf
```

```
# Example of a working server.conf (customize as needed):

local 0.0.0.0
port 1194
proto udp
dev tun
ca certs/ca.crt
cert certs/server.crt
key certs/server.key  # This file should be kept secret
dh certs/dh.pem
server 192.168.123.0 255.255.255.0
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"
push "redirect-gateway def1"
keepalive 10 120
tls-auth certs/ta.key 0 # This file is secret
cipher AES-256-CBC
auth SHA512
comp-lzo
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

# How to run the OpenVPN Docker Container?
```
docker run -it -d --name=openvpn \
--restart=always \
-p 1194:1194/udp \
--cap-add=NET_ADMIN \
-v /data:/etc/openvpn \
ventz/openvpn
```
