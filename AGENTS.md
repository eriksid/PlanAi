# AGENTS.md - Contexto do Projeto S4

Este documento serve como referência de contexto para assistentes de IA trabalhando no projeto S4. Use este arquivo como base de conhecimento em conversas futuras.

---

## 📋 Visão Geral do Projeto

**S4** é um sistema de cibersegurança e controle de internet desenvolvido pela **SETI - Segurança e Tecnologia na Internet Ltda**.

### Características Principais:
- Sistema de gerenciamento de rede e segurança
- Interface web em PHP
- Backend em C++ para operações de sistema
- Gerenciamento de VPN (OpenVPN, WireGuard, IPSec)
- Controle de firewall, proxy, antispam
- Monitoramento e relatórios
- Sistema de balanceamento de carga
- Gerenciamento de certificados SSL/TLS

### Versão Atual:
- PHP: 4.12.0+ (conforme `package.json`)
- MySQL: 4.16.2 (customizado: `/usr/local/mysql-S4.4.16.2/`)

---

## 📁 Estrutura de Diretórios

```
/home/erik/dados_do_meu_s4/usr/src/desenv/
├── S4/
│   ├── cpp/                    # Código C++ (backend)
│   │   ├── docs/               # Documentação (este diretório)
│   │   ├── lib/                # Bibliotecas compartilhadas
│   │   ├── wireguard.cpp       # Backend WireGuard (em desenvolvimento)
│   │   ├── openvpn.cpp         # Backend OpenVPN (referência)
│   │   └── [outros programas]  # Outros módulos C++
│   ├── php/                    # Interface web PHP
│   │   ├── wg_interfaces.php   # Tela de interfaces WireGuard
│   │   ├── wg_peers.php        # Tela de peers WireGuard
│   │   ├── retrieve_wg_*.php   # Endpoints AJAX para WireGuard
│   │   └── [outros arquivos]   # Outros módulos PHP
│   ├── hotspot/                # Portal de hotspot
│   ├── s4_vue/                 # Interface Vue.js (legado)
│   └── s4_vue3/                # Interface Vue.js 3
└── git-scripts/                # Scripts Git hooks
```

---

## 🔧 Padrões de Código C++

### Estrutura Básica de um Programa

```cpp
#include "lib/libs4utils.h"
#include "lib/libnetwork.h"
#include "lib/libdate.h"
#include "lib/libcpputils.h"
#include "lib/libexceptions.h"
#include "lib/libapi.h"
#include "/usr/local/mysql-S4.4.16.2/include/mysql.h"
#include "jsoncpp/include/json/json.h"

using namespace std;

static string NAME = "/var/S4/nome_do_programa";
MYSQL mysql;
MYSQL_RES* result;
MYSQL_ROW row;
unsigned long proc_pid = getpid();

int main(int argc, char* argv[]) {
  // Conectar ao banco
  // Processar argumentos
  // Executar operação
  // Retornar resultado
}
```

### Bibliotecas Comuns

- **`libs4utils.h`**: Utilitários gerais (db_connect, toDB, itos, explode, etc.)
- **`libnetwork.h`**: Funções de rede
- **`libdate.h`**: Manipulação de datas
- **`libcpputils.h`**: Utilitários C++
- **`libexceptions.h`**: Tratamento de exceções
- **`libapi.h`**: Funções de API
- **`lib/libskyline.h`**: Sistema Skyline (cliente-servidor)
- **`lib/libproxy.h`**: Funções de proxy
- **`lib/libqos.h`**: Quality of Service

### Funções de Erro

```cpp
void erro(string mensagem, int codigo, int tipo);
void erro_db(string mensagem);
```

### Funções de Banco de Dados

```cpp
// Conectar ao banco
db_connect(&mysql, "vpn");  // ou outro banco

// Sanitizar strings para SQL
string toDB(const string& value);  // Retorna 'valor' ou NULL

// Converter inteiro para string
string itos(int valor);

// Dividir string
vector<string> explode(string delimitador, string texto);
```

### Formato de Resposta JSON

**Para listagens (jqGrid):**
```json
{
  "page": 1,
  "total": 1,
  "records": 10,
  "rows": [
    {
      "id": "1",
      "cell": ["valor1", "valor2", ...]
    }
  ]
}
```

**Para operações simples:**
```json
{"status": "ok"}
// ou
{"status": "error", "msg": "mensagem de erro"}
```

**⚠️ IMPORTANTE - Retorno JSON:**
- **NÃO usar `json["status"] = "ok"`** em funções que retornam dados estruturados
- A função `success(json)` já trata o retorno adequadamente
- Use `json["status"] = "ok"` apenas quando necessário para compatibilidade com código legado específico
- Para listagens e dados estruturados, retorne diretamente o JSON com os dados (ex: `json["rows"]`, `json["ips"]`, etc.) sem adicionar campo "status"

### Padrão de Argumentos de Linha de Comando

Os programas C++ seguem padrão de argumentos:
- `-list_interfaces`: Lista com paginação
- `-save_interface`: Salva/atualiza registro
- `-load_interface`: Carrega registro específico
- `-delete_interface`: Exclui registro(s)

**Exemplo de uso:**
```bash
/var/S4/wireguard -list_interfaces 'sidx xS4x sord xS4x page xS4x limit xS4x where' 'table'
/var/S4/wireguard -save_interface id status name ...
/var/S4/wireguard -load_interface 1
```

### Padrões Detalhados de Código C++

Esta seção descreve os padrões obrigatórios que devem ser seguidos em todo código C++ do projeto S4.

#### Variáveis

- **Remover variáveis sem uso**: Sempre verificar e remover variáveis declaradas mas não utilizadas
- **Aproximar declaração do uso**: Declarar variáveis o mais próximo possível de onde são utilizadas
- **Verificar posição em vector**: Sempre verificar se a posição existe antes de acessar um elemento do vector
  ```cpp
  // ❌ Ruim
  string valor = meu_vector[5];  // Pode causar erro se não existir
  
  // ✅ Bom
  if (meu_vector.size() > 5) {
    string valor = meu_vector[5];
  }
  ```

#### Arquivos

- **Fechar arquivos**: Sempre fechar arquivos após uso com `close()`
- **Usar fstream**: Preferir `fstream` em vez de `ifstream` ou `ofstream` separados quando possível
- **Manter dono/grupo**: Usar `chown` para manter o mesmo dono/grupo do arquivo original
  ```cpp
  // ✅ Bom
  fstream arq;
  arq.open("arquivo.txt", ios::in | ios::out);
  // ... operações ...
  arq.close();
  ```

#### Argumentos de Linha de Comando

- **Verificar argc primeiro**: Sempre verificar a quantidade de parâmetros (`argc`) antes de declarar variáveis
- **Não usar argv diretamente**: Não usar `argv` pelo meio do código; extrair para variáveis no início
  ```cpp
  // ✅ Bom
  int main(int argc, char* argv[]) {
    if (argc != 4) {
      cout << "Número de parâmetros incorreto." << endl;
      exit(0);
    }
    
    string name = string(argv[1]);
    string lastname = string(argv[2]);
    string age = string(argv[3]);
    
    // ... resto do código ...
  }
  ```

#### SQL

- **Comandos em maiúsculo**: Todos os comandos SQL devem estar em MAIÚSCULO (SELECT, INSERT, UPDATE, DELETE, etc.)
- **Sempre informar colunas**: Nunca usar `SELECT *`; sempre listar explicitamente as colunas
- **Sempre informar database**: Sempre usar `database.tabela` em vez de apenas `tabela`
- **Usar funções de erro SQL**: Usar `mysql_error()` e `mysql_errno()` para tratamento de erros
- **Liberar resultado**: Sempre usar `mysql_free_result()` após consultas SELECT
  ```cpp
  // ✅ Bom
  string query = "SELECT id, name, status FROM vpn.wireguard_connections WHERE id = " + itos(id);
  if (mysql_query(&mysql, query.c_str())) {
    erro_db("Erro ao buscar interface");
  }
  result = mysql_store_result(&mysql);
  // ... processar resultado ...
  mysql_free_result(result);
  ```

#### Formatação e Estrutura

- **Chaves na mesma linha**: Colocar chaves `{` na mesma linha da condição, com espaço antes
- **Usar endl**: Preferir `endl` em vez de `\n` para quebras de linha
- **Indentação correta**: Usar indentação consistente (2 espaços)
- **Verificações primeiro**: Inverter lógica do `if` quando possível para reduzir indentação e melhorar legibilidade
- **Tabulação**: Usar 2 espaços para indentação (não tabs)
- **Sem espaços no final da linha**: Não gerar código com espaços em branco no final das linhas (trailing whitespace)
  ```cpp
  // ✅ Bom
  if (!arquivo.is_open()) {
    cout << "Arquivo não existe" << endl;
    exit(0);
  }
  // ... código continua ...
  
  // ❌ Ruim (else desnecessário)
  if (!arquivo.is_open()) {
    cout << "Arquivo não existe" << endl;
  } else {
    // ... código ...
  }
  ```

#### Controle de Fluxo

- **Usar exit(0)**: Ao invés de `return 0;`, usar `exit(0);` no `main()`
- **Early return**: Preferir retorno antecipado em vez de `else` desnecessário
  ```cpp
  // ✅ Bom - Early return
  if (!file.open()) {
    cout << "Arquivo não existe" << endl;
    exit(0);
  }
  // ... código continua sem else ...
  ```

#### Padrões de Nomenclatura

- **Variáveis**: Usar `snake_case` (underline) para nomes de variáveis
- **Funções**: Usar `camelCase` para nomes de funções
- **Preferir inglês**: Dar preferência por variáveis em inglês
- **Nomes descritivos**: Criar variáveis fáceis de entender
- **Nomes comuns**:
  - `line`: Ao usar a função `getline()`
  - `arq` e `tmp`: Ao abrir um arquivo
  - `sep`: Vector para guardar saída do `explode()`

#### Declaração de Variáveis em Laços

- **Não criar dentro do laço**: Não declarar variáveis dentro de laços de repetição
  ```cpp
  // ❌ Ruim
  while (variavel) {
    string errado = "";
    // ...
  }
  
  for (i = 0; i < variavel.size(); i++) {
    string errado = "";
    // ...
  }
  
  // ✅ Bom
  string certo = "";
  while (variavel) {
    certo = "definir o valor dentro do laço não tem problema";
    // ...
  }
  
  string certo = "";
  for (i = 0; i < variavel.size(); i++) {
    certo = "definir o valor dentro do laço não tem problema";
    // ...
  }
  ```

#### Dicas de Código

1. **Parâmetros primeiro**: Sempre ler e processar parâmetros primeiro. Isso facilita a manutenção e deixa o código mais claro para outros desenvolvedores.

2. **Chaves na mesma linha**: Preferência por colocar `{` na mesma linha da condição para reduzir linhas e melhorar legibilidade:
   ```cpp
   if (true) {
     // ...
   } else {
     // ...
   }
   ```

3. **Early return**: Em condições de continuação do código, usar early return em vez de `else` desnecessário:
   ```cpp
   // ❌ Funciona, mas não é prático
   if (!file.open()) {
     cout << "Arquivo não existe" << endl;
   } else {
     // código continua aqui
   }
   
   // ✅ Melhor - early return
   if (!file.open()) {
     cout << "Arquivo não existe" << endl;
     exit(0);
   }
   // código continua aqui sem else
   ```

#### Git e Versionamento

- **Não commitar binários**: Nunca adicionar código compilado (binários) ao Git
- **Mensagens de commit descritivas**: A mensagem de commit deve descrever claramente o que foi feito, não ser muito curta

---

## 🌐 Padrões de Código PHP

### Estrutura de Arquivos

**Arquivos de Tela (ex: `wg_interfaces.php`):**
- HTML com formulários
- JavaScript/jQuery para interação
- jqGrid para grids paginadas
- Modais para criação/edição

**Arquivos de Backend (ex: `retrieve_wg_interfaces.php`):**
- Processam requisições AJAX
- Chamam programas C++ via `exec()` ou `system()`
- Retornam JSON

### Padrão de Requisições AJAX

```javascript
$.post('retrieve_wg_interfaces.php', {
  escolha: 'save',
  id: id,
  status: status,
  // ... outros campos
}, function(data) {
  // Processar resposta
});
```

### jqGrid

Grids usam jqGrid com:
- Paginação
- Ordenação
- Filtros de busca
- Persistência de preferências do usuário

**Configuração típica:**
```javascript
$("#grid").jqGrid({
  url: 'retrieve_wg_interfaces_table.php',
  datatype: "json",
  colNames: ['Status', 'Nome', ...],
  colModel: [...],
  // ...
});
```

---

## 🔐 Banco de Dados

### Conexão

- **MySQL customizado**: `/usr/local/mysql-S4.4.16.2/`
- **Bancos comuns**: `vpn`, `s4`, `antispam`, etc.

### Tabela WireGuard (Contexto Atual)

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


## 🚧 Contexto Atual: WireGuard

### Status do Desenvolvimento

**Implementado:**
- ✅ Interface PHP (`wg_interfaces.php`, `wg_peers.php`)
- ✅ Backend C++ básico (`wireguard.cpp`)
- ✅ Endpoints AJAX (`retrieve_wg_*.php`)

**Em Desenvolvimento/Correção:**
- 🔄 Migração de schema do banco de dados
- 🔄 Atualização de código C++ para novo schema
- 🔄 Ajustes na interface PHP para novos campos


### Arquivos Relacionados

**C++:**
- `S4/cpp/wireguard.cpp` - Backend principal

**PHP:**
- `S4/php/wg_interfaces.php` - Tela de interfaces
- `S4/php/wg_peers.php` - Tela de peers
- `S4/php/retrieve_wg_interfaces.php` - Endpoint AJAX interfaces
- `S4/php/retrieve_wg_interfaces_table.php` - Dados da grid interfaces
- `S4/php/retrieve_wg_peers.php` - Endpoint AJAX peers
- `S4/php/retrieve_wg_peers_table.php` - Dados da grid peers

**Documentação:**
- `S4/cpp/docs/bugs_crud.md`
---

## 📝 Convenções e Boas Práticas

### Nomenclatura

- **Programas C++**: Nomes descritivos (ex: `wireguard.cpp`, `openvpn.cpp`)
- **Arquivos PHP**: Prefixos por módulo (ex: `wg_*.php` para WireGuard)
- **Variáveis C++**: `snake_case` (underline) - preferir inglês, nomes descritivos
- **Funções C++**: `camelCase`
- **Variáveis JavaScript**: `camelCase`
- **Funções PHP**: `snake_case`

### Segurança

- **Sempre sanitizar** entradas do usuário antes de usar em SQL
- Usar `toDB()` para strings em queries SQL
- Validar todos os parâmetros de entrada
- Usar whitelist para colunas em ORDER BY
- Validar IDs numéricos antes de usar

### Validações Comuns

```cpp
// Porta (0-65535)
if (port < 0 || port > 65535) {
  erro("Porta inválida", 1, 1);
}

// Chave WireGuard (base64, até 88 caracteres)
if (key.length() > 88 || !isBase64(key)) {
  erro("Chave inválida", 1, 1);
}

// IP/CIDR
if (!isValidIP(ip) && !isValidCIDR(cidr)) {
  erro("IP ou CIDR inválido", 1, 1);
}
```

### Tratamento de Erros

- Sempre retornar JSON com status claro
- Usar funções `erro()` e `erro_db()` para erros
- Logar erros quando apropriado
- Fornecer mensagens de erro descritivas

### Performance

- Usar paginação em listagens grandes
- Limitar resultados por padrão
- Usar índices apropriados no banco
- Evitar queries N+1

---

## 🔄 Fluxo de Trabalho Típico

### Adicionar Nova Funcionalidade

1. **Planejar** - Documentar em `docs/`
2. **Backend C++** - Implementar operações necessárias
3. **Frontend PHP** - Criar/atualizar telas
4. **Endpoints AJAX** - Criar arquivos `retrieve_*.php`
5. **Testar** - Validar integração completa
6. **Documentar** - Atualizar este arquivo se necessário

### Modificar Funcionalidade Existente

1. **Analisar** - Entender código atual
2. **Documentar mudanças** - Atualizar `docs/` se necessário
3. **Implementar** - Fazer mudanças incrementais
4. **Testar** - Validar que não quebrou funcionalidades existentes
5. **Atualizar documentação** - Se necessário

---

## 📚 Referências

### Arquivos de Referência

- **`openvpn.cpp`**: Referência para implementação de backend similar
- **`wireguard.cpp`**: Implementação atual do WireGuard (em desenvolvimento)
- **`wg_interfaces.php`**: Referência para interface PHP com jqGrid


---

## ⚠️ Pontos de Atenção




### Permissões

- Verificar permissões corretas nos arquivos PHP
- Alguns podem estar usando permissões de OpenVPN (`p_openvpngrupos`)
- Criar permissões específicas para WireGuard se necessário

### Compatibilidade

- Manter compatibilidade com versões antigas quando possível
- Documentar breaking changes
- Considerar migração de dados quando necessário

---

**Última atualização:** 2025-11-29 
**Mantido por:** Equipe de Desenvolvimento S4

