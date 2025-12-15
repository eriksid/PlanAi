## 📋 Tabela de Problemas — WireGuard / S4

| Módulo             | Problema                                       | Detalhe Técnico                                                         | Responsável | Status         |
| ------------------ | ---------------------------------------------- | ----------------------------------------------------------------------- | ----------- | -------------- |
| Peers / Interfaces | Rota muda de /24 para /32 ao criar peer        | Interface cria rota /32 por peer ao invés de manter a rota da interface | Erik        | Ajustar        |
| Peers / Interfaces | Rotas não são removidas ao excluir peers       | `route -n` mantém /32 mesmo após excluir peers                          | Erik        | Pendente       |
| Peers              | Último peer removido apaga todas as rotas      | Nova criação gera apenas /32                                            | Fábio       | Pendente       |
| SNAT / Navegação   | SNAT não funciona com WireGuard                | Só funciona após parar o NGFW                                           | —           | Em análise     |
| Reader             | Reader não funciona nas interfaces WG          | URLs/domínios não são identificados                                     | Fábio       | Merge pendente |
| Monitoramento      | WG não aparece no campo Interface de Saída     | Nem wg+ nem tun+ aparecem                                               | Fábio       | Pendente       |
| Monitoramento VPN  | Ordenação incorreta (Handshake, Bytes)         | Crescente e decrescente invertidos                                      | Erik        | Pendente       |
| Serviços           | Falta exibição na tela de serviços             | Normal e cluster                                                        | Fábio       | Pendente       |
| Exportação         | Falta exportar conf, QRCode e visualização     | —                                                                       | Fábio       | Pendente       |
| KeepAlive          | Limpar campo não remove da conf                | Só remove após `wg setconf`                                             | Fábio       | Pendente       |
| Filtro Peers       | Filtro por descrição não funciona              | Corrigido uso de `wgi.iface_name`                                       | Erik        | ✅ Resolvido    |
| PDF Peers          | Status com cor errada                          | Corrigido via `/sys/class/net`                                          | Erik        | ✅ Resolvido    |
| Interfaces         | Permite IP/Máscara duplicado                   | Conflito com eth0 e wg                                                  | Erik        | Pendente       |
| Interfaces/Peers   | Alterar IP da interface não atualiza peer      | Peer mantém IP antigo                                                   | Fábio       | Pendente       |
| Peers              | Endpoint não atualiza ao trocar interface      | Valor permanece da seleção anterior                                     | Fábio       | Pendente       |
| Logs               | WG joga logs no dmesg                          | Deveria ter log próprio                                                 | Fábio       | Pendente       |
| DNS                | Resolução externa ao conectar WG               | Falta DNS no peer exportado                                             | Fábio       | Em ajuste      |
| AllowedIPs         | 0.0.0.0/0 quebra SNAT                          | Cria rota default no S4                                                 | Fábio       | Pendente       |
| Ping/Telnet        | Não responde sem IP da interface no AllowedIPs | Necessário adicionar IP da própria WG                                   | —           | Documentar     |
| Preshared Key      | Campo inválido sem gerador                     | Sugestão `wg genpsk`                                                    | Fábio       | Pendente       |
| Preshared Key      | Duplica linha no wg.conf                       | Duas entradas iguais                                                    | Fábio       | Pendente       |
| KeepAlive          | Peer tenta conectar mesmo sem remoto           | Só para após `setconf`                                                  | Fábio       | Pendente       |
| Status Peer        | Desativar não remove do `wg`                   | Só sai após `syncconf`                                                  | Fábio       | Pendente       |
| Preshared Key      | Não aplica no peer sem `syncconf` manual       | Só ativa após comando                                                   | Fábio       | Pendente       |
| AllowedIPs         | Espaços duplicados no arquivo                  | `" ,  "` ao invés de `" , "`                                            | Fábio       | Pendente       |
| AllowedIPs         | No `wg` aparece como `(none)`                  | Mesmo estando no arquivo                                                | Fábio       | Pendente       |

