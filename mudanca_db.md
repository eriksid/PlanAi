# Planejamento de Mudança de Banco de Dados WireGuard

## Análise das Mudanças no Schema

Comparando o schema antigo (usado atualmente no código C++) com o novo schema:

### Campos Removidos (devem ser eliminados do código):

- `local_networks` (TEXT) - REMOVIDO
- `keepalive_value` (INT UNSIGNED) - REMOVIDO  
- `preshared_key` (CHAR(44)) - REMOVIDO

### Campos Renomeados:

- `interface_name` → `iface_name` (VARCHAR(30) → VARCHAR(15))
- `listen_port` → `interface_port` (mantém tipo SMALLINT UNSIGNED)
- `endpoint_address` → `default_endpoint_addr` (mantém tipo VARCHAR(255))

### Campos Adicionados (novos, obrigatórios):

- `name` (VARCHAR(50) NOT NULL) - Nome descritivo da interface
- `iface_ip` (VARCHAR(45) NOT NULL) - IP atribuído à interface
- `iface_network` (VARCHAR(20) NOT NULL) - CIDR da rede VPN

### Campos Adicionados (novos, opcionais):

- `dns_servers` (VARCHAR(255) NULL) - Servidores DNS separados por vírgula
- `mtu` (INT UNSIGNED NULL) - MTU da interface

### Campos Modificados (tipo alterado):

- `public_key`: CHAR(44) → VARCHAR(88)
- `private_key`: CHAR(44) → VARCHAR(88)

### Campos Mantidos:

- `id`, `status`, `description`, `created_at`, `updated_at`

## Correções Necessárias no Documento mudanca_db.md

### 1. ATUALIZAR NOMES DE COLUNAS NAS QUERIES (linhas 31-56)

- Remover completamente `local_networks`, `keepalive_value` e `preshared_key` de SELECT/INSERT/UPDATE
- Substituir `interface_name` → `iface_name`, `listen_port` → `interface_port`, `endpoint_address` → `default_endpoint_addr`
- Incluir os novos campos `name`, `iface_ip`, `iface_network`, `dns_servers`, `mtu` em todas as consultas
- Ajustar tipos de `public_key` e `private_key` para VARCHAR(88)

### 2. ATUALIZAR MAPEAMENTO DE VARIÁVEIS (linhas 60-74)

- Mapear variáveis do formulário/backend para os novos nomes de coluna (ex.: `default_endpoint_addr`)
- Adicionar bindings para `name`, `iface_ip`, `iface_network`, `dns_servers`, `mtu`
- Remover do código as variáveis `local_networks`, `keepalive_value`, `preshared_key`

### 3. AJUSTAR LÓGICA DE VALIDAÇÃO (linhas 77-87)

- Validar `name` (1-50), `iface_name` (1-15), `dns_servers` (≤ 255) e `mtu` (INT > 0)
- Validar `iface_ip` como IPv4/IPv6 e `iface_network` como CIDR
- Atualizar validação de chaves WireGuard para aceitar até 88 caracteres
- Eliminar validações para campos removidos (`local_networks`, `keepalive_value`, `preshared_key`)

### 4. EXEMPLO DE ADAPTAÇÃO (linhas 90-115)

- Novo SELECT (linhas 91-97):
  ```sql
  SELECT id, status, name, iface_name, iface_ip, iface_network,
         interface_port, default_endpoint_addr, dns_servers, mtu,
         public_key, private_key, description
  FROM vpn.wireguard_interfaces;
  ```
- Novo INSERT:
  ```sql
  INSERT INTO vpn.wireguard_interfaces
    (status, name, iface_name, iface_ip, iface_network,
     interface_port, default_endpoint_addr, dns_servers, mtu,
     public_key, private_key, description, created_at, updated_at)
  VALUES (...);
  ```
- Novo UPDATE:
  ```sql
  UPDATE vpn.wireguard_interfaces SET
    status = ?, name = ?, iface_name = ?, iface_ip = ?, iface_network = ?,
    interface_port = ?, default_endpoint_addr = ?, dns_servers = ?, mtu = ?,
    public_key = ?, private_key = ?, description = ?, updated_at = NOW()
  WHERE id = ?;
  ```
- Atualizar exemplos de JSON para refletir os novos campos (sem `local_networks`, `keepalive_value`, `preshared_key`)

### 5. REMOVER/ADAPTAR LÓGICA PARA CAMPOS OBSOLETOS (linhas 118-122)

- Eliminar qualquer uso residual de `local_networks`, `keepalive_value`, `preshared_key`
- Revisar funções utilitárias que manipulavam esses campos
- Adaptar rotinas que usavam `endpoint_address` para `default_endpoint_addr`

## Arquivos a Modificar

1. **S4/cpp/mudanca_db.md** - Atualizar todas as seções conforme correções acima

## Pontos Críticos no Código C++ (wireguard.cpp)

Baseado na análise do código atual:

1. **Linha 277**: SELECT usa `interface_name` → deve ser `iface_name` e adicionar `name`
2. **Linha 279**: SELECT usa `listen_port` → deve ser `interface_port`
3. **Linha 351-360**: Variáveis usam nomes antigos → atualizar para novos nomes
4. **Linha 430-445**: `-save_interface` recebe 13 parâmetros incluindo campos removidos → reduzir e adicionar novos
5. **Linha 507-521**: INSERT usa campos antigos → atualizar completamente
6. **Linha 524-536**: UPDATE usa campos antigos → atualizar completamente
7. **Linha 570-574**: SELECT em `-load_interface` usa campos antigos → atualizar
8. **Linha 583-593**: JSON de resposta usa nomes antigos → atualizar para novos nomes