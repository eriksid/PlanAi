# Lista de Bugtestes

## 1. Apresentando o nome dos status novos, os mesmos são grandes para a visualização, com isso ele se quebra em 2 linhas conforme a visualização do e-mail na colaboração, mesmo que visualize em nova aba separada e em tela maior

* 

## 2. Relatório > SD-WAN > Histórico de link: Ao filtrar por um status marcando o checkbox ele apresenta o resultado corretamente, mas desmarcando o mesmo para apresentar tudo na grid, ele nao desfaz o filtro anterior.

## 3. e todos links estiverem instáveis, e a configuração mencionar que deve trocar no instável, devemos manter todos os links instáveis configurados (funcionando ainda na tabela de balance/redund).

## 4. Na tela, não está claro que a troca é apenas do down->up ou do up->down, precisa melhorar o label e se precisar também colocar info

## 5. Rede > SD-WAN > Monitoramento de link: Monitoramento via TCP, quando fica instavel, ao conseguir resposta nos pacotes enviados aos destinos ele muda para DOWN inves de ja em seguida retornar como UP como ocorre normalmente para o modo ICMP.

iptables -I OUTPUT -o eth1 -d 8.8.8.8 -p tcp --dport 53 -j DROP
iptables -I OUTPUT -o eth1 -d 1.1.1.1 -p tcp --dport 53 -j DROP

iptables -D OUTPUT -o eth1 -d 8.8.8.8 -p tcp --dport 53 -j DROP
iptables -D OUTPUT -o eth1 -d 1.1.1.1 -p tcp --dport 53 -j DROP


Arquvios utiteis:
/home/erik/dados_do_meu_s4/usr/src/desenv/S4/php/app/controllers/balance/BalanceController.php

# NOva lista

- [X] Monitoramento TCP marcando DOWN incorretamente
- Troca indevida de grupos/instâncias
- Seleção errada de link quando todos instáveis
- CFG inconsistente ao mudar de estado

## Troca indevida de grupos/instâncias
- Aonde o arquivo de status é chamado:
```
balance.cpp:243:      string status = getCmdOutput("cat /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(iface_name)+".status");
balance.cpp:828:        system(("echo 'unstable' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:835:        system(("echo 'up' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:848:      system(("echo 'down' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:866:        system(("echo 'down' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:873:      system(("echo 'up' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:916:        system(("echo 'unstable' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:923:        system(("echo 'up' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:938:      system(("echo 'down' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:957:        system(("echo 'down' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:966:      system(("echo 'up' > /usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str());
balance.cpp:974:  if (!fileExists(("/usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str()))
balance.cpp:978:  arq.open(("/usr/local/S4/balance/log/"+normalizeInterfacePPPoE(IFACES[iface_id])+".status").c_str(), ios::in);

dns_entries.cpp:61:    if (!system(("cat /usr/local/S4/balance/log/"+iface+".status 2>/dev/null |grep -ix 'up' >/dev/null 2>&1").c_str())){
dns_ext.cpp:42:    if (!system(("cat /usr/local/S4/balance/log/"+iface+".status |grep -ix 'up' >/dev/null 2>&1").c_str())){
firewall.cpp:11491:  string filepath = "/usr/local/S4/balance/log/"+iface+".status";
lib/libproxy.cpp:1697:        if (!system("grep up /usr/local/S4/balance/log/"+ sep2[0] +".status >/dev/null 2>&1") == 0) {
lib/libqos.cpp:993:          if (!system("grep up /usr/local/S4/balance/log/" + sep2[0] + ".status >/dev/null 2>&1") == 0){
proxy.cpp:8534:            if (system("grep up /usr/local/S4/balance/log/"+ sep2[0] +".status >/dev/null 2>&1") == 0){
relogio.cpp:32:  string fpath = "/usr/local/S4/balance/log/"+iface+".status";
skyline.cpp:350:        if (!system("grep up /usr/local/S4/balance/log/"+ sep2[0]+".status >/dev/null 2>&1") == 0) {
```

**Analise:**
Problema:
Após o retorno do link para o estado UP, o IP configurado permanece como 40.40.40.92, não sendo revertido para o valor original (30.30.30.92).

Cenário:
- Cofigurar "Alternar quando o link operacional ficar instável ou indisponível" 
- O link entrar em estado instável, porém retornar para UP sem transitar para DOWN.
- caminho completo do arquivo: /usr/local/squid/etc/groups/include_interface_groups.cfg

Como simular:
- Inicialmente, o arquivo include_interface_groups.cfg contém o IP 30.30.30.92.
- Quando a perda de pacotes ultrapassa o limite configurado:
  - É executado: echo 'unstable' > /usr/local/S4/balance/log/eth1.status
  - A função configureRouting é chamada com transição de estado UP → DOWN
  - Como resultado, o arquivo include_interface_groups.cfg passa a conter o IP 40.40.40.92
- No teste seguinte, antes de atingir o limite de  UP-->DOWN, não é detectada perda de pacotes:
  - É executado: echo 'up' > /usr/local/S4/balance/log/eth1.status
  - A função configureRouting é chamada com status UP
- No entanto, o IP configurado permanece como 40.40.40.92, não sendo revertido para o valor original (30.30.30.92).

Possíveis causas analisadas:

1. A função configureRouting não é executada por existir apenas uma rota ativa.
   - Hipótese descartada, pois o estado atual do link é UP, condição na qual a função deveria ser executada.

2. O estado anterior é igual ao estado atual.
   - old_status: UP
   - status: UP
   - Não houve transição de estado.



## Arquivos de include dos grupos/instâncias de proxy e nao deveria pois somente quando ele ficasse Indisponível/DOWN de vez poderia fazer a mudança para o eth1. Verificar
**Analise 7:**
Problema:

Os arquivos de include dos grupos/instâncias de proxy ao entrar no estado instável estão sendo alterados indevidamente.
/usr/local/squid/etc/proxy/teste/include_interface_proxy.cfg
/usr/local/squid/etc/groups/include_interface_groups.cfg 

Cenário:
Cadastro de duas redundâncias com políticas distintas de alternância.

1. Redundância 1:
   - Condição para troca de link : "Alternar somente quando o link operacional ficar indisponível".
   - Prioridade: eth2 (prioridade 1), eth1 (prioridade 2).

2. Redundância 2:
   - Condição para troca de link : "Alternar quando o link operacional ficar instável ou indisponível".
   - Prioridade: eth1 (prioridade 1), eth2 (prioridade 2).

Condição:
- O link entra em estado instável, sem transitar para o estado DOWN.

Como simular:
- Inicialmente, o arquivo include_interface_groups.cfg contém o IP 40.40.40.92.
- Quando a perda de pacotes ultrapassa o limite configurado:
  - É executado: echo 'unstable' > /usr/local/S4/balance/log/eth1.status
  - A função configureRouting é chamada com transição de estado UP → DOWN
  - No entanto, o IP configurado é alterado para 30.30.30.92, não sendo mantido o IP original (40.40.40.92).


Causa identificada:
Ao executar a função configureRouting, a função updateLinkProxy avalia apenas a interface, sem considerar a tabela de roteamento.
A função getIfaceRedund verifica exclusivamente se o link está com status 'up', o que impede a identificação da rota efetivamente ativa.

Solução proposta:
Ajustar a função getIfaceRedund para que, ao validar o estado do link, considere também a política de alternância configurada.

- Caso a política permita alternância em estado instável, validar se houve alteração na tabela de roteamento.
  - Se houver alteração, selecionar a primeira interface em estado UP.
  - Este ponto deve ser revisado para o cenário em que todos os links estejam instáveis.

- Caso a política não permita alternância em estado instável, considerar o estado 'unstable' como válido.


## Seleção errada de link quando todos instáveis

**Analise 4:**
Problema:
Quando todos os links presentes na tabela de roteamento entram em estado instável, o serviço mantém como rota ativa o último link marcado como instável, mesmo sendo o de menor prioridade.

Cenário:
- Condição de alternância: "Alternar quando o link operacional ficar instável ou indisponível".
- Prioridades configuradas: eth1 (prioridade 1), eth2 (prioridade 2).

Como simular:

1. Estado inicial:
   - Todos os links encontram-se no estado UP.
2. Degradação do link eth1:
   - A perda de pacotes do link eth1 ultrapassa o limite configurado.
   - É executado: echo 'unstable' > /usr/local/S4/balance/log/eth1.status
   - A função configureRouting é chamada com transição de estado UP → DOWN.
3. Degradação do link eth2:
   - A perda de pacotes do link eth2 ultrapassa o limite configurado.
   - É executado: echo 'unstable' > /usr/local/S4/balance/log/eth2.status
   - A função configureRouting é chamada com transição de estado UP → DOWN.
4. Comportamento observado:
   - Ao identificar que há apenas uma rota ativa, o serviço não executa nova reavaliação.
   - O link eth2 (menor prioridade) permanece como rota ativa.
Comportamento esperado:
- Mesmo com todos os links em estado instável, a rota ativa deve ser selecionada com base na prioridade configurada, mantendo o link eth1 como ativo.

Solução proposta:

Definição:
  Caracteriza-se o cenário de “todos os links instáveis” quando, na transição do último link para estado instável, a tabela de roteamento passa a ter apenas uma rota ativa.

Lógica de decisão:
Dado o cenário de "todos os links instáveis" e tenha mais de um link instável:

- Aplicar ordenação por prioridade, mesmo com os links em estado instável.
- Remover a rota atualmente ativa.
- Selecionar o link instável de maior prioridade.
- Reaplicar a configuração de roteamento utilizando a função configureRouting.





  


## Outros problemas, pois é lido o status up e agora vai estar com "unstable" em alguns casos
caso seja para trocar somente quando indisponível "unstable" é "up".



