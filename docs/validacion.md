# Validación — Práctica #3 / Infraestructura #1

Este documento resume las pruebas ejecutadas sobre la topología final.

## Matriz de validación

| Prueba | Origen | Destino | Resultado esperado | Resultado observado | Evidencia |
|---|---|---|---|---|---|
| SSH desde VLAN10 | PC-VLAN10 | WEB-CAJA `10.21.75.2:22` | Bloqueado | `Connection timed out` | `05-vlan10-ssh-bloqueado.png` |
| SSH desde VLAN20 | PC-VLAN20 | WEB-CAJA `10.21.75.2:22` | Permitido | Login SSH exitoso | `06-vlan20-ssh-permitido.png` |
| HTTP Inventario desde VLAN10 | PC-VLAN10 | `10.21.75.3:80` | Bloqueado por Web Filter | `HTTP/1.1 403 Forbidden` | `07-vlan10-inventario-bloqueado.png` |
| DMZ hacia VLAN10 | WEB-CAJA | `10.21.74.10` | Bloqueado | 100% packet loss | `09-dmz-usuarios-bloqueados.png` |
| DMZ hacia VLAN20 | WEB-CAJA | `10.21.74.141` | Bloqueado | 100% packet loss | `09-dmz-usuarios-bloqueados.png` |
| Ubuntu updates | WEB-CAJA | Ubuntu repositories | Permitido | Repositorios alcanzados correctamente | `08-dmz-updates-permitidos-internet-bloqueado.png` |
| Internet general desde DMZ | WEB-CAJA | `example.com:80` | Bloqueado | Timeout | `08-dmz-updates-permitidos-internet-bloqueado.png` |
| MariaDB | DB-SV | Base local | Operativo | `SELECT * FROM productos;` retorna 3 filas | `10-base-datos-mariadb.png` |
| Apache WEB-CAJA | WEB-CAJA | localhost | Operativo | HTML `Cash Register System` | `11a-web-caja-apache.png` |
| Apache WEB-INVENTARIO | WEB-INV | localhost | Operativo | HTML `Inventory System` | `11b-web-inventario-apache.png` |
| NAT/PAT ISP | ISP / FortiGate | Internet | Operativo | Traducciones activas + ping Google | `12-isp-nat-pat-internet.png` |
| Port Security usuarios | SW-USERS | Gi0/1, Gi0/2 | 1 MAC, 0 violaciones | Correcto | `13-sw-users-port-security.png` |
| Port Security servidores | SW-SERVERS | Gi0/1-3 | 1 MAC, 0 violaciones | Correcto | `03-sw-servers-port-security.png` |
| Ruta por defecto FortiGate | FortiGate | ISP `21.74.3.1` | Instalada | `S* 0.0.0.0/0 via 21.74.3.1` | `19-fortigate-routing-table.png` |
| Internet FortiGate | FortiGate | `8.8.8.8` | Permitido | 0% packet loss | `20-fortigate-internet-dns.png` |
| DNS FortiGate | FortiGate | `google.com` | Resolver | Resuelve y responde | `20-fortigate-internet-dns.png` |

## Comandos utilizados

### VLAN10

```bash
ssh -o ConnectTimeout=5 ariel@10.21.75.2
curl -I http://10.21.75.3
ip addr show ens3
ip route
```

### VLAN20

```bash
ssh ariel@10.21.75.2
ip addr show ens3
ip route
```

### DMZ

```bash
sudo apt update
curl -I --max-time 5 http://example.com
ping -c 3 10.21.74.10
ping -c 3 10.21.74.141
```

### Servidores

```bash
curl http://localhost
systemctl is-active apache2
systemctl is-active ssh
systemctl is-active mariadb
```

### MariaDB

```sql
USE infraestructura1;
SELECT * FROM productos;
```

### Cisco IOS

```text
show vlan brief
show interfaces trunk
show port-security
show interfaces status
show ip nat statistics
show ip nat translations
ping google.com
```

### FortiGate

```text
get router info routing-table all
execute ping 8.8.8.8
execute ping google.com
```
