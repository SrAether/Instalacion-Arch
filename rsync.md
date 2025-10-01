# rsync - Sincronización y Backup Avanzado

El comando `rsync` es tu **sincronizador maestro**. Es la herramienta más potente para sincronizar archivos y directorios, realizar backups incrementales y transferir datos de manera eficiente, tanto localmente como a través de la red.

## **¿Qué es rsync?**

`rsync` te permite:
- **Sincronizar directorios** de manera eficiente
- **Realizar backups incrementales** que solo copian cambios
- **Transferir archivos** con compresión y verificación
- **Mantener permisos y metadatos** intactos

## **¿Por qué es Fundamental en Arch Linux?**

rsync es esencial para administración de sistemas, backups automatizados, deployment de aplicaciones, sincronización de configuraciones y mantenimiento de réplicas de datos.

### **Sintaxis Básica**

```bash
# Sintaxis general
rsync [opciones] origen destino

# Sincronización local
rsync -av directorio1/ directorio2/

# Sincronización remota
rsync -av directorio/ usuario@servidor:/ruta/destino/

# Sincronización desde remoto
rsync -av usuario@servidor:/ruta/origen/ ./directorio/
```

### **Opciones Fundamentales**

```bash
# -a, --archive        = modo archivo (preserva todo)
# -v, --verbose        = modo detallado
# -r, --recursive      = recursivo
# -u, --update         = solo archivos más nuevos
# -z, --compress       = comprimir durante transferencia
# -P, --progress       = mostrar progreso
# --delete             = eliminar archivos en destino que no existen en origen
# --dry-run            = simular sin hacer cambios
# -e, --rsh            = especificar shell remoto (ssh)
# --exclude            = excluir archivos/directorios
```

## **Sincronización Básica**

### **Sincronización Local**

```bash
# Sincronización básica preservando atributos
rsync -av /home/usuario/documentos/ /backup/documentos/

# Sincronización con progreso
rsync -avP /home/usuario/fotos/ /backup/fotos/

# Sincronización eliminando archivos obsoletos
rsync -av --delete /home/usuario/proyectos/ /backup/proyectos/

# Solo actualizar archivos más nuevos
rsync -avu /home/usuario/configs/ /backup/configs/

# Simulación (dry-run) para ver qué se haría
rsync -av --dry-run /home/usuario/datos/ /backup/datos/
```

### **Sincronización Remota**

```bash
# Enviar archivos a servidor remoto
rsync -avz /home/usuario/sitio/ usuario@servidor:/var/www/html/

# Descargar archivos desde servidor
rsync -avz usuario@servidor:/var/backups/ /local/backups/

# Usar puerto SSH específico
rsync -avz -e "ssh -p 2222" archivos/ usuario@servidor:/destino/

# Sincronizar con compresión y progreso
rsync -avzP documentos/ servidor:/backup/documentos/

# Sincronizar eliminando archivos obsoletos en remoto
rsync -avz --delete web/ servidor:/var/www/html/
```

## **Casos de Uso en Arch Linux**

### **Backup del Sistema**

```bash
# Backup completo del sistema (excluyendo directorios temporales)
sudo rsync -aAXv \
    --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found"} \
    / /backup/sistema/

# Backup de configuraciones del sistema
sudo rsync -av /etc/ /backup/configuraciones/etc/

# Backup de home de usuarios
rsync -av /home/ /backup/usuarios/

# Backup de logs importantes
sudo rsync -av /var/log/ /backup/logs/
```

### **Mantenimiento de Servidores**

```bash
# Sincronizar configuraciones a múltiples servidores
SERVERS=("web1.ejemplo.com" "web2.ejemplo.com" "web3.ejemplo.com")

for server in "${SERVERS[@]}"; do
    echo "Sincronizando configuración a $server..."
    rsync -avz /local/config/ "$server:/etc/myapp/"
done

# Deployment de aplicación web
rsync -avz --delete \
    --exclude='*.log' \
    --exclude='node_modules/' \
    ./mi-app/ servidor:/var/www/mi-app/

# Sincronizar bases de datos (dumps)
rsync -avz --compress-level=9 \
    dumps/ servidor:/backup/databases/
```

### **Gestión de Contenido**

```bash
# Sincronizar directorio de medios
rsync -av --progress \
    /home/usuario/multimedia/ \
    servidor:/storage/multimedia/

# Sincronizar documentos con versionado
rsync -av --backup --backup-dir=/backup/versiones/$(date +%Y%m%d) \
    /home/usuario/documentos/ \
    /storage/documentos/

# Sincronizar código fuente
rsync -av --exclude='.git/' \
    --exclude='node_modules/' \
    --exclude='*.log' \
    ./proyecto/ servidor:/deploy/proyecto/
```

## **Opciones Avanzadas**

### **Filtros y Exclusiones**

```bash
# Excluir múltiples patrones
rsync -av \
    --exclude='*.tmp' \
    --exclude='*.log' \
    --exclude='cache/' \
    origen/ destino/

# Usar archivo de exclusiones
cat > exclude.txt << 'EOF'
*.tmp
*.log
*.cache
node_modules/
.git/
.DS_Store
Thumbs.db
EOF

rsync -av --exclude-from=exclude.txt origen/ destino/

# Incluir solo ciertos tipos de archivos
rsync -av \
    --include='*.txt' \
    --include='*.md' \
    --exclude='*' \
    documentos/ backup/textos/

# Filtros complejos
rsync -av \
    --filter='dir-merge /.rsync-filter' \
    --filter='exclude .git/' \
    proyecto/ servidor:/deploy/
```

### **Preservación de Atributos**

```bash
# Preservar todos los atributos posibles
rsync -aAXHv origen/ destino/
# -a = archivo (modo estándar)
# -A = ACLs (listas de control de acceso)
# -X = atributos extendidos
# -H = hard links

# Preservar atributos específicos
rsync -rlptgoD origen/ destino/
# -r = recursivo
# -l = links simbólicos
# -p = permisos
# -t = timestamps
# -g = grupo
# -o = propietario
# -D = dispositivos y archivos especiales

# Sincronización con checksums para verificación
rsync -avc origen/ destino/
```

### **Control de Ancho de Banda**

```bash
# Limitar ancho de banda (en KB/s)
rsync -av --bwlimit=1000 archivos/ servidor:/backup/

# Uso durante horarios específicos
if [[ $(date +%H) -ge 22 ]] || [[ $(date +%H) -le 6 ]]; then
    # Horario nocturno - sin límites
    rsync -av archivos/ servidor:/backup/
else
    # Horario diurno - limitado
    rsync -av --bwlimit=500 archivos/ servidor:/backup/
fi

# Limitar durante backup
rsync -av --bwlimit=2000 --progress \
    /home/usuario/ servidor:/backup/usuario/
```

## **Scripts de Backup Automatizado**

### **Script de Backup Incremental**

```bash
# Backup incremental simple con rsync

# Variables básicas
SOURCE="/home/usuario"
DEST="/backup"
DATE=$(date +%Y%m%d)

# Crear backup incremental
echo "Iniciando backup..."
rsync -av --delete "$SOURCE/" "$DEST/backup-$DATE/"

# Verificar resultado
if [[ $? -eq 0 ]]; then
    echo "✅ Backup completado en $DEST/backup-$DATE/"
else
    echo "❌ Backup falló"
fi
```

# Verificar espacio disponible
check_disk_space() {
    local available=$(df "$BACKUP_DEST" | awk 'NR==2 {print $4}')
    local required=$(du -s "$BACKUP_SOURCE" | awk '{print $1}')
    
    if [[ $available -lt $required ]]; then
        log "ERROR: Espacio insuficiente. Disponible: $available KB, Requerido: $required KB"
        return 1
    fi
    
    log "Espacio verificado. Disponible: $available KB, Requerido: $required KB"
    return 0
}

# Ejecutar backup
main() {
    log "=== Iniciando proceso de backup ==="
    
    check_disk_space || exit 1
    create_incremental_backup || exit 1
    cleanup_old_backups
    
    log "=== Proceso de backup completado ==="
}

main "$@"
```

### **Script de Sincronización Bidireccional**

```bash
#!/bin/bash
# Sincronización bidireccional inteligente

LOCAL_DIR="/home/usuario/sync"
REMOTE_HOST="servidor"
REMOTE_DIR="/home/usuario/sync"
CONFLICT_DIR="/tmp/sync_conflicts_$(date +%Y%m%d_%H%M%S)"

# Función para resolver conflictos
handle_conflicts() {
    local file="$1"
    local local_file="$LOCAL_DIR/$file"
    local remote_file="$REMOTE_HOST:$REMOTE_DIR/$file"
    
    mkdir -p "$CONFLICT_DIR"
    
    # Copiar ambas versiones
    cp "$local_file" "$CONFLICT_DIR/${file}.local"
    rsync "$remote_file" "$CONFLICT_DIR/${file}.remote"
    
    echo "CONFLICTO: $file"
    echo "Local:  $CONFLICT_DIR/${file}.local"
    echo "Remoto: $CONFLICT_DIR/${file}.remote"
}

# Sincronización bidireccional simple
echo "=== Sincronización Bidireccional ==="

# Enviar cambios locales
echo "Enviando cambios locales..."
rsync -av local/ servidor:remoto/

# Recibir cambios remotos
echo "Recibiendo cambios remotos..."
rsync -av servidor:remoto/ local/

echo "Sincronización completada"
        echo "✓ Sincronización completada exitosamente"
    else
        echo "⚠ Existen diferencias - revisar manualmente"
    fi
}

smart_sync
```

## **Monitoreo y Logging**

### **Backup con Logging Detallado**

```bash
#!/bin/bash
# Backup con logging comprehensivo

LOG_FILE="/var/log/rsync_backup.log"
STATS_FILE="/var/log/rsync_stats.log"

# Función de logging con niveles
log_message() {
    local level="$1"
    local message="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
    
    # También enviar a syslog
    logger -t "rsync_backup" "[$level] $message"
}

# Backup con estadísticas detalladas
backup_with_stats() {
    local source="$1"
    local destination="$2"
    
    log_message "INFO" "Iniciando backup de $source a $destination"
    
    # Ejecutar rsync con estadísticas
    rsync -av --stats --human-readable \
        --log-file="$LOG_FILE" \
        "$source/" "$destination/" \
        2>&1 | tee -a "$STATS_FILE"
    
    local exit_code=$?
    
    if [[ $exit_code -eq 0 ]]; then
        log_message "SUCCESS" "Backup completado exitosamente"
    else
        log_message "ERROR" "Backup falló con código: $exit_code"
    fi
    
    # Extraer estadísticas
    local transferred=$(grep "Total transferred file size" "$STATS_FILE" | tail -1)
    local speed=$(grep "Total bytes/sec" "$STATS_FILE" | tail -1)
    
    log_message "STATS" "$transferred"
    log_message "STATS" "$speed"
    
    return $exit_code
}

# Usar la función
backup_with_stats "/home/usuario" "/backup/usuario"
```

### **Monitoreo en Tiempo Real**

```bash
#!/bin/bash
# Monitor de progreso de rsync en tiempo real

monitor_rsync() {
    local pid="$1"
    local source_size=$(du -sb "$2" | cut -f1)
    
    while kill -0 "$pid" 2>/dev/null; do
        local current_size=$(du -sb "$3" 2>/dev/null | cut -f1 || echo 0)
        local percentage=$((current_size * 100 / source_size))
        
        printf "\rProgreso: %d%% (%s / %s)" \
            "$percentage" \
            "$(numfmt --to=iec $current_size)" \
            "$(numfmt --to=iec $source_size)"
        
        sleep 2
    done
    echo
}

# Ejecutar backup con monitor
rsync -av "$SOURCE/" "$DEST/" &
RSYNC_PID=$!
monitor_rsync $RSYNC_PID "$SOURCE" "$DEST"
wait $RSYNC_PID
```

## **Optimización y Performance**

### **Optimización para Archivos Grandes**

```bash
# Para archivos muy grandes
rsync -av --partial --progress \
    --timeout=300 \
    --contimeout=60 \
    archivos_grandes/ destino/

# Usar compresión adaptativa
rsync -av --compress --compress-level=6 \
    --skip-compress=gz/zip/z/rpm/deb/iso/bz2/tbz/tgz/mp3/mp4 \
    multimedia/ servidor:/storage/

# Paralelizar con GNU parallel
find origen/ -mindepth 1 -maxdepth 1 -type d | \
parallel -j4 rsync -av {} destino/
```

### **Optimización de Red**

```bash
# Usar algoritmos de compresión eficientes
rsync -av --compress --compress-level=9 \
    --whole-file \
    archivos/ servidor:backup/

# Configurar SSH para rsync
cat >> ~/.ssh/config << 'EOF'
Host backup-server
    HostName servidor.ejemplo.com
    User backup
    Port 22
    Compression yes
    CompressionLevel 6
    Cipher aes128-ctr
    ControlMaster auto
    ControlPath ~/.ssh/control_%h_%p_%r
    ControlPersist 10m
EOF

# Usar configuración SSH optimizada
rsync -av -e "ssh -F ~/.ssh/config" \
    datos/ backup-server:/storage/
```

## **Recuperación y Restauración**

### **Verificación de Integridad**

```bash
#!/bin/bash
# Verificar integridad de backup

verify_backup() {
    local source="$1"
    local backup="$2"
    
    echo "Verificando integridad entre $source y $backup..."
    
    # Comparar checksums
    (cd "$source" && find . -type f -exec md5sum {} \; | sort) > /tmp/source_checksums
    (cd "$backup" && find . -type f -exec md5sum {} \; | sort) > /tmp/backup_checksums
    
    if diff /tmp/source_checksums /tmp/backup_checksums >/dev/null; then
        echo "✓ Backup verificado - integridad correcta"
        return 0
    else
        echo "✗ Backup corrupto - existen diferencias"
        diff /tmp/source_checksums /tmp/backup_checksums
        return 1
    fi
}

# Verificar backup específico
verify_backup "/home/usuario/importantes" "/backup/importantes"
```

### **Restauración Selectiva**

```bash
#!/bin/bash
# Restauración selectiva desde backup

restore_selective() {
    local backup_dir="$1"
    local restore_target="$2"
    local pattern="$3"
    
    echo "Restaurando archivos que coinciden con: $pattern"
    echo "Desde: $backup_dir"
    echo "Hacia: $restore_target"
    
    # Buscar archivos que coinciden
    mapfile -t files < <(find "$backup_dir" -name "$pattern" -type f)
    
    if [[ ${#files[@]} -eq 0 ]]; then
        echo "No se encontraron archivos que coincidan con el patrón"
        return 1
    fi
    
    echo "Archivos encontrados:"
    printf '%s\n' "${files[@]}"
    
    read -p "¿Continuar con la restauración? (y/N): " -n 1 -r
    echo
    
    if [[ $REPLY =~ ^[Yy]$ ]]; then
        for file in "${files[@]}"; do
            # Calcular ruta relativa
            rel_path="${file#$backup_dir/}"
            target_file="$restore_target/$rel_path"
            
            # Crear directorio si no existe
            mkdir -p "$(dirname "$target_file")"
            
            # Copiar archivo
            cp "$file" "$target_file"
            echo "Restaurado: $rel_path"
        done
        echo "Restauración completada"
    else
        echo "Restauración cancelada"
    fi
}

# Ejemplos de uso
restore_selective "/backup/documentos" "/home/usuario/documentos" "*.pdf"
restore_selective "/backup/configs" "/etc" "*.conf"
```

## **Integración con Otros Comandos**

### **Con [[ssh]] para Transferencias Seguras**

```bash
# Configurar túnel SSH para rsync
ssh -f -N -L 8873:localhost:873 servidor
rsync -av rsync://localhost:8873/modulo/ /local/destino/

# Usar rsync sobre SSH con configuración específica
rsync -av -e "ssh -i ~/.ssh/backup_key -p 2222" \
    datos/ usuario@servidor:/backup/

# Rsync con autenticación por clave
rsync -av -e "ssh -o StrictHostKeyChecking=no" \
    --rsync-path="sudo rsync" \
    /local/ root@servidor:/remote/
```

### **Con [[find]] para Backups Selectivos**

```bash
# Backup solo de archivos modificados recientemente
find /home/usuario -type f -mtime -7 -print0 | \
rsync -av --files-from=- --from0 / /backup/recientes/

# Backup por tipo de archivo
find /home/usuario -name "*.pdf" -print0 | \
rsync -av --files-from=- --from0 / /backup/pdfs/

# Backup excluyendo archivos grandes
find /home/usuario -type f ! -size +100M -print0 | \
rsync -av --files-from=- --from0 / /backup/pequenos/
```

### **Con [[cron]] para Automatización**

```bash
# Configurar cron para backups automáticos
crontab -e

# Agregar entradas:
# Backup diario a las 2 AM
0 2 * * * /usr/local/bin/backup_daily.sh

# Backup semanal los domingos a la 1 AM
0 1 * * 0 /usr/local/bin/backup_weekly.sh

# Sincronización cada 4 horas
0 */4 * * * rsync -av /data/ servidor:/backup/data/
```

## **Troubleshooting rsync**

### **Problemas Comunes**

```bash
# Debug de conexión rsync
rsync -avvv --dry-run origen/ destino/

# Verificar permisos
ls -la origen/
ls -la destino/

# Probar conectividad SSH
ssh -v usuario@servidor

# Verificar espacio disponible
df -h destino/
df -h servidor:/path/
```

### **Recuperación de Errores**

```bash
#!/bin/bash
# Script de recuperación automática

retry_rsync() {
    local max_attempts=3
    local attempt=1
    local success=false
    
    while [[ $attempt -le $max_attempts ]] && [[ $success == false ]]; do
        echo "Intento $attempt de $max_attempts..."
        
        if rsync -av --partial --timeout=300 "$@"; then
            success=true
            echo "rsync exitoso en intento $attempt"
        else
            echo "rsync falló en intento $attempt"
            ((attempt++))
            
            if [[ $attempt -le $max_attempts ]]; then
                echo "Esperando 60 segundos antes del siguiente intento..."
                sleep 60
            fi
        fi
    done
    
    if [[ $success == false ]]; then
        echo "ERROR: rsync falló después de $max_attempts intentos"
        return 1
    fi
    
    return 0
}

# Usar función de retry
retry_rsync /local/datos/ servidor:/backup/datos/
```

## **Enlaces Relacionados**

- [[ssh]] - Transferencias seguras con rsync
- [[find]] - Buscar archivos para backup selectivo
- [[tar]] - Alternativa para archivos comprimidos
- [[cp]] - Copia local básica vs rsync avanzado
- [[scp]] - Transferencia simple vs rsync incremental
- [[cron]] - Automatizar backups con rsync