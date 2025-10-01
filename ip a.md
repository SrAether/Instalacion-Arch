El comando `ip a` (abreviación de `ip addr`) es tu **explorador de interfaces de red**. Es la herramienta moderna para ver y configurar direcciones IP, interfaces de red y su estado. Es fundamental durante la instalación de Arch Linux para verificar configuración de red y diagnóstico de conectividad.

---
### ¿Qué Hace IP A? 🌐
**IP A** (IP Address) muestra información detallada sobre:
1. **Interfaces de red** activas e inactivas
2. **Direcciones IP** asignadas (IPv4 e IPv6)
3. **Estado** de las interfaces (UP/DOWN)
4. **Direcciones MAC** de cada interfaz
5. **MTU** y otros parámetros de red

### Sintaxis Básica
```bash
ip a                    # Mostrar todas las interfaces
ip addr                 # Forma completa
ip addr show            # Equivalente
ip a show interfaz      # Mostrar interfaz específica
```

### Uso Básico
```bash
# Ver todas las interfaces
ip a

# Ver interfaz específica
ip a show eth0
ip a show wlan0
ip a show lo

# Formato más legible
ip -c a                 # Con colores
ip -br a                # Formato breve
```

### En el Contexto de la Instalación de Arch Linux

#### **Verificar Interfaces Durante Instalación**
```bash
# Primer comando después de arrancar desde ISO
ip a

# Verificar que hay interfaces disponibles
# Buscar: eth0, enp0s3, wlan0, etc.
```

#### **Después de Configurar Red con iwctl**
```bash
# Verificar que wlan0 obtuvo IP
ip a show wlan0

# Debe mostrar algo como:
# inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic
```

#### **Post-Instalación: Verificar Configuración**
```bash
# Verificar interfaces después de reiniciar
ip a

# Comprobar que systemd-networkd funciona
ip a show eth0
ip a show enp0s3       # Nombres predictivos de interfaces
```

#### **Diagnóstico de Problemas de Red**
```bash
# Ver si interfaces están UP
ip a | grep "state UP"

# Verificar direcciones asignadas
ip a | grep "inet "

# Buscar interfaces sin configurar
ip a | grep "state DOWN"
```

### Interpretar la Salida de IP A

#### **Ejemplo de Salida Típica**
```bash
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether aa:bb:cc:dd:ee:ff brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic eth0
       valid_lft 86394sec preferred_lft 86394sec
    inet6 fe80::a8bb:ccff:fedd:eeff/64 scope link 
       valid_lft forever preferred_lft forever

3: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 11:22:33:44:55:66 brd ff:ff:ff:ff:ff:ff
```

#### **Desglose de la Información**

**Línea de Interfaz:**
```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
```
- `2:` - Índice de la interfaz
- `eth0` - Nombre de la interfaz
- `<BROADCAST,MULTICAST,UP,LOWER_UP>` - Flags/capacidades
- `mtu 1500` - Tamaño máximo de unidad de transmisión
- `state UP` - Estado de la interfaz

**Dirección MAC:**
```
link/ether aa:bb:cc:dd:ee:ff brd ff:ff:ff:ff:ff:ff
```
- `aa:bb:cc:dd:ee:ff` - Dirección MAC de la interfaz
- `brd ff:ff:ff:ff:ff:ff` - Dirección de broadcast

**Dirección IPv4:**
```
inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic eth0
```
- `192.168.1.100/24` - IP y máscara de subred
- `brd 192.168.1.255` - Dirección de broadcast
- `scope global` - Alcance de la dirección
- `dynamic` - Asignada por DHCP

### Estados de Interfaces y Su Significado

#### **Estados Principales**
```bash
# Interface activa y funcionando
state UP                # Interface operativa

# Interface inactiva
state DOWN              # Interface deshabilitada

# Interface desconocida
state UNKNOWN           # Típico en loopback

# Interface en proceso
state DORMANT           # Interface "durmiendo"
```

#### **Flags Importantes**
```bash
UP                      # Interface habilitada
LOWER_UP                # Enlace físico detectado
BROADCAST               # Soporta broadcast
MULTICAST               # Soporta multicast
LOOPBACK                # Interface de loopback
RUNNING                 # Interface operativa
NOARP                   # No usa ARP
```

### Casos de Uso Específicos en Arch Linux

#### **1. Verificar Conectividad Ethernet**
```bash
# Ver si cable está conectado
ip a show eth0 | grep "LOWER_UP"

# Si no aparece LOWER_UP, cable desconectado o problema físico
ip a show eth0
# Buscar: <BROADCAST,MULTICAST> sin UP = cable desconectado
# Buscar: <BROADCAST,MULTICAST,UP,LOWER_UP> = cable conectado
```

#### **2. Verificar Configuración DHCP**
```bash
# Ver si obtuvo IP automáticamente
ip a show eth0 | grep "inet.*dynamic"

# Ejemplo de salida correcta:
# inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic eth0

# Si no hay "dynamic", IP está configurada estáticamente
```

#### **3. Diagnosticar Problemas Wi-Fi**
```bash
# Ver estado de interfaz Wi-Fi
ip a show wlan0

# Estados típicos:
# state DOWN = interfaz deshabilitada
# state UP sin IP = conectado pero sin DHCP
# state UP con IP = completamente funcional
```

#### **4. Verificar Configuración IPv6**
```bash
# Ver direcciones IPv6
ip a | grep "inet6"

# Tipos de direcciones IPv6:
# ::1/128 = loopback IPv6
# fe80::/64 = link-local (autoconfigurada)
# 2001::/64 = global (de ISP o configurada)
```

### Comandos Relacionados para Diagnóstico

#### **Ver Interfaces Específicas**
```bash
# Solo interfaces UP
ip a | grep -A 10 "state UP"

# Solo direcciones IP
ip a | grep "inet " | awk '{print $2}'

# Ver solo IPv4
ip -4 a

# Ver solo IPv6
ip -6 a
```

#### **Información Resumida**
```bash
# Formato breve y claro
ip -br a
# Salida tipo:
# lo    UNKNOWN   127.0.0.1/8 ::1/128 
# eth0  UP        192.168.1.100/24 fe80::a8bb:ccff:fedd:eeff/64

# Con colores para mejor visualización
ip -c -br a
```

#### **Interfaces Activas Solamente**
```bash
# Solo interfaces UP
ip -br a | grep UP

# Solo interfaces con IP asignada
ip a | grep -B 1 "inet " | grep "^[0-9]"
```

### Combinando con Otros Comandos

#### **Verificar Conectividad Completa**
```bash
# Script de verificación
echo "=== Interfaces de Red ==="
ip -br a

echo -e "\n=== Rutas ==="
ip route

echo -e "\n=== DNS ==="
cat /etc/resolv.conf

echo -e "\n=== Conectividad ==="
ping -c 2 8.8.8.8
```

#### **Monitoreo de Cambios**
```bash
# Ver cambios en tiempo real
ip monitor

# Ver solo cambios de direcciones
ip monitor address

# Ver cambios de rutas
ip monitor route
```

#### **Información de Estadísticas**
```bash
# Ver estadísticas de interfaces
ip -s link

# Estadísticas detalladas
ip -s -s link

# Información específica de interfaz
ip -s link show eth0
```

### Configuración de Interfaces (Comandos Avanzados)

#### **Activar/Desactivar Interfaces**
```bash
# Activar interfaz
sudo ip link set eth0 up

# Desactivar interfaz
sudo ip link set eth0 down

# Verificar cambio
ip a show eth0
```

#### **Asignar Direcciones IP**
```bash
# Asignar IP estática (temporal)
sudo ip addr add 192.168.1.50/24 dev eth0

# Eliminar dirección IP
sudo ip addr del 192.168.1.50/24 dev eth0

# Verificar cambios
ip a show eth0
```

#### **Configurar MTU**
```bash
# Cambiar MTU
sudo ip link set eth0 mtu 1450

# Verificar cambio
ip a show eth0 | grep mtu
```

### Troubleshooting Común

#### **Problema: No Hay Interfaces de Red**
```bash
# Verificar que existen interfaces
ip link

# Si no aparecen, verificar drivers
lspci | grep -i network
lsusb | grep -i network

# Cargar módulos de red si es necesario
sudo modprobe e1000e  # Intel
sudo modprobe r8169   # Realtek
```

#### **Problema: Interfaz UP pero Sin IP**
```bash
# Ver estado detallado
ip a show eth0

# Si está UP pero sin inet, problema con DHCP
sudo dhcpcd eth0           # Intentar DHCP manual
# o
sudo systemctl restart systemd-networkd
```

#### **Problema: Direcciones IPv6 Extrañas**
```bash
# Ver solo IPv4
ip -4 a

# Deshabilitar IPv6 temporalmente si causa problemas
echo 1 | sudo tee /proc/sys/net/ipv6/conf/all/disable_ipv6
```

#### **Problema: MTU Incorrecto**
```bash
# Verificar MTU actual
ip a show eth0 | grep mtu

# Probar diferentes MTUs
ping -M do -s 1472 google.com      # Test MTU 1500
ping -M do -s 1450 google.com      # Test MTU 1478

# Ajustar si es necesario
sudo ip link set eth0 mtu 1450
```

### Scripts Útiles con IP A

#### **Script de Estado de Red**
```bash
#!/bin/bash
# network_status.sh

echo "=== Estado de Interfaces de Red ==="
echo

# Función para mostrar estado de interfaz
show_interface() {
    local iface=$1
    local status=$(ip -br a show $iface 2>/dev/null)
    
    if [ -n "$status" ]; then
        echo "$status"
        
        # Ver si tiene conectividad
        local ip=$(echo "$status" | awk '{print $3}' | cut -d'/' -f1)
        if [ -n "$ip" ] && [ "$ip" != "127.0.0.1" ]; then
            if ping -c 1 -W 2 8.8.8.8 >/dev/null 2>&1; then
                echo "  ✅ Conectividad: OK"
            else
                echo "  ❌ Conectividad: Sin internet"
            fi
        fi
        echo
    fi
}

# Mostrar todas las interfaces activas
for iface in $(ip -br link | grep UP | awk '{print $1}'); do
    show_interface $iface
done
```

#### **Monitor de Cambios de Red**
```bash
#!/bin/bash
# network_monitor.sh

echo "Monitoreando cambios en interfaces de red..."
echo "Presiona Ctrl+C para parar"
echo

# Estado inicial
echo "Estado inicial:"
ip -br a
echo "------------------------"

# Monitorear cambios
ip monitor address | while read line; do
    echo "$(date '+%H:%M:%S'): $line"
    
    # Mostrar estado actualizado cada cambio
    if echo "$line" | grep -q "inet"; then
        echo "Estado actual:"
        ip -br a | grep -v lo
        echo "------------------------"
    fi
done
```

### Diferencias con Comandos Obsoletos

#### **IP A vs IFCONFIG**
```bash
# Comando moderno (preferido)
ip a                            # Ver todas las interfaces
ip a show eth0                  # Ver interfaz específica

# Comando obsoleto (evitar)
ifconfig                        # Puede no estar instalado
ifconfig eth0                   # Funcionalidad limitada

# ifconfig no siempre está disponible en instalaciones mínimas
```

#### **Ventajas de IP A**
```bash
# Más información por defecto
ip a                            # Muestra IPv4, IPv6, MAC, MTU, flags

# Mejor formato para scripts
ip -br a                        # Formato parseable

# Funcionalidad extendida
ip -s link                      # Estadísticas
ip -c a                         # Colores
```

### Configuración Permanente vs Temporal

#### **Cambios Temporales (ip command)**
```bash
# Se pierden al reiniciar
sudo ip addr add 192.168.1.50/24 dev eth0
sudo ip link set eth0 up
```

#### **Configuración Permanente**
```bash
# systemd-networkd
sudo nano /etc/systemd/network/20-ethernet.network

# NetworkManager
nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.1.50/24

# netctl
sudo nano /etc/netctl/ethernet-static
```

### Buenas Prácticas

#### **1. Verificar Antes de Configurar**
```bash
# Siempre ver estado actual primero
ip a
ip route
```

#### **2. Usar Formato Legible**
```bash
# Para diagnóstico rápido
ip -c -br a                     # Colores y formato breve

# Para información detallada
ip a show interfaz_específica
```

#### **3. Combinar con Otras Herramientas**
```bash
# Diagnóstico completo
ip a && ip route && ping -c 1 8.8.8.8
```

#### **4. Documentar Configuración**
```bash
# Guardar configuración actual
ip a > ~/network_config_$(date +%Y%m%d).txt
```

---
### Enlaces Relacionados
- [[ping]] - Verificar conectividad después de configurar interfaces
- [[iwctl]] - Configurar Wi-Fi que luego se verifica con ip a
- [[systemd-networkd]] - Configuración permanente de red
- [[netstat]] - Ver conexiones activas de las interfaces



