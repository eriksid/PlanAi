#contexto:
Tela: VPN > Wireguard > Interfaces:
Status (select obrigatorio)

Nome (input obrigatorio)
Descrição (input opcional)

Interface (input readonly obrigatorio) vem do back

IP (input obrigatorio)
Rede (input obrigatorio)
Porta (input obrigatorio)

Endereço endpoint padrão do peer (input opcional)
(i) O endereço informado neste campo, será exibido no cadastro dos peers de WireGuard em <i>menu superior VPN, menu lateral Wireguard submenu Peers</i>.<br><br> Informe o endereço de conexão, por exemplo: vpn.meudomínio.<br><br>O endereço deste campo será utilizado apenas nos novos peers cadastrados. <br> Peers existentes necessitam ser editados e reconfigurados novamente no computador/dispositivo do usuário.

Chave pública (input obrigatorio) vem do back
Chave privada: (input oculto obrigatorio) vem do back



# como está:
## no front


escolha=save_interface&id=&status=enabled&name=Filial&iface_name=&iface_ip=10.0.0.1&iface_network=10.0.0.0%2F24&interface_port=51820&default_endpoint_addr=192.168.10.1&dns_servers=8.8.8.8&mtu=1500&description=aaaaaaaaaaaaaaaa&public_key=4Z03Qe3Oszi%2Ff2wKxRfVYDO4Oa2DMqxblJJ1DnSWQG4%3D&private_key=

## No back:
iface_name = aaaaaaaaaaaaaaaa, iface_ip = , iface_network = , interface_port = , default_endpoint_addr = , dns_servers = 4Z03Qe3Oszi/f2wKxRfVYDO4Oa2DMqxblJJ1DnSWQG4=, mtu = , public_key = , private_key = , description = 

# todo:
- id é só no banco remover do front (modal de cadastro) e back receber id
- nome não está pegando o valor do campo
- 27/11/2025 13:49:56 - MSG(/var/S4/wireguard): DEBUG: argc = 13, id = , status = enabled, name = , 