git add S4/php/wg_interfaces.php S4/php/wg_peers.php
git commit -m "refactor: substituir \$.ajax por \$.post em módulos WireGuard

Substitui todas as chamadas \$.ajax por \$.post nos arquivos de interfaces
e peers do WireGuard para simplificar o código e melhorar a legibilidade.

Alterações:
- wg_interfaces.php: 4 substituições (save, load, getJqGridParams, saveJqGridParams)
- wg_peers.php: 5 substituições (loadInterfaces, save, load, getJqGridParams, saveJqGridParams)

Mantém a mesma funcionalidade, utilizando a sintaxe resumida do jQuery
para requisições POST. Requisições síncronas preservadas com \$.ajaxSetup."


git commit -m "#S4-12684 #S4-12681 - VPN - WireGuard - Substitui todas as chamadas \$.ajax por \$.post nos arquivos de interfaces
e peers do WireGuard para simplificar o código e melhorar a legibilidade."


-- <span class="hint">Ex: wg0, wg1...</span>
++ <td><label for="wg_interface_name">getInfoTip("Ex: wg0, wg1...") Interface:</label></td>


TODO:
Erro de comunicação ao salvar peer.
/var/S4/wireguard -save_peer  enabled 1 teste 10.10.10.2  10.0.0.0/24 25 4Z03Qe3Oszi/f2wKxRfVYDO4Oa2DMqxblJJ1DnSWQG4= oKuQR2eEu98HPpP7EbBoqKrQVYpBLlvOXPBbTUEWGEk= 


Campos:
Tela: VPN > Wireguard > Interfaces:
- Status: manter
- Interface: trocar por somente leitura e preencher com por exemplo wg0
- Novo campo: Nome
- Porta: manter
- Endpoint trocar por - Endereço endpoint padrão do peer: 
    - (i) O endereço informado neste campo, será exibido no cadastro dos peers de WireGuard em <i>menu superior VPN, menu lateral Wireguard  submenu Peers</i>.<br><br>    Informe o endereço de conexão, por exemplo: vpn.meudomínio.<br><br>O endereço deste campo será utilizado apenas nos novos peers cadastrados. <br>    Peers existentes necessitam ser editados e reconfigurados novamente no computador/dispositivo do usuário.
- Novo campo: Rede VPN
- Remover comapo:  Redes locais
- Keepalive padrão: manter
- Chave pública: manter
- Chave privada: oculto ?
- Preshared key: remover?

Tela: VPN > Wireguard > Peers:
- Interface ajustar para ler do nome ve vez de descrição
- Nome da conexão: manter
- IP interno do peer: trocar por select de ips livres da rede vpn da interface selecionada
- Endpoint do peer: preencher com o endpoint padrão
- Redes permitidas: salvar rede vpn no banco
- Keepalive: manter
- Chave pública: manter
- Chave privada: manter
-  Preshared key: manter

 



git commit -m "#S4-12681 - VPN > Wireguard > Interfaces: Ajustes após mudança de Banco de Dados
- Campos removidos: local_networks, keepalive_value, preshared_key
- novos campos: iface_name, iface_ip, iface_network, interface_port, dns_servers e mtu.
- public_key: CHAR(44) → VARCHAR(88)
- private_key: CHAR(44) → VARCHAR(88)
- Refatora todo o CRUD (list, save, load) para suportar a nova estrutura no banco.
- Implementa generateIfaceName para criação automática de nomes de interface (wgX).
- Ajusta listagens (JSON) e relatórios (PDF) para exibir os novos campos."





```
  $params = Array(
    "-save_interface",
    $id,
    $status,
    $name,
    $iface_name,
    $iface_ip,
    $iface_network,
    $interface_port,
    $default_endpoint_addr,
    $dns_servers,
    $mtu,
    $public_key,
    $private_key,
    $description
  );
```



DROP TABLE IF EXISTS vpn.wireguard_peers;
DROP TABLE IF EXISTS vpn.wireguard_interfaces;

-- #S4-10045 - Wireguard servers/ifaces
CREATE TABLE vpn.wireguard_interfaces (
  id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  status ENUM('enabled', 'disabled') NOT NULL DEFAULT 'enabled',
  name VARCHAR(50) NOT NULL,
  description VARCHAR(255) NULL,
  iface_name VARCHAR(15) NOT NULL UNIQUE,          -- ex: wg0
  iface_ip VARCHAR(45) NOT NULL,                   -- ex: 172.31.0.1 (45 pra suportar IPv6 também) 
  iface_network VARCHAR(20) NOT NULL,              -- ex: 172.31.0.0/24 
  iface_port SMALLINT UNSIGNED NOT NULL,           -- ex: 51820 
  dns_servers VARCHAR(255) NULL,                   -- ex: "1.1.1.1,8.8.8.8"
  mtu INT UNSIGNED NULL,
  default_endpoint_addr VARCHAR(255) NULL,
  private_key VARCHAR(88) NULL,
  public_key  VARCHAR(88) NOT NULL, 
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
              ON UPDATE CURRENT_TIMESTAMP
  ) ENGINE=InnoDB
    DEFAULT CHARSET = utf8mb4
    COLLATE = utf8mb4_unicode_ci;
-- #S4-10045 - Wireguard users/peers
CREATE TABLE vpn.wireguard_peers (
  id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  interface_id INT UNSIGNED NOT NULL,
  status ENUM('enabled', 'disabled') NOT NULL DEFAULT 'enabled',
  peer_type ENUM('user', 'site') NOT NULL DEFAULT 'user',
  conn_name    VARCHAR(100) NOT NULL,     -- ex: "Notebook Fabio", "Filial 1"
  description  VARCHAR(255) NULL,
  conn_address_cidr VARCHAR(45) NOT NULL, -- ex: 10.10.0.10/32
  allowed_networks  TEXT NULL,            -- ex: "192.168.10.0/24,192.168.11.0/24"
  endpoint_addr VARCHAR(255) NULL,
  keepalive_value TINYINT UNSIGNED NULL,
  public_key    VARCHAR(88) NOT NULL,
  private_key   VARCHAR(88) NULL,
  preshared_key VARCHAR(88) NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  CONSTRAINT fk_wg_peers_interface
    FOREIGN KEY (interface_id)
    REFERENCES vpn.wireguard_interfaces (id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  UNIQUE KEY uk_wg_peers_interface_addr (interface_id, conn_address_cidr) -- IP de túnel único por interface
  ) ENGINE=InnoDB
    DEFAULT CHARSET = utf8mb4
    COLLATE = utf8mb4_unicode_ci;



## Estrutura do wireguard

DIR_WG=/usr/local/wireguard


mkdir -p /usr/local/wireguard/etc/peers
umask 077 /usr/local/wireguard/etc/


private_key=$(/usr/local/wireguard/bin/wg genkey)
public_key=$(echo "$private_key" | /usr/local/wireguard/bin/wg pubkey)
echo "Public key: $public_key"
echo "Private key:$private"


## Interface
cd /usr/local/wireguard/etc

cat > wg0.conf << EOF
[Interface]
PrivateKey = $private_key
ListenPort = 51820
EOF

cat > variables.wg0 <<EOF
#variables
WG_IFACE=wg0
MY_ENDPOINT_PORT=51820
WG_ADDRESS=172.24.14.1/24

EOF


/etc/init.d/wireguard start


## Peer
cd /usr/local/wireguard/etc/peers
mkdir filial
cd filial
/usr/local/wireguard/bin/wg genkey | tee private_filial.key | /usr/local/wireguard/bin/wg pubkey > public_filial.key
ll
cat *.key

cd /usr/local/wireguard/etc/
 cat >> wg0.conf << EOF
#filial
[Peer]
PublicKey = $(cat peers/filial/public_filial.key)
Endpoint = 172.30.15.200:51820
AllowedIPs = 172.24.14.0/24, 192.168.10.0/24
PersistentKeepalive = 25
EOF



## Peer cliente
cat > cliente.conf << EOF
[Interface]
PrivateKey = $(cat peers/filial/private_filial.key)
ListenPort = 51820
#Matriz
[Peer]
PublicKey = $public_key
Endpoint = 172.30.15.200:51820
AllowedIPs = 172.24.14.0/24, 172.30.15.0/24
PersistentKeepalive = 25
EOF




## TODO
* nome do peer.conf
* peer 
* interface
* monitoramento
* relatorio






[Interface]
PrivateKey = KJ1JIGt4wcKmGlbuqochpcj016Y3969owLLFdMjNZlY=
ListenPort = 51820
#filial
[Peer]
PublicKey = d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
Endpoint = 172.30.15.200:51820
AllowedIPs = 172.24.14.0/24, 192.168.10.0/24
PersistentKeepalive = 25



Public key: RNZxCP3GP+WA5//ez3mjiiJgM6EMg7NCtRxP3aOZK2A=
Private key:KCp7SW8N0CnN9IM2xb5dYlQzy4/VUPtEq8rfc06Q1FM=



cJvYDCTNSYzornx/wIsHI7QwEHGGHXekpb/tRojqBng=



------------------------

[Interface]
PrivateKey = cJvYDCTNSYzornx/wIsHI7QwEHGGHXekpb/tRojqBng=
Address = 172.24.14.2/24

[Peer]
PublicKey = d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
AllowedIPs = 172.24.14.0/24, 172.30.15.0/24
Endpoint = 172.30.15.200:51820
PersistentKeepalive = 25




## Crud

- editar o peer está com problema IP interno do peer: não prenche
- botão editar  não aparece na grid




git commit -m "#S4-12684 #S4-12681 Correções WireGuard: botão editar, IP interno, exportação e ajustes de UI

- Fix: botão editar não aparecia na grid de interfaces
- Fix: IP interno não selecionava corretamente ao editar peer
- Fix: coluna "Excluir" cortando texto (aumento para 25px)
- Add: exportação PDF e CSV para interfaces e peers
- Fix: parâmetros e validações no save_peer
- Fix: ordem de conexão ao banco no save_interface
"