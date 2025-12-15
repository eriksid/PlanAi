## Planejamento

## Objetivo
- Adicionar permissões e usar elas nas telas wg_peers.php e wg_interfaces.php e no backend wireguard.cpp

`S4_grupos.php`:

```
<tbody>
  <tr>
    <td><span class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_interfaces" id="vpn_wireguard_interfaces" class="checkbox4" checked=""
        style="display: none;">Menu WireGuard Interfaces</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_interfaces_cad" id="vpn_wireguard_interfaces_cad" class="checkbox4 vpn_wireguard_interfaces"
        checked="" style="display: none;">Adicionar Interface</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_interfaces_edit" id="vpn_wireguard_interfaces_edit"
        class="checkbox4 vpn_wireguard_interfaces" checked="" style="display: none;">Editar Interface</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_interfaces_del" id="vpn_wireguard_interfaces_del" class="checkbox4 vpn_wireguard_interfaces"
        checked="" style="display: none;">Excluir Interface</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_interfaces_export" id="vpn_wireguard_interfaces_export"
        class="checkbox4 vpn_wireguard_interfaces" checked="" style="display: none;">Exportar para arquivo</td>
  </tr>
  <tr>
    <td><span class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_peers" id="vpn_wireguard_peers" class="checkbox4" checked="" style="display: none;">Menu
      WireGuard Peers</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_peers_cad" id="vpn_wireguard_peers_cad" class="checkbox4 vpn_wireguard_peers" checked=""
        style="display: none;">Adicionar Peer</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_peers_edit" id="vpn_wireguard_peers_edit" class="checkbox4 vpn_wireguard_peers" checked=""
        style="display: none;">Editar Peer</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_peers_del" id="vpn_wireguard_peers_del" class="checkbox4 vpn_wireguard_peers" checked=""
        style="display: none;">Excluir Peer</td>
  </tr>
  <tr>
    <td align="left" style="padding-left:20px;"><span
        class="cust_checkbox checkbox cust_checkbox_on">&nbsp;&nbsp;&nbsp;&nbsp;</span><input type="checkbox"
        name="vpn_wireguard_peers_export" id="vpn_wireguard_peers_export" class="checkbox4 vpn_wireguard_peers"
        checked="" style="display: none;">Exportar para arquivo</td>
  </tr>
</tbody>
```

## backend:
Arquivo : S4/cpp/auth_s4.cpp

é chamado via  S4/php/retrieve_s4_grupos.php



## pacote da versão:
- criar somente texto em `/home/erik/dados_do_meu_s4/usr/src/desenv/S4/cpp/docs/banco.md`
## Exemplo de banco:

- Observação: precisa ser em ingles as colunas

```
-- S4.firewall_menu_out definition

CREATE TABLE `firewall_menu_out` (
  `grupo` varchar(100) NOT NULL,
  `cad` enum('0','1') DEFAULT '0',
  `edit` enum('0','1') DEFAULT '0',
  `del` enum('0','1') DEFAULT '0',
  `export` enum('0','1') DEFAULT '0',
  `motive` enum('0','1') DEFAULT '0',
  `alert` enum('0','1') DEFAULT '0',
  `risk` enum('0','1','2','3') NOT NULL DEFAULT '0',
  `config` enum('0','1') DEFAULT '0',
  PRIMARY KEY (`grupo`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8mb3_general

```

# TODO:
-- #S4-10045 - Wireguard users/peers

CREATE TABLE `vpn_menu_wireguard_peers` (
     `grupo` varchar(100) NOT NULL,
  `cad` enum('0','1') DEFAULT '0',
  `edit` enum('0','1') DEFAULT '0',
  `del` enum('0','1') DEFAULT '0',
  `export` enum('0','1') DEFAULT '0',
    PRIMARY KEY (`grupo`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;


-- #S4-10045 - Wireguard servers/ifaces


CREATE TABLE `vpn_menu_wireguard_interfaces` (
     `grupo` varchar(100) NOT NULL,
  `cad` enum('0','1') DEFAULT '0',
  `edit` enum('0','1') DEFAULT '0',
  `del` enum('0','1') DEFAULT '0',
  `export` enum('0','1') DEFAULT '0',
    PRIMARY KEY (`grupo`)
) ENGINE=InnoDB DEFAULT CHARSET
