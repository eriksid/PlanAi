# Planejamento de liberação de login gogole no hotpost

## Decrição

Dado que um usuário queria acessar o hotpos via google ao precionar o botão "Entrar com google"
Então atualmente não está abriando corretamente, mostra erro "Sua conexão não é a particular" na url accounts.google.com
E mesmo após liberação temporária. Logs do Squid mostravam TCP_DENIED com a ação peek para o IP de destino.


## Opção 1 - Liberação via Squid

O bloqueio ocorre devido ao funcionamento do recurso SslBump do Squid em duas etapas:

Etapa 1 (SslBump1): A conexão inicial é estabelecida via IP (CONNECT IP:443). As regras de ACL existentes baseadas apenas em dstdomain falhavam nesta etapa, pois o domínio ainda não era conhecido, resultando em bloqueio imediato.

SNI: Embora existisse uma regra para o domínio, ela nunca era alcançada porque a conexão era derrubada na Etapa 1.

### Solução :
 Alteração realizada no arquivo de configuração /usr/local/squid/etc/proxy/hotspot/rules_acl.cfg no servidor 172.30.15.200.

A correção consistiu em duas partes:

* Permitir Etapa 1: Criação da regra acl Step1 at_step SslBump1 e liberação explícita para o MAC. Isso permite que o handshake inicial ocorra.
* Validação SNI: Adição de regra ssl::server_name para validar o domínio Google extraído do handshake TLS.


## Opção 2 - Liberação via Iptables

O bloqueio ocorre devido a regra 