# stat - Información Detallada de Archivos y Sistema

El comando `stat` es tu **inspector de archivos**. Proporciona información exhaustiva sobre archivos, directorios y sistemas de archivos, revelando metadatos que otros comandos no muestran, siendo esencial para administración de sistemas y forensia digital.

## **¿Qué es stat?**

`stat` te permite:
- **Ver metadatos completos** de archivos y directorios
- **Obtener información del sistema de archivos**
- **Acceder a timestamps precisos** y permisos detallados
- **Analizar inodos** y enlaces

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, `stat` es crucial para debugging de permisos, análisis forense, scripting avanzado, monitoreo de cambios en archivos y troubleshooting del sistema de archivos.

### **Sintaxis Básica**

```bash
# Información básica de archivo
stat archivo.txt

# Información de directorio
stat /home/usuario/

# Información del sistema de archivos
stat -f /

# Formato personalizado
stat -c "%n: %s bytes" archivo.txt
```

### **Opciones Fundamentales**

```bash
# -c, --format=FORMAT  = usar formato personalizado
# -f, --file-system    = mostrar información del sistema de archivos
# -L, --dereference    = seguir enlaces simbólicos
# -t, --terse          = formato conciso
# --printf=FORMAT      = printf-style formatting
```

## **Información Básica de Archivos**

### **Salida Estándar de stat**

```bash
# Información completa de archivo
stat documento.txt

# Ejemplo de salida:
#   File: documento.txt
#   Size: 1024      Blocks: 8          IO Block: 4096   regular file
#   Device: 803h/2051d    Inode: 123456      Links: 1
#   Access: (0644/-rw-r--r--)  Uid: (1000/usuario)   Gid: (1000/usuario)
#   Access: 2024-01-15 10:30:00.123456789 +0100
#   Modify: 2024-01-15 10:25:30.987654321 +0100
#   Change: 2024-01-15 10:25:30.987654321 +0100
#   Birth: 2024-01-15 10:25:30.987654321 +0100

# Información de directorio
stat /etc/
```

### **Interpretación de la Información**

```bash
# File: nombre del archivo
# Size: tamaño en bytes
# Blocks: bloques de disco utilizados
# IO Block: tamaño del bloque de E/S
# Device: dispositivo que contiene el archivo
# Inode: número de inodo
# Links: número de enlaces duros
# Access: permisos y propietario
# Access: última vez que se accedió al archivo
# Modify: última modificación del contenido
# Change: último cambio de metadatos
# Birth: fecha de creación (si está disponible)
```

## **Casos de Uso en Arch Linux**

### **Análisis de Permisos y Propietarios**

```bash
# Verificar permisos detallados
stat -c "Archivo: %n, Permisos: %a (%A), Propietario: %U:%G" /etc/passwd

# Comparar permisos entre archivos
for file in /etc/passwd /etc/shadow /etc/group; do
    echo "$(stat -c '%n: %a %U:%G' "$file")"
done

# Verificar archivos SUID/SGID
find /usr/bin -perm /6000 -exec stat -c "%n: %a %U:%G" {} \;

# Auditoría de permisos en directorio web
stat -c "%n: %a %U:%G %y" /var/www/html/*
```

### **Análisis de Timestamps**

```bash
# Ver timestamps en formato legible
stat -c "Archivo: %n
Acceso:    %x
Modificado: %y
Cambio:     %z
Creación:   %w" archivo.txt

# Encontrar archivos modificados recientemente
find /etc -name "*.conf" -exec stat -c "%y %n" {} \; | sort

# Comparar timestamps entre archivos
stat -c "%n: %Y" archivo1 archivo2 | \
while read timestamp file; do
    echo "$file: $(date -d @$timestamp)"
done

# Archivos accedidos en las últimas 24 horas
find /home -atime -1 -exec stat -c "%x %n" {} \;
```

### **Monitoreo del Sistema de Archivos**

```bash
# Información del sistema de archivos
stat -f /

# Ejemplo de salida del sistema de archivos:
#   File: "/"
#   ID: 8030000000000 Namelen: 255     Type: ext4
#   Block size: 4096       Fundamental block size: 4096
#   Blocks: Total: 12345678   Free: 9876543   Available: 9000000
#   Inodes: Total: 3145728    Free: 2900000

# Verificar espacio disponible en particiones
for mount in / /home /var; do
    echo "=== $mount ==="
    stat -f -c "Bloques libres: %f de %b (%P disponible)" "$mount"
done

# Monitorear uso de inodos
stat -f -c "Inodos libres: %d de %c (%P disponible)" /
```

## **Formatos Personalizados**

### **Formatos de Salida Comunes**

```bash
# Formato conciso para scripts
stat -c "%n %s %Y" archivo.txt          # nombre tamaño timestamp_modificación

# Información de permisos
stat -c "%n: %a (%A) %U:%G" archivo.txt # nombre permisos_octal permisos_simbólico usuario:grupo

# Tamaño legible para humanos
stat -c "%n: %s bytes (%s)" archivo.txt | \
sed 's/bytes (\([0-9]*\))/bytes (\1 = $(numfmt --to=iec \1))/'

# Información completa en una línea
stat -c "%n|%s|%a|%U|%G|%x|%y|%z" archivo.txt
```

### **Scripts con Formatos Personalizados**

```bash
#!/bin/bash
# Script para análisis detallado de archivos

analyze_file() {
    local file="$1"
    
    echo "=== Análisis de $file ==="
    
    # Información básica
    echo "Tamaño: $(stat -c '%s' "$file") bytes ($(stat -c '%s' "$file" | numfmt --to=iec))"
    echo "Tipo: $(stat -c '%F' "$file")"
    echo "Permisos: $(stat -c '%a (%A)' "$file")"
    echo "Propietario: $(stat -c '%U:%G (%u:%g)' "$file")"
    echo "Inodo: $(stat -c '%i' "$file")"
    echo "Enlaces: $(stat -c '%h' "$file")"
    echo "Dispositivo: $(stat -c '%t:%T' "$file")"
    
    # Timestamps
    echo "Acceso: $(stat -c '%x' "$file")"
    echo "Modificación: $(stat -c '%y' "$file")"
    echo "Cambio: $(stat -c '%z' "$file")"
    
    # Información adicional según el tipo
    if [[ -f "$file" ]]; then
        echo "Bloques: $(stat -c '%b' "$file")"
        echo "Tamaño de bloque: $(stat -c '%B' "$file")"
    fi
    
    if [[ -L "$file" ]]; then
        echo "Enlace a: $(readlink "$file")"
    fi
    
    echo
}

# Usar la función
analyze_file "/etc/passwd"
analyze_file "/home/usuario/.bashrc"
```

### **Comparación de Archivos**

```bash
#!/bin/bash
# Comparar metadatos de archivos

compare_files() {
    local file1="$1"
    local file2="$2"
    
    echo "Comparando $file1 y $file2:"
    echo
    
    # Tamaños
    local size1=$(stat -c '%s' "$file1")
    local size2=$(stat -c '%s' "$file2")
    echo "Tamaños: $size1 vs $size2 bytes"
    [[ $size1 -eq $size2 ]] && echo "  ✓ Mismo tamaño" || echo "  ✗ Tamaños diferentes"
    
    # Permisos
    local perm1=$(stat -c '%a' "$file1")
    local perm2=$(stat -c '%a' "$file2")
    echo "Permisos: $perm1 vs $perm2"
    [[ "$perm1" == "$perm2" ]] && echo "  ✓ Mismos permisos" || echo "  ✗ Permisos diferentes"
    
    # Propietarios
    local owner1=$(stat -c '%U:%G' "$file1")
    local owner2=$(stat -c '%U:%G' "$file2")
    echo "Propietarios: $owner1 vs $owner2"
    [[ "$owner1" == "$owner2" ]] && echo "  ✓ Mismo propietario" || echo "  ✗ Propietarios diferentes"
    
    # Timestamps de modificación
    local mtime1=$(stat -c '%Y' "$file1")
    local mtime2=$(stat -c '%Y' "$file2")
    echo "Última modificación:"
    echo "  $file1: $(date -d @$mtime1)"
    echo "  $file2: $(date -d @$mtime2)"
    
    if [[ $mtime1 -gt $mtime2 ]]; then
        echo "  → $file1 es más reciente"
    elif [[ $mtime2 -gt $mtime1 ]]; then
        echo "  → $file2 es más reciente"
    else
        echo "  ✓ Misma fecha de modificación"
    fi
    
    echo
}

# Usar la función
compare_files "archivo1.txt" "archivo2.txt"
```

## **Análisis de Sistemas de Archivos**

### **Información Detallada del Sistema de Archivos**

```bash
# Estadísticas completas del sistema de archivos
analyze_filesystem() {
    local mount_point="$1"
    
    echo "=== Análisis del sistema de archivos: $mount_point ==="
    
    # Información básica
    echo "Tipo: $(stat -f -c '%T' "$mount_point")"
    echo "ID: $(stat -f -c '%i' "$mount_point")"
    echo "Longitud máxima de nombre: $(stat -f -c '%l' "$mount_point")"
    
    # Bloques
    local total_blocks=$(stat -f -c '%b' "$mount_point")
    local free_blocks=$(stat -f -c '%f' "$mount_point")
    local available_blocks=$(stat -f -c '%a' "$mount_point")
    local block_size=$(stat -f -c '%s' "$mount_point")
    
    echo "Bloques:"
    echo "  Total: $total_blocks ($(($total_blocks * $block_size | numfmt --to=iec)))"
    echo "  Libres: $free_blocks ($(($free_blocks * $block_size | numfmt --to=iec)))"
    echo "  Disponibles: $available_blocks ($(($available_blocks * $block_size | numfmt --to=iec)))"
    echo "  Tamaño de bloque: $block_size bytes"
    
    # Inodos
    local total_inodes=$(stat -f -c '%c' "$mount_point")
    local free_inodes=$(stat -f -c '%d' "$mount_point")
    
    echo "Inodos:"
    echo "  Total: $total_inodes"
    echo "  Libres: $free_inodes"
    echo "  Usados: $(($total_inodes - $free_inodes))"
    echo "  Uso: $(echo "scale=2; (($total_inodes - $free_inodes) * 100) / $total_inodes" | bc)%"
    
    echo
}

# Analizar sistemas de archivos montados
for fs in / /home /var /tmp; do
    [[ -d "$fs" ]] && analyze_filesystem "$fs"
done
```

### **Monitoreo de Espacio y Rendimiento**

```bash
#!/bin/bash
# Monitor de sistemas de archivos

monitor_filesystem() {
    local threshold=${1:-90}  # Umbral de alerta por defecto 90%
    
    echo "=== Monitor de Sistemas de Archivos ==="
    echo "Umbral de alerta: ${threshold}%"
    echo
    
    # Obtener puntos de montaje
    local mount_points=($(findmnt -rn -o TARGET | grep -E '^/(|home|var|tmp|opt)$'))
    
    for mount in "${mount_points[@]}"; do
        local total_blocks=$(stat -f -c '%b' "$mount")
        local available_blocks=$(stat -f -c '%a' "$mount")
        local used_blocks=$(($total_blocks - $available_blocks))
        local usage_percent=$(echo "scale=1; ($used_blocks * 100) / $total_blocks" | bc)
        
        echo -n "$mount: ${usage_percent}% usado "
        
        if (( $(echo "$usage_percent >= $threshold" | bc -l) )); then
            echo "⚠ ALERTA - Espacio bajo"
        else
            echo "✓ OK"
        fi
        
        # Información adicional para alertas
        if (( $(echo "$usage_percent >= $threshold" | bc -l) )); then
            local block_size=$(stat -f -c '%s' "$mount")
            local available_gb=$(echo "scale=2; ($available_blocks * $block_size) / 1024^3" | bc)
            echo "  Espacio disponible: ${available_gb} GB"
        fi
    done
    
    echo
}

# Ejecutar monitoreo
monitor_filesystem 85
```

## **Scripting Avanzado con stat**

### **Backup Basado en Metadatos**

```bash
#!/bin/bash
# Backup inteligente basado en metadatos

BACKUP_SOURCE="/home/usuario/documentos"
BACKUP_DEST="/backup/documentos"
BACKUP_LOG="/var/log/smart_backup.log"

smart_backup() {
    local source="$1"
    local destination="$2"
    
    log_message() {
        echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$BACKUP_LOG"
    }
    
    log_message "Iniciando backup inteligente de $source a $destination"
    
    find "$source" -type f | while read -r file; do
        # Obtener metadatos del archivo fuente
        local src_mtime=$(stat -c '%Y' "$file")
        local src_size=$(stat -c '%s' "$file")
        local src_inode=$(stat -c '%i' "$file")
        
        # Calcular ruta de destino
        local rel_path="${file#$source/}"
        local dest_file="$destination/$rel_path"
        
        # Verificar si necesita backup
        local needs_backup=true
        
        if [[ -f "$dest_file" ]]; then
            local dest_mtime=$(stat -c '%Y' "$dest_file")
            local dest_size=$(stat -c '%s' "$dest_file")
            
            # No hacer backup si archivo destino es igual o más reciente y del mismo tamaño
            if [[ $dest_mtime -ge $src_mtime ]] && [[ $dest_size -eq $src_size ]]; then
                needs_backup=false
            fi
        fi
        
        if [[ $needs_backup == true ]]; then
            # Crear directorio si no existe
            mkdir -p "$(dirname "$dest_file")"
            
            # Copiar archivo preservando metadatos
            cp -p "$file" "$dest_file"
            
            log_message "Copiado: $rel_path ($(numfmt --to=iec $src_size))"
        fi
    done
    
    log_message "Backup completado"
}

smart_backup "$BACKUP_SOURCE" "$BACKUP_DEST"
```

### **Análisis Forense de Archivos**

```bash
#!/bin/bash
# Herramientas de análisis forense

forensic_analysis() {
    local target="$1"
    local report_file="forensic_report_$(date +%Y%m%d_%H%M%S).txt"
    
    echo "=== REPORTE FORENSE ===" > "$report_file"
    echo "Fecha del análisis: $(date)" >> "$report_file"
    echo "Analista: $(whoami)" >> "$report_file"
    echo "Objetivo: $target" >> "$report_file"
    echo >> "$report_file"
    
    if [[ -f "$target" ]]; then
        echo "--- METADATOS DEL ARCHIVO ---" >> "$report_file"
        stat "$target" >> "$report_file"
        echo >> "$report_file"
        
        # Verificar integridad
        echo "--- INTEGRIDAD ---" >> "$report_file"
        echo "MD5: $(md5sum "$target" | cut -d' ' -f1)" >> "$report_file"
        echo "SHA256: $(sha256sum "$target" | cut -d' ' -f1)" >> "$report_file"
        echo >> "$report_file"
        
        # Análisis de timestamps
        echo "--- ANÁLISIS TEMPORAL ---" >> "$report_file"
        local atime=$(stat -c '%X' "$target")
        local mtime=$(stat -c '%Y' "$target")
        local ctime=$(stat -c '%Z' "$target")
        
        echo "Último acceso: $(date -d @$atime) (timestamp: $atime)" >> "$report_file"
        echo "Última modificación: $(date -d @$mtime) (timestamp: $mtime)" >> "$report_file"
        echo "Último cambio de metadatos: $(date -d @$ctime) (timestamp: $ctime)" >> "$report_file"
        
        # Anomalías temporales
        if [[ $mtime -gt $ctime ]]; then
            echo "⚠ ANOMALÍA: mtime posterior a ctime" >> "$report_file"
        fi
        
        if [[ $atime -lt $mtime ]]; then
            echo "⚠ NOTA: archivo no accedido desde última modificación" >> "$report_file"
        fi
        
        echo >> "$report_file"
        
        # Información del propietario
        echo "--- PROPIETARIO ---" >> "$report_file"
        echo "Usuario: $(stat -c '%U (%u)' "$target")" >> "$report_file"
        echo "Grupo: $(stat -c '%G (%g)' "$target")" >> "$report_file"
        echo "Permisos: $(stat -c '%a (%A)' "$target")" >> "$report_file"
        echo >> "$report_file"
        
    elif [[ -d "$target" ]]; then
        echo "--- ANÁLISIS DE DIRECTORIO ---" >> "$report_file"
        stat "$target" >> "$report_file"
        echo >> "$report_file"
        
        echo "--- CONTENIDO DEL DIRECTORIO ---" >> "$report_file"
        find "$target" -maxdepth 1 -exec stat -c "%n: %y %a %U:%G" {} \; >> "$report_file"
        echo >> "$report_file"
    fi
    
    echo "Reporte generado: $report_file"
}

# Usar la función
forensic_analysis "/etc/passwd"
```

## **Integración con Otros Comandos**

### **Con [[find]] para Búsquedas Avanzadas**

```bash
# Buscar archivos por tamaño exacto
find /home -type f -exec stat -c '%s %n' {} \; | grep '^1024 '

# Buscar archivos modificados en rango de fechas
start_date=$(date -d "2024-01-01" +%s)
end_date=$(date -d "2024-01-31" +%s)

find /home -type f -exec stat -c '%Y %n' {} \; | \
while read timestamp file; do
    if [[ $timestamp -ge $start_date ]] && [[ $timestamp -le $end_date ]]; then
        echo "$file: $(date -d @$timestamp)"
    fi
done

# Buscar archivos con permisos específicos
find /etc -type f -exec stat -c '%a %n' {} \; | grep '^644 '
```

### **Con [[ls]] para Comparaciones**

```bash
# Comparar salidas de ls y stat
compare_ls_stat() {
    local file="$1"
    
    echo "=== Comparación ls vs stat para $file ==="
    echo "ls -la:"
    ls -la "$file"
    echo
    echo "stat:"
    stat "$file"
}

compare_ls_stat "/etc/passwd"
```

### **Con [[du]] para Análisis de Espacio**

```bash
# Combinar du y stat para análisis detallado
analyze_directory_space() {
    local dir="$1"
    
    echo "=== Análisis de espacio: $dir ==="
    
    # Tamaño reportado por du
    local du_size=$(du -sb "$dir" | cut -f1)
    echo "Tamaño según du: $(numfmt --to=iec $du_size)"
    
    # Información del directorio con stat
    echo "Metadatos del directorio:"
    stat -c "Tamaño: %s bytes, Bloques: %b, Inodo: %i" "$dir"
    
    # Archivos más grandes
    echo "Archivos más grandes:"
    find "$dir" -type f -exec stat -c '%s %n' {} \; | \
    sort -nr | head -5 | \
    while read size file; do
        echo "  $(numfmt --to=iec $size): $file"
    done
    
    echo
}

analyze_directory_space "/var/log"
```

## **Monitoreo y Alertas**

### **Sistema de Alertas basado en Metadatos**

```bash
#!/bin/bash
# Sistema de alertas para cambios en archivos críticos

WATCH_FILES=(
    "/etc/passwd"
    "/etc/shadow"
    "/etc/sudoers"
    "/etc/ssh/sshd_config"
)

BASELINE_FILE="/var/log/file_baseline.dat"
ALERT_LOG="/var/log/file_alerts.log"

# Crear baseline inicial
create_baseline() {
    echo "Creando baseline de archivos críticos..."
    > "$BASELINE_FILE"
    
    for file in "${WATCH_FILES[@]}"; do
        if [[ -f "$file" ]]; then
            echo "$file|$(stat -c '%Y|%s|%a|%U|%G|%i' "$file")" >> "$BASELINE_FILE"
        fi
    done
    
    echo "Baseline creado en $BASELINE_FILE"
}

# Verificar cambios
check_changes() {
    local changes_detected=false
    
    while IFS='|' read -r file mtime size perms user group inode; do
        if [[ -f "$file" ]]; then
            local current_mtime=$(stat -c '%Y' "$file")
            local current_size=$(stat -c '%s' "$file")
            local current_perms=$(stat -c '%a' "$file")
            local current_user=$(stat -c '%U' "$file")
            local current_group=$(stat -c '%G' "$file")
            local current_inode=$(stat -c '%i' "$file")
            
            # Verificar cambios
            if [[ "$current_mtime" != "$mtime" ]]; then
                log_alert "$file" "Modificado" "$(date -d @$mtime) → $(date -d @$current_mtime)"
                changes_detected=true
            fi
            
            if [[ "$current_size" != "$size" ]]; then
                log_alert "$file" "Tamaño cambiado" "$size → $current_size bytes"
                changes_detected=true
            fi
            
            if [[ "$current_perms" != "$perms" ]]; then
                log_alert "$file" "Permisos cambiados" "$perms → $current_perms"
                changes_detected=true
            fi
            
            if [[ "$current_user" != "$user" ]] || [[ "$current_group" != "$group" ]]; then
                log_alert "$file" "Propietario cambiado" "$user:$group → $current_user:$current_group"
                changes_detected=true
            fi
            
        else
            log_alert "$file" "Archivo eliminado" "El archivo ya no existe"
            changes_detected=true
        fi
    done < "$BASELINE_FILE"
    
    if [[ $changes_detected == false ]]; then
        echo "No se detectaron cambios en archivos críticos"
    fi
}

log_alert() {
    local file="$1"
    local change_type="$2"
    local details="$3"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    local message="[$timestamp] ALERTA: $file - $change_type: $details"
    echo "$message" | tee -a "$ALERT_LOG"
    
    # Enviar notificación del sistema si está disponible
    if command -v notify-send >/dev/null; then
        notify-send "Alerta de Seguridad" "$file: $change_type"
    fi
}

# Crear baseline si no existe
[[ ! -f "$BASELINE_FILE" ]] && create_baseline

# Verificar cambios
check_changes
```

## **Enlaces Relacionados**

- [[ls]] - Información básica vs detallada de stat
- [[find]] - Buscar archivos con criterios de stat
- [[du]] - Tamaño de directorios vs metadatos de stat
- [[file]] - Tipo de archivo complementando stat
- [[chmod]] - Modificar permisos que stat muestra
- [[touch]] - Modificar timestamps que stat reporta