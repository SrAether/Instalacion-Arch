# df - Espacio en Disco y Sistemas de Archivos

El comando `df` es tu **medidor de espacio en disco**. Te muestra el uso del espacio en todos los sistemas de archivos montados, siendo fundamental para monitorear el almacenamiento y prevenir problemas de espacio.

## **¿Qué es df?**

`df` te permite:
- **Ver espacio disponible** en sistemas de archivos
- **Monitorear uso de disco** en tiempo real
- **Identificar particiones llenas** antes de que causen problemas
- **Gestionar el almacenamiento** del sistema

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, `df` es esencial para monitorear el espacio en disco, especialmente importante para `/`, `/home`, `/var` y particiones swap, permitiendo prevenir problemas de sistema por falta de espacio.

### **Sintaxis Básica**

```bash
# Ver uso de espacio en todos los sistemas de archivos
df

# Formato legible para humanos
df -h

# Ver solo sistemas de archivos locales
df -l

# Mostrar información específica de una partición
df /home
```

### **Opciones Fundamentales**

```bash
# -h, --human-readable    = mostrar tamaños en formato legible (K, M, G)
# -l, --local            = mostrar solo sistemas de archivos locales
# -T, --print-type       = mostrar tipo de sistema de archivos
# -i, --inodes           = mostrar información de inodos
# -a, --all              = incluir sistemas de archivos de 0 bloques
# -x, --exclude-type     = excluir tipos específicos
# -t, --type             = mostrar solo tipos específicos
```

## **Uso Básico**

### **Visualización Estándar**

```bash
# Uso básico - mostrar todos los sistemas de archivos
df
# Filesystem     1K-blocks    Used Available Use% Mounted on
# /dev/sda1       20971520 8388608  11534336  43% /
# /dev/sda2      104857600 2097152 102760448   3% /home
# tmpfs            2097152       0   2097152   0% /dev/shm

# Formato legible para humanos
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1        20G  8.0G   11G  43% /
# /dev/sda2       100G  2.0G   98G   3% /home
# tmpfs           2.0G     0  2.0G   0% /dev/shm

# Con tipos de sistema de archivos
df -hT
# Filesystem     Type      Size  Used Avail Use% Mounted on
# /dev/sda1      ext4       20G  8.0G   11G  43% /
# /dev/sda2      ext4      100G  2.0G   98G   3% /home
# tmpfs          tmpfs     2.0G     0  2.0G   0% /dev/shm
```

### **Información Específica**

```bash
# Ver espacio de directorio específico
df /home
df /var/log
df /tmp

# Solo sistemas de archivos locales (excluir red)
df -hl

# Incluir sistemas de archivos vacíos
df -ha

# Ver información de inodos
df -hi
# Filesystem      Inodes   IUsed   IFree IUse% Mounted on
# /dev/sda1       1.3M     180K    1.1M   14% /
# /dev/sda2       6.5M     100     6.5M    1% /home
```

## **Casos de Uso en Arch Linux**

### **Monitoreo del Sistema**

```bash
# Verificar espacio en particiones críticas
df -h / /home /var /tmp

# Monitorear solo particiones que no son temporales
df -hT -x tmpfs -x devtmpfs

# Ver sistemas de archivos por tipo
df -t ext4 -h       # Solo ext4
df -t btrfs -h      # Solo btrfs
df -x tmpfs -h      # Excluir tmpfs

# Verificar espacio libre en directorio actual
df -h .

# Mostrar solo particiones con uso mayor al 80%
df -h | awk 'NR>1 && $5+0 > 80 {print $0}'
```

### **Análisis de Almacenamiento**

```bash
# Crear reporte de uso de disco
echo "=== Reporte de Espacio en Disco - $(date) ==="
df -hT | grep -E '^(/dev/|Filesystem)'

# Análisis de particiones críticas para Arch Linux
echo "=== Particiones Críticas ==="
for mount in / /home /var /boot; do
    if mountpoint -q "$mount" 2>/dev/null; then
        echo "$mount:"
        df -h "$mount" | tail -1
    fi
done

# Verificar espacio de swap
echo "=== Información de Swap ==="
swapon --show
free -h
```

### **Detección de Problemas**

```bash
# Script para detectar particiones llenas
check_disk_space() {
    local threshold=${1:-90}  # Umbral por defecto 90%
    
    echo "=== Verificación de Espacio en Disco ==="
    echo "Umbral de alerta: ${threshold}%"
    echo
    
    df -h | awk -v threshold="$threshold" '
    NR > 1 && $5 != "-" {
        usage = $5
        gsub(/%/, "", usage)
        if (usage > threshold) {
            print "🚨 ALERTA: " $6 " está al " $5 " de capacidad"
            print "   Espacio libre: " $4
        } else if (usage > threshold-10) {
            print "⚠️  ADVERTENCIA: " $6 " está al " $5 " de capacidad"
        } else {
            print "✅ OK: " $6 " está al " $5 " de capacidad"
        }
    }'
}

# Usar la función
check_disk_space 85
```

## **Análisis Avanzado**

### **Monitoreo con Alertas**

```bash
#!/bin/bash
# Script de monitoreo avanzado de disco

ALERT_EMAIL="admin@ejemplo.com"
LOG_FILE="/var/log/disk_monitor.log"
CRITICAL_THRESHOLD=95
WARNING_THRESHOLD=85

monitor_disk_usage() {
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    local alerts_sent=false
    
    echo "[$timestamp] Iniciando monitoreo de disco" >> "$LOG_FILE"
    
    # Verificar cada sistema de archivos
    df -h | awk 'NR>1 && $5 != "-" {print $5, $6, $4}' | while read usage mount available; do
        # Remover el símbolo %
        local percent=$(echo "$usage" | tr -d '%')
        
        if [[ $percent -ge $CRITICAL_THRESHOLD ]]; then
            local message="CRÍTICO: $mount está al $usage de capacidad (solo $available libre)"
            echo "[$timestamp] $message" >> "$LOG_FILE"
            
            # Enviar alerta por email si está configurado
            if command -v mail >/dev/null; then
                echo "$message" | mail -s "ALERTA CRÍTICA: Disco lleno en $mount" "$ALERT_EMAIL"
            fi
            
            # Notificación del sistema
            if command -v notify-send >/dev/null; then
                notify-send "Disco Lleno" "$message" --urgency=critical
            fi
            
            alerts_sent=true
            
        elif [[ $percent -ge $WARNING_THRESHOLD ]]; then
            local message="ADVERTENCIA: $mount está al $usage de capacidad"
            echo "[$timestamp] $message" >> "$LOG_FILE"
            
            if command -v notify-send >/dev/null; then
                notify-send "Advertencia de Disco" "$message" --urgency=normal
            fi
        fi
    done
    
    # Log de verificación completada
    echo "[$timestamp] Monitoreo completado" >> "$LOG_FILE"
}

# Ejecutar monitoreo
monitor_disk_usage
```

### **Análisis de Crecimiento**

```bash
#!/bin/bash
# Análisis de crecimiento de uso de disco

HISTORY_FILE="/var/log/disk_usage_history.log"

track_disk_usage() {
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Registrar uso actual
    echo "[$timestamp]" >> "$HISTORY_FILE"
    df -h | grep -E '^/dev/' >> "$HISTORY_FILE"
    echo "" >> "$HISTORY_FILE"
}

analyze_growth() {
    local days=${1:-7}  # Analizar últimos 7 días por defecto
    
    echo "=== Análisis de Crecimiento de Disco (últimos $days días) ==="
    
    # Obtener datos de hace N días y actuales
    local old_data=$(grep -A 10 "$(date -d "$days days ago" '+%Y-%m-%d')" "$HISTORY_FILE" | tail -10)
    local current_data=$(df -h | grep -E '^/dev/')
    
    # Comparar y mostrar diferencias
    echo "Cambios en el uso de disco:"
    echo "$current_data" | while read filesystem size used avail percent mount; do
        echo "📊 $mount: $percent usado ($avail libre)"
    done
}

# Función para limpiar logs antiguos del historial
cleanup_history() {
    local keep_days=${1:-30}
    local cutoff_date=$(date -d "$keep_days days ago" '+%Y-%m-%d')
    
    # Crear archivo temporal con datos recientes
    awk -v date="$cutoff_date" '
    /^\[/ {
        if ($1 >= "[" date) keep=1; else keep=0
    }
    keep==1 {print}
    ' "$HISTORY_FILE" > "${HISTORY_FILE}.tmp"
    
    mv "${HISTORY_FILE}.tmp" "$HISTORY_FILE"
    echo "Historial limpiado - manteniendo últimos $keep_days días"
}

# Ejecutar funciones
track_disk_usage
analyze_growth 7
```

## **Optimización y Limpieza**

### **Identificar Directorios que Consumen Espacio**

```bash
# Combinar df con du para análisis completo
analyze_space_usage() {
    local mount_point="$1"
    
    echo "=== Análisis de $mount_point ==="
    
    # Información general con df
    echo "Información general:"
    df -h "$mount_point"
    echo
    
    # Directorios que más espacio consumen
    echo "Top 10 directorios por tamaño:"
    du -h "$mount_point"/* 2>/dev/null | sort -hr | head -10
    echo
    
    # Archivos grandes individuales
    echo "Archivos más grandes (>100MB):"
    find "$mount_point" -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k5 -hr | head -10
}

# Script de limpieza automática
auto_cleanup() {
    local mount_point="$1"
    local threshold="$2"  # Porcentaje de uso para activar limpieza
    
    local current_usage=$(df "$mount_point" | awk 'NR==2 {print $5}' | tr -d '%')
    
    if [[ $current_usage -gt $threshold ]]; then
        echo "🧹 Iniciando limpieza automática de $mount_point (uso: ${current_usage}%)"
        
        # Limpiar archivos temporales
        find "$mount_point/tmp" -type f -atime +7 -delete 2>/dev/null
        
        # Limpiar logs antiguos comprimidos
        find "$mount_point/var/log" -name "*.gz" -mtime +30 -delete 2>/dev/null
        
        # Limpiar cache de paquetes (si es root)
        if [[ "$mount_point" == "/" ]] && [[ $(id -u) -eq 0 ]]; then
            pacman -Sc --noconfirm
        fi
        
        # Verificar espacio liberado
        local new_usage=$(df "$mount_point" | awk 'NR==2 {print $5}' | tr -d '%')
        local freed=$((current_usage - new_usage))
        echo "✅ Limpieza completada. Espacio liberado: ${freed}%"
    fi
}

# Ejemplos de uso
analyze_space_usage "/"
auto_cleanup "/" 90
```

## **Sistemas de Archivos Especiales**

### **Análisis de tmpfs y RAM**

```bash
# Ver sistemas de archivos en memoria
df -h -t tmpfs

# Análisis completo de memoria y swap
analyze_memory_filesystems() {
    echo "=== Análisis de Sistemas de Archivos en Memoria ==="
    
    echo "Sistemas tmpfs:"
    df -h -t tmpfs
    echo
    
    echo "Información de memoria física:"
    free -h
    echo
    
    echo "Información de swap:"
    swapon --show
    df -h /swapfile 2>/dev/null || echo "No hay archivo de swap"
    echo
    
    echo "Uso de /tmp (si es tmpfs):"
    if df -T /tmp | grep -q tmpfs; then
        echo "/tmp está en RAM (tmpfs)"
        du -sh /tmp/* 2>/dev/null | sort -hr | head -10
    else
        echo "/tmp está en disco"
    fi
}

analyze_memory_filesystems
```

### **Análisis de Puntos de Montaje**

```bash
# Ver todos los puntos de montaje con información detallada
show_mount_details() {
    echo "=== Información Detallada de Montajes ==="
    
    # Combinando df con findmnt para información completa
    echo "Sistemas de archivos y opciones de montaje:"
    findmnt -D | head -20
    echo
    
    # Espacio por tipo de sistema de archivos
    echo "Espacio por tipo de sistema de archivos:"
    df -hT | awk 'NR>1 {fs[$2]+=$3; total[$2]+=$2} END {for (f in fs) printf "%-10s %10s\n", f, fs[f]}' 2>/dev/null
    echo
    
    # Sistemas de archivos de red
    echo "Sistemas de archivos de red:"
    df -hT | grep -E 'nfs|cifs|fuse' || echo "No se encontraron sistemas de archivos de red"
}

show_mount_details
```

## **Automatización y Scripting**

### **Script de Reporte Diario**

```bash
#!/bin/bash
# Reporte diario de espacio en disco

REPORT_FILE="/var/log/daily_disk_report_$(date +%Y%m%d).log"
EMAIL_RECIPIENT="admin@ejemplo.com"

generate_daily_report() {
    {
        echo "=========================================="
        echo "REPORTE DIARIO DE ESPACIO EN DISCO"
        echo "Fecha: $(date)"
        echo "Servidor: $(hostname)"
        echo "=========================================="
        echo
        
        echo "=== RESUMEN GENERAL ==="
        df -h | grep -E '^(/dev/|Filesystem)'
        echo
        
        echo "=== SISTEMAS DE ARCHIVOS CRÍTICOS ==="
        df -h | awk 'NR>1 && $5+0 > 85 {print "⚠️ " $0}'
        echo
        
        echo "=== INFORMACIÓN DE INODOS ==="
        df -hi | awk 'NR>1 && $5+0 > 85 {print "⚠️ " $0}'
        echo
        
        echo "=== SISTEMAS DE ARCHIVOS POR TIPO ==="
        df -hT | grep -v tmpfs | sort -k2
        echo
        
        echo "=== MEMORIA Y SWAP ==="
        free -h
        echo
        swapon --show 2>/dev/null || echo "No hay swap configurado"
        echo
        
        echo "=== DIRECTORIO /var/log ==="
        du -sh /var/log/* 2>/dev/null | sort -hr | head -10
        echo
        
        echo "=========================================="
        echo "Fin del reporte - $(date)"
        echo "=========================================="
        
    } > "$REPORT_FILE"
    
    # Enviar por email si está configurado
    if command -v mail >/dev/null && [[ -n "$EMAIL_RECIPIENT" ]]; then
        mail -s "Reporte Diario de Disco - $(hostname)" "$EMAIL_RECIPIENT" < "$REPORT_FILE"
    fi
    
    echo "Reporte generado: $REPORT_FILE"
}

# Función para agregar a crontab diario
# 0 8 * * * /path/to/this/script.sh
generate_daily_report
```

### **Monitor en Tiempo Real**

```bash
#!/bin/bash
# Monitor de espacio en disco en tiempo real

monitor_realtime() {
    local interval=${1:-5}  # Actualizar cada 5 segundos por defecto
    
    while true; do
        clear
        echo "=== MONITOR DE DISCO EN TIEMPO REAL ==="
        echo "Actualización cada ${interval}s - $(date)"
        echo "Presiona Ctrl+C para salir"
        echo
        
        # Mostrar información principal
        df -h | grep -E '^(/dev/|Filesystem)'
        echo
        
        # Mostrar alertas si las hay
        local alerts=$(df -h | awk 'NR>1 && $5+0 > 90 {print "🚨 " $6 " (" $5 ")"}')
        if [[ -n "$alerts" ]]; then
            echo "=== ALERTAS ==="
            echo "$alerts"
            echo
        fi
        
        # Mostrar actividad de I/O si iostat está disponible
        if command -v iostat >/dev/null; then
            echo "=== ACTIVIDAD DE DISCO ==="
            iostat -h 1 1 | tail -n +4 | head -10
        fi
        
        sleep "$interval"
    done
}

# Ejecutar monitor
monitor_realtime 3
```

## **Integración con Otros Comandos**

### **Con [[du]] para Análisis Detallado**

```bash
# Combinar df y du para análisis completo
combined_analysis() {
    local path="$1"
    
    echo "=== Análisis Combinado df + du para $path ==="
    
    # Información general con df
    echo "Información del sistema de archivos:"
    df -h "$path"
    echo
    
    # Análisis detallado con du
    echo "Uso detallado por directorios:"
    du -h --max-depth=1 "$path" 2>/dev/null | sort -hr | head -10
}

combined_analysis "/var"
```

### **Con [[find]] para Limpieza Dirigida**

```bash
# Encontrar archivos grandes para limpieza
find_large_files() {
    local mount_point="$1"
    local size_threshold="${2:-100M}"
    
    echo "=== Archivos grandes (>${size_threshold}) en $mount_point ==="
    
    # Mostrar espacio actual
    df -h "$mount_point"
    echo
    
    # Encontrar archivos grandes
    echo "Archivos grandes encontrados:"
    find "$mount_point" -type f -size "+${size_threshold}" -exec ls -lh {} \; 2>/dev/null | \
    sort -k5 -hr | head -20
}

find_large_files "/" "500M"
```

### **Con [[systemctl]] para Servicios**

```bash
# Verificar espacio antes de iniciar servicios que usan mucho disco
check_space_before_service() {
    local service_name="$1"
    local required_space_gb="$2"
    local mount_point="${3:-/}"
    
    local available_gb=$(df -BG "$mount_point" | awk 'NR==2 {print $4}' | tr -d 'G')
    
    if [[ $available_gb -lt $required_space_gb ]]; then
        echo "❌ No hay suficiente espacio para $service_name"
        echo "Requerido: ${required_space_gb}GB, Disponible: ${available_gb}GB"
        return 1
    else
        echo "✅ Espacio suficiente para $service_name"
        systemctl start "$service_name"
        return 0
    fi
}

# check_space_before_service "postgresql" 5 "/var"
```

## **Enlaces Relacionados**

- [[du]] - Análisis detallado de uso de espacio por directorios
- [[find]] - Buscar archivos grandes para liberar espacio
- [[ls]] - Ver tamaños de archivos individuales  
- [[mount]] - Gestionar puntos de montaje que df muestra
- [[free]] - Ver uso de memoria y swap complementario a df
- [[lsblk]] - Ver estructura de bloques y particiones