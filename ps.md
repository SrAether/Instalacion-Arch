# ps - Procesos del Sistema

El comando `ps` es tu **monitor de procesos**. Te permite ver qué programas están ejecutándose en tu sistema, cuántos recursos consumen y cómo están organizados, siendo fundamental para el monitoreo y troubleshooting del sistema.

## **¿Qué es ps?**

`ps` te permite:
- **Ver procesos activos** en el sistema
- **Monitorear uso de recursos** (CPU, memoria)
- **Identificar procesos problemáticos** o zombies
- **Gestionar servicios** y aplicaciones

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, `ps` es esencial para monitorear el rendimiento del sistema, identificar procesos que consumen demasiados recursos, debugging de servicios systemd y administración general del sistema.

### **Sintaxis Básica**

```bash
# Procesos del usuario actual
ps

# Todos los procesos del sistema
ps aux

# Procesos en formato árbol
ps axf

# Procesos con información detallada
ps -ef
```

### **Opciones Fundamentales**

```bash
# a = mostrar procesos de todos los usuarios
# u = formato orientado al usuario
# x = incluir procesos sin terminal
# f = formato completo con árbol
# e = mostrar todas las variables de entorno
# -p PID = mostrar proceso específico
# -C comando = mostrar procesos de comando específico
```

## **Visualización de Procesos**

### **Formatos Básicos**

```bash
# Vista básica de procesos del usuario
ps
# PID TTY          TIME CMD
# 1234 pts/0    00:00:01 bash
# 5678 pts/0    00:00:00 ps

# Todos los procesos con detalles
ps aux
# USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root         1  0.0  0.1 169544 11876 ?        Ss   10:30   0:02 /sbin/init
# usuario   1234  0.0  0.2  21532  4312 pts/0    Ss   10:35   0:00 -bash

# Formato completo con jerarquía
ps axf
# Muestra procesos en forma de árbol

# Formato extendido
ps -ef
# UID        PID  PPID  C STIME TTY          TIME CMD
# root         1     0  0 10:30 ?        00:00:02 /sbin/init
```

### **Información de las Columnas**

```bash
# USER/UID = usuario propietario del proceso
# PID = identificador del proceso
# PPID = PID del proceso padre
# %CPU = porcentaje de CPU utilizado
# %MEM = porcentaje de memoria utilizada
# VSZ = memoria virtual en KB
# RSS = memoria física en KB
# TTY = terminal asociado
# STAT = estado del proceso
# START = hora de inicio
# TIME = tiempo total de CPU
# COMMAND/CMD = comando ejecutado
```

## **Estados de Procesos**

### **Códigos de Estado (STAT)**

```bash
# R = Running (ejecutándose)
# S = Sleeping (durmiendo, interruptible)
# D = Disk sleep (durmiendo, no interruptible)
# T = Stopped (detenido)
# Z = Zombie (proceso zombie)
# X = Dead (proceso muerto)
# < = alta prioridad
# N = baja prioridad
# L = páginas bloqueadas en memoria
# s = líder de sesión
# l = multi-hilo
# + = en primer plano
```

### **Identificar Procesos Problemáticos**

```bash
# Buscar procesos zombie
ps aux | grep '[Zz]ombie\|<defunct>'
ps aux | awk '$8 ~ /^Z/ { print $2, $11 }'

# Procesos que consumen más CPU
ps aux --sort=-%cpu | head -10

# Procesos que consumen más memoria
ps aux --sort=-%mem | head -10

# Procesos en estado D (uninterruptible sleep)
ps aux | awk '$8 ~ /^D/ { print $2, $11 }'

# Procesos con alta prioridad
ps axo pid,ni,comm | grep -E '^[[:space:]]*[0-9]+[[:space:]]+\-'
```

## **Casos de Uso en Arch Linux**

### **Monitoreo del Sistema**

```bash
# Ver servicios systemd activos
ps aux | grep systemd

# Monitorear servidor web
ps aux | grep -E 'nginx|apache|httpd'

# Ver procesos de base de datos
ps aux | grep -E 'mysql|postgres|mariadb'

# Monitorear uso de memoria por proceso
ps aux --sort=-%mem | head -20

# Ver procesos del kernel
ps aux | grep '^\[.*\]$'
```

### **Debugging de Rendimiento**

```bash
# Procesos con mayor tiempo de CPU
ps aux --sort=-time | head -10

# Procesos iniciados recientemente
ps aux --sort=start_time | tail -10

# Ver carga por usuario
ps aux | awk '{user[$1]++; cpu[$1]+=$3; mem[$1]+=$4} END {for (u in user) printf "%-10s %3d procesos, CPU: %5.1f%%, MEM: %5.1f%%\n", u, user[u], cpu[u], mem[u]}' | sort -k4 -nr

# Analizar procesos padre-hijo
ps axf | grep -A 5 -B 5 proceso_específico
```

### **Administración de Servicios**

```bash
# Ver todos los servicios systemd
ps aux | grep '/lib/systemd\|/usr/lib/systemd' | grep -v grep

# Verificar estado de SSH
ps aux | grep sshd

# Monitorear NetworkManager
ps aux | grep NetworkManager

# Ver procesos de login
ps aux | grep -E 'login|getty'

# Verificar servicios de audio
ps aux | grep -E 'pulseaudio|pipewire|alsa'
```

## **Filtrado y Búsqueda Avanzada**

### **Buscar Procesos Específicos**

```bash
# Por nombre de comando
ps -C firefox
ps aux | grep firefox

# Por PID específico
ps -p 1234
ps -p 1234,5678,9012

# Por usuario
ps -u usuario
ps aux | grep ^usuario

# Por grupo
ps -g grupo

# Por terminal
ps -t pts/0
```

### **Filtros Personalizados**

```bash
# Procesos con más de X minutos de CPU
ps aux | awk '$10 > "00:05:00" {print $0}'

# Procesos que usan más de X% de memoria
ps aux | awk '$4 > 5.0 {print $2, $4, $11}'

# Procesos sin terminal (demonios)
ps aux | awk '$7 == "?" {print $2, $11}'

# Procesos hijos de systemd
ps axf | grep -A 20 '/sbin/init\|systemd'
```

## **Formato Personalizado**

### **Campos Personalizados**

```bash
# Formato básico personalizado
ps -eo pid,ppid,user,comm
ps -eo pid,pcpu,pmem,comm --sort=-pcpu

# Formato completo personalizado
ps -eo pid,ppid,uid,gid,vsz,rss,tty,stat,start,time,comm

# Formato para monitoreo
ps -eo pid,pcpu,pmem,etime,comm --sort=-pcpu | head -20

# Formato para debugging
ps -eo pid,ppid,pgid,sid,tty,comm
```

### **Scripts de Monitoreo**

```bash
# Monitor simple de procesos

echo "=== Monitor de Procesos - $(date) ==="

# Top 10 procesos por CPU
echo "=== TOP 10 por CPU ==="
ps aux --sort=-%cpu | head -11

# Top 10 procesos por Memoria
echo "=== TOP 10 por Memoria ==="
ps aux --sort=-%mem | head -11

# Buscar procesos zombie
echo "=== Procesos Zombie ==="
ps aux | awk '$8 ~ /^Z/ {print $2, $11}'
```
analyze_user_load() {
    echo "=== Carga por Usuario ==="
    ps aux | awk '
    NR > 1 {
        user[$1]++
        cpu[$1] += $3
        mem[$1] += $4
        vsz[$1] += $5
        rss[$1] += $6
    }
    END {
        printf "%-12s %8s %8s %8s %10s %10s\n", "Usuario", "Procesos", "CPU%", "MEM%", "VSZ(MB)", "RSS(MB)"
        printf "%-12s %8s %8s %8s %10s %10s\n", "--------", "--------", "----", "----", "------", "------"
        for (u in user) {
            printf "%-12s %8d %8.1f %8.1f %10.1f %10.1f\n", 
                   u, user[u], cpu[u], mem[u], vsz[u]/1024, rss[u]/1024
        }
    }' | sort -k3 -nr
}

# Función para detectar procesos problemáticos
detect_issues() {
    echo "=== Detección de Problemas ==="
    
    # Procesos zombie
    local zombies=$(ps aux | awk '$8 ~ /^Z/ {print $2}' | wc -l)
    echo "Procesos zombie: $zombies"
    
    # Procesos con alta CPU
    local high_cpu=$(ps aux | awk '$3 > 80 {print $2, $3, $11}')
    if [[ -n "$high_cpu" ]]; then
        echo "Procesos con alta CPU (>80%):"
        echo "$high_cpu"
    fi
    
    # Procesos con alta memoria
    local high_mem=$(ps aux | awk '$4 > 50 {print $2, $4, $11}')
    if [[ -n "$high_mem" ]]; then
        echo "Procesos con alta memoria (>50%):"
        echo "$high_mem"
    fi
    
    # Procesos en estado D
    local blocked=$(ps aux | awk '$8 ~ /^D/ {print $2, $11}')
    if [[ -n "$blocked" ]]; then
        echo "Procesos bloqueados (estado D):"
        echo "$blocked"
    fi
}

# Usar las funciones
analyze_user_load
echo
detect_issues
```

## **Integración con Otros Comandos**

### **Con [[kill]] para Gestión de Procesos**

```bash
# Encontrar y terminar procesos por nombre
ps aux | grep firefox | awk '{print $2}' | xargs kill

# Terminar procesos zombie (terminar padre)
ps aux | awk '$8 ~ /^Z/ {print $3}' | sort -u | xargs kill

# Matar procesos que consumen mucha CPU
ps aux --sort=-%cpu | head -5 | awk 'NR>1 && $3>90 {print $2}' | xargs kill -TERM

# Reiniciar proceso específico
sudo systemctl restart $(ps aux | grep nginx | awk '{print $11}' | head -1)
```

### **Con [[grep]] y [[awk]] para Análisis**

```bash
# Análisis de patrones de procesos
ps aux | grep -v grep | grep -E 'python|node|java'

# Contar procesos por tipo
ps aux | awk '{print $11}' | sort | uniq -c | sort -nr

# Estadísticas de memoria por comando
ps aux | awk '{cmd=$11; mem[cmd]+=$6} END {for (c in mem) printf "%-20s %10.1f MB\n", c, mem[c]/1024}' | sort -k2 -nr

# Tiempo total de CPU por usuario
ps aux | awk '{user=$1; gsub(/[:-]/, " ", $10); split($10, t); time[user]+=t[1]*3600+t[2]*60+t[3]} END {for (u in time) printf "%-12s %8.1f horas\n", u, time[u]/3600}'
```

### **Con [[systemctl]] para Servicios**

```bash
# Verificar servicios vs procesos
systemctl list-units --type=service --state=active | while read -r service _; do
    if [[ "$service" == *".service" ]]; then
        echo -n "$service: "
        ps aux | grep "${service%.service}" | grep -v grep | wc -l
    fi
done

# Correlacionar servicios fallidos con procesos
systemctl --failed | grep service | while read -r service _; do
    echo "=== $service ==="
    ps aux | grep "${service%.service}" | grep -v grep
done
```

## **Monitoreo Continuo**

### **Watch con ps**

```bash
# Monitoreo en tiempo real de CPU
watch -n 2 'ps aux --sort=-%cpu | head -20'

# Monitoreo de memoria
watch -n 5 'ps aux --sort=-%mem | head -15'

# Monitoreo de procesos específicos
watch -n 1 'ps aux | grep -E "nginx|apache|mysql"'

# Monitoreo de carga del sistema
watch -n 3 'ps aux | awk "{sum+=\$3} END {print \"CPU total:\", sum\"%\"}"'
```

### **Scripts de Alertas**

```bash
#!/bin/bash
# Sistema de alertas basado en ps

ALERT_LOG="/var/log/process_alerts.log"
CPU_THRESHOLD=80
MEM_THRESHOLD=70

check_alerts() {
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Verificar CPU alta
    ps aux | awk -v thresh=$CPU_THRESHOLD -v ts="$timestamp" '
    $3 > thresh {
        print ts " HIGH_CPU: PID " $2 " (" $11 ") using " $3 "% CPU"
    }' >> "$ALERT_LOG"
    
    # Verificar memoria alta
    ps aux | awk -v thresh=$MEM_THRESHOLD -v ts="$timestamp" '
    $4 > thresh {
        print ts " HIGH_MEM: PID " $2 " (" $11 ") using " $4 "% memory"
    }' >> "$ALERT_LOG"
    
    # Verificar procesos zombie
    local zombie_count=$(ps aux | awk '$8 ~ /^Z/ {count++} END {print count+0}')
    if [[ $zombie_count -gt 0 ]]; then
        echo "$timestamp ZOMBIE: $zombie_count zombie processes detected" >> "$ALERT_LOG"
    fi
    
    # Verificar procesos bloqueados
    ps aux | awk -v ts="$timestamp" '
    $8 ~ /^D/ {
        print ts " BLOCKED: PID " $2 " (" $11 ") in uninterruptible sleep"
    }' >> "$ALERT_LOG"
}

# Ejecutar verificación
check_alerts

# Mostrar alertas recientes
echo "=== Alertas de las últimas 24 horas ==="
find "$ALERT_LOG" -mtime -1 -exec tail -20 {} \;
```

## **Troubleshooting con ps**

### **Problemas Comunes**

```bash
# Sistema lento - identificar causa
echo "=== Análisis de rendimiento ==="
echo "Procesos con mayor CPU:"
ps aux --sort=-%cpu | head -10
echo
echo "Procesos con mayor memoria:"
ps aux --sort=-%mem | head -10
echo
echo "Procesos en estado no interrumpible:"
ps aux | awk '$8 ~ /^D/ {print $2, $8, $11}'

# Memoria insuficiente
echo "=== Análisis de memoria ==="
ps aux | awk '{mem+=$6} END {printf "Memoria total usada por procesos: %.1f MB\n", mem/1024}'
ps aux --sort=-%mem | head -15

# Demasiados procesos
echo "=== Conteo de procesos ==="
echo "Total de procesos: $(ps aux | wc -l)"
echo "Procesos por usuario:"
ps aux | awk '{user[$1]++} END {for (u in user) printf "%-12s %5d\n", u, user[u]}' | sort -k2 -nr
```

### **Información de Debugging**

```bash
# Información completa de proceso específico
debug_process() {
    local pid="$1"
    
    echo "=== Debug del proceso PID $pid ==="
    echo "Información básica:"
    ps -p "$pid" -o pid,ppid,uid,gid,vsz,rss,tty,stat,start,time,comm
    echo
    echo "Variables de entorno:"
    ps -p "$pid" -e
    echo
    echo "Árbol de procesos:"
    ps axf | grep -A 10 -B 5 "$pid"
    echo
    echo "Archivos abiertos (si /proc está disponible):"
    [[ -d "/proc/$pid" ]] && ls -la "/proc/$pid/fd/" 2>/dev/null | head -10
}

# Usar la función
# debug_process 1234
```

## **Enlaces Relacionados**

- [[top]] - Monitor interactivo complementario a ps
- [[kill]] - Terminar procesos identificados con ps
- [[systemctl]] - Gestionar servicios que aparecen en ps
- [[jobs]] - Ver trabajos del shell actual
- [[nohup]] - Ejecutar procesos que verás con ps
- [[pgrep]] - Buscar procesos por nombre (alternativa a ps|grep)