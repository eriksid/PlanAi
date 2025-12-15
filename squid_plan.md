# Habilitar Acesso HTTPS (SNI) para Endereço MAC

## Descrição do Objetivo
O endereço MAC `30:4b:07:e1:60:2c` não consegue acessar `accounts.google.com`. A configuração existente usa ACLs `dstdomain` (`listdomain_google`), que falham para conexões HTTPS interceptadas transparentemente onde o destino aparece como um IP. Adicionaremos uma ACL `ssl::server_name` para corresponder ao SNI (Server Name Indication) e permitir o tráfego.

## Alterações Propostas

### Configuração
#### [MODIFICAR] [rules_acl.cfg](file:///usr/local/squid/etc/proxy/hotspot/rules_acl.cfg)
- Definir uma nova ACL `listdomain_google_SNI` usando `ssl::server_name` apontando para a lista existente de domínios do Google.
- Adicionar uma regra `http_access allow` para o endereço MAC usando esta nova ACL de SNI.

O novo conteúdo para o bloco do MAC específico ficará assim:
```squid
#ARP=30:4b:07:e1:60:2c
acl 30:4b:07:e1:60:2c.arp arp 30:4b:07:e1:60:2c
# Correspondência dstdomain existente
http_access allow 30:4b:07:e1:60:2c.arp listdomain_google
# Nova correspondência SNI
acl listdomain_google_SNI ssl::server_name "/usr/local/squid/etc/listas/listdomain_google/squid_list"
http_access allow 30:4b:07:e1:60:2c.arp listdomain_google_SNI
url_rewrite_access deny 30:4b:07:e1:60:2c.arp listdomain_google
```

## Plano de Verificação

### Verificação Manual
- Aplicar as alterações em `rules_acl.cfg` no servidor remoto `172.30.15.200`.
- Recarregar a configuração do Squid: `squid -k reconfigure -n hotspot -f /usr/local/squid/etc/proxy/hotspot/squid.conf`.
- Solicitar ao usuário que verifique a conectividade no dispositivo cliente.
