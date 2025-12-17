# Registrar pacotes NAT vindos do host 172.30.15.26 no chain PROXY_hotspot usando NFLOG
# - -t nat : tabela NAT
# - -I PROXY_hotspot : insere a regra no início do chain PROXY_hotspot
# - -s 172.30.15.26 : filtra pacotes com origem nesse IP
# - -j NFLOG : envia eventos para o subsistema NFLOG (útil para inspeção/log centralizado)
# - --nflog-group 0 : grupo NFLOG (ajuste se usar múltiplos consumidores)
# - --nflog-prefix debug : prefixo que aparecerá nos logs para identificar a origem
sudo iptables -t nat -I PROXY_hotspot -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix debug

# Para acompanhar em tempo real os logs gerados pelo ulogd (syslog emulação):
# Ajuste o caminho do arquivo conforme a configuração do ulogd no seu sistema
tail -f /var/log/ulog/ulogd_syslogemu.log

# Observações:
# - Execute os comandos como root (use sudo se necessário).
# - Para remover a regra adicionada use:
#   sudo iptables -t nat -D PROXY_hotspot -s 172.30.15.26 -j NFLOG --nflog-group 0 --nflog-prefix debug
# - Verifique a configuração do ulogd para garantir que o destino do log está correto.
# - O prefixo `debug` ajuda a filtrar/identificar entradas relacionadas quando múltiplas fontes enviam para NFLOG.
