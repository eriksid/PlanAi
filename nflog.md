
#bypass
iptables -t nat -I PROXY_hotspot 1 -s 172.30.15.26 \  -m comment --comment bypass_google -j RETURN


iptables -t nat -D PROXY_hotspot -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix debug



iptables -t nat -I PROXY_hotspot 2 -s 172.30.15.26 \
  -j NFLOG --nflog-group 0 --nflog-prefix "NAO_BYPASS"


iptables -t nat -D PROXY_hotspot -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix debug




#raw
iptables -t raw -I PREROUTING 1 -s 172.30.15.26  -j NFLOG --nflog-group 0 --nflog-prefix "RAW_PREROUTING"
#mangle PREROUTING
iptables -t mangle -I PREROUTING 1  -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "MANGLE_PREROUTING"

#nat PREROUTING
iptables -t nat -I PREROUTING 1   -s 172.30.15.26  -j NFLOG --nflog-group 0 --nflog-prefix "NAT_PREROUTING" 

#FORWARD
iptables -t mangle -I FORWARD 1  -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "MANGLE_FORWARD"
iptables -t filter -I FORWARD 1   -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "FILTER_FORWARD"

#INPUT
iptables -t mangle -I INPUT 1 -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "MANGLE_INPUT"
iptables -t filter -I INPUT 1 -s 172.30.15.26  -j NFLOG --nflog-group 0 --nflog-prefix "FILTER_INPUT"


#OUTPUT
iptables -t raw -I OUTPUT 1 \
  -s 172.30.15.26 \
  -j NFLOG --nflog-group 0 --nflog-prefix "RAW_OUTPUT"

iptables -t mangle -I OUTPUT 1  -s 172.30.15.26  -j NFLOG --nflog-group 0 --nflog-prefix "MANGLE_OUTPUT"


iptables -t filter -I OUTPUT 1 -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "FILTER_OUTPUT"

# aqui não chega nada
#POSTROUTING
iptables -t mangle -I POSTROUTING 1 -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "MANGLE_POSTROUTING"

iptables -t nat -I POSTROUTING 1 -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix "NAT_POSTROUTING"
