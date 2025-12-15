

  # Implementação da Tela de Monitoramento WireGuard

## Objetivo

Adicionar seção de monitoramento WireGuard na tela `lsvpn.php` exibindo informações em tempo real dos peers conectados, seguindo o padrão já existente para OpenVPN e IPSec.


## Exemplo de saída do comando wg:
```
root@erik 09:52 [/usr/local/wireguard/bin] git(---):: ./wg
interface: wg0
  public key: MuZySZmKRriErWbAIlK4mf4Z2yUvPhtNeMKHfDiUBh0=
  private key: (hidden)
  listening port: 51820

peer: d7JM7Ydj8V+7T1SJgUR0zXVHmO28phqUS1VWGniDnBA=
  endpoint: 192.168.10.128:64891
  allowed ips: 172.24.14.2/32
  latest handshake: 44 seconds ago
  transfer: 25.49 KiB received, 4.70 KiB sent
  ```

## Estrutura da Implementação

### 1. Backend C++ (wireguard.cpp)

**Arquivo:** `S4/cpp/wireguard.cpp`

**Nova função:** `-get_monitoring_status`

- Executa comando `/usr/local/wireguard/bin/wg show` para todas as interfaces ou interface específica
- Parseia a saída do comando `wg show` que retorna:
  - Interface: nome (ex: wg0), porta de escuta, chave pública
  - Peers: chave pública, endpoint, allowed IPs, último handshake, transfer (received/sent)
- Busca no banco de dados os peers cadastrados para correlacionar chave pública com nome do peer
- Retorna JSON estruturado com:
  - `conn_name`: Nome do peer (do banco)
  - `conn_ip`: IP interno do peer (do banco, campo `conn_address_cidr`)
  - `iface_name`: Nome da interface (ex: wg0)
  - `latest_handshake`: Último handshake formatado (ex: "44 seconds ago" ou timestamp)
  - `endpoint`: Endpoint ativo do peer
  - `bytes_sent`: Dados enviados formatados (ex: "4.70 KiB")
  - `bytes_received`: Dados recebidos formatados (ex: "25.49 KiB")
  - `public_key`: Chave pública do peer (para correlação)

**Implementação:**

- Usar `getCmdOutputLines("/usr/local/wireguard/bin/wg show")`
- Parsear linha por linha identificando:
  - `interface:` → nome interface
  - `peer:` →  public_key
  - `endpoint:` → endpoint do peer
  - `allowed ips:` → IPs permitidos
  - `latest handshake:` → tempo desde último handshake
  - `transfer:` → bytes recebidos e enviados
- Fazer JOIN com tabela `vpn.wireguard_peers` usando `public_key` para obter `conn_name` e `conn_address_cidr`
- Fazer JOIN com tabela `vpn.wireguard_interfaces` para obter `iface_name`

### 2. Frontend PHP (lsvpn.php)

**Arquivo:** `S4/php/lsvpn.php`

**Modificações:**

- Adicionar nova seção de tabela para WireGuard (similar às seções OpenVPN e IPSec)
- Colunas da tabela:

  1. Nome do Peer (`conn_name`)
  2. IP (`conn_ip` - extrair do `conn_address_cidr`)
  3. Interface (`iface_name`)
  4. Último Handshake (`latest_handshake`)
  5. Endpoint (`endpoint`)
  6. Dados Enviados (`bytes_sent`)
  7. Dados Recebidos (`bytes_received`)

- Integrar com sistema de atualização automática existente (a cada 5 segundos)
- Adicionar suporte ao tablesorter para ordenação
- Exibir contador de peers ativos no cabeçalho da tabela
- Tratar caso de nenhum peer conectado

**Chamada ao backend:**

```php
$cmd = "sudo /var/S4/wireguard -get_monitoring_status";
$ret = run($cmd, "Erro ao recuperar peers WireGuard!");
$json = json_decode($ret, TRUE);
```

### 3. Formatação de Dados

- **Último Handshake:** Manter formato original do `wg show` (ex: "44 seconds ago", "2 minutes ago") ou converter para formato legível
- **Bytes:** Converter valores como "25.49 KiB", "4.70 KiB" para formato legível (pode manter como está ou converter para KB/MB/GB)
- **IP:** Extrair apenas o IP do formato CIDR (ex: "172.24.14.2/32" → "172.24.14.2")

## Arquivos a Modificar

1. `S4/cpp/wireguard.cpp` - Adicionar função `-get_monitoring_status`
2. `S4/php/lsvpn.php` - Adicionar seção de monitoramento WireGuard

## Padrões a Seguir

- Usar `getCmdOutputLines()` para executar comandos
- Retornar JSON estruturado usando `success(json)` do libapi.h
- Seguir padrão de tratamento de erros com `try/catch` e `fail()`
- Usar `toDB()` e `toDBInt()` para sanitização de dados SQL
- Manter consistência visual com seções OpenVPN e IPSec existentes
- Usar `tablesorter` para ordenação de colunas
- Integrar com sistema de atualização automática existente