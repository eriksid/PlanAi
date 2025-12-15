### Tela: VPN > Wireguard > Interfaces:

**Status** (select obrigatório)
- Valores: "Habilitado" (enabled) / "Desabilitado" (disabled)

**Nome** (input obrigatório)
- Nome descritivo da interface
- Máximo: 50 caracteres
- Campo: `name`

**Descrição** (input opcional)
- Observações sobre a interface
- Máximo: 255 caracteres
- Campo: `description`

**Interface** (input readonly obrigatório)
- Nome da interface do sistema (ex: wg0, wg1)
- Gerado automaticamente pelo backend
- Máximo: 15 caracteres
- Campo: `iface_name`
- Não editável pelo usuário

**IP** (input obrigatório)
- IP atribuído à interface WireGuard
- Formato: IPv4 ou IPv6 válido
- Exemplo: 10.0.0.1 ou 2001:db8::1
- Campo: `iface_ip`

**Rede** (input obrigatório)
- CIDR da rede VPN
- Formato: IP/máscara (ex: 10.0.0.0/24 ou 2001:db8::/64)
- Campo: `iface_network`

**Porta** (input obrigatório)
- Porta de escuta da interface
- Range: 1-65535
- Campo: `interface_port`

**Endereço endpoint padrão do peer** (input opcional)
- Endereço de conexão para os peers
- Exemplo: vpn.meudomínio.com.br ou 192.168.1.1
- Máximo: 255 caracteres
- Campo: `default_endpoint_addr`
- **Tooltip/Info:** O endereço informado neste campo será exibido no cadastro dos peers de WireGuard em <i>menu superior VPN, menu lateral Wireguard submenu Peers</i>.<br><br> Informe o endereço de conexão, por exemplo: vpn.meudomínio.<br><br>O endereço deste campo será utilizado apenas nos novos peers cadastrados. <br> Peers existentes necessitam ser editados e reconfigurados novamente no computador/dispositivo do usuário.

**Servidores DNS** (input opcional)
- Servidores DNS separados por vírgula
- Exemplo: 8.8.8.8,8.8.4.4 ou 2001:4860:4860::8888
- Máximo: 255 caracteres
- Campo: `dns_servers`

**MTU** (input opcional)
- MTU (Maximum Transmission Unit) da interface
- Range: 1-65535 (típico: 1280-1500)
- Campo: `mtu`

**Chave pública** (input readonly obrigatório)
- Chave pública WireGuard (base64)
- Gerada automaticamente pelo backend
- Máximo: 88 caracteres
- Campo: `public_key`
- Não editável pelo usuário (apenas visualização)

**Chave privada** (input oculto readonly obrigatório)
- Chave privada WireGuard (base64)
- Gerada automaticamente pelo backend
- Máximo: 88 caracteres
- Campo: `private_key`
- Oculto por segurança, não editável pelo usuário

---

### Campos Removidos (não devem aparecer na tela):
- ❌ Redes locais (`local_networks`) - REMOVIDO
- ❌ Keepalive padrão (`keepalive_value`) - REMOVIDO
- ❌ Preshared key (`preshared_key`) - REMOVIDO (era da interface, não dos peers)