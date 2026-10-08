# Plan de direccionamiento — Práctica #3 / Infraestructura #1

## Redes

| Segmento | Red | Máscara | Gateway | Función |
|---|---|---|---|---|
| WAN FortiGate | `21.74.3.0/30` | `255.255.255.252` | ISP `21.74.3.1` | Enlace ISP ↔ FortiGate |
| VLAN10 | `10.21.74.0/25` | `255.255.255.128` | `10.21.74.1` | Usuarios restringidos |
| VLAN20 | `10.21.74.128/25` | `255.255.255.128` | `10.21.74.129` | Usuarios con SSH hacia DMZ |
| VLAN30 | `10.21.75.0/28` | `255.255.255.240` | `10.21.75.1` | DMZ de servidores |
| Administración | `192.168.79.0/24` | `255.255.255.0` | Según VMnet | Acceso GUI a FortiGate |
| GNS3 NAT | Dinámica | — | Dinámico | Salida del ISP a Internet |

## Equipos

| Equipo | Interfaz / VLAN | Dirección | Gateway / ruta |
|---|---|---|---|
| `ISP-2174` | Fa0/0 | DHCP | Gateway aprendido de GNS3 NAT |
| `ISP-2174` | Fa1/0 | `21.74.3.1/30` | Directamente conectada |
| `FG-2174` | port1 / WAN-ISP | `21.74.3.2/30` | Default route `21.74.3.1` |
| `FG-2174` | VLAN10-USERS | `10.21.74.1/25` | Gateway VLAN10 |
| `FG-2174` | VLAN20-USERS | `10.21.74.129/25` | Gateway VLAN20 |
| `FG-2174` | VLAN30-DMZ | `10.21.75.1/28` | Gateway DMZ |
| `FG-2174` | port3 | `192.168.79.99/24` | Administración |
| `PC-VLAN10-2174` | ens3 | `10.21.74.11/25` observado por DHCP | `10.21.74.1` |
| `PC-VLAN20-2174` | ens3 | `10.21.74.141/25` observado por DHCP | `10.21.74.129` |
| `WEB-CAJA-2174` | ens3 | `10.21.75.2/28` | `10.21.75.1` |
| `WEB-INV-2174` | ens3 | `10.21.75.3/28` | `10.21.75.1` |
| `DB-SV-2174` | ens3 | `10.21.75.4/28` | `10.21.75.1` |

## DHCP

### VLAN10-USERS

- Pool: `10.21.74.10 - 10.21.74.100`
- Gateway: `10.21.74.1`
- Máscara: `/25`
- DNS: `8.8.8.8` y `1.1.1.1`

### VLAN20-USERS

- Pool: `10.21.74.140 - 10.21.74.230`
- Gateway: `10.21.74.129`
- Máscara: `/25`
- DNS: servicio DNS predeterminado del FortiGate

## VLANs y puertos

### SW-USERS-2174

| Puerto | Modo | VLAN | Uso |
|---|---|---|---|
| Gi0/0 | trunk | 10,20,30 | FortiGate port2 |
| Gi0/1 | access | 10 | PC-VLAN10-2174 |
| Gi0/2 | access | 20 | PC-VLAN20-2174 |
| Gi1/2 | trunk | 30; native 999 | SW-SERVERS-2174 |
| Gi0/3, Gi1/0, Gi1/1, Gi1/3 | access / shutdown | 999 | No utilizados |

### SW-SERVERS-2174

| Puerto | Modo | VLAN | Uso |
|---|---|---|---|
| Gi0/1 | access | 30 | WEB-CAJA-2174 |
| Gi0/2 | access | 30 | WEB-INV-2174 |
| Gi0/3 | access | 30 | DB-SV-2174 |
| Gi1/2 | trunk | 30; native 999 | SW-USERS-2174 |
| Gi0/0, Gi1/0, Gi1/1, Gi1/3 | access / shutdown | 999 | No utilizados |
