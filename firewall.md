# firewall - Protección de Red y Seguridad

El **firewall** es tu **guardian de red**. Es un sistema de seguridad que controla el tráfico de red entrante y saliente basándose en reglas predeterminadas, siendo fundamental para proteger tu sistema Arch Linux de amenazas externas e internas.

## **¿Qué es un Firewall?**

Un firewall te permite:
- **Bloquear tráfico malicioso** y conexiones no autorizadas
- **Controlar qué servicios** son accesibles desde la red
- **Monitorear conexiones** entrantes y salientes
- **Implementar políticas de seguridad** específicas

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, configurar un firewall es esencial para proteger servicios como SSH, servidores web, bases de datos y cualquier aplicación que escuche en puertos de red.

### **Firewall en Arch Linux - UFW**

```bash
# Instalar UFW (Uncomplicated Firewall)
sudo pacman -S ufw

# Habilitar UFW
sudo systemctl enable ufw
sudo systemctl start ufw

# Verificar estado
sudo ufw status verbose
```

### **iptables - El Firewall Base**

```bash
# iptables es el sistema de firewall base en Linux
# UFW es una interfaz simplificada para iptables

# Ver reglas actuales de iptables
sudo iptables -L -n -v

# Ver reglas con números de línea
sudo iptables -L --line-numbers

# Verificar que iptables esté funcionando
sudo systemctl status iptables
```

## **Configuración Básica con UFW**

### **Comandos Fundamentales**

```bash
# Habilitar firewall
sudo ufw enable

# Deshabilitar firewall
sudo ufw disable

# Ver estado detallado
sudo ufw status verbose

# Resetear todas las reglas
sudo ufw --force reset

# Ver reglas numeradas
sudo ufw status numbered
```

### **Política por Defecto**

```bash
# Denegar todo el tráfico entrante por defecto
sudo ufw default deny incoming

# Permitir todo el tráfico saliente por defecto
sudo ufw default allow outgoing

# Denegar reenvío (forwarding)
sudo ufw default deny forward

# Ver configuración actual
sudo ufw status verbose
```

## **Reglas Básicas de Firewall**

### **Permitir Servicios Comunes**

```bash
# Permitir SSH (puerto 22)
sudo ufw allow ssh
sudo ufw allow 22

# Permitir HTTP (puerto 80)
sudo ufw allow http
sudo ufw allow 80

# Permitir HTTPS (puerto 443)
sudo ufw allow https
sudo ufw allow 443

# Permitir rango de puertos
sudo ufw allow 1000:2000/tcp

# Permitir desde IP específica
sudo ufw allow from 192.168.1.100

# Permitir desde subred
sudo ufw allow from 192.168.1.0/24
```

### **Bloquear Conexiones**

```bash
# Denegar puerto específico
sudo ufw deny 23

# Denegar desde IP específica
sudo ufw deny from 203.0.113.100

# Denegar protocolo específico
sudo ufw deny out 25

# Bloquear salida a puerto específico
sudo ufw deny out 1194
```

## **Casos de Uso en Arch Linux**

### **Servidor Web Seguro**

```bash
#!/bin/bash
# Configuración de firewall para servidor web

echo "=== Configurando Firewall para Servidor Web ==="

# Resetear configuración
sudo ufw --force reset

# Política por defecto
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir SSH desde red local únicamente
sudo ufw allow from 192.168.1.0/24 to any port 22

# Permitir HTTP y HTTPS desde cualquier lugar
sudo ufw allow 80
sudo ufw allow 443

# Permitir ping (ICMP)
sudo ufw allow in on any to any port 22 proto tcp

# Habilitar firewall
sudo ufw enable

# Mostrar configuración
sudo ufw status verbose

echo "✅ Firewall configurado para servidor web"
```

### **Estación de Trabajo Personal**

```bash
#!/bin/bash
# Configuración de firewall para uso personal

echo "=== Configurando Firewall Personal ==="

# Política restrictiva
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir loopback (importante para aplicaciones locales)
sudo ufw allow in on lo
sudo ufw allow out on lo

# Permitir SSH solo desde red local
sudo ufw allow from 192.168.0.0/16 to any port 22

# Permitir respuestas a conexiones establecidas
sudo ufw allow in on any to any port 22 proto tcp

# Bloquear escaneos de puertos comunes
sudo ufw deny 23   # Telnet
sudo ufw deny 135  # RPC
sudo ufw deny 139  # NetBIOS
sudo ufw deny 445  # SMB

# Habilitar firewall
sudo ufw enable

echo "✅ Firewall personal configurado"
```

### **Servidor de Base de Datos**

```bash
#!/bin/bash
# Configuración para servidor de base de datos

echo "=== Configurando Firewall para Servidor DB ==="

# Política restrictiva
sudo ufw default deny incoming
sudo ufw default allow outgoing

# SSH administrativa desde IPs específicas
sudo ufw allow from 192.168.1.10 to any port 22
sudo ufw allow from 192.168.1.11 to any port 22

# PostgreSQL desde servidores web
sudo ufw allow from 192.168.1.20 to any port 5432
sudo ufw allow from 192.168.1.21 to any port 5432

# MySQL/MariaDB desde servidores web
sudo ufw allow from 192.168.1.20 to any port 3306
sudo ufw allow from 192.168.1.21 to any port 3306

# Monitoreo desde servidor de gestión
sudo ufw allow from 192.168.1.100 to any port 3000

# Habilitar firewall
sudo ufw enable

echo "✅ Firewall de base de datos configurado"
```

## **Reglas Avanzadas**

### **Reglas por Interfaz de Red**

```bash
# Permitir tráfico en interfaz específica
sudo ufw allow in on eth0 to any port 80

# Denegar en interfaz específica
sudo ufw deny in on wlan0 to any port 22

# Reglas para VPN
sudo ufw allow in on tun0
sudo ufw allow out on tun0

# Ver interfaces disponibles
ip link show
```

### **Reglas Complejas**

```bash
# Permitir SSH con límite de conexiones
sudo ufw limit ssh

# Regla específica con protocolo
sudo ufw allow from 192.168.1.0/24 to any port 3306 proto tcp

# Permitir solo durante horario específico (requiere configuración adicional)
# Esto se hace mejor con iptables directamente

# Logging de reglas específicas
sudo ufw allow log 22
sudo ufw deny log from 203.0.113.0/24
```

### **Reglas con Comentarios**

```bash
# UFW con comentarios para mejor documentación
sudo ufw allow 22 comment 'SSH para administración'
sudo ufw allow 80 comment 'HTTP servidor web'
sudo ufw allow 443 comment 'HTTPS servidor web'
sudo ufw allow from 192.168.1.0/24 comment 'Red local completa'

# Ver reglas con comentarios
sudo ufw status verbose
```

## **Monitoreo y Logs**

### **Configurar Logging**

```bash
# Habilitar logging
sudo ufw logging on

# Niveles de logging
sudo ufw logging low     # Paquetes bloqueados
sudo ufw logging medium  # Paquetes bloqueados + límites alcanzados
sudo ufw logging high    # Paquetes bloqueados + permitidos + límites
sudo ufw logging full    # Todo el tráfico

# Ver logs del firewall
sudo journalctl -u ufw
tail -f /var/log/ufw.log

# Logs de iptables en el kernel
dmesg | grep -i "ufw\|iptables"
```

### **Análisis de Logs**

```bash
#!/bin/bash
# Script de análisis de logs de firewall

analyze_firewall_logs() {
    local log_file="/var/log/ufw.log"
    local days=${1:-1}
    
    echo "=== Análisis de Logs de Firewall (últimos $days días) ==="
    
    if [[ ! -f "$log_file" ]]; then
        echo "Archivo de log no encontrado. Habilita logging con: sudo ufw logging on"
        return 1
    fi
    
    # IPs que más intentos bloqueados tienen
    echo "Top 10 IPs bloqueadas:"
    grep "$(date --date="$days days ago" "+%b %d")" "$log_file" 2>/dev/null | \
    grep "BLOCK" | awk '{print $12}' | sed 's/SRC=//' | sort | uniq -c | sort -nr | head -10
    echo
    
    # Puertos más atacados
    echo "Puertos más atacados:"
    grep "$(date --date="$days days ago" "+%b %d")" "$log_file" 2>/dev/null | \
    grep "BLOCK" | awk '{print $15}' | sed 's/DPT=//' | sort | uniq -c | sort -nr | head -10
    echo
    
    # Protocolos bloqueados
    echo "Protocolos bloqueados:"
    grep "$(date --date="$days days ago" "+%b %d")" "$log_file" 2>/dev/null | \
    grep "BLOCK" | awk '{print $14}' | sed 's/PROTO=//' | sort | uniq -c | sort -nr
    echo
    
    # Resumen por hora
    echo "Actividad por hora:"
    grep "$(date "+%b %d")" "$log_file" 2>/dev/null | \
    awk '{print $3}' | awk -F: '{print $1":00"}' | sort | uniq -c | sort -k2n
}

analyze_firewall_logs 1
```

### **Monitor en Tiempo Real**

```bash
#!/bin/bash
# Monitor de firewall en tiempo real

monitor_firewall() {
    echo "=== Monitor de Firewall en Tiempo Real ==="
    echo "Presiona Ctrl+C para salir"
    echo
    
    # Crear pipes nombrados para monitoreo
    temp_log="/tmp/firewall_monitor.$$"
    
    # Función de limpieza
    cleanup() {
        rm -f "$temp_log"
        exit 0
    }
    trap cleanup EXIT
    
    # Monitor continuo
    while true; do
        clear
        echo "=== Monitor de Firewall - $(date) ==="
        echo
        
        # Estado actual
        echo "Estado del firewall:"
        sudo ufw status | head -10
        echo
        
        # Conexiones actuales
        echo "Conexiones activas:"
        ss -tuln | head -15
        echo
        
        # Últimos eventos bloqueados (si hay logs)
        if [[ -f /var/log/ufw.log ]]; then
            echo "Últimos bloqueos:"
            tail -5 /var/log/ufw.log | grep "BLOCK" | while read line; do
                echo "$(date '+%H:%M:%S'): $line"
            done
        fi
        
        # Estadísticas de iptables
        echo "Estadísticas de iptables:"
        sudo iptables -L -n -v | grep -E "Chain|DROP|REJECT" | head -10
        
        sleep 5
    done
}

monitor_firewall
```

## **Configuración Avanzada con iptables**

### **Reglas iptables Directas**

```bash
# Para casos donde UFW no es suficiente, usar iptables directamente

# Permitir tráfico establecido
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Permitir loopback
sudo iptables -A INPUT -i lo -j ACCEPT

# Límite de conexiones SSH (anti-brute force)
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP

# Bloquear ping floods
sudo iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 2 -j ACCEPT
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# Port knocking (secuencia de puertos para abrir SSH)
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 1001 -m recent --set --name KNOCK1
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 1002 -m recent --rcheck --seconds 5 --name KNOCK1 -m recent --set --name KNOCK2
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -m recent --rcheck --seconds 5 --name KNOCK2 -j ACCEPT
```

### **Script de Configuración Completa**

```bash
#!/bin/bash
# Script de configuración avanzada de firewall

setup_advanced_firewall() {
    echo "=== Configuración Avanzada de Firewall ==="
    
    # Verificar si se ejecuta como root
    if [[ $EUID -ne 0 ]]; then
        echo "Este script debe ejecutarse como root"
        exit 1
    fi
    
    # Backup de reglas actuales
    iptables-save > /etc/iptables/iptables.rules.backup.$(date +%Y%m%d)
    
    # Limpiar reglas existentes
    iptables -F
    iptables -X
    iptables -t nat -F
    iptables -t nat -X
    iptables -t mangle -F
    iptables -t mangle -X
    
    # Política por defecto
    iptables -P INPUT DROP
    iptables -P FORWARD DROP
    iptables -P OUTPUT ACCEPT
    
    # Permitir loopback
    iptables -A INPUT -i lo -j ACCEPT
    iptables -A OUTPUT -o lo -j ACCEPT
    
    # Permitir tráfico establecido y relacionado
    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    
    # Protección contra ataques comunes
    # SYN flood protection
    iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 3 -j ACCEPT
    
    # Ping flood protection
    iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 2 -j ACCEPT
    
    # Port scan protection
    iptables -A INPUT -m state --state NEW -p tcp --tcp-flags ALL ALL -j DROP
    iptables -A INPUT -m state --state NEW -p tcp --tcp-flags ALL NONE -j DROP
    
    # Servicios específicos
    # SSH con protección anti-brute force
    iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set --name SSH
    iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 --name SSH -j DROP
    iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    
    # HTTP/HTTPS
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    
    # Logging de paquetes rechazados
    iptables -A INPUT -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "iptables_INPUT: " --log-level 6
    iptables -A FORWARD -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "iptables_FORWARD: " --log-level 6
    
    # Guardar reglas
    iptables-save > /etc/iptables/iptables.rules
    
    # Habilitar servicio iptables
    systemctl enable iptables
    systemctl start iptables
    
    echo "✅ Firewall avanzado configurado"
    echo "📄 Backup guardado en: /etc/iptables/iptables.rules.backup.$(date +%Y%m%d)"
}

# Ejecutar solo si se llama directamente
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    setup_advanced_firewall
fi
```

## **Troubleshooting de Firewall**

### **Problemas Comunes**

```bash
# Verificar si el firewall está bloqueando conexiones
troubleshoot_firewall() {
    echo "=== Diagnóstico de Firewall ==="
    
    # Estado del firewall
    echo "Estado de UFW:"
    sudo ufw status verbose
    echo
    
    # Reglas de iptables
    echo "Reglas de iptables:"
    sudo iptables -L -n -v | head -20
    echo
    
    # Puertos en escucha
    echo "Puertos en escucha:"
    ss -tuln | head -20
    echo
    
    # Conexiones activas
    echo "Conexiones activas:"
    ss -tun | head -10
    echo
    
    # Verificar servicios bloqueados
    echo "Verificando servicios comunes:"
    for port in 22 80 443 3306 5432; do
        if ss -tuln | grep -q ":$port "; then
            echo "Puerto $port: ✅ En escucha"
        else
            echo "Puerto $port: ❌ No en escucha"
        fi
    done
}

# Probar conectividad a puerto específico
test_port_connectivity() {
    local host="$1"
    local port="$2"
    
    echo "Probando conectividad a $host:$port..."
    
    if command -v nc >/dev/null; then
        if nc -z "$host" "$port" 2>/dev/null; then
            echo "✅ Puerto $port accesible en $host"
        else
            echo "❌ Puerto $port no accesible en $host"
        fi
    else
        echo "netcat no disponible, instalalo con: sudo pacman -S openbsd-netcat"
    fi
}

troubleshoot_firewall
```

### **Backup y Restauración**

```bash
# Backup de configuración UFW
backup_firewall_config() {
    local backup_dir="/etc/ufw/backup_$(date +%Y%m%d_%H%M%S)"
    
    echo "Creando backup de configuración de firewall..."
    sudo mkdir -p "$backup_dir"
    
    # Backup UFW
    sudo cp -r /etc/ufw/* "$backup_dir/"
    
    # Backup iptables
    sudo iptables-save > "$backup_dir/iptables.rules"
    
    # Backup configuración del sistema
    sudo cp /etc/default/ufw "$backup_dir/" 2>/dev/null
    
    echo "Backup creado en: $backup_dir"
}

# Restaurar configuración
restore_firewall_config() {
    local backup_dir="$1"
    
    if [[ ! -d "$backup_dir" ]]; then
        echo "Directorio de backup no encontrado: $backup_dir"
        return 1
    fi
    
    echo "Restaurando configuración desde: $backup_dir"
    
    # Deshabilitar firewall
    sudo ufw disable
    
    # Restaurar archivos UFW
    sudo cp -r "$backup_dir"/* /etc/ufw/
    
    # Restaurar iptables si existe
    if [[ -f "$backup_dir/iptables.rules" ]]; then
        sudo iptables-restore < "$backup_dir/iptables.rules"
    fi
    
    # Recargar UFW
    sudo ufw reload
    
    echo "Configuración restaurada"
}

backup_firewall_config
```

## **Seguridad Adicional**

### **Fail2ban - Protección Anti-Brute Force**

```bash
# Instalar fail2ban para protección adicional
sudo pacman -S fail2ban

# Configurar fail2ban
sudo tee /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
logpath = /var/log/nginx/error.log
maxretry = 6
EOF

# Habilitar fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Ver estado
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

### **Configuración de Seguridad de Red**

```bash
# Configuraciones adicionales de seguridad en /etc/sysctl.conf
sudo tee -a /etc/sysctl.conf << 'EOF'
# Protección contra IP spoofing
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Ignorar redirects ICMP
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0

# Ignorar ping broadcasts
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Protección contra SYN flood
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 3

# Log de paquetes con direcciones imposibles
net.ipv4.conf.all.log_martians = 1
EOF

# Aplicar configuraciones
sudo sysctl -p
```

## **Enlaces Relacionados**

- [[ssh]] - Configurar acceso SSH seguro con firewall
- [[systemctl]] - Gestionar servicios de firewall
- [[ps]] - Ver procesos de servicios de red
- [[netstat]] - Analizar conexiones de red y puertos
- [[ss]] - Herramienta moderna para análisis de sockets
- [[df]] - Monitorear espacio para logs de firewall