# Documentação do Incidente (Formato Jira)

**Título:** Correção de Bloqueio de Acesso Google para MAC 30:4b:07:e1:60:2c no Hotspot

**Descrição:**
O usuário relatou que o dispositivo com MAC `30:4b:07:e1:60:2c` estava sendo bloqueado ao tentar acessar `accounts.google.com`, mesmo após liberação temporária.
Logs do Squid mostravam `TCP_DENIED` com a ação `peek` para o IP de destino.

**Causa Raiz:**
O bloqueio ocorria devido ao funcionamento do recurso SslBump do Squid em duas etapas:
1.  **Etapa 1 (SslBump1):** A conexão inicial é estabelecida via IP (`CONNECT IP:443`). As regras de ACL existentes baseadas apenas em `dstdomain` falhavam nesta etapa, pois o domínio ainda não era conhecido, resultando em bloqueio imediato.
2.  **SNI:** Embora existisse uma regra para o domínio, ela nunca era alcançada porque a conexão era derrubada na Etapa 1.

**Solução Técnica:**
Alteração realizada no arquivo de configuração `/usr/local/squid/etc/proxy/hotspot/rules_acl.cfg` no servidor `172.30.15.200`.

A correção consistiu em duas partes:
1.  **Permitir Etapa 1:** Criação da regra `acl Step1 at_step SslBump1` e liberação explícita para o MAC. Isso permite que o handshake inicial ocorra.
2.  **Validação SNI:** Adição de regra `ssl::server_name` para validar o domínio Google extraído do handshake TLS.

**Implementação (Trecho de Código):**
```squid
#ARP=30:4b:07:e1:60:2c
acl 30:4b:07:e1:60:2c.arp arp 30:4b:07:e1:60:2c

# ACLs Auxiliares
acl Step1 at_step SslBump1
acl listdomain_google_SNI ssl::server_name "/usr/local/squid/etc/listas/listdomain_google/squid_list"

# REGRAS DE ACESSO
# 1. Permitir conexão inicial (IP)
http_access allow 30:4b:07:e1:60:2c.arp Step1
# 2. Permitir acesso baseado no nome do servidor (SNI)
http_access allow 30:4b:07:e1:60:2c.arp listdomain_google_SNI
# 3. Fallback (Compatibilidade)
http_access allow 30:4b:07:e1:60:2c.arp listdomain_google

url_rewrite_access deny 30:4b:07:e1:60:2c.arp listdomain_google
```

**Resultados de Teste:**
- Correção aplicada e serviço recarregado (`squid -k reconfigure`).
- Usuário confirmou que o acesso foi restabelecido com sucesso.
