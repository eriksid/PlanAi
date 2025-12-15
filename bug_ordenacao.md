### Bug de ordenação

## Decrição

Peers: Problema na ordenação das colunas:
* Status
* Tipo

## SQL da ordenação status
```sql
SELECT SQL_CALC_FOUND_ROWS wgp.id, wgp.interface_id, wgp.status, wgp.peer_type, wgp.conn_name, wgp.description, wgp.conn_address_cidr, wgp.allowed_networks, wgp.endpoint_addr, wgp.keepalive_value, wgp.public_key, wgp.private_key, wgp.preshared_key, wgp.created_at, wgp.updated_at, wgi.name,wgi.iface_name as iface_name, wgi.status FROM vpn.wireguard_peers AS wgp LEFT JOIN vpn.wireguard_interfaces AS wgi ON wgi.id = wgp.interface_id   ORDER BY wgp.conn_name ASC LIMIT 0 , 50
```

## SQL da ordenação tipo
```sql
SELECT SQL_CALC_FOUND_ROWS wgp.id, wgp.interface_id, wgp.status, wgp.peer_type, wgp.conn_name, wgp.description, wgp.conn_address_cidr, wgp.allowed_networks, wgp.endpoint_addr, wgp.keepalive_value, wgp.public_key, wgp.private_key, wgp.preshared_key, wgp.created_at, wgp.updated_at, wgi.name,wgi.iface_name as iface_name, wgi.status FROM vpn.wireguard_peers AS wgp LEFT JOIN vpn.wireguard_interfaces AS wgi ON wgi.id = wgp.interface_id   ORDER BY wgp.conn_name ASC LIMIT 0 , 50



## TODO

* Verificar aonde está sendo feita a ordenação
* Ajustar par mandar o nome da coluna correta pois todas mandam conn_name