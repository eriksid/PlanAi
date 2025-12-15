# Documentação da Biblioteca libs4.js

## Visão Geral

A biblioteca `libs4.js` é uma biblioteca JavaScript modular que fornece funções utilitárias para o sistema S4. Ela encapsula funcionalidades relacionadas a diálogos, validações, cálculos de risco de segurança, manipulação de dados e muito mais.

A biblioteca utiliza o padrão **IIFE (Immediately Invoked Function Expression)** para criar um escopo isolado e evitar poluição do namespace global.

---

## Estrutura da Biblioteca

A biblioteca é organizada em um módulo auto-executável que retorna um objeto contendo todas as funções públicas:

```javascript
var libs4 = (function () {
  // Variáveis privadas
  // Funções privadas
  // Funções públicas
  
  return {
    // API pública
  };
})();
```

---

## Categorias de Funções

### 1. Diálogos e Modais

#### `load_start(id, msg)`
Exibe um diálogo modal de carregamento com uma mensagem e um indicador visual (gif animado).

**Parâmetros:**

- `id` (string): ID do elemento HTML que será usado como container do diálogo
- `msg` (string): Mensagem a ser exibida durante o carregamento

**Características:**

- Modal bloqueante (não permite interação com o resto da página)
- Sem botão de fechar (closeOnEscape: false)
- Largura adaptativa baseada no elemento
- Altura mínima: 160px, máxima: 450px

**Exemplo de uso:**
```javascript
libs4.load_start("div_carregamento", "Processando dados...");
```

---

#### `load_stop(id)`
Fecha o diálogo de carregamento.

**Parâmetros:**

- `id` (string): ID do elemento HTML do diálogo

**Exemplo de uso:**
```javascript
libs4.load_stop("div_carregamento");
```

---

#### `sucesso(id, msg, opt, funcok)`
Exibe um diálogo de sucesso com mensagem e botão "Ok".

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `msg` (string): Mensagem de sucesso
- `opt` (object, opcional): Opções adicionais (ex: `{minHeight: 200}`)
- `funcok` (function, opcional): Função callback executada ao clicar em "Ok"

**Exemplo de uso:**
```javascript
libs4.sucesso("div_resultado", "Operação realizada com sucesso!", {}, function() {
  console.log("Usuário confirmou");
});
```

---

#### `informacao(id, msg, opt, funcok)`
Similar ao `sucesso`, mas com ícone de informação.

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `msg` (string): Mensagem informativa
- `opt` (object, opcional): Opções adicionais
- `funcok` (function, opcional): Callback ao clicar em "Ok"

---

#### `alerta(id, msg, tit, btname, funcOk, lineInit, funcClose, closeOnEscape, customWidth)`
Exibe um diálogo de alerta/atenção.

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `msg` (string): Mensagem de alerta
- `tit` (string, opcional): Título do diálogo (padrão: "Atenção")
- `btname` (string, opcional): Texto do botão (padrão: "OK")
- `funcOk` (function, opcional): Callback ao clicar no botão
- `lineInit` (boolean, opcional): Adiciona linha inicial (padrão: true)
- `funcClose` (function, opcional): Callback ao fechar o diálogo
- `closeOnEscape` (boolean, opcional): Permite fechar com ESC (padrão: true)
- `customWidth` (number, opcional): Largura customizada

**Exemplo de uso:**
```javascript
libs4.alerta("div_aviso", "Esta ação não pode ser desfeita!", 
  "Atenção", "Entendi", function() {
    // Ação ao confirmar
  });
```

---

#### `confirmation(id, msg, tit, btok, fok, btcancel, width, fcancel)`
Cria um diálogo de confirmação com dois botões (OK e Cancelar).

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `msg` (string): Mensagem de confirmação
- `tit` (string): Título do diálogo
- `btok` (string): Texto do botão de confirmação
- `fok` (function): Callback ao confirmar
- `btcancel` (string): Texto do botão de cancelamento
- `width` (number, opcional): Largura customizada
- `fcancel` (function, opcional): Callback ao cancelar

**Exemplo de uso:**
```javascript
libs4.confirmation("div_confirm", "Deseja realmente excluir?", 
  "Confirmação", "Sim", function() {
    // Ação de exclusão
  }, "Não");
```

---

#### `showDialog(id, msg, tit, buttons, btcancel, width)`
Cria um diálogo com múltiplos botões customizados.

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `msg` (string): Mensagem do diálogo
- `tit` (string): Título
- `buttons` (array): Array de objetos `{text: "Texto", func: function() {}}`
- `btcancel` (string): Texto do botão de cancelamento
- `width` (number, opcional): Largura customizada

**Exemplo de uso:**
```javascript
libs4.showDialog("div_dialog", "Escolha uma opção:", "Opções",
  [
    {text: "Opção 1", func: function() { console.log("1"); }},
    {text: "Opção 2", func: function() { console.log("2"); }}
  ],
  "Cancelar"
);
```

---

#### `open_dialog(id, tit, btname)`
Abre um diálogo simples a partir de um elemento HTML existente.

**Parâmetros:**

- `id` (string): ID do elemento HTML
- `tit` (string, opcional): Título do diálogo
- `btname` (string, opcional): Texto do botão (padrão: "OK")

---

#### `confirmModal(modalId, msgWarn, showCheckbox, width)`
Cria um modal de confirmação com opção de checkbox para confirmar ciência.

**Parâmetros:**

- `modalId` (string): ID do elemento HTML
- `msgWarn` (string): Mensagem de aviso
- `showCheckbox` (boolean): Exibe checkbox de confirmação
- `width` (string, opcional): Largura (padrão: 'auto')

**Retorna:** Promise que resolve com `true` se confirmado, `false` se cancelado.

**Exemplo de uso:**
```javascript
libs4.confirmModal("#modal", "Esta ação é irreversível!", true)
  .then(function(confirmed) {
    if (confirmed) {
      // Prosseguir
    }
  });
```

---

### 2. Validações

#### `checkIp(par)`
Valida se uma string é um endereço IP válido (formato IPv4).

**Parâmetros:**

- `par` (string): String a ser validada

**Retorna:** `true` se válido, `false` caso contrário

**Exemplo:**
```javascript
libs4.checkIp("192.168.1.1"); // true
libs4.checkIp("999.999.999.999"); // false
```

---

#### `checkIpWithMask(par)`
Valida se uma string é um endereço IP com máscara CIDR válido (ex: 192.168.1.0/24).

**Parâmetros:**

- `par` (string): String no formato IP/Máscara

**Retorna:** `true` se válido, `false` caso contrário

**Validações:**

- Formato: `xxx.xxx.xxx.xxx/yy` onde yy está entre 0 e 32
- Cada octeto do IP deve estar entre 0 e 255

---

#### `checkMail(m)`
Valida se uma string é um endereço de e-mail válido.

**Parâmetros:**

- `m` (string): String a ser validada

**Retorna:** `true` se válido, `false` caso contrário

**Regex utilizado:** `/^([a-zA-Z0-9_\.\-\+])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/`

---

#### `checkMailOrDomain(md)`
Valida e-mail ou domínio (permite `@dominio` ou `*@dominio`).

**Parâmetros:**

- `md` (string): E-mail ou domínio a validar

**Retorna:** `true` se válido, `false` caso contrário

---

#### `checkDomain(domain)`
Valida se uma string é um domínio válido.

**Parâmetros:**

- `domain` (string): Domínio a validar

**Retorna:** `true` se válido, `false` caso contrário

**Regex utilizado:** `/^(?:[a-z0-9](?:[a-z0-9-]{0,61}[a-z0-9])?\.)+[a-z]{2,}$/i`

---

#### `checkDomainOrIp(domain)`
Valida se é um IP válido ou um domínio válido.

**Parâmetros:**

- `domain` (string): IP ou domínio a validar

**Retorna:** `true` se válido, `false` caso contrário

---

#### `checkPassword(p)`
Valida se uma senha não contém caracteres proibidos.

**Caracteres proibidos:** `'`, `&`, `+`, `\`, `´`, `ª`, `º`, `°`

**Parâmetros:**

- `p` (string): Senha a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `checkPasswordMail(p)`
Similar ao `checkPassword`, mas permite o caractere `+`.

**Parâmetros:**

- `p` (string): Senha a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `checkPasswordASCII(p)`
Valida se todos os caracteres da senha estão no range ASCII válido (32 a 254).

**Parâmetros:**

- `p` (string): Senha a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `checkPasswordFtp(p)`
Valida senha para usuários FTP (proíbe: `'`, `&`, `+`, `"`, `´`, `ª`, `º`, `°`).

**Parâmetros:**

- `p` (string): Senha a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `checkTexto(p)`
Valida texto genérico (proíbe: `'`, `&`, `+`, `\`, `´`, `ª`, `º`, `°`, `` ` ``).

**Parâmetros:**

- `p` (string): Texto a validar

**Retorna:** `true` se válido, `false` caso contrário

---

#### `isSafeText(str)`
Valida texto seguro para evitar injeção (usado em interfaces de rede).

**Caracteres proibidos:** `'`, `&`, `+`, `\`, `´`, `ª`, `º`, `°`, `<`, `>`, `;`, `"`

**Parâmetros:**

- `str` (string): Texto a validar

**Retorna:** `true` se seguro, `false` caso contrário

---

#### `isSafeStr(p)`
Valida se a string contém apenas caracteres ASCII (0x00 a 0x7F).

**Parâmetros:**

- `p` (string): String a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `validatePorts(portas, label)`
Valida uma lista de portas (suporta ranges como `100:200` e múltiplas portas separadas por vírgula).

**Parâmetros:**

- `portas` (string): Lista de portas (ex: "80,443,100:200")
- `label` (string): Label para mensagens de erro

**Retorna:** String vazia se válido, string com erros separados por `|` caso contrário

**Validações:**

- Máximo de 15 portas por regra
- Cada porta deve estar entre 1 e 65535
- Em ranges, a porta inicial deve ser menor que a final

**Exemplo:**
```javascript
var erros = libs4.validatePorts("80,443,100:200", "Portas de destino");
if (erros) {
  console.log("Erros:", erros.split("|"));
}
```

---

#### `validateGroupName(groupName)`
Valida o nome de um grupo.

**Parâmetros:**

- `groupName` (string): Nome do grupo a validar

**Retorna:** String vazia se válido, string com erros em HTML caso contrário

**Regras:**

- Não pode estar vazio
- Máximo de 64 caracteres
- Não pode ser "default" (case-insensitive)
- Não pode iniciar ou terminar com ponto (`.`)
- Apenas letras, números, hífen, ponto e underline são permitidos

---

### 3. Cálculo de Risco de Segurança

O sistema de cálculo de risco avalia a vulnerabilidade de regras de firewall baseado em vários fatores.

#### `calcSecurityRisk(comesFrom, params, protocols, ports, ipOriginRanges, ipDestinyRanges, defaultScores)`
Calcula o índice de risco de segurança para uma regra de firewall.

**Parâmetros:**

- `comesFrom` (string): Tipo de regra (`'snat_policy'`, `'dnat'`, `'forward'`, `'redirect'`)
- `params` (object): Parâmetros da regra
- `protocols` (object): Pesos de risco por protocolo
- `ports` (object): Pesos de risco por porta
- `ipOriginRanges` (object): Pesos de risco por range de IP de origem
- `ipDestinyRanges` (object): Pesos de risco por range de IP de destino
- `defaultScores` (object): Valores padrão de risco

**Retorna:** Objeto com:

- `value` (number): Valor do risco (0-100+)
- `status` (string): Status do cálculo (`'completed'`, `'pending'`, `'unable_to_calc'`)
- `suggestions` (array): Array de sugestões para melhorar a segurança

**Níveis de Risco:**

- **Baixo:** < 45
- **Médio:** 45-70
- **Alto:** 71-100
- **Crítico:** > 100

---

#### `calcPolicySnatRisk(params, protocols, ports, ipOriginRanges, ipDestinyRanges, defaultScores)`
Calcula o risco para políticas SNAT (Firewall Saída).

**Fórmula:** `PESO_RISCO_ORIGEM + PESO_MAC_ADDRESS + PESO_RISCO_DESTINO + PONTUACAO_PROTOCOLO + PONTUACAO_PORTA`

**Fatores considerados:**

- IP de origem (quanto mais amplo, maior o risco)
- Presença de MAC address (ausência adiciona 4 pontos)
- IP de destino (quanto mais amplo, maior o risco)
- Protocolo utilizado
- Porta de destino

---

#### `calcDnatRisk(params, protocols, ports, ipOriginRanges, defaultScores)`
Calcula o risco para regras DNAT (Firewall Entrada).

**Fórmula:** `PESO_RISCO_ORIGEM + PESO_PROTOCOLO + PONTUACAO_PORTA_ENTRADA + PONTUAÇÃO_PORTA_SAIDA`

**Fatores considerados:**
- IPs de origem
- Protocolo
- Porta de entrada
- Porta de saída (ausência adiciona 90 pontos se não for ICMP/GRE/ESP)

---

#### `calcForwardRisk(params, protocols, ports, ipOriginRanges, ipDestinyRanges)`
Calcula o risco para regras de Controle entre Redes (Forward).

**Fórmula:** `PESO_RISCO_ORIGEM + PESO_MAC_ADDRESS + PESO_RISCO_DESTINO + PONTUACAO_PROTOCOLO + PONTUACAO_PORTA`

---

#### `calcRedirectRisk(params, protocols, ports, ipOriginRanges, ipDestinyRanges)`
Calcula o risco para regras de Redirecionamento.

**Fórmula:** `PESO_RISCO_ORIGEM + PESO_PROTOCOLO + PESO_RISCO_DESTINO + PONTUAÇÃO_PORTA_SAIDA`

---

#### `validateRisk(modal, permission, risk, multipleItems)`
Valida o risco calculado contra as permissões do usuário e solicita confirmação se necessário.

**Parâmetros:**

- `modal` (object): Gerenciador de modais
- `permission` (string): Nível de permissão do usuário (`"0"`, `"1"`, `"2"`, `"3"`)
- `risk` (object): Objeto de risco calculado
- `multipleItems` (boolean): Se está validando múltiplas regras

**Retorna:** Promise que resolve com objeto:

- `code: 'success'` se aprovado
- `code: 'confirmation_refused'` se cancelado

**Limites por permissão:**

- `"0"`: Limite 45 (permite até médio)
- `"1"`: Limite 71 (permite até alto)
- `"2"`: Limite 100 (permite até crítico)
- `"3"`: Sem limite (0)

---

#### `makeSecurityRiskBar(footerElem, label, customBarId)`
Cria uma barra visual de indicação de risco.

**Parâmetros:**

- `footerElem` (string/jQuery): Elemento onde a barra será inserida
- `label` (string): Label da barra
- `customBarId` (string, opcional): ID customizado para a barra

**Exemplo:**
```javascript
libs4.makeSecurityRiskBar("#footer", "Nível de Risco", "risk-bar-1");
```

---

#### `changeSecurityRiskBar(customBarId, riskResult)`
Atualiza a barra de risco com o resultado do cálculo.

**Parâmetros:**

- `customBarId` (string): ID da barra
- `riskResult` (object): Objeto com `value`, `status`, `suggestions`

**Cores e larguras:**

- Baixo: Verde (#16b912), 5%
- Médio: Amarelo (#D3A900), 30%
- Alto: Laranja (#FF7800), 60%
- Crítico: Vermelho (#CC0000), 100%

---

#### `formatterCellSecurityRisk(val, options, rowData)`
Formatador para células de risco em grids (jqGrid).

**Parâmetros:**

- `val` (object/string): Valor do risco (pode ser JSON string)
- `options` (object): Opções do jqGrid
- `rowData` (object): Dados da linha

**Retorna:** HTML formatado para exibição na célula

---

#### `getRiskDesc(value)`
Retorna a descrição HTML formatada do nível de risco.

**Parâmetros:**

- `value` (number): Valor do risco

**Retorna:** String HTML com descrição colorida

---

#### `getRiskLimit(perm)`
Retorna o limite de risco baseado na permissão.

**Parâmetros:**

- `perm` (string): Nível de permissão

**Retorna:** Número representando o limite

---

### 4. Utilitários de Interface

#### `enableTipScrolling(td, table)`
Habilita scroll em tooltips quando o conteúdo é muito grande.

**Parâmetros:**

- `td` (jQuery/HTMLElement): Elemento TD que terá o tooltip
- `table` (string): Seletor do tbody da grid

**Funcionalidades:**

- Scroll vertical com roda do mouse
- Scroll horizontal com Shift + roda do mouse
- Altura máxima baseada na altura da janela

---

#### `gridOnResize(comFiltro, comGbox)`
Ajusta o tamanho de uma grid (jqGrid) ao redimensionar a janela.

**Parâmetros:**

- `comFiltro` (boolean): Se há accordion de filtro acima da grid
- `comGbox` (boolean): Se precisa ajustar o objeto gbox_table externo

**Funcionalidade:**
- Usa debounce (30ms) para evitar múltiplas chamadas
- Calcula novo width e height baseado na janela
- Considera altura do `#info_slave` se existir

---

#### `toast(message, type, position, show, hideAfter)`
Exibe notificações toast (estilo Material Design).

**Parâmetros:**

- `message` (string): Mensagem a exibir
- `type` (string): Tipo (`'success'`, `'info'`, `'warning'`, `'error'`) - padrão: `'success'`
- `position` (string): Posição (`'topright'`, `'bottomleft'`, etc.) - padrão: `'topright'`
- `show` (boolean): Se deve exibir (padrão: `true`)
- `hideAfter` (number): Tempo em ms para esconder (padrão: 5000, -1 para não esconder)

**Exemplo:**
```javascript
libs4.toast("Operação realizada com sucesso!", "success", "topright", true, 3000);
```

---

#### `info_box(id, message, type, width)`
Cria uma caixa de informação.

**Parâmetros:**

- `id` (string/jQuery): ID ou seletor do elemento
- `message` (string): Mensagem a exibir
- `type` (string): Tipo (`'info'`, `'attention'`) - padrão: `'attention'`
- `width` (string): Largura (ex: `'50%'` ou `'500px'`)

---

#### `addInfoSlave(id)`
Adiciona uma caixa informativa sobre modo Filial (replicação ativa).

**Parâmetros:**

- `id` (string/jQuery): ID ou seletor do elemento

---

### 5. Manipulação de Dados

#### `clearSpaces(str)`
Remove todos os espaços, tabs, form feeds e line feeds de uma string.

**Parâmetros:**

- `str` (string): String a processar

**Retorna:** String sem espaços

---

#### `unique(array)`
Remove entradas duplicadas de um array.

**Parâmetros:**

- `array` (array): Array a processar

**Retorna:** Array sem duplicatas

**Exemplo:**
```javascript
libs4.unique([1, 2, 2, 3, 3, 3]); // [1, 2, 3]
```

---

#### `isArray(p)`
Verifica se um valor é um array.

**Parâmetros:**

- `p` (any): Valor a verificar

**Retorna:** `true` se for array, `false` caso contrário

---

#### `isJson(p)`
Verifica se uma string é um JSON válido.

**Parâmetros:**

- `p` (string): String a verificar

**Retorna:** 
- `0` se válido
- `1` se erro de parsing
- `2` se não é um objeto

---

#### `isValidJSON(str)`
Valida JSON usando `JSON.parse`.

**Parâmetros:**

- `str` (string): String JSON a validar

**Retorna:** `true` se válido, `false` caso contrário

---

#### `isJsonEqual(jsonA, jsonB)`
Compara dois objetos JSON para verificar se são iguais (deep comparison).

**Parâmetros:**

- `jsonA` (object): Primeiro objeto
- `jsonB` (object): Segundo objeto

**Retorna:** `true` se iguais, `false` caso contrário

---

#### `wordwrap(str, width, brk, cut)`
Quebra uma string em linhas de tamanho máximo.

**Parâmetros:**

- `str` (string): String a quebrar
- `width` (number): Largura máxima (padrão: 75)
- `brk` (string): Caractere de quebra (padrão: `'\n'`)
- `cut` (boolean): Se deve cortar palavras (padrão: `false`)

**Retorna:** String quebrada

---

#### `formatMessage(msg)`
Remove duplicatas de `xS4x_LF` (marcador de quebra de linha) de uma mensagem.

**Parâmetros:**

- `msg` (string): Mensagem a formatar

**Retorna:** Mensagem formatada

---

### 6. Codificação e Segurança

#### `encodeHtml(value)`
Codifica HTML para evitar interpretação pelo navegador (previne XSS).

**Parâmetros:**

- `value` (string): Valor a codificar

**Retorna:** String codificada (ex: `<` vira `&lt;`)

**Exemplo:**
```javascript
libs4.encodeHtml("<script>alert('xss')</script>");
// Retorna: "&lt;script&gt;alert('xss')&lt;/script&gt;"
```

---

#### `decodeHtml(value)`
Decodifica HTML para permitir interpretação pelo navegador.

**Parâmetros:**

- `value` (string): Valor a decodificar

**Retorna:** String decodificada

---

#### `encodeURIString(str)`
Extensão do `encodeURIComponent` que também codifica caracteres adicionais.

**Parâmetros:**

- `str` (string): String a codificar

**Retorna:** String codificada

**Caracteres adicionais codificados:** `!`, `'`, `(`, `)`, `*`, `_`, `~`

---

### 7. Senhas

#### `gen_pass(element)`
Gera uma senha aleatória de 16 caracteres e a insere em um campo.

**Parâmetros:**

- `element` (string): ID do elemento input

**Características:**

- 16 caracteres
- Letras maiúsculas, minúsculas e números
- 3 caracteres especiais aleatórios: `*!@#$?<>[]{}()`
- Foca no campo após gerar

**Exemplo:**
```javascript
libs4.gen_pass("input_senha");
```

---

#### `show_pass(element, icon)`
Alterna a visibilidade de uma senha (mostra/oculta).

**Parâmetros:**

- `element` (string): ID do campo de senha
- `icon` (string): ID do ícone (usa Font Awesome: `fa-eye` / `fa-eye-slash`)

**Funcionalidade:**
- Alterna entre `type="password"` e `type="text"`
- Alterna ícone entre `fa-eye` e `fa-eye-slash`

---

#### `getInfoPasswordStrength(id, strength)`
Retorna HTML com informações sobre força de senha.

**Parâmetros:**

- `id` (string): ID do campo de senha (opcional)
- `strength` (string/number): Nível de força (`"0"` a `"3"` ou `"FALSE"`)

**Níveis:**

- `"0"`: Fraca
- `"1"`: Média
- `"2"`: Forte
- `"3"`: Muito Forte
- `"FALSE"`: Sem informação

**Retorna:** String HTML com informações

---

#### `infoStrenghtPass(inputIdPass, idDivBarPass, idDivTextStrenghtPass, valueStrengthPass)`
Configura indicador de força de senha em um campo.

**Parâmetros:**

- `inputIdPass` (string): ID do campo de senha
- `idDivBarPass` (string): ID da div da barra de força
- `idDivTextStrenghtPass` (string): ID da div do texto de força
- `valueStrengthPass` (string): Valor inicial da força

**Funcionalidade:**
- Usa plugin `pstrength()` para calcular força
- Mostra/oculta barra e texto conforme digitação
- Atualiza tooltip com informações

---

### 8. Validação de Domínios e IPs

#### `fixDomains(addressList)`
Corrige e normaliza uma lista de endereços (IPs e domínios).

**Parâmetros:**

- `addressList` (array): Array de strings com IPs/domínios

**Retorna:** Objeto com:
- `site_list` (object): Objeto com `ip` e `domain` (strings separadas por vírgula)
- `site_list_txt` (string): Lista formatada em texto (separada por `\n`)
- `site_list_rows` (object): Array de objetos `{ip: "..."}` ou `{domain: "..."}`
- `auto_adjust_sites` (array): Array de objetos `{old: "...", new: "..."}` com ajustes feitos

**Funcionalidades:**

- Remove protocolos (http://, https://, ftp://)
- Remove caracteres especiais inválidos
- Corrige múltiplos pontos/hífens
- Remove sub-redes conflitantes (se uma sub-rede está contida em outra)
- Valida IPs e domínios
- Adiciona IP padrão `240.0.0.0/4` se não houver IPs
- Adiciona domínio padrão `.impossible-to-match.local` se não houver domínios

**Exemplo:**
```javascript
var resultado = libs4.fixDomains(["192.168.1.1", "example.com", "http://test.com"]);
// resultado.site_list.ip = "192.168.1.1"
// resultado.site_list.domain = "example.com,test.com"
// resultado.auto_adjust_sites = [{old: "http://test.com", new: "test.com"}]
```

---

#### `valideDomains(listaEnderecos)`
Valida uma lista de endereços e identifica inválidos e conflitantes.

**Parâmetros:**

- `listaEnderecos` (array): Array de strings com IPs/domínios

**Retorna:** Objeto com:
- `invalidos` (array): Array de endereços inválidos
- `validos` (array): Array de endereços válidos
- `json` (object): Estrutura JSON com IPs e domínios
- `conflitantes` (object): Objeto com domínios conflitantes (chave: domínio pai, valor: array de filhos)
- `validados` (object): Objeto com `ip` e `domain` (strings separadas por vírgula)
- `listValidadosAndDomainsOk` (array): Lista de válidos sem conflitantes
- `jsonDomains` (object): JSON apenas com domínios válidos
- `jsonDomainsValidados` (object): Objeto com IPs e domínios válidos

**Validações:**

- IPs válidos (formato e range)
- Domínios válidos (regex e caracteres permitidos)
- Identifica domínios conflitantes (ex: `example.com` e `.example.com`)

---

#### `ValidateAndRemoveDomains(field, listDomainsAndIps, saveList)`
Valida e remove domínios/IPs inválidos ou conflitantes, exibindo diálogos apropriados.

**Parâmetros:**

- `field` (string): ID do campo textarea com a lista
- `listDomainsAndIps` (array): Array de strings com IPs/domínios
- `saveList` (function): Função callback para salvar a lista validada

**Funcionalidade:**
- Valida usando `valideDomains()`
- Se houver inválidos, exibe diálogo perguntando se deseja remover
- Se houver conflitantes, exibe diálogo com opções para corrigir
- Atualiza o campo e chama `saveList()` com os dados validados

---

#### `isDomainRegistered(domain, registeredDomains)`
Verifica se um domínio está registrado (é subdomínio ou domínio pai de algum na lista).

**Parâmetros:**

- `domain` (string): Domínio a verificar
- `registeredDomains` (array): Lista de domínios registrados

**Retorna:** `true` se registrado, `false` caso contrário

---

#### `getParentDomain(domain, registeredDomains)`
Retorna o domínio pai de um domínio na lista de registrados.

**Parâmetros:**

- `domain` (string): Domínio a verificar
- `registeredDomains` (array): Lista de domínios registrados

**Retorna:** String com o domínio pai ou string vazia

---

### 9. Formatação de Grids (jqGrid)

#### `defaultFormatterCell(cell_value, options, row_object, encode_html)`
Formatador padrão para células de grid que centraliza valores vazios ou com `-`.

**Parâmetros:**

- `cell_value` (any): Valor da célula
- `options` (object): Opções do jqGrid
- `row_object` (object): Objeto da linha
- `encode_html` (boolean, opcional): Se deve codificar HTML (padrão: `true`)

**Retorna:** Valor formatado (substitui vazio/null por `-` e centraliza)

---

#### `defaultFormatterTitleCell(row_id, value, row_data, encode_html)`
Formatador de título (tooltip) para células de grid.

**Parâmetros:**

- `row_id` (string): ID da linha
- `value` (any): Valor da célula
- `row_data` (object): Dados da linha
- `encode_html` (boolean, opcional): Se deve codificar HTML (padrão: `true`)

**Retorna:** String com atributo `title` formatado

---

#### `extendColsNotPresentUserConfig(init_col, order, col_model)`
Adiciona índices de colunas novas que não existem na configuração do usuário.

**Parâmetros:**

- `init_col` (number): Índice inicial (padrão: 0)
- `order` (array): Array de índices de ordem atual
- `col_model` (array): Array do modelo de colunas

**Retorna:** Array de ordem atualizado

**Uso:** Quando novas colunas são adicionadas à grid, garante que apareçam na ordem padrão.

---

### 10. Utilitários Diversos

#### `checkUrl(url)`
Valida se uma string é uma URL válida.

**Parâmetros:**

- `url` (string): URL a validar

**Retorna:** `true` se válida, `false` caso contrário

---

#### `isInt(str)`
Verifica se uma string representa um número inteiro.

**Parâmetros:**

- `str` (string): String a verificar

**Retorna:** `true` se for inteiro, `false` caso contrário

**Regex:** `/^\d+$/`

---

#### `validateInputDate(date_value)`
Valida um valor de data no formato `DD/MM/YYYY`.

**Parâmetros:**

- `date_value` (string): Data no formato `DD/MM/YYYY`

**Retorna:** `true` se válida, `false` caso contrário

**Validações:**

- Verifica se o mês e dia são válidos
- Verifica se o ano é válido
- Usa `Date` object para validação

---

#### `setTitleForDialog(id, w, h, t)`
Define título e dimensões de um diálogo existente.

**Parâmetros:**

- `id` (string): ID do elemento
- `w` (number): Largura
- `h` (number): Altura mínima
- `t` (string): Título

---

#### `setButtonCloseForDialog(id)`
Define apenas o botão "Fechar" para um diálogo.

**Parâmetros:**

- `id` (string): ID do elemento

---

#### `getButtonsForDialogToPerfilRo(id)`
Similar ao `setButtonCloseForDialog`, usado para perfis somente leitura.

**Parâmetros:**

- `id` (string): ID do elemento

---

#### `getInfoTip(msg)`
Retorna HTML de um ícone de informação com tooltip.

**Parâmetros:**

- `msg` (string): Mensagem do tooltip

**Retorna:** String HTML com `<img>` e tooltip

**Exemplo:**
```javascript
var html = libs4.getInfoTip("Esta é uma informação importante");
// Retorna: "<img src='imagens/info.png' class='info' title='Esta é uma informação importante'/>"
```

---

### 11. Sistema de Sugestões

A biblioteca mantém um sistema interno de sugestões usado durante o cálculo de risco.

#### `clearSuggestions()`
Limpa o array de sugestões.

**Uso interno:** Chamado no início de cada cálculo de risco.

---

#### `addSuggestion(suggestion)`
Adiciona uma sugestão ao array (evita duplicatas).

**Parâmetros:**

- `suggestion` (string/object): Sugestão a adicionar
  - Se string: chave de tradução (ex: `'suggestion.snat.origin_ip_missing'`)
  - Se object: `{key: "...", value: "..."}`

**Funcionalidade:**
- Verifica duplicatas antes de adicionar
- Usado internamente pelas funções de cálculo de risco

---

#### `getSuggestions()`
Retorna o array de sugestões atual.

**Retorna:** Array de sugestões

---

#### `t(p)`
Função de tradução que converte chaves em textos.

**Parâmetros:**

- `p` (string/object): Chave de tradução ou objeto com `{key: "...", value: "..."}`

**Retorna:** Texto traduzido ou a chave original se não encontrado

**Exemplo:**
```javascript
libs4.t("suggestion.snat.origin_ip_missing");
// Retorna: "Especifique os endereços IP de origem que serão permitidos na regra."
```

**Estrutura de traduções:**

As traduções estão no objeto `suggestion_text` dentro da biblioteca, organizadas por categoria:

- `suggestion.snat.*`: Sugestões para políticas SNAT
- `suggestion.dnat.*`: Sugestões para regras DNAT
- `suggestion.forward.*`: Sugestões para regras Forward
- `suggestion.redirect.*`: Sugestões para regras Redirect
- `error.*`: Mensagens de erro

---

### 12. Firewall e Regras

#### `saveFirewallRule(modalManager, conf)`
Salva uma regra de firewall via requisição AJAX.

**Parâmetros:**

- `modalManager` (object): Gerenciador de modais
- `conf` (object): Configuração com:
  - `mode` (string): Modo (`"ADD"` ou `"EDIT"`)
  - `url` (string): URL do endpoint
  - `data` (object): Dados a enviar

**Retorna:** Promise que resolve com:

- `{code: 'success'}` se sucesso
- `{code: 'error', list: [...]}` se erro

**Funcionalidade:**
- Usa `axios` para fazer POST
- Exibe toast de sucesso
- Trata erros e exibe alertas

---

#### `clearErrors(form)`
Remove classes de erro e ícones de erro de um formulário.

**Parâmetros:**

- `form` (jQuery): Objeto jQuery do formulário

**Funcionalidade:**
- Remove classe `has-error` de `.form-group`
- Remove elementos `.icon-error`

---

#### `showErrors(form, errors)`
Exibe erros de validação em um formulário.

**Parâmetros:**

- `form` (jQuery): Objeto jQuery do formulário
- `errors` (array): Array de objetos `{field: "seletor"}`

**Funcionalidade:**
- Adiciona classe `has-error` aos campos com erro
- Adiciona ícone de alerta nos painéis (accordions) que contêm campos com erro

---

### 13. Funções Auxiliares de Risco

#### `getIpWeight(rangeDatabase, ip, from)`
Calcula o peso de risco de um IP baseado no range de rede.

**Parâmetros:**

- `rangeDatabase` (object): Base de dados com pesos por máscara CIDR
- `ip` (string): IP a calcular (pode ser com ou sem máscara)
- `from` (string): Tipo de regra (`'snat'`, `'dnat'`, `'forward'`, `'redirect'`)

**Retorna:** Número representando o peso de risco

**Funcionalidade:**
- Se IP não tem máscara, adiciona automaticamente:
  - `/32` se último octeto != 0
  - `/24` se último octeto == 0 e penúltimo != 0
  - `/16` se últimos dois octetos == 0
- Busca o peso na base de dados baseado na máscara e tipo de regra

---

#### `getPortWeight(portsDatabase, ports, from, showSugg)`
Calcula o peso de risco de uma ou mais portas.

**Parâmetros:**

- `portsDatabase` (object): Base de dados com pesos por porta
- `ports` (string): Porta(s) (pode ser range `100:200` ou múltiplas `80,443`)
- `from` (string): Tipo de regra
- `showSugg` (boolean, opcional): Se deve adicionar sugestões (padrão: `true`)

**Retorna:** Número representando o maior peso encontrado

**Funcionalidade:**
- Processa ranges de portas (expande `100:200` em portas individuais)
- Processa múltiplas portas separadas por vírgula
- Retorna o maior peso encontrado
- Adiciona sugestões se portas inseguras forem detectadas:
  - SNAT: portas com peso >= 5
  - DNAT/Redirect: portas com peso >= 14

---

#### `isRiskDefaultValue(defaultScores, key, item)`
Verifica se um IP está na lista de valores padrão (baixo risco).

**Parâmetros:**

- `defaultScores` (object): Objeto com listas de IPs padrão
- `key` (string): Chave (`"ip_origin"` ou `"ip_target"`)
- `item` (string): IP a verificar

**Retorna:** `true` se é valor padrão, `false` caso contrário

**Uso:** Se um IP é padrão, o risco pode ser zerado (regras de baixo risco).

---

#### `calcMultipleSecurityRisk(comesFrom, params, protocols, ports, ipOriginRanges, ipDestinyRanges, defaultScores)`
Calcula risco para múltiplas regras de uma vez.

**Parâmetros:**

- `comesFrom` (string): Tipo de regra
- `params` (array): Array de objetos de regras
- Demais parâmetros: Mesmos de `calcSecurityRisk`

**Retorna:** Array de objetos `{name: "...", value: number}`

---

## Variáveis Privadas

A biblioteca mantém algumas variáveis privadas para controle interno:

- `suggestions`: Array de sugestões usado durante cálculos de risco
- `out_ifaces`: Interfaces de saída para configuração de videoconferência
- `in_ifaces`: Interfaces de entrada para configuração de videoconferência
- `isSyncing`: Flag para evitar loops infinitos em sincronização
- `videoConferenceData`: Dados de configuração de videoconferência

---

## Dependências

A biblioteca depende de:

- **jQuery**: Para manipulação DOM e diálogos (jQuery UI)
- **jqGrid**: Para grids (funções de formatação)
- **axios**: Para requisições HTTP (função `saveFirewallRule`)
- **Font Awesome**: Para ícones (função `show_pass`)

---

## Padrões e Boas Práticas

### 1. **Encapsulamento**
A biblioteca usa IIFE para criar um escopo isolado, evitando poluição do namespace global.

### 2. **Validação de Entrada**
Muitas funções validam parâmetros antes de processar, retornando valores padrão ou erros apropriados.

### 3. **Tratamento de Erros**
Funções de validação retornam valores booleanos ou strings de erro, permitindo tratamento flexível pelo código chamador.

### 4. **Modularidade**
Funções são organizadas por categoria, facilitando manutenção e compreensão.

### 5. **Reutilização**
Funções utilitárias são projetadas para serem reutilizáveis em diferentes contextos do sistema.

---

## Exemplos de Uso Completos

### Exemplo 1: Fluxo de Validação e Salvamento de Regra de Firewall

```javascript
// 1. Validar dados
var erros = libs4.validatePorts("80,443", "Portas");
if (erros) {
  libs4.alerta("div_erro", erros.split("|").join("<br>"));
  return;
}

// 2. Calcular risco
var risk = libs4.calcSecurityRisk(
  'snat_policy',
  params,
  protocols,
  ports,
  ipOriginRanges,
  ipDestinyRanges,
  defaultScores
);

// 3. Validar risco contra permissões
var validation = await libs4.validateRisk(modal, permission, risk);
if (validation.code !== 'success') {
  return; // Usuário cancelou ou não tem permissão
}

// 4. Salvar regra
var result = await libs4.saveFirewallRule(modalManager, {
  mode: "ADD",
  url: "/api/firewall/snat",
  data: formData
});

if (result.code === 'success') {
  libs4.toast("Regra salva com sucesso!", "success");
}
```

---

### Exemplo 2: Validação e Correção de Lista de Domínios

```javascript
// Obter lista do textarea
var lista = $("#text_area_sites").val().split("\n");

// Validar e corrigir
libs4.ValidateAndRemoveDomains("text_area_sites", lista, function(validados) {
  // Callback chamado após validação
  console.log("Domínios validados:", validados);
  
  // Salvar no servidor
  axios.post("/api/sites", validados);
});
```

---

### Exemplo 3: Diálogo de Confirmação com Múltiplas Opções

```javascript
libs4.showDialog("div_acoes", "Escolha uma ação:", "Ações",
  [
    {
      text: "Editar",
      func: function() {
        abrirModalEdicao();
      }
    },
    {
      text: "Duplicar",
      func: function() {
        duplicarRegra();
      }
    },
    {
      text: "Excluir",
      func: function() {
        excluirRegra();
      }
    }
  ],
  "Cancelar"
);
```

---

## Notas de Implementação

### Tooltips com Scroll
A função `enableTipScrolling` modifica o atributo `title` do elemento para incluir HTML com classes CSS. É necessário que o CSS correspondente esteja definido para que o scroll funcione corretamente.

### Cálculo de Risco
O sistema de cálculo de risco é baseado em pesos configuráveis. Os pesos são fornecidos via parâmetros e podem variar conforme a configuração do sistema. Valores altos indicam maior risco.

### Validação de Domínios
A validação de domínios é complexa e inclui:

- Remoção de protocolos
- Normalização de caracteres
- Detecção de conflitos (subdomínios)
- Validação de formato

### Grids (jqGrid)
As funções de formatação são específicas para jqGrid e seguem o padrão de formatters do plugin.

---

## Manutenção e Extensibilidade

Para adicionar novas funções à biblioteca:

1. Defina a função dentro do IIFE
2. Adicione ao objeto de retorno no final do arquivo
3. Documente a função seguindo o padrão deste documento
4. Considere adicionar testes se aplicável

Para modificar funções existentes:

1. Mantenha a compatibilidade com a assinatura atual
2. Se necessário quebrar compatibilidade, documente claramente
3. Considere adicionar parâmetros opcionais em vez de modificar obrigatórios

---

## Conclusão

A biblioteca `libs4.js` é uma peça central do sistema S4, fornecendo funcionalidades essenciais para interface, validação, segurança e manipulação de dados. Esta documentação serve como referência completa para desenvolvedores que precisam entender, usar ou estender a biblioteca.

Para questões ou sugestões de melhoria, consulte a equipe de desenvolvimento do S4.

---

**Última atualização:** 2025-01-27  
**Versão da biblioteca:** Conforme código em `S4/php/js/src/libs4.js`

