# ss - Explorador de Sockets de Red

**ss** es el **investigador de conexiones** de red en Arch Linux. Es el reemplazo moderno de netstat, mostrando información detallada sobre sockets de red, conexiones activas y puertos en escucha.

## **¿Qué es ss?**

ss te permite:
- **Ver conexiones de red activas** (TCP, UDP)
- **Identificar puertos abiertos** y servicios en escucha
- **Monitorear tráfico de red** y estado de conexiones
- **Diagnosticar problemas de red** y seguridad

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, ss es esencial para verificar servicios de red, diagnosticar problemas de conectividad, y monitorear la seguridad del sistema durante y después de la instalación.

### **Uso Básico**

```bash
# Ver todas las conexiones
ss

# Conexiones TCP
ss -t

# Conexiones UDP  
ss -u

# Puertos en escucha
ss -l

# Información numérica (sin resolver nombres)
ss -n
```

## **Opciones Principales**

### **Tipos de Conexiones**

```bash
# TCP únicamente
ss -t

# UDP únicamente
ss -u

# TCP y UDP
ss -tu

# Sockets Unix (locales)
ss -x

# Todos los tipos
ss -a
```

### **Estados de Conexión**

```bash
# Solo conexiones en escucha
ss -l

# Solo conexiones establecidas
ss state established

# Conexiones específicas por estado
ss state listening
ss state connected
ss state syn-sent
ss state syn-recv
```

## **Información Detallada**

### **Ver Procesos y PIDs**

```bash
# Mostrar procesos asociados
ss -p

# TCP con procesos
ss -tp

# Puertos en escucha con procesos
ss -tlp

# Ejemplo de salida:
# LISTEN  0  128  *:22  *:*  users:(("sshd",pid=1234,fd=3))
# ESTAB   0  0    192.168.1.100:22  192.168.1.50:45678  users:(("sshd",pid=5678,fd=4))
```

### **Información Numérica vs Nombres**

```bash
# Con nombres de host y servicio (por defecto)
ss -t

# Solo números (más rápido)
ss -tn

# Ejemplo comparativo:
# Con nombres:    ssh     establecida
# Sin nombres:    :22     ESTAB
```

### **Información Extendida**

```bash
# Información detallada de memoria
ss -m

# Información de opciones de socket
ss -o

# Información de estadísticas
ss -i

# Toda la información disponible
ss -eipn
```

## **Casos de Uso Prácticos**

### **Verificar Servicios Durante Instalación**

```bash
# Después de instalar SSH en Arch
sudo systemctl start sshd

# Verificar que SSH está escuchando
ss -tln | grep :22
# Salida esperada: LISTEN  0  128  *:22  *:*

# Verificar con información de proceso
ss -tlp | grep ssh
# Salida: LISTEN  0  128  *:22  *:*  users:(("sshd",pid=1234,fd=3))
```

### **Diagnosticar Problemas de Red**

```bash
# Ver todas las conexiones activas
ss -tuln

# Buscar conexiones sospechosas
ss -t | grep ESTAB

# Ver qué procesos tienen conexiones abiertas
ss -tp | grep -v LISTEN

# Ejemplo de análisis:
# ¿Hay conexiones inesperadas?
# ¿Servicios corriendo que no deberían estar?
# ¿Conexiones desde IPs desconocidas?
```

### **Monitoreo de Servicios Web**

```bash
# Verificar servidor web
ss -tln | grep :80   # HTTP
ss -tln | grep :443  # HTTPS

# Ver conexiones activas al servidor web
ss -t | grep :80

# Contar conexiones por servicio
ss -t | grep :80 | wc -l
```

## **Filtros y Búsquedas**

### **Filtrar por Puerto**

```bash
# Puerto específico
ss -tln sport = :22
ss -tln dport = :80

# Rango de puertos
ss -tln sport ge :1024
ss -tln sport le :1023

# Múltiples puertos
ss -tln '( sport = :22 or sport = :80 )'
```

### **Filtrar por Dirección IP**

```bash
# Conexiones desde IP específica
ss -t src 192.168.1.100

# Conexiones hacia IP específica
ss -t dst 192.168.1.50

# Red específica
ss -t src 192.168.1.0/24

# Localhost únicamente
ss -tl src 127.0.0.1
```

### **Filtros Combinados**

```bash
# Conexiones SSH establecidas
ss -t state established '( dport = :22 or sport = :22 )'

# Servicios web en escucha
ss -tln '( sport = :80 or sport = :443 )'

# Conexiones externas (no localhost)
ss -t '( not src 127.0.0.1 and not dst 127.0.0.1 )'
```

## **Comparación con netstat**

### **Equivalencias Comunes**

```bash
# netstat vs ss equivalentes:

# netstat -tuln
ss -tuln

# netstat -tulp  
ss -tulp

# netstat -i
ip link show

# netstat -r
ip route show

# netstat -s
ss -s
```

### **Ventajas de ss sobre netstat**

```bash
# ss es más rápido
time ss -tuln
time netstat -tuln

# ss tiene mejor filtrado
ss -t state established
# (netstat requiere grep adicional)

# ss muestra más información
ss -i  # Info de opciones TCP
ss -m  # Info de memoria de sockets
```

## **Monitoreo de Seguridad**

### **Identificar Conexiones Sospechosas**

```bash
# Ver todas las conexiones establecidas
ss -t state established

# Buscar conexiones hacia puertos inusuales
ss -tn | grep -E ":(4444|6666|31337)"

# Ver procesos con muchas conexiones
ss -tp | awk '{print $NF}' | sort | uniq -c | sort -nr

# Conexiones desde IPs externas
ss -t '( not src 127.0.0.1 and not src 192.168.0.0/16 and not src 10.0.0.0/8 )'
```

### **Auditoría de Puertos Abiertos**

```bash
# Servicios en escucha (potenciales vectores de ataque)
ss -tln

# Verificar que solo servicios necesarios están abiertos
echo "=== Puertos TCP en escucha ==="
ss -tln | awk '$1=="LISTEN" {print $4}' | sed 's/.*://' | sort -n | uniq

echo "=== Puertos UDP en escucha ==="
ss -uln | awk '$1=="UNCONN" {print $4}' | sed 's/.*://' | sort -n | uniq
```

### **Detectar Servicios Inesperados**

```bash
# Lista de puertos comúnmente seguros
expected_ports="22 80 443"

# Verificar puertos inesperados
ss -tln | grep LISTEN | while read line; do
    port=$(echo $line | awk '{print $4}' | sed 's/.*://')
    if ! echo $expected_ports | grep -q "$port"; then
        echo "⚠️ Puerto inesperado abierto: $port"
        ss -tlp | grep ":$port"
    fi
done
```

## **Información de Estadísticas**

### **Resumen de Sockets**

```bash
# Estadísticas generales
ss -s

# Salida típica:
# Total: 147 (kernel 0)
# TCP:   12 (estab 4, closed 3, orphaned 0, synrecv 0, timewait 2/0), ports 0
# Transport Total     IP        IPv6
# *         147       -         -
# RAW       1         0         1
# UDP       8         5         3
# TCP       9         6         3
# INET      18        11        7
# FRAG      0         0         0
```

### **Información Detallada de Conexiones**

```bash
# Información de memoria por socket
ss -tm

# Información de opciones TCP
ss -to

# Información de timers
ss -te

# Información completa
ss -temo
```

## **Monitoreo en Tiempo Real**

### **Observar Cambios en Tiempo Real**

```bash
# Actualizar cada 2 segundos
watch -n 2 'ss -tuln'

# Monitorear conexiones específicas
watch -n 1 'ss -t state established'

# Contar conexiones activas
watch -n 5 'echo "Conexiones TCP: $(ss -t | grep -c ESTAB)"'
```

### **Logging de Conexiones**

```bash
# Registrar conexiones cada minuto
log_connections() {
    echo "$(date): $(ss -t | grep -c ESTAB) conexiones establecidas" >> /tmp/connections.log
    ss -tn state established >> /tmp/connections.log
    echo "---" >> /tmp/connections.log
}

# Ejecutar cada minuto
while true; do
    log_connections
    sleep 60
done
```

## **Troubleshooting con ss**

### **Servicio No Responde**

```bash
# 1. Verificar que el servicio está escuchando
ss -tln | grep :puerto

# 2. Verificar el proceso asociado
ss -tlp | grep :puerto

# 3. Verificar en qué interfaces escucha
ss -tln | grep :puerto
# Si muestra 127.0.0.1:puerto solo escucha en localhost
# Si muestra *:puerto escucha en todas las interfaces
```

### **Conexión Rechazada**

```bash
# 1. Verificar que hay algo escuchando
ss -tln | grep :puerto

# 2. Verificar firewall
sudo iptables -L | grep puerto

# 3. Verificar desde donde se permite conexión
ss -tln | grep :puerto
# Ver si escucha en la IP correcta
```

### **Demasiadas Conexiones**

```bash
# Ver límites del sistema
cat /proc/sys/net/core/somaxconn

# Conexiones por estado
ss -t | awk '{print $1}' | sort | uniq -c

# Procesos con más conexiones
ss -tp | grep -o 'pid=[0-9]*' | sort | uniq -c | sort -nr
```

## **Análisis de Rendimiento**

### **Información de Buffers**

```bash
# Información de memoria de sockets
ss -tm | head -20

# Buscar sockets con buffers llenos
ss -tm | grep -E "(rmem:|wmem:)" | awk '$NF > 65536'
```

### **Opciones TCP Avanzadas**

```bash
# Ver opciones TCP activas
ss -to | head -20

# Información de congestion control
ss -ti | grep -E "(cubic|reno|bbr)"

# RTT y otras métricas
ss -ti | grep rtt
```

## **Enlaces Relacionados**

- [[netstat]] - Comando tradicional para información de red
- [[firewall]] - Configurar reglas para puertos mostrados por ss
- [[systemctl]] - Gestionar servicios de red
- [[ps]] - Ver procesos asociados a conexiones de red
- [[top]] - Monitorear recursos de procesos de red
- [[lsof]] - Ver archivos abiertos por procesos de red