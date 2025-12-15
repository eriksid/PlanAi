----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Iniciando get_monitoring_status
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Executando comando: /usr/local/wireguard/bin/wg show
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): wg_output.size(): 10
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Conectando ao banco de dados
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Conectado ao banco de dados com sucesso
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Iniciando parsing do wg_output
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[0]: [interface: wg0]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Nova interface detectada: wg0
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[1]: [public key: MuZySZmKRriErWbAIlK4mf4Z2yUvPhtNeMKHfDiUBh0=]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[2]: [private key: (hidden)]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[3]: [listening port: 51820]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[4]: []
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[5]: [peer: d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Novo peer detectado: public_key=d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[6]: [endpoint: 192.168.10.128:64891]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[7]: [allowed ips: 172.24.14.2/32]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[8]: [latest handshake: 1 minute, 16 seconds ago]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): LINE[9]: [transfer: 253.88 KiB received, 37.49 KiB sent]
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Parsing concluído. current_iface: wg0, peers_data.size(): 0
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.public_key: d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.iface_name: wg0
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.endpoint: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.allowed_ips: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.latest_handshake: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.bytes_received: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): current_peer.bytes_sent: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Último peer adicionado: public_key=d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Total de peers parseados: 1
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Iniciando busca de peers no banco de dados
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Processando peer[0]: public_key=d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   iface_name: wg0
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   endpoint: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   allowed_ips: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   latest_handshake: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   bytes_received: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   bytes_sent: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): SQL: SELECT wp.conn_name, wp.conn_address_cidr, wi.iface_name FROM vpn.wireguard_peers AS wp INNER JOIN vpn.wireguard_interfaces AS wi ON wp.interface_id = wi.id WHERE wp.public_key = 'd7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=' AND wp.status = 'enabled'
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Peer encontrado no banco:
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   conn_name: filial
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   conn_address_cidr: 172.24.14.2/32
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   iface_name_db: wg1
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): JSON peer criado:
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   conn_name: filial
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   conn_ip: 172.24.14.2
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   iface_name: wg0
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   latest_handshake: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   endpoint: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   bytes_sent: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard):   bytes_received: 
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Peer adicionado ao json_rows. Total: 1
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): JSON final preparado. Total de rows: 1
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): JSON completo: {
        "rows" : 
        [
                {
                        "bytes_received" : "0 B",
                        "bytes_sent" : "0 B",
                        "conn_ip" : "172.24.14.2",
                        "conn_name" : "filial",
                        "endpoint" : "-",
                        "iface_name" : "wg0",
                        "latest_handshake" : "-",
                        "public_key" : "d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA="
                }
        ]
}

----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Conexão com banco fechada
----------------------------------
01/12/2025 16:38:25 - MSG(/var/S4/wireguard): Variáveis limpas
