#  Planejamento – Desconectar Peer WireGuard

Este documento apresenta o planejamento completo para implementação da funcionalidade **“Desconectar Peer”** no módulo WireGuard do S4, seguindo o mesmo padrão, organização e estilo do arquivo original.

---

## Visão Geral

WireGuard **não possui** um comando explícito de “disconnect”.
Para desconectar um peer, utilizamos **operações que invalidam a sessão**:

* `wg set <iface> peer <pubkey> remove`
* ou `wg set <iface> peer <pubkey> endpoint 0.0.0.0:0`
* ou zerar allowed-ips

A solução recomendada é **remover o peer da interface no kernel**, pois é simples, clara e comporta-se como “desconectar”.

**Status atual da interface que você mostrou (tela):**

* Peer listado
* Botão “Desconectar” aparece
* Backend ainda não implementado

---

#  Fase 1: Backend (C++)

### 1.1 Nova ação no wrapper WireGuard

Adicionar uma nova operação no executável:

```
-wireguard_disconnect_peer
```

### 1.2 Parâmetros necessários:

* `iface_name` (wg0, wg1, ...)
* `peer_public_key`

### 1.3 Comando a executar:

```bash
/usr/local/wireguard/bin/wg set <iface> peer <public_key> remove
```

Esse comando remove imediatamente:

* endpoint ativo
* allowed-ips
* sessão atual
* estatísticas
* referência no kernel

### 1.4 Implementação C++ (padrão AGENTS.md)

Passos:

1. Validar parâmetros `iface_name` e `peer_public_key`
2. Consultar no banco se o peer existe
3. Montar comando wg:

   ```cpp
   string cmd = "/usr/local/wireguard/bin/wg set " + iface + " peer " + pubkey + " remove";
   ```
4. Executar com `getCmdOutput()`
5. Se saída tiver erro → `throw_exception(...)`
6. Retornar:

   ```json
   { "status": "OK", "message": "Peer desconectado com sucesso" }
   ```

### 1.5 Arquivos a modificar

* `S4/cpp/wireguard.cpp`

  * adicionar novo case no `main()`
  * função: `disconnectPeer(mysql, iface_name, peer_public_key)`

### 1.6 Critérios de sucesso

* Peer some no `wg show`
* Último handshake vira “—”
* Endpoint desaparece
* Grid de monitoramento atualiza corretamente

---

# 🔧 Fase 2: Endpoint PHP

### 2.1 Criar ação no retrieve

Arquivo:

* `S4/php/retrieve_wg_peers.php`

Adicionar:

```php
case 'disconnect':
    disconnectPeer();
    break;
```

### 2.2 Função `disconnectPeer()`

A função deve:

1. Receber `iface_name` e `peer_id`
2. Consultar no banco a public_key do peer
3. Chamar o binário:

```php
$cmd = "/usr/local/wireguard/wireguard -disconnect_peer "
     . "iface_name=$iface_name "
     . "peer_public_key=$public_key ";
```

4. Retornar JSON para o frontend

---

# 🖥️ Fase 3: Frontend — `wg_peers.php`

### 3.1 Botão “Desconectar”

No jqGrid, a coluna “Desconectar” deve chamar:

```js
disconnectPeerPeer(id, iface_name);
```

### 3.2 Função JavaScript

```js
function disconnectPeer(id, iface) {
    $.ajax({
        url: 'retrieve_wg_peers.php',
        type: 'POST',
        data: {
            oper: 'disconnect',
            id: id,
            iface_name: iface
        },
        success: function(r) {
            alert("Peer desconectado!");
            $("#gridPeers").trigger("reloadGrid");
        }
    });
}
```

### 3.3 Comportamento esperado

* Remover o peer instantaneamente da sessão
* Estatísticas → “0B”
* Handshake → “-”
* Endpoint → “-”
* Linha permanece (peer não excluído, só desconectado)

---

# 🧪 Fase 4: Testes Práticos

### Testes essenciais:

1. Peer conectado → clicar “Desconectar”

2. Executar:

   ```
   wg show wg0
   ```

   Resultado esperado:

   * Peer removido
   * Sessão derrubada
   * handshake vazio
   * endpoint vazio

3. Cliente:

   * Túnel cai
   * Cliente tenta renegociar (mas sem peer no kernel, é ignorado)

---

# 📡 Alternativas (opcionais)

### A) Resetar endpoint

```bash
wg set wg0 peer <pubkey> endpoint 0.0.0.0:0
```

Só reinicia sessão — o peer pode reconectar.

### B) Zerar allowed-ips

```bash
wg set wg0 peer <pubkey> allowed-ips ""
```

Peer fica “ignorado”.

---

# 📅 Cronograma sugerido

| Dia | Tarefa               |
| --- | -------------------- |
| 1   | Implementar C++      |
| 2   | Implementar PHP      |
| 3   | Integrar JS + jqGrid |
| 4   | Testes práticos      |
| 5   | Ajustes e validações |

---

# ✅ Checklist final

* [ ] C++ implementado
* [ ] Endpoint retrieve criado
* [ ] Botão chama AJAX
* [ ] Sessão derrubada com `wg set iface peer PUBKEY remove`
* [ ] Monitoramento atualizado
* [ ] Logs/erros funcionando

