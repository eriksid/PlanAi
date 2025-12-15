-- #S4-10045 - Permissoes Wireguard servers/interfaces
CREATE TABLE `S4`.`vpn_menu_wireguard_interfaces` (
  `grupo` varchar(100) NOT NULL,
  `cad` enum('0','1') DEFAULT '0',
  `edit` enum('0','1') DEFAULT '0',
  `del` enum('0','1') DEFAULT '0',
  `export` enum('0','1') DEFAULT '0',
  PRIMARY KEY (`grupo`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- #S4-10045 - Permissoes Wireguard users/peers
CREATE TABLE `S4`.`vpn_menu_wireguard_peers` (
  `grupo` varchar(100) NOT NULL,
  `cad` enum('0','1') DEFAULT '0',
  `edit` enum('0','1') DEFAULT '0',
  `del` enum('0','1') DEFAULT '0',
  `export` enum('0','1') DEFAULT '0',
  PRIMARY KEY (`grupo`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- #S4-10045 - Campos na tabela mod_vpn para WireGuard
ALTER TABLE `S4`.`mod_vpn` ADD COLUMN `perm_wireguard_interfaces` enum('0','1') DEFAULT '0' AFTER `perm_openvpnimporter`;
ALTER TABLE `S4`.`mod_vpn` ADD COLUMN `perm_wireguard_peers` enum('0','1') DEFAULT '0' AFTER `perm_wireguard_interfaces`;
UPDATE `S4`.`mod_vpn` SET perm_wireguard_peers = '1', perm_wireguard_interfaces = '1' WHERE perm_openvpnusuarios = '1';

-- #S4-10045 - Adicionando permissoes do grupo default grupos do openvpn 
INSERT INTO `S4`.`vpn_menu_wireguard_peers` (grupo, cad,edit,del, export) SELECT grupo, '1','1','1','1' FROM S4.vpn_menu_usuarios WHERE cad = '1';
INSERT INTO `S4`.`vpn_menu_wireguard_interfaces` (grupo, cad,edit,del, export) SELECT grupo, '1','1','1','1' FROM S4.vpn_menu_usuarios WHERE cad = '1';