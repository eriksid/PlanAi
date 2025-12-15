# Implementar Backend C++ para WireGuard

## Objetivo

Implementar o programa `/var/S4/wireguard` em C++ para fornecer funcionalidades de backend para o gerenciamento de interfaces WireGuard, substituindo as chamadas temporárias ao OpenVPN e integrando com a interface PHP existente.

## Contexto

- A interface PHP (`wg_interfaces.php`) já existe e está funcional
- O arquivo `retrieve_wg_interfaces_table.php` atualmente chama `/var/S4/openvpn -list_groups` (temporário)
- O arquivo `retrieve_wg_interfaces.php` precisa ser expandido para salvar/carregar interfaces
- O arquivo `wireguard.cpp` está vazio e precisa ser implementado
- O banco de dados `vpn` já possui a tabela `wireguard_connections` (mencionada no backup.cpp)

## Arquivos a Modificar/Criar

### 1. `S4/cpp/wireguard.cpp` (Implementar)

Implementar programa completo com as seguintes operações:

- **`-list_interfaces`**: Lista interfaces WireGuard com paginação, ordenação e filtros
  - Parâmetros: filtro (formato: `sidx xS4x sord xS4x page xS4x limit xS4x where`), tipo de saída ("table" ou "pdf")
  - Retorna JSON no formato jqGrid com colunas: status, name, type, obs, total_users, start_in, expire_in
  - Consulta a tabela `vpn.wireguard_connections` (ou similar)
  - Segue o padrão de `openvpn.cpp -list_groups`

- **`-save_interface`**: Salva ou atualiza uma interface WireGuard
  - Parâmetros: id (opcional, se vazio cria novo), status, interface_name, description, listen_port, endpoint_address, local_networks, keepalive_value, public_key, private_key, preshared_key
  - Retorna JSON: `{"status": "ok"}` ou `{"status": "error", "msg": "..."}`
  - Valida campos obrigatórios (interface_name, listen_port, public_key)
  - Insere ou atualiza no banco de dados

- **`-load_interface`**: Carrega dados de uma interface específica
  - Parâmetros: id
  - Retorna JSON com todos os campos da interface

- **`-delete_interface`**: Exclui uma ou mais interfaces
  - Parâmetros: ids (separados por vírgula)
  - Retorna JSON com status da operação

### 2. `S4/php/retrieve_wg_interfaces.php` (Expandir)

Adicionar tratamento para salvar/carregar interfaces:

- Adicionar caso `escolha == ""` (sem escolha) ou novo parâmetro para detectar operação de salvar/carregar
- Se receber `id` no POST: chamar `/var/S4/wireguard -load_interface <id>`
- Se receber dados de interface (sem `escolha`): chamar `/var/S4/wireguard -save_interface` com todos os parâmetros
- Retornar JSON apropriado

### 3. `S4/php/retrieve_wg_interfaces_table.php` (Atualizar)

Substituir chamada do OpenVPN:

- Linha 28: Trocar `/var/S4/openvpn -list_groups` por `/var/S4/wireguard -list_interfaces`

## Estrutura do Código C++

### Includes e Dependências

- Usar mesmas bibliotecas de `openvpn.cpp`: `libs4utils.h`, `libcpputils.h`, `libnetwork.h`, `libdate.h`, `libexceptions.h`, `libapi.h`
- Incluir MySQL: `/usr/local/mysql-S4.4.16.2/include/mysql.h`
- JSON: usar `Json::Value` (já disponível no projeto)

### Estrutura Principal

```cpp
static string NAME = "/var/S4/wireguard";
MYSQL mysql;
MYSQL_RES* result;
MYSQL_ROW row;

int main(int argc, char* argv[]) {
  // Verificar argumentos
  // Conectar ao banco
  // Processar operação baseada em argv[1]
  // Retornar resultado
}
```

### Validações

- Validar porta (0-65535)
- Validar formato de chave pública (base64, 44 caracteres)
- Validar formato de chave privada (base64, 44 caracteres, opcional)
- Validar formato de preshared key (base64, 44 caracteres, opcional)
- Validar formato de redes locais (CIDR)

### Banco de Dados

- Conectar ao banco `vpn`
- Tabela esperada: `wireguard_connections` ou similar
- Campos esperados: id, status, interface_name, description, listen_port, endpoint_address, local_networks, keepalive_value, public_key, private_key, preshared_key, created_at, updated_at

## Padrões a Seguir

- Seguir estrutura e estilo de código de `openvpn.cpp`
- Usar funções de erro: `erro()`, `erro_db()`
- Usar funções de utilitário: `db_connect()`, `toDB()`, `itos()`, `explode()`, etc.
- Retornar JSON no formato esperado pelo jqGrid para listagens
- Retornar JSON simples `{"status": "ok"}` ou `{"status": "error", "msg": "..."}` para operações de salvar/carregar

## Testes

- Testar listagem com paginação
- Testar criação de nova interface
- Testar edição de interface existente
- Testar exclusão de interface
- Testar validações de campos
- Verificar integração com interface PHP