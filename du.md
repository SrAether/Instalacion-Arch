# du - Análisis de Uso de Disco

**du** (Disk Usage) es tu **detective de espacio en disco**. Te permite analizar detalladamente dónde se encuentra el almacenamiento utilizado en tu sistema Arch Linux, siendo fundamental para gestión de espacio, limpieza del sistema y optimización del rendimiento.

## **¿Qué es du?**

du te permite:
- **Analizar el uso de espacio** de directorios y archivos específicos
- **Identificar directorios grandes** que consumen espacio
- **Monitorear el crecimiento** de directorios en el tiempo
- **Encontrar archivos** que ocupan espacio innecesario

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, du es esencial para:
- Gestionar el espacio limitado en particiones `/` y `/home`
- Identificar logs grandes en `/var/log`
- Analizar el cache de pacman en `/var/cache/pacman`
- Optimizar el uso de almacenamiento en servidores

### **Verificar Disponibilidad**

```bash
# du está incluido en coreutils (preinstalado en Arch)
which du
du --version

# Verificar que funciona correctamente
du --help | head -5
```

## **Sintaxis Básica de du**

### **Estructura del Comando**

```bash
# Sintaxis general
du [opciones] [directorio/archivo]

# Uso de espacio del directorio actual
du

# Uso de espacio de un directorio específico
du /home/usuario

# Uso de espacio con formato legible
du -h /var/log

# Uso total de un directorio (sin subdirectorios)
du -s /home/usuario
```

## **Operaciones Básicas**

### **Análisis Básico de Directorios**

```bash
# Uso del directorio actual en formato legible
du -h

# Resumen total de un directorio
du -sh /home/usuario

# Análisis de subdirectorios (un nivel)
du -h --max-depth=1 /home/usuario

# Análisis de todos los subdirectorios
du -ah /var/log

# Mostrar solo archivos (no directorios)
du -ah /home/usuario | grep -v '/$'
```

### **Unidades y Formatos**

```bash
# Formato legible para humanos (K, M, G)
du -h /var/cache

# En kilobytes (por defecto)
du -k /tmp

# En megabytes
du -m /usr

# En gigabytes
du --block-size=G /home

# En bytes exactos
du -b archivo.txt

# Con separador de miles
du -h --block-size=1 /var | numfmt --to=iec
```

### **Control de Profundidad**

```bash
# Solo un nivel de profundidad
du -h --max-depth=1 /

# Dos niveles de profundidad
du -h --max-depth=2 /home

# Mostrar solo el total (profundidad 0)
du -sh /usr/share

# Análisis completo sin límite
du -ah /etc
```

## **Análisis Avanzado**

### **Encontrar Directorios Grandes**

```bash
# Encontrar los directorios que más espacio ocupan

# Top 10 directorios más grandes
echo "=== Directorios Más Grandes ==="
du -h . | sort -hr | head -10

# Análisis por nivel
echo "=== Análisis por Nivel ==="
du -h --max-depth=1 . | sort -hr

# Análisis del sistema
echo "=== Directorios Principales del Sistema ==="
du -sh /{home,usr,var,opt} 2>/dev/null | sort -hr
```
        echo "📦 Cache de Pacman:"
        du -sh /var/cache/pacman/pkg/
        echo "Número de paquetes en cache: $(ls /var/cache/pacman/pkg/*.pkg.tar.* 2>/dev/null | wc -l)"
        echo
    fi
    
    # Logs del sistema
    echo "📄 Logs del sistema:"
    du -sh /var/log/* 2>/dev/null | sort -hr | head -10
}

find_large_directories "/home" 15
analyze_system_space
```

### **Monitoreo de Crecimiento de Archivos**

```bash
#!/bin/bash
# Script para monitorear el crecimiento de directorios

monitor_directory_growth() {
    local target_dir="$1"
    local interval="${2:-60}"  # segundos
    local log_file="/tmp/du_monitor_$(date +%Y%m%d_%H%M%S).log"
    
    if [[ ! -d "$target_dir" ]]; then
        echo "❌ Directorio no encontrado: $target_dir"
        return 1
    fi
    
    echo "=== Monitor de Crecimiento: $target_dir ==="
    echo "Intervalo: ${interval}s"
    echo "Log: $log_file"
    echo "Presiona Ctrl+C para detener"
    echo
    
    # Función de limpieza
    cleanup() {
        echo
        echo "📊 Resumen guardado en: $log_file"
        exit 0
    }
    trap cleanup INT
    
    # Obtener tamaño inicial
    initial_size=$(du -sb "$target_dir" | cut -f1)
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Tamaño inicial: $(du -sh "$target_dir" | cut -f1)" | tee -a "$log_file"
    
    while true; do
        sleep "$interval"
        
        current_size=$(du -sb "$target_dir" | cut -f1)
        human_size=$(du -sh "$target_dir" | cut -f1)
        growth=$((current_size - initial_size))
        
        if [[ $growth -gt 0 ]]; then
            growth_human=$(echo $growth | numfmt --to=iec)
            echo "$(date '+%Y-%m-%d %H:%M:%S') - Tamaño: $human_size (+$growth_human)" | tee -a "$log_file"
        else
            echo "$(date '+%Y-%m-%d %H:%M:%S') - Tamaño: $human_size (sin cambios)" | tee -a "$log_file"
        fi
        
        # Mostrar archivos que más han crecido cada 5 iteraciones
        if [[ $(($(date +%s) % 300)) -eq 0 ]]; then
            echo "📈 Archivos con mayor actividad:" | tee -a "$log_file"
            find "$target_dir" -type f -mmin -$((interval/60 + 1)) -exec du -h {} \; 2>/dev/null | sort -hr | head -5 | tee -a "$log_file"
        fi
    done
}

# Función para análisis histórico
analyze_growth_history() {
    local log_file="$1"
    
    if [[ ! -f "$log_file" ]]; then
        echo "❌ Archivo de log no encontrado: $log_file"
        return 1
    fi
    
    echo "=== Análisis de Historial de Crecimiento ==="
    echo
    
    # Extraer datos de crecimiento
    echo "📊 Crecimiento por hora:"
    grep -o '+[0-9.]*[KMGT]*' "$log_file" | \
    head -20 | \
    while read growth; do
        echo "  $growth"
    done
    
    echo
    echo "📈 Tendencia de crecimiento:"
    tail -10 "$log_file"
}

# Ejemplo de uso
monitor_directory_growth "/var/log" 30
```

### **Análisis de Tipos de Archivo**

```bash
#!/bin/bash
# Script para analizar uso de espacio por tipo de archivo

analyze_file_types() {
    local target_dir="${1:-.}"
    
    echo "=== Análisis de Uso por Tipo de Archivo en $target_dir ==="
    echo
    
    # Crear archivo temporal para resultados
    local temp_file="/tmp/file_analysis_$$"
    
    # Analizar todos los archivos
    find "$target_dir" -type f -exec file {} \; 2>/dev/null > "$temp_file.types"
    find "$target_dir" -type f -exec du -b {} \; 2>/dev/null > "$temp_file.sizes"
    
    # Analizar por extensión
    echo "📄 Uso por extensión de archivo:"
    find "$target_dir" -type f -name "*.*" -exec basename {} \; 2>/dev/null | \
    awk -F. '{print $NF}' | \
    sort | uniq -c | sort -nr | head -15 | \
    while read count ext; do
        total_size=0
        find "$target_dir" -type f -name "*.${ext}" -exec du -b {} \; 2>/dev/null | \
        awk '{sum += $1} END {printf "%-10s %6d archivos %s\n", ext, count, (sum > 0 ? sprintf("(%s)", numfmt_to_iec(sum)) : "")}' ext="$ext" count="$count"
    done
    echo
    
    # Analizar por tipo MIME
    echo "🗂️ Uso por tipo de archivo:"
    declare -A type_sizes
    declare -A type_counts
    
    while IFS= read -r line; do
        file_path=$(echo "$line" | cut -d: -f1)
        file_type=$(echo "$line" | cut -d: -f2- | sed 's/^ *//' | cut -d, -f1)
        
        if [[ -f "$file_path" ]]; then
            size=$(du -b "$file_path" 2>/dev/null | cut -f1)
            type_sizes["$file_type"]=$((${type_sizes["$file_type"]} + size))
            type_counts["$file_type"]=$((${type_counts["$file_type"]} + 1))
        fi
    done < "$temp_file.types"
    
    # Mostrar resultados ordenados por tamaño
    for type in "${!type_sizes[@]}"; do
        size=${type_sizes["$type"]}
        count=${type_counts["$type"]}
        human_size=$(echo $size | numfmt --to=iec)
        printf "%-50s %6d archivos %10s\n" "$type" "$count" "$human_size"
    done | sort -k3 -hr | head -20
    
    echo
    
    # Archivos más grandes
    echo "📊 Archivos individuales más grandes:"
    find "$target_dir" -type f -exec du -h {} \; 2>/dev/null | \
    sort -hr | head -15
    
    # Limpiar archivos temporales
    rm -f "$temp_file.types" "$temp_file.sizes"
}

# Función auxiliar para convertir bytes a formato legible
numfmt_to_iec() {
    echo "$1" | numfmt --to=iec
}

analyze_file_types "/home/usuario"
```

## **Casos de Uso Específicos**

### **Limpieza de Cache de Pacman**

```bash
#!/bin/bash
# Script para análisis y limpieza del cache de pacman

analyze_pacman_cache() {
    local cache_dir="/var/cache/pacman/pkg"
    
    echo "=== Análisis del Cache de Pacman ==="
    echo
    
    if [[ ! -d "$cache_dir" ]]; then
        echo "❌ Directorio de cache no encontrado: $cache_dir"
        return 1
    fi
    
    # Tamaño total del cache
    total_size=$(du -sh "$cache_dir" | cut -f1)
    total_files=$(find "$cache_dir" -name "*.pkg.tar.*" | wc -l)
    
    echo "📦 Tamaño total del cache: $total_size"
    echo "📄 Número de paquetes: $total_files"
    echo
    
    # Paquetes más grandes
    echo "🔝 Paquetes más grandes en cache:"
    find "$cache_dir" -name "*.pkg.tar.*" -exec du -h {} \; | \
    sort -hr | head -15
    echo
    
    # Paquetes duplicados (versiones múltiples)
    echo "🔄 Paquetes con múltiples versiones:"
    find "$cache_dir" -name "*.pkg.tar.*" | \
    sed 's/-[0-9].*$//' | sort | uniq -d | \
    while read package_base; do
        echo "  $package_base:"
        find "$cache_dir" -name "${package_base}-*.pkg.tar.*" -exec basename {} \; | sort
        echo
    done | head -30
    
    # Paquetes no instalados
    echo "❌ Paquetes en cache no instalados actualmente:"
    local not_installed=0
    find "$cache_dir" -name "*.pkg.tar.*" | \
    while read pkg_file; do
        pkg_name=$(basename "$pkg_file" | sed -E 's/-[0-9]+.*$//')
        if ! pacman -Q "$pkg_name" >/dev/null 2>&1; then
            echo "  $(basename $pkg_file)"
            ((not_installed++))
        fi
    done | head -20
    
    echo
    echo "🧹 Comandos de limpieza recomendados:"
    echo "  sudo pacman -Sc    # Limpiar paquetes no instalados"
    echo "  sudo pacman -Scc   # Limpiar todo el cache"
    echo "  paccache -r        # Mantener solo 3 versiones (si tienes pacman-contrib)"
}

# Función para limpieza inteligente
smart_cache_cleanup() {
    echo "=== Limpieza Inteligente del Cache ==="
    echo
    
    # Verificar espacio disponible
    available_space=$(df /var/cache/pacman/pkg | awk 'NR==2 {print $4}')
    cache_size=$(du -s /var/cache/pacman/pkg | cut -f1)
    
    echo "💾 Espacio disponible: $(echo $available_space | numfmt --to=iec)"
    echo "📦 Tamaño del cache: $(echo $cache_size | numfmt --to=iec)"
    
    # Si el cache es muy grande comparado con el espacio disponible
    if [[ $cache_size -gt $((available_space * 2)) ]]; then
        echo "⚠️ El cache es grande comparado con el espacio disponible"
        echo "🧹 Recomendación: limpiar cache antiguo"
        
        if command -v paccache >/dev/null; then
            echo "Ejecutando paccache para mantener 2 versiones..."
            sudo paccache -rk2
        else
            echo "Instala pacman-contrib para usar paccache: sudo pacman -S pacman-contrib"
        fi
    else
        echo "✅ El tamaño del cache es razonable"
    fi
}

analyze_pacman_cache
smart_cache_cleanup
```

### **Análisis de Logs del Sistema**

```bash
#!/bin/bash
# Script para análisis de logs del sistema

analyze_system_logs() {
    local log_dir="/var/log"
    
    echo "=== Análisis de Logs del Sistema ==="
    echo
    
    if [[ ! -d "$log_dir" ]]; then
        echo "❌ Directorio de logs no encontrado: $log_dir"
        return 1
    fi
    
    # Tamaño total de logs
    total_size=$(du -sh "$log_dir" | cut -f1)
    echo "📊 Tamaño total de logs: $total_size"
    echo
    
    # Logs más grandes
    echo "📄 Logs más grandes:"
    find "$log_dir" -type f -exec du -h {} \; 2>/dev/null | \
    sort -hr | head -15
    echo
    
    # Logs por tipo
    echo "🗂️ Logs por categoría:"
    du -sh "$log_dir"/{journal,btmp,wtmp,lastlog,pacman.log,Xorg.*.log} 2>/dev/null | sort -hr
    echo
    
    # Logs de journalctl
    if systemctl is-active systemd-journald >/dev/null; then
        echo "📰 Análisis de Journal:"
        journalctl --disk-usage
        echo
        
        echo "⏰ Período de logs en journal:"
        echo "  Desde: $(journalctl --list-boots | tail -1 | awk '{print $4" "$5}')"
        echo "  Hasta: $(journalctl --list-boots | head -1 | awk '{print $4" "$5}')"
        echo
    fi
    
    # Logs que crecen rápidamente
    echo "📈 Logs con actividad reciente (última hora):"
    find "$log_dir" -type f -mmin -60 -exec du -h {} \; 2>/dev/null | sort -hr
    echo
    
    # Recomendaciones de limpieza
    echo "🧹 Opciones de limpieza:"
    echo "  sudo journalctl --vacuum-time=30d    # Mantener journal 30 días"
    echo "  sudo journalctl --vacuum-size=100M   # Limitar journal a 100MB"
    echo "  sudo logrotate -f /etc/logrotate.conf # Forzar rotación de logs"
    
    # Verificar configuración de logrotate
    if [[ -f /etc/logrotate.conf ]]; then
        echo "  Configuración de logrotate encontrada"
    else
        echo "  ⚠️ Considera configurar logrotate para gestión automática"
    fi
}

# Función para limpieza segura de logs
safe_log_cleanup() {
    echo "=== Limpieza Segura de Logs ==="
    echo
    
    # Hacer backup de logs importantes antes de limpiar
    local backup_dir="/tmp/log_backup_$(date +%Y%m%d)"
    mkdir -p "$backup_dir"
    
    echo "💾 Creando backup de logs importantes..."
    sudo cp /var/log/pacman.log "$backup_dir/" 2>/dev/null
    sudo cp /var/log/Xorg.0.log "$backup_dir/" 2>/dev/null
    
    # Limpiar journal manteniendo 30 días
    echo "🧹 Limpiando journal (manteniendo 30 días)..."
    sudo journalctl --vacuum-time=30d
    
    # Rotar logs
    echo "🔄 Forzando rotación de logs..."
    sudo logrotate -f /etc/logrotate.conf
    
    echo "✅ Limpieza completada"
    echo "📁 Backup disponible en: $backup_dir"
}

analyze_system_logs
```

### **Monitoreo de Uso de Home**

```bash
#!/bin/bash
# Script para monitorear el uso del directorio home

monitor_home_usage() {
    local user_dir="/home/$(whoami)"
    
    echo "=== Análisis del Directorio Home ==="
    echo "Usuario: $(whoami)"
    echo "Directorio: $user_dir"
    echo
    
    # Tamaño total del home
    total_size=$(du -sh "$user_dir" | cut -f1)
    echo "📁 Tamaño total del home: $total_size"
    echo
    
    # Subdirectorios principales
    echo "📊 Subdirectorios principales:"
    du -h --max-depth=1 "$user_dir" 2>/dev/null | sort -hr | head -20
    echo
    
    # Archivos ocultos grandes
    echo "👻 Archivos/directorios ocultos grandes:"
    du -sh "$user_dir"/.[!.]* 2>/dev/null | sort -hr | head -10
    echo
    
    # Cache de aplicaciones
    echo "🗂️ Cache de aplicaciones:"
    if [[ -d "$user_dir/.cache" ]]; then
        du -h --max-depth=1 "$user_dir/.cache" 2>/dev/null | sort -hr | head -15
    fi
    echo
    
    # Configuraciones de aplicaciones
    echo "⚙️ Configuraciones de aplicaciones:"
    if [[ -d "$user_dir/.config" ]]; then
        du -h --max-depth=1 "$user_dir/.config" 2>/dev/null | sort -hr | head -15
    fi
    echo
    
    # Archivos grandes específicos
    echo "📄 Archivos individuales más grandes:"
    find "$user_dir" -type f -exec du -h {} \; 2>/dev/null | sort -hr | head -15
    echo
    
    # Archivos temporales
    echo "🗑️ Posibles archivos temporales a limpiar:"
    find "$user_dir" -name "*.tmp" -o -name "*.temp" -o -name "core.*" -o -name ".DS_Store" 2>/dev/null | \
    head -20 | while read file; do
        echo "  $(du -h "$file" 2>/dev/null || echo "0B") - $file"
    done
    
    # Recomendaciones
    echo
    echo "💡 Recomendaciones de limpieza:"
    echo "  # Limpiar cache del navegador:"
    echo "  rm -rf ~/.cache/mozilla ~/.cache/chromium"
    echo "  # Limpiar thumbnails:"
    echo "  rm -rf ~/.cache/thumbnails"
    echo "  # Limpiar archivos temporales:"
    echo "  find ~ -name '*.tmp' -delete"
    echo "  # Limpiar papelera:"
    echo "  rm -rf ~/.local/share/Trash/*"
}

monitor_home_usage
```

## **Comparación y Diferencias**

### **du vs df - Diferencias Clave**

```bash
#!/bin/bash
# Script para mostrar diferencias entre du y df

compare_du_df() {
    local target_dir="${1:-/}"
    
    echo "=== Comparación du vs df para $target_dir ==="
    echo
    
    # Mostrar resultado de df
    echo "📊 df (espacio del filesystem):"
    df -h "$target_dir"
    echo
    
    # Mostrar resultado de du
    echo "📁 du (uso de archivos/directorios):"
    du -sh "$target_dir"
    echo
    
    # Explicar diferencias
    echo "🔍 Diferencias clave:"
    echo "• df muestra el espacio usado/disponible en el FILESYSTEM completo"
    echo "• du muestra el espacio usado por ARCHIVOS y DIRECTORIOS específicos"
    echo "• df incluye metadatos del filesystem, inodos, etc."
    echo "• du puede mostrar menos uso si hay archivos eliminados pero aún abiertos"
    echo "• df considera reservas del sistema (típicamente 5% en ext4)"
    echo
    
    # Detectar discrepancias
    filesystem_usage=$(df "$target_dir" | awk 'NR==2 {print $3}')
    directory_usage=$(du -s "$target_dir" | cut -f1)
    
    if [[ $filesystem_usage -gt $((directory_usage * 110 / 100)) ]]; then
        echo "⚠️ Discrepancia detectada:"
        echo "  Posibles causas:"
        echo "  - Archivos eliminados pero aún abiertos por procesos"
        echo "  - Reserva del sistema"
        echo "  - Archivos en otros puntos de montaje"
        echo "  - Metadatos del filesystem"
    fi
}

compare_du_df "/"
```

### **du vs ls - Cuándo Usar Cada Uno**

```bash
#!/bin/bash
# Comparación entre du y ls para análisis de archivos

compare_du_ls() {
    local target_dir="${1:-.}"
    
    echo "=== Comparación du vs ls ==="
    echo
    
    echo "📋 ls -la (información detallada de archivos):"
    ls -lah "$target_dir" | head -10
    echo
    
    echo "📊 du -h (uso de espacio):"
    du -h --max-depth=1 "$target_dir" | sort -hr | head -10
    echo
    
    echo "🎯 Cuándo usar cada uno:"
    echo "• ls: Ver permisos, fechas, propietarios, enlaces"
    echo "• du: Analizar uso de espacio, encontrar directorios grandes"
    echo "• ls: Trabajar con archivos individuales"
    echo "• du: Análisis de almacenamiento y limpieza"
}

compare_du_ls "/var/log"
```

## **Optimización y Performance**

### **Optimización de du para Directorios Grandes**

```bash
#!/bin/bash
# Script optimizado para análisis de directorios muy grandes

fast_directory_analysis() {
    local target_dir="$1"
    local max_depth="${2:-2}"
    
    echo "=== Análisis Rápido de Directorio Grande ==="
    echo "Directorio: $target_dir"
    echo "Profundidad máxima: $max_depth"
    echo
    
    # Análisis paralelo por subdirectorios principales
    echo "🚀 Ejecutando análisis paralelo..."
    
    find "$target_dir" -maxdepth 1 -type d | while read subdir; do
        if [[ "$subdir" != "$target_dir" ]]; then
            (
                size=$(du -sh "$subdir" 2>/dev/null | cut -f1)
                echo "$size $subdir"
            ) &
        fi
    done | sort -hr
    
    # Esperar que terminen todos los procesos
    wait
    
    echo
    echo "✅ Análisis paralelo completado"
}

# Función para encontrar archivos grandes rápidamente
find_large_files_fast() {
    local target_dir="$1"
    local min_size="${2:-100M}"
    
    echo "=== Búsqueda Rápida de Archivos Grandes (≥$min_size) ==="
    echo
    
    # Usar find con -size para búsqueda eficiente
    find "$target_dir" -type f -size +"$min_size" -exec du -h {} \; 2>/dev/null | \
    sort -hr | head -20
}

fast_directory_analysis "/usr" 2
find_large_files_fast "/home" "50M"
```

### **Script de Análisis Programado**

```bash
#!/bin/bash
# Script para análisis programado de uso de disco

scheduled_disk_analysis() {
    local report_file="/var/log/disk_usage_$(date +%Y%m%d).log"
    local alert_threshold=90  # Porcentaje de uso para alertas
    
    echo "=== Análisis Programado de Disco - $(date) ===" | tee "$report_file"
    echo | tee -a "$report_file"
    
    # Análisis de uso general
    echo "📊 Uso de filesystem:" | tee -a "$report_file"
    df -h | tee -a "$report_file"
    echo | tee -a "$report_file"
    
    # Verificar si algún filesystem supera el umbral
    df | awk 'NR>1 {gsub(/%/, "", $5); if($5 > threshold) print $0}' threshold="$alert_threshold" | while read line; do
        echo "🚨 ALERTA: Filesystem con uso alto: $line" | tee -a "$report_file"
        
        # Analizar qué está ocupando espacio
        filesystem=$(echo "$line" | awk '{print $6}')
        echo "🔍 Análisis detallado de $filesystem:" | tee -a "$report_file"
        du -h --max-depth=1 "$filesystem" 2>/dev/null | sort -hr | head -10 | tee -a "$report_file"
        echo | tee -a "$report_file"
    done
    
    # Top directorios que más han crecido
    echo "📈 Directorios con mayor actividad:" | tee -a "$report_file"
    find /var /home /tmp -type f -mtime -1 -exec dirname {} \; 2>/dev/null | \
    sort | uniq -c | sort -nr | head -10 | tee -a "$report_file"
    echo | tee -a "$report_file"
    
    # Limpieza automática si es necesario
    auto_cleanup_if_needed "$alert_threshold"
    
    echo "📄 Reporte guardado en: $report_file"
}

# Función de limpieza automática
auto_cleanup_if_needed() {
    local threshold="$1"
    
    # Verificar si /tmp está lleno
    tmp_usage=$(df /tmp | awk 'NR==2 {gsub(/%/, "", $5); print $5}')
    if [[ $tmp_usage -gt $threshold ]]; then
        echo "🧹 Limpiando /tmp automáticamente..." | tee -a "$report_file"
        find /tmp -type f -mtime +7 -delete 2>/dev/null
        echo "✅ Limpieza de /tmp completada" | tee -a "$report_file"
    fi
    
    # Verificar si el cache de pacman es muy grande
    cache_size=$(du -s /var/cache/pacman/pkg 2>/dev/null | cut -f1)
    available_space=$(df /var/cache/pacman/pkg | awk 'NR==2 {print $4}')
    
    if [[ $cache_size -gt $((available_space / 2)) ]]; then
        echo "🧹 Cache de pacman muy grande, ejecutando limpieza..." | tee -a "$report_file"
        if command -v paccache >/dev/null; then
            paccache -rk2 2>/dev/null
            echo "✅ Cache de pacman limpiado" | tee -a "$report_file"
        fi
    fi
}

# Configurar como tarea cron
setup_scheduled_analysis() {
    echo "=== Configuración de Análisis Programado ==="
    echo
    
    # Crear script ejecutable
    cat > /usr/local/bin/disk-analysis.sh << 'EOF'
#!/bin/bash
# Script de análisis programado generado automáticamente

# ... (incluir funciones del script aquí)
scheduled_disk_analysis
EOF
    
    chmod +x /usr/local/bin/disk-analysis.sh
    
    # Agregar a cron
    echo "Agregando tarea a cron (ejecutar diariamente a las 6:00 AM):"
    echo "0 6 * * * /usr/local/bin/disk-analysis.sh"
    echo
    echo "Para instalar:"
    echo "echo '0 6 * * * /usr/local/bin/disk-analysis.sh' | crontab -"
}

scheduled_disk_analysis
```

## **Troubleshooting**

### **Problemas Comunes**

```bash
#!/bin/bash
# Script de troubleshooting para du

troubleshoot_du() {
    echo "=== Troubleshooting de du ==="
    echo
    
    # Verificar permisos
    echo "🔐 Verificando permisos:"
    if du /root >/dev/null 2>&1; then
        echo "  ✅ Acceso a /root disponible"
    else
        echo "  ⚠️ Sin acceso a /root (normal para usuarios no-root)"
    fi
    
    # Verificar filesystem
    echo "💾 Verificando filesystem:"
    df -T | head -5
    echo
    
    # Problemas de rendimiento
    echo "⚡ Diagnóstico de rendimiento:"
    
    # Test de velocidad de du
    start_time=$(date +%s.%N)
    du -sh /usr >/dev/null 2>&1
    end_time=$(date +%s.%N)
    duration=$(echo "$end_time - $start_time" | bc)
    
    echo "  Tiempo para analizar /usr: ${duration}s"
    
    if (( $(echo "$duration > 10" | bc -l) )); then
        echo "  ⚠️ Análisis lento detectado"
        echo "    - Considera usar --max-depth para limitar profundidad"
        echo "    - Verifica si hay problemas de I/O en el disco"
        echo "    - Usa análisis paralelo para directorios grandes"
    else
        echo "  ✅ Rendimiento normal"
    fi
    
    # Verificar inconsistencias
    echo
    echo "🔍 Verificando inconsistencias du vs df:"
    
    root_du=$(du -s / 2>/dev/null | cut -f1)
    root_df=$(df / | awk 'NR==2 {print $3}')
    
    if [[ -n "$root_du" && -n "$root_df" ]]; then
        ratio=$(echo "scale=2; $root_df / $root_du" | bc)
        echo "  Ratio df/du: $ratio"
        
        if (( $(echo "$ratio > 1.5" | bc -l) )); then
            echo "  ⚠️ Discrepancia significativa detectada"
            echo "    Posibles causas:"
            echo "    - Archivos eliminados pero procesos aún los tienen abiertos"
            echo "    - Reserva del filesystem"
            echo "    - Montajes ocultos"
            echo
            echo "  Comandos para investigar:"
            echo "    lsof +L1           # Archivos eliminados pero abiertos"
            echo "    mount | grep -v proc # Verificar montajes"
        else
            echo "  ✅ Ratio normal"
        fi
    fi
    
    # Verificar archivos eliminados pero abiertos
    echo
    echo "👻 Verificando archivos eliminados pero abiertos:"
    if command -v lsof >/dev/null; then
        deleted_files=$(lsof +L1 2>/dev/null | wc -l)
        if [[ $deleted_files -gt 0 ]]; then
            echo "  ⚠️ $deleted_files archivos eliminados pero aún abiertos"
            echo "  Ejecuta 'lsof +L1' para ver detalles"
        else
            echo "  ✅ No hay archivos eliminados abiertos"
        fi
    else
        echo "  📦 lsof no disponible (sudo pacman -S lsof)"
    fi
}

troubleshoot_du
```

## **Enlaces Relacionados**

- [[df]] - Verificar espacio disponible en filesystems
- [[find]] - Buscar archivos específicos por tamaño
- [[ls]] - Listar archivos con información detallada
- [[sort]] - Ordenar resultados de du por tamaño
- [[grep]] - Filtrar resultados de análisis de du
- [[ps]] - Ver procesos que pueden estar usando archivos eliminados