FROM alpine:latest
RUN apk update && apk add bash openvpn iptables

#  Mount over entire /etc/openvpn
VOLUME ["/etc/openvpn"]

# Enable Forwarding - no need - enabled by default
#RUN sysctl -w net.ipv4.ip_forward=1

WORKDIR /etc/openvpn
EXPOSE 1194/udp

COPY start-vpn.sh /

# Run actual OpenVPN daemon
ENTRYPOINT ["/start-vpn.sh"]
