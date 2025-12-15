# Planejamento de Testes - Telas WireGuard

Este documento contém um planejamento básico de testes para validar o funcionamento das telas de gerenciamento e monitoramento WireGuard no sistema S4.

---

## 📋 Visão Geral

**Objetivo:** Validar o funcionamento completo das telas de WireGuard, garantindo que todas as funcionalidades estejam operacionais e que a integração entre frontend (PHP) e backend (C++) esteja correta.

**Telas a Testar:**
1. **`lsvpn.php`** - Monitoramento de conexões VPN (OpenVPN, IPSec, WireGuard)
2. **`wg_interfaces.php`** - Gerenciamento de interfaces WireGuard
3. **`wg_peers.php`** - Gerenciamento de peers WireGuard

---

## 🔍 1. Testes da Tela `lsvpn.php` (Monitoramento VPN)

### 1.1. Pré-requisitos
- [ ] Ter pelo menos uma interface WireGuard cadastrada e ativa
- [ ] Ter pelo menos um peer WireGuard cadastrado
- [ ] Ter um peer conectado (ou simular conexão)

### 1.2. Testes de Visualização

#### Teste 1.2.1 - Exibição da Tabela WireGuard
- [ ] Acessar a tela `lsvpn.php`
- [ ] Verificar se a seção "Peers WireGuard conectados" é exibida
- [ ] Verificar se o contador de peers está correto no cabeçalho
- [ ] Verificar se as colunas estão visíveis:
  - [ ] Nome do Peer
  - [ ] IP
  - [ ] Interface
  - [ ] Último Handshake
  - [ ] Endpoint
  - [ ] Dados Enviados
  - [ ] Dados Recebidos
  - [ ] Desconectar

#### Teste 1.2.2 - Caso Sem Peers Conectados
- [ ] Desconectar todos os peers WireGuard
- [ ] Recarregar a página `lsvpn.php`
- [ ] Verificar se a mensagem "Nenhum peer conectado no momento." é exibida
- [ ] Verificar se o contador mostra "(Nenhum peer conectado)"

#### Teste 1.2.3 - Caso Com Peers Conectados
- [ ] Conectar pelo menos um peer WireGuard
- [ ] Recarregar a página `lsvpn.php`
- [ ] Verificar se os dados do peer são exibidos corretamente:
  - [ ] Nome do peer corresponde ao cadastrado
  - [ ] IP está no formato correto
  - [ ] Interface está correta
  - [ ] Último handshake está formatado
  - [ ] Endpoint está exibido (se houver)
  - [ ] Dados enviados/recebidos estão formatados

### 1.3. Testes de Funcionalidade

#### Teste 1.3.1 - Atualização Automática
- [ ] Verificar se o botão "Pausar monitoramento" está visível
- [ ] Verificar se a data/hora atual é exibida
- [ ] Aguardar 5 segundos e verificar se a página recarrega automaticamente
- [ ] Clicar em "Pausar monitoramento" e verificar se para de atualizar
- [ ] Clicar em "Ativar monitoramento" e verificar se volta a atualizar

#### Teste 1.3.2 - Ordenação da Tabela
- [ ] Clicar no cabeçalho "Nome do Peer" e verificar ordenação
- [ ] Clicar no cabeçalho "IP" e verificar ordenação
- [ ] Clicar no cabeçalho "Interface" e verificar ordenação
- [ ] Clicar no cabeçalho "Último Handshake" e verificar ordenação
- [ ] Verificar se a ordenação é mantida após atualização automática

#### Teste 1.3.3 - Desconectar Peer
- [ ] Clicar no botão "Desconectar" de um peer conectado
- [ ] Verificar se a confirmação é exibida
- [ ] Confirmar a desconexão
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se o peer desaparece da lista após recarregar
- [ ] Verificar se a URL é limpa (sem parâmetros `wg_error` ou `wg_success`)

#### Teste 1.3.4 - Tratamento de Erros
- [ ] Tentar desconectar um peer que não existe mais
- [ ] Verificar se mensagem de erro é exibida
- [ ] Verificar se a mensagem de erro é removida da URL após exibição

### 1.4. Testes de Integração

#### Teste 1.4.1 - Integração com Backend
- [ ] Verificar se o comando `sudo /var/S4/wireguard -get_monitoring_status` é executado
- [ ] Verificar se a resposta JSON é parseada corretamente
- [ ] Verificar se erros do backend são tratados adequadamente

---

## 🔧 2. Testes da Tela `wg_interfaces.php` (Gerenciamento de Interfaces)

### 2.1. Pré-requisitos
- [ ] Ter permissão de acesso à tela (`p_openvpngrupos`)
- [ ] Ter acesso ao banco de dados `vpn`

### 2.2. Testes de Visualização

#### Teste 2.2.1 - Exibição da Grid
- [ ] Acessar a tela `wg_interfaces.php`
- [ ] Verificar se a grid jqGrid é carregada
- [ ] Verificar se as colunas estão visíveis:
  - [ ] Status
  - [ ] Nome descritivo
  - [ ] Descrição
  - [ ] Interface
  - [ ] IP
  - [ ] Rede
  - [ ] Porta
  - [ ] Endpoint
  - [ ] DNS
  - [ ] MTU
  - [ ] Peers (contador)
  - [ ] Editar
  - [ ] Excluir

#### Teste 2.2.2 - Paginação e Ordenação
- [ ] Verificar se a paginação funciona (se houver mais de 50 registros)
- [ ] Testar ordenação por diferentes colunas
- [ ] Verificar se a ordenação é salva nas preferências do usuário

### 2.3. Testes de CRUD - Criar Interface

#### Teste 2.3.1 - Criar Nova Interface (Sucesso)
- [ ] Clicar no botão "Interface" (adicionar)
- [ ] Verificar se o modal é aberto com título "Novo interface Wireguard"
- [ ] Preencher os campos obrigatórios:
  - [ ] Status: Habilitado
  - [ ] Nome descritivo: "Interface Teste"
  - [ ] IP: "10.10.0.1"
  - [ ] Rede: "10.10.0.0/24"
  - [ ] Porta: "51820"
- [ ] Preencher campos opcionais:
  - [ ] Descrição: "Interface para testes"
  - [ ] Endpoint: "vpn.teste.com.br"
  - [ ] DNS: "1.1.1.1,8.8.8.8"
  - [ ] MTU: "1420"
- [ ] Verificar se chave privada é gerada automaticamente
- [ ] Clicar em "Salvar"
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se o modal é fechado
- [ ] Verificar se a grid é recarregada com o novo registro
- [ ] Verificar se a chave pública foi gerada automaticamente

#### Teste 2.3.2 - Criar Interface - Validações
- [ ] Tentar salvar sem preencher "Nome descritivo" → deve exibir erro
- [ ] Tentar salvar sem preencher "IP" → deve exibir erro
- [ ] Tentar salvar com IP inválido (ex: "999.999.999.999") → deve exibir erro
- [ ] Tentar salvar sem preencher "Rede" → deve exibir erro
- [ ] Tentar salvar com rede inválida (ex: "10.10.0.0/99") → deve exibir erro
- [ ] Tentar salvar sem preencher "Porta" → deve exibir erro
- [ ] Tentar salvar com porta inválida (ex: "99999") → deve exibir erro
- [ ] Tentar salvar com DNS inválido (ex: "dns.invalido") → deve exibir erro
- [ ] Tentar salvar com MTU inválido (ex: "100") → deve exibir erro

#### Teste 2.3.3 - Criar Interface - Campos Opcionais
- [ ] Criar interface sem preencher campos opcionais
- [ ] Verificar se a interface é criada com sucesso
- [ ] Verificar se campos opcionais ficam vazios no banco

### 2.4. Testes de CRUD - Editar Interface

#### Teste 2.4.1 - Editar Interface (Sucesso)
- [ ] Clicar no botão "Editar" de uma interface existente
- [ ] Verificar se o modal é aberto com título "Editar interface Wireguard"
- [ ] Verificar se todos os campos são preenchidos corretamente
- [ ] Verificar se a chave privada NÃO é exibida (campo oculto)
- [ ] Verificar se a chave pública é exibida (somente leitura)
- [ ] Modificar alguns campos (ex: descrição, DNS)
- [ ] Clicar em "Salvar"
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se as alterações são refletidas na grid

#### Teste 2.4.2 - Editar Interface - Validações
- [ ] Tentar salvar com nome descritivo maior que 50 caracteres → deve exibir erro
- [ ] Tentar salvar com descrição maior que 255 caracteres → deve exibir erro
- [ ] Tentar salvar com endpoint maior que 255 caracteres → deve exibir erro

### 2.5. Testes de CRUD - Excluir Interface

#### Teste 2.5.1 - Excluir Interface (Sucesso)
- [ ] Selecionar uma interface que não tenha peers associados
- [ ] Clicar no botão "Excluir"
- [ ] Verificar se a confirmação é exibida
- [ ] Confirmar a exclusão
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se a interface desaparece da grid

#### Teste 2.5.2 - Excluir Interface - Validações
- [ ] Tentar excluir interface que possui peers associados
- [ ] Verificar se mensagem de erro apropriada é exibida
- [ ] Verificar se a interface não é excluída

#### Teste 2.5.3 - Excluir Múltiplas Interfaces
- [ ] Selecionar múltiplas interfaces (checkbox)
- [ ] Clicar no botão "Excluir" da toolbar
- [ ] Verificar se confirmação mostra quantidade correta
- [ ] Confirmar exclusão
- [ ] Verificar se todas as interfaces selecionadas são excluídas


### 2.6. Testes de Funcionalidades Adicionais

#### Teste 2.6.1 - Exportar Interfaces
- [ ] Clicar no botão "Exportar"
- [ ] Selecionar formato PDF
- [ ] Verificar se arquivo é gerado e baixado
- [ ] Repetir com formato CSV
- [ ] Verificar se dados exportados estão corretos

#### Teste 2.6.2 - Ajustar Colunas
- [ ] Clicar no botão "Ajustar colunas"
- [ ] Ocultar algumas colunas
- [ ] Verificar se colunas são ocultadas na grid
- [ ] Recarregar a página
- [ ] Verificar se preferências são mantidas

#### Teste 2.6.3 - Filtros da Grid
- [ ] Usar filtros da toolbar para buscar por nome
- [ ] Usar filtros para buscar por IP
- [ ] Verificar se resultados são filtrados corretamente

---

## 👥 3. Testes da Tela `wg_peers.php` (Gerenciamento de Peers)

### 3.1. Pré-requisitos
- [ ] Ter pelo menos uma interface WireGuard cadastrada
- [ ] Ter acesso ao banco de dados `vpn`

### 3.2. Testes de Visualização

#### Teste 3.2.1 - Exibição da Grid
- [ ] Acessar a tela `wg_peers.php`
- [ ] Verificar se a grid jqGrid é carregada
- [ ] Verificar se as colunas estão visíveis:
  - [ ] Status
  - [ ] Interface
  - [ ] Nome da conexão
  - [ ] IP interno
  - [ ] Endpoint
  - [ ] Redes permitidas
  - [ ] Keepalive
  - [ ] Editar
  - [ ] Excluir

### 3.3. Testes de CRUD - Criar Peer

#### Teste 3.3.1 - Criar Novo Peer (Sucesso)
- [ ] Clicar no botão "Peer" (adicionar)
- [ ] Verificar se o modal é aberto com título "Novo peer Wireguard"
- [ ] Verificar se chaves pública/privada são geradas automaticamente
- [ ] Preencher os campos obrigatórios:
  - [ ] Status: Habilitado
  - [ ] Interface: Selecionar uma interface existente
  - [ ] Nome da conexão: "Peer Teste"
  - [ ] IP interno: Selecionar um IP disponível da lista
- [ ] Preencher campos opcionais:
  - [ ] Endpoint: "vpn.teste.com.br:51820"
  - [ ] Redes permitidas: "192.168.1.0/24,10.0.0.0/8"
  - [ ] Keepalive: "25"
  - [ ] Preshared key: (deixar vazio ou preencher)
- [ ] Verificar se o endpoint padrão da interface é preenchido automaticamente
- [ ] Clicar em "Salvar"
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se a grid é recarregada com o novo peer

#### Teste 3.3.2 - Criar Peer - Validações
- [ ] Tentar salvar sem selecionar interface → deve exibir erro
- [ ] Tentar salvar sem preencher "Nome da conexão" → deve exibir erro
- [ ] Tentar salvar sem selecionar "IP interno" → deve exibir erro
- [ ] Tentar salvar com nome maior que 100 caracteres → deve exibir erro
- [ ] Tentar salvar com endpoint maior que 255 caracteres → deve exibir erro
- [ ] Tentar salvar com rede permitida inválida (ex: "192.168.1") → deve exibir erro
- [ ] Tentar salvar com keepalive inválido (ex: "99999") → deve exibir erro
- [ ] Tentar salvar com preshared key inválida → deve exibir erro

#### Teste 3.3.3 - Criar Peer - Carregamento de IPs Disponíveis
- [ ] Abrir modal de novo peer
- [ ] Selecionar uma interface
- [ ] Verificar se o select de "IP interno" é populado com IPs disponíveis
- [ ] Verificar se IPs já em uso não aparecem na lista
- [ ] Verificar se o endpoint padrão da interface é preenchido

### 3.4. Testes de CRUD - Editar Peer

#### Teste 3.4.1 - Editar Peer (Sucesso)
- [ ] Clicar no botão "Editar" de um peer existente
- [ ] Verificar se o modal é aberto com título "Editar peer Wireguard"
- [ ] Verificar se todos os campos são preenchidos corretamente
- [ ] Verificar se o IP atual do peer aparece na lista (mesmo que já esteja em uso)
- [ ] Modificar alguns campos (ex: nome, endpoint, redes permitidas)
- [ ] Clicar em "Salvar"
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se as alterações são refletidas na grid

#### Teste 3.4.2 - Editar Peer - Mudança de Interface
- [ ] Editar um peer existente
- [ ] Alterar a interface selecionada
- [ ] Verificar se a lista de IPs disponíveis é atualizada
- [ ] Verificar se o endpoint padrão é atualizado
- [ ] Salvar e verificar se a alteração foi aplicada

### 3.5. Testes de CRUD - Excluir Peer

#### Teste 3.5.1 - Excluir Peer (Sucesso)
- [ ] Selecionar um peer
- [ ] Clicar no botão "Excluir"
- [ ] Verificar se a confirmação é exibida
- [ ] Confirmar a exclusão
- [ ] Verificar se mensagem de sucesso é exibida
- [ ] Verificar se o peer desaparece da grid

#### Teste 3.5.2 - Excluir Múltiplos Peers
- [ ] Selecionar múltiplos peers (checkbox)
- [ ] Clicar no botão "Excluir" da toolbar
- [ ] Verificar se confirmação mostra quantidade correta
- [ ] Confirmar exclusão
- [ ] Verificar se todos os peers selecionados são excluídos

### 3.6. Testes de Funcionalidades Adicionais

#### Teste 3.6.1 - Exportar Peers
- [ ] Clicar no botão "Exportar"
- [ ] Selecionar formato PDF
- [ ] Verificar se arquivo é gerado e baixado
- [ ] Repetir com formato CSV
- [ ] Verificar se dados exportados estão corretos

#### Teste 3.6.2 - Filtros e Busca
- [ ] Usar filtros da toolbar para buscar por nome
- [ ] Usar filtros para buscar por interface
- [ ] Usar filtros para buscar por IP
- [ ] Verificar se resultados são filtrados corretamente

---

## 🔗 4. Testes de Integração Entre Telas

### 4.1. Fluxo Completo - Criar Interface e Peer

#### Teste 4.1.1 - Fluxo End-to-End
- [ ] Criar uma nova interface em `wg_interfaces.php`
- [ ] Verificar se a interface aparece na lista de `wg_peers.php` ao criar peer
- [ ] Criar um peer associado à interface criada
- [ ] Verificar se o peer aparece em `lsvpn.php` quando conectado
- [ ] Verificar se o contador de peers na interface é atualizado

### 4.2. Testes de Dependências

#### Teste 4.2.1 - Excluir Interface com Peers
- [ ] Criar uma interface
- [ ] Criar um peer associado à interface
- [ ] Tentar excluir a interface
- [ ] Verificar se há validação impedindo exclusão
- [ ] Excluir o peer primeiro
- [ ] Tentar excluir a interface novamente
- [ ] Verificar se agora é possível excluir

---

## ✅ 5. Checklist de Validação Final (Resumo Rápido)

### 5.1. Funcionalidades Básicas
- [ ] Todas as telas carregam sem erros
- [ ] Todas as grids exibem dados corretamente
- [ ] CRUD completo funciona em todas as telas
- [ ] Validações de formulário funcionam
- [ ] Mensagens de erro/sucesso são exibidas corretamente

### 5.2. Integração Backend
- [ ] Comandos C++ são executados corretamente
- [ ] Respostas JSON são parseadas corretamente
- [ ] Erros do backend são tratados adequadamente
- [ ] Dados são persistidos no banco corretamente

### 5.3. Interface do Usuário
- [ ] Modais abrem e fecham corretamente
- [ ] Formulários são resetados após salvar
- [ ] Grids são atualizadas após operações
- [ ] Filtros e ordenação funcionam
- [ ] Exportação funciona

### 5.4. Segurança
- [ ] Dados são sanitizados (prevenção XSS)
- [ ] Validações são feitas no frontend e backend
- [ ] Permissões são verificadas
- [ ] SQL injection não é possível

---

## 📊 6. Resultados dos Testes

**Data do Teste:** _______________

**Testador:** _______________

**Ambiente:** _______________

### 6.1. Resultados - Tela `lsvpn.php`

#### Teste 1.2.1 - Exibição da Tabela WireGuard
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.2.2 - Caso Sem Peers Conectados
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.2.3 - Caso Com Peers Conectados
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.3.1 - Atualização Automática
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.3.2 - Ordenação da Tabela
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.3.3 - Desconectar Peer
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.3.4 - Tratamento de Erros
**Resultado:** _______________
**Observações:** _______________

#### Teste 1.4.1 - Integração com Backend
**Resultado:** _______________
**Observações:** _______________

---

### 6.2. Resultados - Tela `wg_interfaces.php`

#### Teste 2.2.1 - Exibição da Grid
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.2.2 - Paginação e Ordenação
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.3.1 - Criar Nova Interface (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.3.2 - Criar Interface - Validações
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.3.3 - Criar Interface - Campos Opcionais
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.4.1 - Editar Interface (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.4.2 - Editar Interface - Validações
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.5.1 - Excluir Interface (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.5.2 - Excluir Interface - Validações
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.5.3 - Excluir Múltiplas Interfaces
**Resultado:**  erro delVariosInterfaces is not
**Observações:** _______________

#### Teste 2.6.1 - Exportar Interfaces
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.6.2 - Ajustar Colunas
**Resultado:** _______________
**Observações:** _______________

#### Teste 2.6.3 - Filtros da Grid
**Resultado:** _______________
**Observações:** _______________

---

### 6.3. Resultados - Tela `wg_peers.php`

#### Teste 3.2.1 - Exibição da Grid
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.3.1 - Criar Novo Peer (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.3.2 - Criar Peer - Validações
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.3.3 - Criar Peer - Carregamento de IPs Disponíveis
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.4.1 - Editar Peer (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.4.2 - Editar Peer - Mudança de Interface
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.5.1 - Excluir Peer (Sucesso)
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.5.2 - Excluir Múltiplos Peers
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.6.1 - Exportar Peers
**Resultado:** _______________
**Observações:** _______________

#### Teste 3.6.2 - Filtros e Busca
**Resultado:** _______________
**Observações:** _______________

---

### 6.4. Resultados - Testes de Integração

#### Teste 4.1.1 - Fluxo End-to-End
**Resultado:** _______________
**Observações:** _______________

#### Teste 4.2.1 - Excluir Interface com Peers
**Resultado:** _______________
**Observações:** _______________

---

## 📝 7. Observações e Problemas Encontrados

### Problemas Encontrados:

1. **Problema:** _______________
   - **Tela:** _______________
   - **Severidade:** [ ] Crítico [ ] Alto [ ] Médio [ ] Baixo
   - **Descrição:** _______________

2. **Problema:** _______________
   - **Tela:** _______________
   - **Severidade:** [ ] Crítico [ ] Alto [ ] Médio [ ] Baixo
   - **Descrição:** _______________

---

## 🎯 8. Próximos Passos Após Testes

Após completar os testes, documentar:
- [ ] Bugs encontrados e suas correções
- [ ] Melhorias sugeridas
- [ ] Casos de teste adicionais necessários
- [ ] Documentação de uso atualizada

---

**Última atualização:** 2025-01-XX
**Versão:** 1.0

