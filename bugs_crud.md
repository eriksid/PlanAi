

# Planejamento de Desenvolvimento - WireGuard CRUD e Funcionalidades

Este documento apresenta o planejamento completo para revisão, correção e implementação das funcionalidades do módulo WireGuard no sistema S4.

---

## 📋 Visão Geral

O módulo WireGuard está em desenvolvimento e necessita de revisão completa do CRUD (Create, Read, Update, Delete), implementação de funcionalidades de gerenciamento de arquivos de configuração, monitoramento, auditoria, permissões e relatórios.

**Status Atual:**
- ✅ Estrutura básica do CRUD implementada (C++ e PHP)
- ✅ Schema do banco de dados atualizado e migrado
- ✅ Backend C++ com operações básicas (`-list_interfaces`, `-save_interface`, `-load_interface`, `-delete_interface`, `-list_peers`, `-save_peer`, `-load_peer`, `-delete_peer`)
- ✅ Interface PHP com grids jqGrid funcionais
- ✅ Endpoints AJAX implementados
- ✅ Bugs de edição corrigidos (IP interno, botão editar, endpoint)
- ⚠️ Funcionalidades avançadas pendentes (geração de arquivos, monitoramento, auditoria)
- ⚠️ Testes práticos de conexão não realizados

**Bugs Conhecidos e Corrigidos:**


Ver `S4/cpp/docs/TODO.md` para histórico completo de bugs e correções.

---

## 🎯 Fase 1: Revisão e Correção do CRUD

### 1.1 Cadastrar (Create)

**Objetivo:** Garantir que o cadastro de interfaces e peers funcione corretamente com todas as validações.

**Tarefas:**
1. **Revisar validações no backend C++ (`wireguard.cpp` - `-save_interface` e `-save_peer`)**
   - Verificar se todas as validações estão funcionando corretamente
   - Testar casos extremos (valores vazios, caracteres especiais, limites de tamanho)
   - Validar formato de chaves WireGuard (base64, 44 ou 88 caracteres)
   - Validar formato CIDR para redes
   - Validar IPs (IPv4 e IPv6)
   - Validar portas (1-65535)
   - Validar MTU (576-9000)

2. **Revisar validações no frontend PHP (`wg_interfaces.php` e `wg_peers.php`)**
   - Garantir que validações JavaScript estão sincronizadas com backend
   - Melhorar mensagens de erro para o usuário
   - Adicionar validação em tempo real nos campos

3. **Testar fluxo completo de cadastro**
   - Criar interface via interface web
   - Verificar se dados foram salvos corretamente no banco
   - Verificar se erros são tratados adequadamente

**Arquivos a revisar:**
- `S4/cpp/wireguard.cpp` (funções `-save_interface` e `-save_peer`)
- `S4/php/wg_interfaces.php` (função `saveWgInterface()`)
- `S4/php/wg_peers.php` (função de salvar peer)
- `S4/php/retrieve_wg_interfaces.php` (endpoint de salvar)
- `S4/php/retrieve_wg_peers.php` (endpoint de salvar)

**Padrões de Código C++ a Seguir:**
- Usar `toDB(mysql, value)` para sanitizar strings (não apenas `toDB(value)`)
- Usar `toDBInt(id_str)` para IDs numéricos
- Usar `throw_db_exception(mysql, mensagem, sql)` para erros de banco
- Usar `throw_exception(mensagem)` para erros gerais
- Usar `throw_parameter_exception(mensagem)` para erros de parâmetros
- Usar `success(json)` e `fail(exception)` para retornar JSON padronizado
- Sempre usar `mysql_free_result()` após consultas SELECT
- Sempre usar `exit(0)` no final do `main()`

**Critérios de sucesso:**
- ✅ Cadastro funciona sem erros
- ✅ Todas as validações funcionam corretamente
- ✅ Mensagens de erro são claras e úteis
- ✅ Dados são salvos corretamente no banco

---

### 1.2 Editar (Update)

**Objetivo:** Garantir que a edição de interfaces e peers funcione corretamente.

**Tarefas:**
1. **Revisar função de carregamento (`-load_interface` e `-load_peer`)**
   - Verificar se todos os campos são carregados corretamente
   - Verificar se campos opcionais são tratados adequadamente (NULL)
   - Testar com registros inexistentes

2. **Revisar função de atualização**
   - Garantir que atualização usa o mesmo código de cadastro (já implementado com UPSERT)
   - Verificar se campos não editáveis são preservados (ex: `iface_name` após criação)
   - Testar atualização parcial (apenas alguns campos)

3. **Melhorar interface de edição**
   - Garantir que modal de edição preenche todos os campos corretamente
   - Adicionar indicadores visuais de campos obrigatórios
   - Melhorar feedback visual durante edição

**Arquivos a revisar:**
- `S4/cpp/wireguard.cpp` (funções `-load_interface` e `-load_peer`)
- `S4/php/wg_interfaces.php` (função `openWgInterfaceDialog()`)
- `S4/php/wg_peers.php` (função de abrir dialog de edição)

**Padrões de Código C++ a Seguir:**
- Usar `toDBInt(id)` para validar e sanitizar ID antes da query
- Usar `throw_not_found_exception(mensagem)` quando registro não existe
- Sempre liberar resultado com `mysql_free_result(result)` após uso
- Usar `success(json)` para retornar dados carregados

**Critérios de sucesso:**
- ✅ Edição carrega todos os dados corretamente
- ✅ Atualização funciona sem erros
- ✅ Campos não editáveis são protegidos
- ✅ Interface é intuitiva e clara

---

### 1.3 Excluir (Delete)

**Objetivo:** Garantir que a exclusão funcione corretamente com todas as verificações necessárias.

**Tarefas:**
1. **Revisar função de exclusão (`-delete_interface` e `-delete_peer`)**
   - Verificar se exclusão em cascata funciona (peers são excluídos quando interface é excluída)
   - Adicionar verificação de dependências antes de excluir
   - Melhorar mensagens de confirmação
   - Implementar exclusão múltipla (já parcialmente implementado)

2. **Adicionar verificações de segurança**
   - Verificar se interface/peer está em uso antes de excluir
   - Adicionar confirmação explícita para exclusão
   - Logar exclusões para auditoria

3. **Testar casos especiais**
   - Excluir interface com peers associados
   - Excluir múltiplos registros
   - Tentar excluir registro inexistente

**Arquivos a revisar:**
- `S4/cpp/wireguard.cpp` (funções `-delete_interface` e `-delete_peer`)
- `S4/php/wg_interfaces.php` (funções `delInterface()` e `delVariosInterfaces()`)
- `S4/php/wg_peers.php` (funções de exclusão)

**Padrões de Código C++ a Seguir:**
- Usar `toDBInt(id)` para validar IDs antes de excluir
- Verificar dependências antes de excluir (ex: peers de uma interface)
- Usar `throw_db_exception(mysql, mensagem, sql)` para erros de exclusão
- Usar `success(json)` com mensagem de confirmação após exclusão bem-sucedida

**Critérios de sucesso:**
- ✅ Exclusão funciona corretamente
- ✅ Dependências são tratadas adequadamente
- ✅ Confirmações são claras
- ✅ Exclusões são logadas

---

### 1.4 Exportar Lista da Grid

**Objetivo:** Implementar funcionalidade de exportação para PDF e CSV.

**Tarefas:**
1. **Implementar exportação para PDF**
   - Revisar código existente em `wireguard.cpp` (já tem suporte parcial para PDF em `-list_interfaces` e `-list_peers`)
   - Adicionar opção de exportação no botão "Exportar" da toolbar
   - Criar função JavaScript para chamar exportação (similar a outros módulos)
   - Implementar endpoint PHP para processar exportação

2. **Implementar exportação para CSV**
   - Adicionar opção CSV no backend C++
   - Criar função para gerar CSV formatado
   - Implementar endpoint PHP para CSV

3. **Adicionar opções de exportação**
   - Permitir exportar apenas registros selecionados
   - Permitir exportar com filtros aplicados
   - Adicionar opções de orientação (retrato/paisagem) para PDF

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar suporte CSV em `-list_interfaces` e `-list_peers`)
- `S4/php/wg_interfaces.php` (adicionar função `exportarInterfaces()`)
- `S4/php/wg_peers.php` (adicionar função `exportarPeers()`)
- `S4/php/retrieve_wg_interfaces.php` (adicionar case `export`)
- `S4/php/retrieve_wg_peers.php` (adicionar case `export`)

**Padrões de Código C++ a Seguir:**
- Reutilizar lógica de `-list_interfaces` e `-list_peers` existente
- Adicionar parâmetro para formato de saída (PDF/CSV)
- Para CSV: gerar formato separado por vírgula com cabeçalhos
- Para PDF: usar função `gera_pdf()` existente (já implementada)
- Usar `toDB(mysql, value)` para valores em queries
- Seguir padrão de paginação e filtros existente

**Referências:**
- Ver implementação em `S4/php/lista_negra.php` (linhas 888-936)
- Ver implementação em `S4/php/lista_conteudo.php` (linhas 504-564)
- Ver código existente em `wireguard.cpp` para geração de PDF (linhas ~810-904)

**Critérios de sucesso:**
- ✅ Exportação PDF funciona
- ✅ Exportação CSV funciona
- ✅ Filtros e seleções são respeitados
- ✅ Arquivos são gerados corretamente

---

### 1.5 Filtros e Ordenações

**Objetivo:** Garantir que filtros e ordenações funcionem corretamente na grid.

**Tarefas:**
1. **Revisar filtros da grid**
   - Verificar se `filterToolbar` está funcionando corretamente
   - Testar filtros em todas as colunas
   - Verificar se filtros são persistidos (já implementado com `getJqGridParams`)

2. **Revisar ordenação**
   - Verificar se ordenação por colunas funciona
   - Testar ordenação ascendente/descendente
   - Verificar se whitelist de colunas está completa (já implementado)

3. **Melhorar experiência do usuário**
   - Adicionar indicadores visuais de coluna ordenada
   - Melhorar feedback durante filtragem
   - Adicionar filtros avançados se necessário

**Arquivos a revisar:**
- `S4/cpp/wireguard.cpp` (whitelist `ALLOWED_SORT_COLUMNS` e `ALLOWED_PEER_SORT_COLUMNS`)
- `S4/php/wg_interfaces.php` (configuração do jqGrid)
- `S4/php/wg_peers.php` (configuração do jqGrid)
- `S4/php/retrieve_wg_interfaces_table.php` (processamento de filtros)
- `S4/php/retrieve_wg_peers_table.php` (processamento de filtros)

**Critérios de sucesso:**
- ✅ Filtros funcionam em todas as colunas
- ✅ Ordenação funciona corretamente
- ✅ Preferências do usuário são salvas
- ✅ Performance é adequada mesmo com muitos registros

---

## 🔧 Fase 2: Criação de Arquivos WireGuard

### 2.1 Estrutura de Diretórios

**Objetivo:** Criar e gerenciar estrutura de diretórios para arquivos de configuração WireGuard.

**Tarefas:**
1. **Definir estrutura padrão**
   ```
   /usr/local/wireguard/etc/
   ├── wg0.conf          # Configuração da interface wg0
   ├── wg1.conf          # Configuração da interface wg1
   ├── variables.wg0     # Variáveis da interface wg0
   └── peers/
       ├── peer_1/        # Diretório do peer 1
       │   ├── private_peer_1.key
       │   └── public_peer_1.key
       └── peer_2/        # Diretório do peer 2
   ```

2. **Implementar funções de gerenciamento**
   - Criar função para gerar estrutura de diretórios
   - Criar função para verificar se diretórios existem
   - Implementar permissões corretas (umask 077)
   - Usar `chown` para manter dono/grupo original dos arquivos
   - Verificar existência de diretórios antes de criar arquivos

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar funções de gerenciamento de arquivos)

**Padrões de Código C++ a Seguir:**
- Verificar existência de diretórios com `opendir()` ou `access()`
- Criar diretórios com `mkdir()` e permissões apropriadas
- Usar `chown()` para manter dono/grupo após criar arquivos
- Sempre fechar arquivos com `close()` após uso
- Usar `fstream` em vez de `ifstream`/`ofstream` separados quando possível

**Critérios de sucesso:**
- ✅ Estrutura de diretórios é criada automaticamente
- ✅ Permissões são configuradas corretamente
- ✅ Diretórios são organizados logicamente

---

### 2.2 Geração de Arquivo de Interface

**Objetivo:** Gerar arquivo de configuração da interface WireGuard (`wgX.conf`).

**Tarefas:**
1. **Implementar função `-generate_interface_config`**
   - Ler dados da interface do banco
   - Gerar arquivo `[Interface]` com:
     - `PrivateKey`
     - `ListenPort`
     - `Address` (se necessário)
   - Salvar em `/usr/local/wireguard/etc/wgX.conf`

2. **Implementar função `-generate_interface_variables`**
   - Gerar arquivo `variables.wgX` com variáveis:
     - `WG_IFACE=wgX`
     - `MY_ENDPOINT_PORT=porta`
     - `WG_ADDRESS=ip/rede`

3. **Integrar com CRUD**
   - Chamar geração de arquivo após salvar interface
   - Atualizar arquivo quando interface for editada
   - Remover arquivo quando interface for excluída

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar funções de geração de arquivos)

**Exemplo de implementação:**
```cpp
void generateInterfaceConfig(MYSQL& mysql, const string& iface_name) {
  // Verificar se diretório existe
  string dir_path = "/usr/local/wireguard/etc";
  if (access(dir_path.c_str(), F_OK) != 0) {
    mkdir(dir_path.c_str(), 0700);
  }
  
  // Buscar dados da interface
  string sql = "SELECT private_key, iface_port FROM vpn.wireguard_interfaces WHERE iface_name = " + toDB(mysql, iface_name);
  // ... executar query ...
  
  // Gerar conteúdo do arquivo
  fstream arq;
  string file_path = dir_path + "/" + iface_name + ".conf";
  arq.open(file_path, ios::out);
  // ... escrever conteúdo ...
  arq.close();
  
  // Manter dono/grupo original (se arquivo já existia)
  // chown(file_path.c_str(), uid, gid);
}
```

**Padrões de Código C++ a Seguir:**
- Usar `toDB(mysql, value)` para valores em queries SQL
- Verificar existência de diretórios antes de criar arquivos
- Usar `fstream` para operações de arquivo
- Sempre fechar arquivos com `close()`
- Usar `chown()` para manter permissões originais

**Critérios de sucesso:**
- ✅ Arquivo de interface é gerado corretamente
- ✅ Arquivo é atualizado quando interface muda
- ✅ Arquivo é removido quando interface é excluída
- ✅ Permissões são mantidas corretamente

---

### 2.3 Geração de Arquivo de Peer

**Objetivo:** Gerar arquivos de configuração para peers (tanto no servidor quanto para download).

**Tarefas:**
1. **Implementar função `-generate_peer_config_server`**
   - Adicionar seção `[Peer]` no arquivo da interface
   - Incluir:
     - `PublicKey`
     - `Endpoint` (se fornecido)
     - `AllowedIPs`
     - `PersistentKeepalive` (se fornecido)

2. **Implementar função `-generate_peer_config_client`**
   - Gerar arquivo de configuração para o cliente
   - Incluir:
     - `[Interface]` com `PrivateKey` e `Address`
     - `[Peer]` com `PublicKey`, `Endpoint`, `AllowedIPs`, `PersistentKeepalive`

3. **Gerenciar chaves de peers**
   - Salvar chaves privadas/públicas em diretórios seguros
   - Gerar chaves automaticamente se não fornecidas
   - Manter chaves organizadas por peer

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar funções de geração de peer)

**Padrões de Código C++ a Seguir:**
- Usar `toDB(mysql, value)` para valores em queries SQL
- Usar `toDBInt(id)` para IDs numéricos
- Verificar existência de diretórios antes de criar arquivos
- Usar `fstream` para operações de arquivo
- Sempre fechar arquivos com `close()`
- Usar `chown()` para manter permissões originais

**Critérios de sucesso:**
- ✅ Configuração do peer é adicionada ao servidor
- ✅ Arquivo de configuração do cliente é gerado
- ✅ Chaves são gerenciadas com segurança
- ✅ Arquivos são atualizados quando peer muda

---

### 2.4 Aplicar Configurações no Sistema

**Objetivo:** Aplicar configurações WireGuard no sistema operacional.

**Tarefas:**
1. **Implementar função `-apply_interface_config`**
   - Executar `wg-quick up wgX` ou `wg-quick down wgX`
   - Verificar se interface foi criada/removida
   - Tratar erros adequadamente

2. **Implementar função `-reload_interface_config`**
   - Recarregar configuração sem derrubar interface
   - Usar `wg syncconf` quando possível

3. **Adicionar verificações de segurança**
   - Verificar se WireGuard está instalado
   - Verificar permissões antes de executar comandos
   - Validar configuração antes de aplicar

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar funções de aplicação)

**Padrões de Código C++ a Seguir:**
- Usar `getCmdOutput()` ou `system()` para executar comandos WireGuard
- Verificar se WireGuard está instalado antes de executar comandos
- Validar configuração antes de aplicar
- Usar caminhos completos: `/usr/local/wireguard/bin/wg` e `/usr/local/wireguard/bin/wg-quick`
- Tratar erros de execução adequadamente com `throw_exception()`

**Critérios de sucesso:**
- ✅ Configurações são aplicadas no sistema
- ✅ Erros são tratados adequadamente
- ✅ Interface WireGuard funciona corretamente

---

## 🧪 Fase 3: Testes Práticos de Conexão

### 3.1 Preparação do Ambiente

**Objetivo:** Preparar ambiente para testes práticos.

**Tarefas:**
1. **Configurar servidor S4**
   - Garantir que WireGuard está instalado
   - Verificar se portas estão abertas no firewall
   - Configurar roteamento se necessário

2. **Preparar clientes de teste**
   - Configurar pelo menos 2 clientes (pode ser VMs ou máquinas físicas)
   - Instalar WireGuard nos clientes
   - Preparar para importar configurações

**Critérios de sucesso:**
- ✅ Ambiente está preparado
- ✅ WireGuard está funcionando no servidor
- ✅ Clientes estão prontos para conectar

---

### 3.2 Teste de Conexão Básica

**Objetivo:** Testar conexão básica entre servidor e cliente.

**Tarefas:**
1. **Criar interface no S4**
   - Cadastrar interface via interface web
   - Verificar se arquivo foi gerado
   - Aplicar configuração no sistema

2. **Criar peer no S4**
   - Cadastrar peer via interface web
   - Baixar arquivo de configuração do cliente
   - Importar configuração no cliente

3. **Testar conectividade**
   - Verificar se cliente consegue conectar
   - Testar ping entre servidor e cliente
   - Verificar logs do WireGuard

**Critérios de sucesso:**
- ✅ Interface é criada corretamente
- ✅ Peer é criado corretamente
- ✅ Cliente consegue conectar
- ✅ Comunicação funciona

---

### 3.3 Teste de Funcionalidades Avançadas

**Objetivo:** Testar funcionalidades mais complexas.

**Tarefas:**
1. **Testar múltiplos peers**
   - Criar vários peers na mesma interface
   - Verificar se todos conseguem conectar
   - Testar comunicação entre peers

2. **Testar redes permitidas**
   - Configurar `allowed_networks` diferentes
   - Verificar se roteamento funciona corretamente
   - Testar acesso a redes específicas

3. **Testar edição e exclusão**
   - Editar peer e verificar se conexão continua funcionando
   - Excluir peer e verificar se arquivo é removido
   - Testar exclusão de interface com peers

**Critérios de sucesso:**
- ✅ Múltiplos peers funcionam
- ✅ Redes permitidas funcionam corretamente
- ✅ Edição e exclusão não quebram conexões ativas

---

## 📥 Fase 4: Baixar/Enviar Configuração

### 4.1 Baixar Configuração do Cliente

**Objetivo:** Permitir que usuário baixe arquivo de configuração do peer.

**Tarefas:**
1. **Implementar endpoint de download**
   - Criar função `-download_peer_config` no C++
   - Gerar arquivo de configuração do cliente
   - Retornar caminho do arquivo ou conteúdo

2. **Adicionar botão na interface**
   - Adicionar botão "Baixar Config" na grid de peers
   - Implementar função JavaScript para chamar download
   - Abrir arquivo em nova aba ou fazer download

3. **Formatar arquivo corretamente**
   - Garantir que formato está correto para WireGuard
   - Incluir todos os campos necessários
   - Adicionar comentários úteis

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar `-download_peer_config`)
- `S4/php/wg_peers.php` (adicionar botão e função de download)
- `S4/php/retrieve_wg_peers.php` (adicionar case de download)

**Padrões de Código C++ a Seguir:**
- Usar `toDBInt(id)` para validar ID do peer
- Usar `toDB(mysql, value)` para valores em queries SQL
- Gerar arquivo temporário ou retornar conteúdo diretamente
- Usar `success(json)` com caminho do arquivo ou conteúdo

**Critérios de sucesso:**
- ✅ Download funciona corretamente
- ✅ Arquivo está no formato correto
- ✅ Arquivo pode ser importado no cliente

---

### 4.2 Enviar/Importar Configuração

**Objetivo:** Permitir que usuário envie arquivo de configuração para criar peer.

**Tarefas:**
1. **Implementar upload de arquivo**
   - Criar endpoint para receber arquivo
   - Validar formato do arquivo
   - Extrair informações do arquivo

2. **Parser de configuração WireGuard**
   - Implementar parser para ler arquivo `.conf`
   - Extrair chaves, IPs, endpoints, etc.
   - Validar dados extraídos

3. **Criar peer a partir do arquivo**
   - Preencher formulário com dados extraídos
   - Permitir edição antes de salvar
   - Salvar peer normalmente

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar função de parse)
- `S4/php/wg_peers.php` (adicionar upload e parser)
- `S4/php/retrieve_wg_peers.php` (adicionar case de upload)

**Padrões de Código C++ a Seguir:**
- Validar formato do arquivo antes de fazer parse
- Usar `fstream` para ler arquivo de configuração
- Parsear seções `[Interface]` e `[Peer]` corretamente
- Validar dados extraídos antes de retornar
- Usar `throw_exception()` para erros de parse
- Sempre fechar arquivo com `close()` após leitura

**Critérios de sucesso:**
- ✅ Upload funciona corretamente
- ✅ Arquivo é parseado corretamente
- ✅ Peer é criado com dados do arquivo

---

## 📝 Fase 5: Auditoria de Ações

### 5.1 Estrutura de Auditoria

**Objetivo:** Implementar sistema de auditoria para rastrear ações dos usuários.

**Tarefas:**
1. **Definir tabela de auditoria**
   - Criar tabela `vpn.wireguard_audit` (se não existir)
   - Campos: `id`, `user`, `action`, `entity_type`, `entity_id`, `details`, `timestamp`

2. **Implementar funções de log**
   - Criar função `logWireGuardAction()` no C++
   - Registrar todas as ações (create, update, delete)
   - Incluir detalhes relevantes (quais campos mudaram)

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar função de log)
- SQL para criar tabela de auditoria (se necessário)

**Padrões de Código C++ a Seguir:**
- Usar `toDB(mysql, value)` para todos os valores em INSERT
- Usar prepared statements quando possível (já implementado em outras funções)
- Usar `NOW()` do MySQL para timestamp
- Não lançar exceções em função de log (logar erro mas não interromper operação principal)

**Critérios de sucesso:**
- ✅ Tabela de auditoria existe
- ✅ Todas as ações são logadas
- ✅ Logs contêm informações úteis

---

### 5.2 Interface de Visualização

**Objetivo:** Permitir que administradores visualizem logs de auditoria.

**Tarefas:**
1. **Criar tela de auditoria**
   - Criar `wg_audit.php` com grid de logs
   - Implementar filtros (por usuário, ação, data)
   - Adicionar exportação de logs

2. **Implementar endpoint de listagem**
   - Criar `-list_audit` no C++
   - Retornar logs com paginação
   - Permitir filtros

**Arquivos a criar:**
- `S4/php/wg_audit.php` (nova tela)
- `S4/php/retrieve_wg_audit.php` (endpoint)
- `S4/cpp/wireguard.cpp` (adicionar `-list_audit`)

**Padrões de Código C++ a Seguir:**
- Seguir padrão de `-list_interfaces` e `-list_peers` para paginação
- Usar whitelist de colunas para ORDER BY
- Usar `toDB(mysql, value)` para valores em WHERE
- Usar `mysql_free_result()` após consultas SELECT
- Retornar formato JSON do jqGrid

**Critérios de sucesso:**
- ✅ Logs são exibidos corretamente
- ✅ Filtros funcionam
- ✅ Interface é intuitiva

---

## 📊 Fase 6: Monitoramento

### 6.1 Coleta de Dados

**Objetivo:** Coletar dados de monitoramento das interfaces e peers.

**Tarefas:**
1. **Implementar função de coleta**
   - Usar `wg show` para obter estatísticas
   - Coletar dados de cada interface e peer
   - Armazenar em banco ou cache

2. **Dados a coletar:**
   - Status da interface (up/down)
   - Bytes enviados/recebidos por peer
   - Último handshake
   - Endpoint ativo
   - IPs permitidos

**Arquivos a criar/modificar:**
- `S4/cpp/wireguard.cpp` (adicionar `-get_status` ou similar)

**Padrões de Código C++ a Seguir:**
- Usar `getCmdOutput("/usr/local/wireguard/bin/wg show")` para obter status
- Parsear saída do comando `wg show`
- Usar `toDBInt(id)` para IDs em queries
- Usar `success(json)` para retornar dados de status

**Critérios de sucesso:**
- ✅ Dados são coletados corretamente
- ✅ Coleta é eficiente
- ✅ Dados são atualizados regularmente

---

### 6.2 Interface de Monitoramento

**Objetivo:** Exibir dados de monitoramento na interface web.

**Tarefas:**
1. **Criar tela de monitoramento**
   - Adicionar seção de monitoramento em `wg_interfaces.php`
   - Exibir status de cada interface
   - Exibir estatísticas de cada peer

2. **Adicionar atualização automática**
   - Usar AJAX para atualizar dados periodicamente
   - Adicionar indicadores visuais (verde/vermelho)
   - Mostrar gráficos se possível

**Arquivos a criar/modificar:**
- `S4/php/wg_interfaces.php` (adicionar seção de monitoramento)
- `S4/php/wg_peers.php` (adicionar estatísticas)
- `S4/php/retrieve_wg_status.php` (novo endpoint)

**Critérios de sucesso:**
- ✅ Dados são exibidos corretamente
- ✅ Atualização automática funciona
- ✅ Interface é clara e útil

---

## 🔐 Fase 7: Permissões

### 7.1 Definir Permissões

**Objetivo:** Implementar sistema de permissões para WireGuard.

**Tarefas:**
1. **Definir permissões necessárias**
   - `p_wireguard_interfaces` - Gerenciar interfaces
   - `p_wireguard_peers` - Gerenciar peers
   - `p_wireguard_view` - Apenas visualizar
   - `p_wireguard_audit` - Ver auditoria

2. **Atualizar código PHP**
   - Substituir `permission("p_openvpngrupos")` por permissões corretas
   - Adicionar verificações de permissão em todas as ações
   - Verificar permissões no backend C++ se necessário

**Arquivos a modificar:**
- `S4/php/wg_interfaces.php` (linha 9)
- `S4/php/wg_peers.php`
- `S4/php/retrieve_wg_*.php` (adicionar verificações)

**Critérios de sucesso:**
- ✅ Permissões são verificadas
- ✅ Usuários só veem o que têm permissão
- ✅ Ações são bloqueadas sem permissão

---

### 7.2 Integração com Sistema de Permissões

**Objetivo:** Integrar com sistema de permissões existente do S4.

**Tarefas:**
1. **Registrar permissões no sistema**
   - Adicionar permissões na tabela de permissões
   - Criar permissões padrão para perfis existentes

2. **Testar permissões**
   - Testar com diferentes perfis de usuário
   - Verificar se restrições funcionam
   - Garantir que admin tem acesso total

**Critérios de sucesso:**
- ✅ Permissões estão registradas
- ✅ Integração funciona corretamente
- ✅ Testes passam

---

## 📈 Fase 8: Relatórios

### 8.1 Relatórios Básicos

**Objetivo:** Implementar relatórios básicos de interfaces e peers.

**Tarefas:**
1. **Relatório de interfaces**
   - Listar todas as interfaces
   - Mostrar estatísticas (quantidade de peers, status)
   - Adicionar gráficos se possível

2. **Relatório de peers**
   - Listar todos os peers
   - Agrupar por interface
   - Mostrar estatísticas de uso

**Arquivos a criar:**
- `S4/php/wg_reports.php` (nova tela de relatórios)

**Padrões de Código C++ a Seguir:**
- Reutilizar funções de listagem existentes (`-list_interfaces`, `-list_peers`)
- Usar `toDB(mysql, value)` para valores em queries
- Usar `mysql_free_result()` após consultas SELECT
- Retornar dados agregados quando necessário (COUNT, SUM, etc.)

**Critérios de sucesso:**
- ✅ Relatórios são gerados corretamente
- ✅ Dados são precisos
- ✅ Interface é clara

---

### 8.2 Relatórios Avançados

**Objetivo:** Implementar relatórios mais detalhados.

**Tarefas:**
1. **Relatório de uso**
   - Mostrar tráfego por peer
   - Mostrar tráfego por interface
   - Gráficos de uso ao longo do tempo

2. **Relatório de conexões**
   - Mostrar peers conectados/desconectados
   - Tempo de conexão
   - Último handshake

3. **Exportação de relatórios**
   - Permitir exportar relatórios em PDF/CSV
   - Agendar relatórios periódicos (se necessário)

**Arquivos a criar/modificar:**
- `S4/php/wg_reports.php` (adicionar relatórios avançados)
- `S4/cpp/wireguard.cpp` (adicionar funções de relatório)

**Padrões de Código C++ a Seguir:**
- Criar funções específicas para relatórios (ex: `-report_usage`, `-report_connections`)
- Usar `getCmdOutput("/usr/local/wireguard/bin/wg show")` para dados de tráfego
- Agregar dados usando SQL (SUM, COUNT, GROUP BY)
- Usar `toDB(mysql, value)` para valores em queries
- Retornar dados em formato JSON estruturado para gráficos

**Critérios de sucesso:**
- ✅ Relatórios avançados funcionam
- ✅ Dados são úteis e precisos
- ✅ Exportação funciona

---

## 📅 Cronograma Sugerido

### Semana 1-2: Fase 1 (CRUD)
- Revisar e corrigir cadastro, edição, exclusão
- Implementar exportação
- Revisar filtros e ordenações

### Semana 3: Fase 2 (Arquivos)
- Implementar geração de arquivos
- Integrar com CRUD
- Testar criação de arquivos

### Semana 4: Fase 3 (Testes)
- Preparar ambiente
- Testar conexões básicas
- Testar funcionalidades avançadas

### Semana 5: Fase 4 (Download/Upload)
- Implementar download de configuração
- Implementar upload/importação
- Testar fluxo completo

### Semana 6: Fase 5 (Auditoria)
- Implementar sistema de auditoria
- Criar interface de visualização
- Testar logs

### Semana 7: Fase 6 (Monitoramento)
- Implementar coleta de dados
- Criar interface de monitoramento
- Testar atualização automática

### Semana 8: Fase 7 (Permissões)
- Definir e implementar permissões
- Integrar com sistema existente
- Testar com diferentes perfis

### Semana 9: Fase 8 (Relatórios)
- Implementar relatórios básicos
- Implementar relatórios avançados
- Testar exportação

---

## ✅ Checklist Final

Antes de considerar o projeto completo, verificar:

- [ ] CRUD funciona completamente (create, read, update, delete)
- [ ] Exportação funciona (PDF e CSV)
- [ ] Filtros e ordenações funcionam
- [ ] Arquivos são gerados corretamente
- [ ] Conexões funcionam na prática
- [ ] Download/upload de configuração funciona
- [ ] Auditoria está funcionando
- [ ] Monitoramento está funcionando
- [ ] Permissões estão implementadas
- [ ] Relatórios estão funcionando
- [ ] Documentação está atualizada
- [ ] Testes foram realizados
- [ ] Código foi revisado
- [ ] Não há erros conhecidos

---

## 📚 Referências e Notas

### Arquivos Importantes

**C++:**
- `S4/cpp/wireguard.cpp` - Backend principal

**PHP:**
- `S4/php/wg_interfaces.php` - Tela de interfaces
- `S4/php/wg_peers.php` - Tela de peers
- `S4/php/retrieve_wg_interfaces.php` - Endpoint interfaces
- `S4/php/retrieve_wg_peers.php` - Endpoint peers
- `S4/php/retrieve_wg_interfaces_table.php` - Dados da grid interfaces
- `S4/php/retrieve_wg_peers_table.php` - Dados da grid peers

**Documentação:**
- `S4/cpp/docs/AGENTS.md` - Contexto do projeto, padrões de código e estrutura
- `S4/cpp/docs/TODO.md` - Tarefas pendentes e bugs conhecidos/corrigidos
- `S4/cpp/docs/bugs_crud.md` - Este documento (planejamento de desenvolvimento)

**Código de Referência:**
- `S4/cpp/wireguard.cpp` - Implementação atual do backend WireGuard
- `S4/cpp/openvpn.cpp` - Referência para implementação similar (OpenVPN)

### Comandos WireGuard Úteis

**Caminhos Completos:**
- Binário principal: `/usr/local/wireguard/bin/wg`
- Script de gerenciamento: `/usr/local/wireguard/bin/wg-quick`
- Diretório de configuração: `/usr/local/wireguard/etc/`

**Comandos:**
```bash
# Gerar chaves
/usr/local/wireguard/bin/wg genkey
echo "chave_privada" | /usr/local/wireguard/bin/wg pubkey

# Gerenciar interfaces
/usr/local/wireguard/bin/wg-quick up wg0
/usr/local/wireguard/bin/wg-quick down wg0
/usr/local/wireguard/bin/wg syncconf wg0 /usr/local/wireguard/etc/wg0.conf

# Ver status
/usr/local/wireguard/bin/wg show
/usr/local/wireguard/bin/wg show wg0
```

**Nota:** Sempre usar caminhos completos nos comandos executados pelo código C++.

### Padrões do Projeto

**Funções de Banco de Dados:**
- Sempre usar `toDB(mysql, value)` para sanitizar strings em SQL (requer parâmetro `mysql`)
- Sempre usar `toDBInt(id_str)` para IDs numéricos (valida e retorna string ou "NULL")
- Sempre usar `database.tabela` em vez de apenas `tabela` em queries SQL
- Sempre usar comandos SQL em MAIÚSCULO (SELECT, INSERT, UPDATE, DELETE)
- Sempre listar colunas explicitamente (nunca usar `SELECT *`)
- Sempre liberar resultados MySQL com `mysql_free_result()` após consultas SELECT

**Tratamento de Erros:**
- Usar `throw_db_exception(mysql, mensagem, sql)` para erros de banco de dados
- Usar `throw_exception(mensagem)` para erros gerais
- Usar `throw_parameter_exception(mensagem)` para erros de parâmetros
- Usar `throw_not_found_exception(mensagem)` quando registro não existe
- Usar `success(json)` para retornar JSON de sucesso
- Usar `fail(exception)` para retornar JSON de erro

**Arquivos:**
- Sempre fechar arquivos após uso com `close()`
- Preferir `fstream` em vez de `ifstream`/`ofstream` separados
- Usar `chown()` para manter dono/grupo original dos arquivos
- Verificar existência de diretórios antes de criar arquivos

**Código:**
- Sempre validar entrada do usuário
- Sempre usar prepared statements quando possível (já implementado em `upsert_wireguard_interface` e `upsert_wireguard_peer`)
- Sempre usar `exit(0)` no final do `main()` (não `return 0`)
- Verificar `argc` antes de acessar `argv`
- Extrair `argv` para variáveis no início da função
- Não declarar variáveis dentro de laços de repetição

---

**Última atualização:** 2025-01-27  
**Status:** Planejamento revisado e alinhado com AGENTS.md  
**Próximos passos:** Iniciar Fase 1 - Revisão do CRUD

---

## 📌 Notas Importantes

### Padrões de Código C++ Obrigatórios

Este planejamento assume que todo código C++ seguirá os padrões definidos em `AGENTS.md`:

1. **Funções de Banco de Dados:**
   - `toDB(mysql, value)` - Requer conexão MySQL como primeiro parâmetro
   - `toDBInt(id_str)` - Para IDs numéricos
   - Sempre usar `database.tabela` em queries

2. **Tratamento de Exceções:**
   - `throw_db_exception(mysql, mensagem, sql)` - Erros de banco
   - `throw_exception(mensagem)` - Erros gerais
   - `throw_parameter_exception(mensagem)` - Erros de parâmetros
   - `success(json)` e `fail(exception)` - Retorno JSON padronizado

3. **Arquivos:**
   - Verificar existência de diretórios antes de criar arquivos
   - Usar `chown()` para manter permissões originais
   - Sempre fechar arquivos com `close()`

4. **Comandos WireGuard:**
   - Sempre usar caminhos completos: `/usr/local/wireguard/bin/wg`
   - Verificar instalação antes de executar comandos

### Bugs Conhecidos

Consulte `S4/cpp/docs/TODO.md` para lista completa de bugs conhecidos e corrigidos. Os principais bugs de edição já foram corrigidos:
- IP interno do peer não preenchia
- Botão editar não aparecia na grid
- Campo endpoint não preenchia corretamente

