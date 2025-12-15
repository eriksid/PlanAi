# 🔵 **Exemplo Completo — Site-to-Site**

## **Topologia**

**Servidor (Matriz)**

* IP WG: `10.20.0.1`
* LAN: `192.168.1.0/24`
* Porta: `51820`

**Cliente (Filial)**

* IP WG: `10.20.0.2`
* LAN: `192.168.2.0/24`

---

## 🟦 **server.conf — Matriz**

```ini
[Interface]
PrivateKey = <SERVER_PRIVATE_KEY>
Address    = 10.20.0.1/24
ListenPort = 51820

# Peer da Filial
[Peer]
PublicKey  = <CLIENT_PUBLIC_KEY>
AllowedIPs = 10.20.0.2/32, 192.168.2.0/24
```

---

## 🟩 **client.conf — Filial**

```ini
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address    = 10.20.0.2/24

# Peer da Matriz
[Peer]
PublicKey  = <SERVER_PUBLIC_KEY>
Endpoint   = <SERVER_PUBLIC_IP>:51820
AllowedIPs = 10.20.0.1/32, 192.168.1.0/24
PersistentKeepalive = 25
```

---

# 🔵 **Cenário: Site → Cliente (Acesso Remoto)**

* Servidor só expõe o acesso VPN
* Cliente acessa o site
* Sem troca de rotas entre LANs

---

## 🟦 **server.conf – Site**

```ini
[Interface]
PrivateKey = <SERVER_PRIVATE_KEY>
Address    = 10.30.0.1/24
ListenPort = 51820

# Cliente
[Peer]
PublicKey  = <CLIENT_PUBLIC_KEY>
AllowedIPs = 10.30.0.2/32
```

---

## 🟩 **client.conf – Cliente**

```ini
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address    = 10.30.0.2/24

# Servidor
[Peer]
PublicKey  = <SERVER_PUBLIC_KEY>
Endpoint   = <SERVER_PUBLIC_IP>:51820
AllowedIPs = 0.0.0.0/0, ::/0   # Roteia tudo pelo servidor
PersistentKeepalive = 25
```


