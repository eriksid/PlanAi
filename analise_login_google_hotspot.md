# Implementação: Correção Login Google Hotspot com SSL

## Obsjetivo
Corrigir a falha no login do Google em instâncias de Hotspot com Interceptação SSL ativa. O problema ocorre devido ao Certificate Pinning/HSTS do Google, exigindo que o tráfego de autenticação não seja interceptado (bypass/splice).

## Proposed Changes

### LibProxy (`lib/libproxy.cpp`)

#### [MODIFY] [libproxy.cpp](file:///home/erik/dados_do_meu_s4/usr/src/desenv/S4/cpp/lib/libproxy.cpp)

- Alterar a função `rewriteWhatsappRules(MYSQL &mysql)`.
- Adicionar lógica para buscar grupos configurados como Hotspot (via `proxy_nav.authentications` ou `proxy_nav.groups`).
- Adicionar regras de `ssl_bump splice` para os domínios do Google no arquivo `no_ssl_whatsapp.cfg` gerado, direcionado aos usuários desses grupos.
- Domínios: `accounts.google.com`, `ssl.gstatic.com`, `googleapis.com`, `googleusercontent.com`.

**Lógica da Query SQL:**
Buscar grupos que estão vinculados a autenticações do tipo `LDAP_HOTSPOT` ou usados em parâmetros de captive portal.

```sql
SELECT DISTINCT g.name 
FROM proxy_nav.groups g
JOIN proxy_nav.authentications a ON (
    JSON_UNQUOTE(JSON_EXTRACT(a.jparameters, '$.master.grupo')) = g.name OR 
    JSON_UNQUOTE(JSON_EXTRACT(a.jparameters, '$.master.cp_groups')) = g.name
)
WHERE a.type IN ('LDAP_HOTSPOT', 'S4_HOTSPOT') -- Verificar tipos corretos
```
*Refinamento*: Como o foco é a interceptação SSL que ocorre por *grupo* no Squid, se o grupo for usado em Hotspot, devemos liberar.


## Testes
1. Executar o comando que aciona a reescrita (ou simular a chamada da função se possível).
2. Verificar o conteúdo de `/usr/local/squid/etc/groups/no_ssl_whatsapp.cfg` (ou o path correto se for dentro de `/tmp` durante teste).
3. Confirmar se as linhas `ssl_bump splice ... group_name_users` para os domínios do Google estão presentes.
