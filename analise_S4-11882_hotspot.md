# Análise da Tarefa S4-11882 - Autenticação Google no Hotspot

## 📋 Informações Gerais da Tarefa

**Chave:** S4-11882  
**Tipo:** Feature  
**Status Atual:** Aguardando Correção (Bug Teste)  
**Prioridade:** Medium  
**Versão:** 4.18.9 (Release: 2025-11-26)

**Epic Pai:** [S4-8758](https://desenv.atlassian.net/browse/S4-8758) - "Navegação > Hotspot: Possibilitar outras formas de autenticação (Google, Microsoft, CPF)"

**Responsável:** Gustavo Satig  
**Criador:** Daniel de Ataide

**Tempo Estimado:** 1 semana (144.000 segundos / 40 horas)  
**Tempo Gasto:** 1 semana, 4 dias, 5 horas e 30 minutos (279.000 segundos / 77,5 horas)

**Link Jira:** <https://desenv.atlassian.net/browse/S4-11882>

---

## 📝 Descrição

"[Hotspot] Disponibilizamos a autenticação com Google, tornando o acesso mais simples e rápido para os usuários."

**Descrição Técnica:** Hotspot: Disponibilizar autenticação com Google

---

## 🔗 Links Relacionados

### Documentação Confluence

- **Página Principal:** <https://desenv.atlassian.net/wiki/spaces/S4/pages/1834549256/S4-11882+-+Documenta+o+esbo+o+e+planejamento+de+tarefas>
- **ID da Página:** 1834549256
- **Título:** "S4-11882 - Documentação, esboço e planejamento de tarefas"

### Bugs Relacionados

- **S4-12661:** [Navegação: Problemas identificados em relação a autenticação do Google](https://desenv.atlassian.net/browse/S4-12661)
  - Status: Em Desenvolvimento
  - Tipo: Bug Teste
  - Relator: Mauro Henrique Gaertner
  - Responsável: Gustavo Satig
  - Tempo Estimado: 3 horas
  - Progresso: 100% (338.400 segundos gastos)

---

## 📦 Subtarefas

### S4-12538 - Análise da tarefa

- **Tipo:** Pesquisa e Elaboração
- **Status:** ✅ Entregue
- **Descrição:** Sub-tarefa para itens relacionados a pesquisa e elaboração

### S4-12661 - Navegação: Problemas identificados em relação a autenticação do Google

- **Tipo:** Bug Teste
- **Status:** 🔄 Em Desenvolvimento
- **Descrição:** Problemas identificados pela equipe de testes (QA)
- **Relator:** Mauro Henrique Gaertner
- **Responsável:** Gustavo Satig

---

## 📊 Status das Etapas (Conforme Documentação Confluence)

### ✅ Etapa 1 – Migração de versão (dados e estruturas iniciais) - CONCLUÍDA

| Item | Descrição | Status |
|------|-----------|--------|
| 1 | Criar redundância de links `onlys4_hotspot_auth` para autenticação Google | ✅ DONE |
| 2 | Criar lista de domínios `onlys4_hotspot_auth_domains` e popular com os 12 domínios fixos do Google | ✅ DONE |
| 3 | Criar listas `onlys4_hotspot_auth_ip_allowed` e `onlys4_hotspot_auth_mac_allowed` (tipos ip e mac, inicialmente vazias) | ✅ DONE |
| 4 | Criar regra `onlys4_hotspot_auth_rule` com `src_iface = eth+`, vinculando listas de IP/MAC e associando ao balance | ✅ DONE |
| 5 | Criar políticas `onlys4_hotspot_auth_policy_app` e `onlys4_hotspot_auth_policy_fqdn`, apontando para a regra acima | ✅ DONE |
| 6 | Adicionar migração do arquivo `lighttpd.conf` utilizando PHP 8.3 para o hotspot | ✅ DONE |

### ✅ Etapa 2 – Liberação dinâmica e autenticação Google - CONCLUÍDA

| Item | Descrição | Status |
|------|-----------|--------|
| 1 | Implementar operação no `proxy.cpp` que recebe payload (`auth_name`, `mode_source`, `client_ip`/`client_mac`, `ttl_seconds`) | ✅ DONE |
| 2 | Após inserir, executar reload do NGFW (`/usr/local/sbin/s4-ngfw-reload`) | ✅ DONE |
| 3 | Agendar remoção após `ttl_seconds` (300 s padrão) | ✅ DONE |
| 4 | Garantir que a lista suporte múltiplos usuários (simultâneos, sem sobrescrever entradas existentes) | ✅ DONE |

### ✅ Etapa 3 – Ajustes de Front (URIs Google/Facebook na replicação) - CONCLUÍDA

| Item | Descrição | Status |
|------|-----------|--------|
| 1 | Ajustar layout para duas linhas por servidor (em vez de colunas) → linha 1 com _Google URI_ e linha 2 com _Facebook URI_ | ✅ DONE |
| 2 | Corrigir comportamento do "Mostrar mais" para expandir/ocultar de fato | ✅ DONE |
| 3 | Respeitar seleção de provedores (Google/Facebook) e renderizar linhas somente para os ativos | ✅ DONE |
| 4 | Ajustar telas que utilizam da **tabela de redundancia, listas e regras de firewall** pra não mostrar tudo oq for **onlys4_hotspot_auth** | ✅ DONE |

### 🔄 Etapa 4 – Ajustes de documentações (Google App e login no Hotspot) - EM ANDAMENTO

| Item | Descrição | Status |
|------|-----------|--------|
| 1 | Renovar documentações antigas para o novo formato de criação do app Google e autenticação Hotspot | 🔄 DOING |
| 2 | Atualizar passo a passo do Google Cloud Console (consent screen, scopes, OAuth client ID e redirect URIs novas) | 🔄 DOING |
| 3 | Incluir exemplos com 2 linhas por servidor (Google / Facebook) e botão de copiar | 🔄 DOING |
| 4 | Atualizar prints das telas e adicionar changelog de versão na doc | 🔄 DOING |

### ⏳ Etapa 5 – Comentário na tarefa de finalização - PENDENTE

| Item | Descrição | Status |
|------|-----------|--------|
| 1 | Adicionar comentário com informações para QA | ⏳ TODO |

---

## 🏗️ Arquitetura e Componentes Técnicos

### Estruturas de Firewall Criadas

#### Balance (Redundância)

- **Nome:** `onlys4_hotspot_auth`
- **Função:** Garante saída correta por múltiplos links (ex.: eth1, eth2)
- **Comando de criação:**

  ```bash
  /var/S4/check_balance -cad_table onlys4_hotspot_auth redu "@@eth1$$1@@eth2$$2@@" - false
  ```

#### Listas de Firewall

- **Lista de domínios:** `onlys4_hotspot_auth_domains` (tipo: domain)
- **Lista de IPs:** `onlys4_hotspot_auth_ip_allowed` (tipo: ip, inicialmente vazia)
- **Lista de MACs:** `onlys4_hotspot_auth_mac_allowed` (tipo: mac, inicialmente vazia)

**Score padrão das listas:**

```json
{"dnat":0,"forward":0,"redirect":0,"snat":0,"suggestions":[]}
```

**Domínios fixos do Google (12 domínios):**

- accounts.google.com
- accounts.google.com.br
- oauth2.googleapis.com
- openidconnect.googleapis.com
- www.googleapis.com
- www.gstatic.com
- ssl.gstatic.com
- fonts.googleapis.com
- fonts.gstatic.com
- lh3.googleusercontent.com
- apis.google.com
- content.googleapis.com

#### Regra SNAT

- **Nome:** `onlys4_hotspot_auth_rule`
- **Interface/Origem:** `eth+` (pega todas as eth* de saída)
- **Vinculações:** Listas de IP/MAC e balance `onlys4_hotspot_auth`

#### Políticas SNAT

- **Política Aplicação:** `onlys4_hotspot_auth_policy_app`
- **Política FQDN:** `onlys4_hotspot_auth_policy_fqdn`
- **Aplicação:** `always`
- **Vinculações:** Lista de domínios e balance `onlys4_hotspot_auth`

### Backend - Liberação Dinâmica

**Arquivo:** `proxy.cpp`

**Fluxo de Autenticação:**

1. Backend recebe payload JSON:

   ```json
   {
     "auth_name": "hotsatig",
     "mode_source": "ip",
     "client_ip": "172.30.45.10",
     "client_mac": "d0:94:66:9e:0e:61",
     "ttl_seconds": 300
   }
   ```

2. Backend insere IP ou MAC na lista correspondente:
   - `onlys4_hotspot_auth_ip_allowed` ou
   - `onlys4_hotspot_auth_mac_allowed`

3. Executa reload do NGFW:

   ```bash
   /usr/local/sbin/s4-ngfw-reload
   ```

4. Após TTL (default 300s), remove entrada e executa novo reload

5. Suporta múltiplos usuários simultâneos

### Migração PHP 8.3

**Arquivo:** `lighttpd.conf`

**Configuração:**

```conf
server.modules += ("mod_fastcgi")
fastcgi.server = ( ".php" =>
  ( "localhost" =>
    ( "socket" => "/var/run/php/php8.3-fpm.sock",
      "broken-scriptfilename" => "enable"
    )
  )
)
```

**Observação:** O arquivo de configuração do lighttpd foi modificado para ser apenas para o hotspot. Pode haver algum problema de PHP no hotspot e no wizard.

---

## 🐛 Problemas Identificados

### Bug S4-12661

- **Título:** Navegação: Problemas identificados em relação a autenticação do Google
- **Status:** Em Desenvolvimento
- **Impacto:** A tarefa principal está aguardando a correção deste bug para prosseguir

---

## ⚠️ Pendências

### Itens Pendentes (conforme referência original)

- Item 29
- Item 35
- Item 37
- Item 39

_Nota: Estes itens precisam ser verificados na documentação completa do Confluence para identificar o que são especificamente._

### Pendências Ativas

1. **Etapa 4:** Finalização da documentação (em andamento)
2. **Etapa 5:** Adicionar comentário para QA (pendente)
3. **Bug S4-12661:** Correção de problemas de autenticação Google (em desenvolvimento)

---

## 📈 Métricas e Progresso

- **Progresso Geral:** ~80% (4 de 5 etapas concluídas)
- **Tempo Gasto vs Estimado:** 193% (279.000s / 144.000s)
  - Indica complexidade maior que o esperado ou escopo expandido durante o desenvolvimento

---

## 🔍 Observações Importantes

1. **Status Atual:** A tarefa está no status "Aguardando Correção (Bug Teste)", indicando que há um bloqueio relacionado ao bug S4-12661 que precisa ser resolvido antes de prosseguir.

2. **Reaproveitamento de Código:** A documentação menciona que foi feito reaproveitamento de botões e estilizações existentes, evitando recriar do zero.

3. **Ocultação de Estruturas:** As telas que utilizam tabelas de redundância, listas e regras de firewall foram ajustadas para não exibir estruturas relacionadas a `onlys4_hotspot_auth`, mantendo a interface limpa.

4. **Documentação Técnica Completa:** A documentação no Confluence contém comandos SQL detalhados, exemplos de payloads JSON e instruções completas de configuração.

---

## 📚 Referências e Materiais

- [Google API PHP Client v2.18.4](https://github.com/googleapis/google-api-php-client/releases/tag/v2.18.4)
- [Google API PHP Client Repository](https://github.com/googleapis/google-api-php-client)
- [Documentação Confluence - Link direto](https://desenv.atlassian.net/wiki/x/kwGRAg)

---

_Última atualização: 2025-12-11_  
_Informações coletadas via MCP Atlassian_

---

## Pendencias:

### Análise item 29:
Problema: HTACCESS QUE FICA INCORRETO
Status: Feito
Solução:
Adicionar --exclude='.htaccess' no rsync /var/S4/wizard_page/hotspot

  ```
  logd "-- Executando procedimentos para reescrever as autenticacoes hotspot..."
  execute "/var/S4/proxy -rsync_hotspot '0'"
  ```

* chama a função:
  rewriteHotspotAuthConfEnd
  mas nesse ponto não faz rsync

### Análise item 35
problema:
Ao editar a instancia colocar como Interceptação SSL ativa, na pagina do Hotspot ao clicar na opção de logar com o Google nao vai e nao redireciona para a tela de escolher a conta.
Status: Andamento

O ID do cliente está sempre disponível na guia Clientes da plataforma de autenticação do Google.
656093994992-t9cfqj77uqmes2rrfj6btjaikab089rt.apps.googleusercontent.com
