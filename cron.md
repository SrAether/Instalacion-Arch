# cron - Programación de Tareas Automáticas

El comando `cron` es tu **automatizador de tareas**. Te permite programar la ejecución de comandos y scripts de manera automática en horarios específicos, siendo fundamental para mantenimiento del sistema, backups y tareas administrativas.

## **¿Qué es cron?**

`cron` te permite:
- **Programar tareas automáticas** en horarios específicos
- **Ejecutar scripts de mantenimiento** sin intervención manual
- **Automatizar backups** y limpieza del sistema
- **Gestionar tareas recurrentes** del sistema

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, cron es esencial para automatizar actualizaciones del sistema, limpiar logs, realizar backups programados, monitorear servicios y ejecutar tareas de mantenimiento regulares.

### **Componentes de cron**

```bash
# Demonio cron
systemctl status cronie          # Arch Linux usa cronie por defecto

# Archivos de configuración principales
/etc/crontab                     # Crontab del sistema
/var/spool/cron/                 # Crontabs de usuarios
/etc/cron.d/                     # Archivos de cron adicionales
/etc/cron.daily/                 # Scripts diarios
/etc/cron.weekly/                # Scripts semanales
/etc/cron.monthly/               # Scripts mensuales
```

### **Instalación y Configuración**

```bash
# Instalar cronie en Arch Linux
sudo pacman -S cronie

# Habilitar y iniciar el servicio
sudo systemctl enable cronie
sudo systemctl start cronie

# Verificar estado
sudo systemctl status cronie

# Ver logs de cron
sudo journalctl -u cronie
```

## **Formato de Crontab**

### **Sintaxis Básica**

```bash
# Formato: minuto hora día_mes mes día_semana comando
# * * * * * comando_a_ejecutar
# │ │ │ │ │
# │ │ │ │ └─── Día de la semana (0-7, 0 y 7 = domingo)
# │ │ │ └──── Mes (1-12)
# │ │ └───── Día del mes (1-31)
# │ └────── Hora (0-23)
# └─────── Minuto (0-59)

# Ejemplos básicos:
# 0 2 * * *        = Todos los días a las 2:00 AM
# 30 14 * * 1      = Todos los lunes a las 2:30 PM
# 0 0 1 * *        = El primer día de cada mes a medianoche
# */15 * * * *     = Cada 15 minutos
# 0 */2 * * *      = Cada 2 horas
```

### **Caracteres Especiales**

```bash
# * = cualquier valor
# , = lista de valores (1,3,5)
# - = rango de valores (1-5)
# / = intervalos (*/15 = cada 15)
# @ = atajos especiales

# Atajos especiales:
@reboot          # Al iniciar el sistema
@yearly o @annually   # Una vez al año (0 0 1 1 *)
@monthly         # Una vez al mes (0 0 1 * *)
@weekly          # Una vez a la semana (0 0 * * 0)
@daily o @midnight    # Una vez al día (0 0 * * *)
@hourly          # Una vez por hora (0 * * * *)
```

## **Gestión de Crontab**

### **Comandos Básicos de crontab**

```bash
# Ver crontab del usuario actual
crontab -l

# Editar crontab del usuario actual
crontab -e

# Eliminar crontab del usuario actual
crontab -r

# Ver crontab de otro usuario (como root)
sudo crontab -l -u usuario

# Editar crontab de otro usuario (como root)
sudo crontab -e -u usuario

# Instalar crontab desde archivo
crontab archivo_crontab.txt
```

### **Crear y Editar Tareas**

```bash
# Abrir editor de crontab
crontab -e

# Ejemplos de tareas comunes:

# Backup diario a las 3:00 AM
0 3 * * * /usr/local/bin/backup_script.sh

# Limpiar archivos temporales cada hora
0 * * * * find /tmp -type f -mtime +1 -delete

# Reiniciar servicio web cada domingo a las 4:00 AM
0 4 * * 0 sudo systemctl restart nginx

# Monitorear espacio en disco cada 30 minutos
*/30 * * * * /home/usuario/scripts/check_disk_space.sh

# Actualizar base de datos de locate diariamente
0 2 * * * sudo updatedb

# Sincronizar archivos cada 6 horas
0 */6 * * * rsync -av /home/usuario/docs/ servidor:/backup/docs/
```

## **Casos de Uso en Arch Linux**

### **Mantenimiento del Sistema**

```bash
# Crontab para administrador del sistema
sudo crontab -e

# Actualizar lista de paquetes diariamente
0 6 * * * pacman -Sy

# Limpiar cache de pacman semanalmente
0 3 * * 0 pacman -Sc --noconfirm

# Rotar logs semanalmente
0 4 * * 0 /usr/sbin/logrotate /etc/logrotate.conf

# Limpiar archivos temporales diariamente
0 1 * * * find /tmp -type f -atime +7 -delete

# Verificar integridad del sistema mensualmente
0 2 1 * * /usr/bin/pacman -Qkk > /var/log/pacman-integrity.log

# Backup de configuraciones diariamente
0 5 * * * tar czf /backup/etc-$(date +\%Y\%m\%d).tar.gz /etc
```

### **Monitoreo y Alertas**

```bash
# Scripts de monitoreo automatizado

# Verificar espacio en disco cada 15 minutos
*/15 * * * * /home/admin/scripts/monitor_disk.sh

# Monitorear servicios críticos cada 5 minutos
*/5 * * * * /home/admin/scripts/check_services.sh

# Verificar carga del sistema cada hora
0 * * * * /home/admin/scripts/check_load.sh

# Ejemplo de script simple de monitoreo de disco
cat > monitor_disk.sh << 'EOF'
#!/bin/bash
DISK_USAGE=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ $DISK_USAGE -gt 90 ]; then
    echo "ALERTA: Disco al ${DISK_USAGE}%"
fi
EOF

chmod +x monitor_disk.sh
```

### **Backups Automatizados**

```bash
# Sistema de backups programados

# Backup incremental diario
0 2 * * * /home/admin/scripts/backup_incremental.sh

# Backup completo semanal
0 1 * * 0 /home/admin/scripts/backup_completo.sh

# Sincronización remota diaria
0 4 * * * rsync -avz /home/ usuario@servidor:/backup/home/

# Ejemplo de script de backup incremental
cat > /home/admin/scripts/backup_incremental.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backup/incremental"
SOURCE_DIR="/home"
DATE=$(date +%Y%m%d)

# Crear directorio de backup
mkdir -p "$BACKUP_DIR/$DATE"

# Backup incremental usando rsync
rsync -av --link-dest="$BACKUP_DIR/current" \
    "$SOURCE_DIR/" "$BACKUP_DIR/$DATE/"

# Actualizar enlace current
rm -f "$BACKUP_DIR/current"
ln -s "$DATE" "$BACKUP_DIR/current"

# Limpiar backups antiguos (mantener 30 días)
find "$BACKUP_DIR" -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;

# Log del resultado
echo "$(date): Backup incremental completado" >> /var/log/backup.log
EOF

chmod +x /home/admin/scripts/backup_incremental.sh
```

## **Variables de Entorno en cron**

### **Configurar Variables**

```bash
# En el crontab, definir variables al inicio
crontab -e

# Ejemplo de crontab con variables:
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=admin@ejemplo.com
HOME=/home/usuario

# Variables personalizadas
BACKUP_DIR=/backup
LOG_FILE=/var/log/cron_tasks.log

# Tareas usando las variables
0 2 * * * $BACKUP_DIR/scripts/daily_backup.sh >> $LOG_FILE 2>&1
0 4 * * 0 $BACKUP_DIR/scripts/weekly_backup.sh >> $LOG_FILE 2>&1
```

### **Variables Importantes**

```bash
# SHELL = shell a usar para ejecutar comandos
SHELL=/bin/bash

# PATH = rutas donde buscar comandos
PATH=/usr/local/bin:/usr/bin:/bin

# MAILTO = email para enviar salida de cron
MAILTO=admin@ejemplo.com

# HOME = directorio home para el usuario
HOME=/home/usuario

# LANG = configuración de idioma
LANG=en_US.UTF-8

# TZ = zona horaria
TZ=America/Mexico_City
```

## **Redirección de Salida y Logs**

### **Gestión de Salida**

```bash
# Diferentes formas de manejar la salida de cron

# Enviar todo a /dev/null (silencioso)
0 2 * * * /path/to/script.sh > /dev/null 2>&1

# Guardar salida en log
0 2 * * * /path/to/script.sh >> /var/log/script.log 2>&1

# Enviar solo errores a log
0 2 * * * /path/to/script.sh 2>> /var/log/script_errors.log

# Enviar salida normal y errores a diferentes archivos
0 2 * * * /path/to/script.sh >> /var/log/script.log 2>> /var/log/script_errors.log

# Usar logger para enviar a syslog
0 2 * * * /path/to/script.sh | logger -t "my_script"

# Combinar con timestamp
0 2 * * * echo "$(date): Iniciando backup" >> /var/log/backup.log; /path/to/backup.sh >> /var/log/backup.log 2>&1
```

### **Script con Logging Avanzado**

```bash
#!/bin/bash
# Script con logging completo para cron

LOG_FILE="/var/log/cron_task.log"
SCRIPT_NAME=$(basename "$0")

# Función de logging
log_message() {
    local level="$1"
    local message="$2"
    echo "$(date '+%Y-%m-%d %H:%M:%S') [$level] [$SCRIPT_NAME] $message" >> "$LOG_FILE"
}

# Función para manejo de errores
handle_error() {
    local exit_code=$?
    if [ $exit_code -ne 0 ]; then
        log_message "ERROR" "Script falló con código: $exit_code"
        exit $exit_code
    fi
}

# Configurar trap para errores
trap handle_error ERR

# Inicio del script
log_message "INFO" "Iniciando tarea programada"

# Tu código aquí
echo "Ejecutando tarea..."
sleep 2
echo "Tarea completada"

# Fin del script
log_message "INFO" "Tarea completada exitosamente"
```

## **Cron del Sistema vs Usuario**

### **Crontab del Sistema (/etc/crontab)**

```bash
# Ver crontab del sistema
sudo cat /etc/crontab

# Formato del crontab del sistema incluye campo de usuario:
# minuto hora día_mes mes día_semana usuario comando

# Ejemplo de /etc/crontab:
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

# Tareas del sistema
0  2  *  *  * root    /usr/bin/updatedb
0  3  *  *  * root    /usr/sbin/logrotate /etc/logrotate.conf
0  4  *  *  0 root    /usr/bin/pacman -Sc --noconfirm
```

### **Directorios de cron**

```bash
# Scripts en directorios especiales (ejecutados como root)

# Scripts diarios
ls -la /etc/cron.daily/

# Scripts semanales
ls -la /etc/cron.weekly/

# Scripts mensuales
ls -la /etc/cron.monthly/

# Ejemplo de script en /etc/cron.daily/
sudo cat > /etc/cron.daily/cleanup_tmp << 'EOF'
#!/bin/bash
# Limpiar archivos temporales antiguos

find /tmp -type f -atime +7 -delete
find /var/tmp -type f -atime +7 -delete

# Log de la limpieza
echo "$(date): Limpieza de archivos temporales completada" >> /var/log/cleanup.log
EOF

sudo chmod +x /etc/cron.daily/cleanup_tmp
```

## **Troubleshooting cron**

### **Problemas Comunes**

```bash
# Verificar que cronie esté ejecutándose
systemctl status cronie

# Ver logs de cron
sudo journalctl -u cronie -f

# Ver logs de ejecución específicos
sudo journalctl | grep CRON

# Verificar sintaxis de crontab
crontab -l | crontab -T  # Verificar archivo temporal

# Probar comando manualmente
/bin/bash -c "comando_del_crontab"

# Verificar permisos de archivos
ls -la /var/spool/cron/
sudo ls -la /var/spool/cron/root
```

### **Debug de Tareas cron**

```bash
# Script de debug para cron
cat > debug_cron.sh << 'EOF'
#!/bin/bash
# Script de debug para tareas cron

DEBUG_LOG="/tmp/cron_debug.log"

echo "=== DEBUG CRON - $(date) ===" >> "$DEBUG_LOG"
echo "Usuario: $(whoami)" >> "$DEBUG_LOG"
echo "PATH: $PATH" >> "$DEBUG_LOG"
echo "HOME: $HOME" >> "$DEBUG_LOG"
echo "SHELL: $SHELL" >> "$DEBUG_LOG"
echo "Directorio actual: $(pwd)" >> "$DEBUG_LOG"
echo "Variables de entorno:" >> "$DEBUG_LOG"
env >> "$DEBUG_LOG"
echo "=========================" >> "$DEBUG_LOG"

# Tu comando aquí
/path/to/your/command >> "$DEBUG_LOG" 2>&1
EOF

# Usar en crontab:
# */5 * * * * /path/to/debug_cron.sh
```

### **Verificar Ejecución**

```bash
# Script para verificar última ejecución
check_cron_execution() {
    local script_name="$1"
    local log_file="${2:-/var/log/cron.log}"
    
    echo "=== Verificación de ejecución de $script_name ==="
    
    # Buscar en logs de cron
    if grep -q "$script_name" "$log_file"; then
        echo "Última ejecución encontrada:"
        grep "$script_name" "$log_file" | tail -5
    else
        echo "No se encontraron ejecuciones de $script_name"
    fi
    
    # Verificar si el archivo existe y es ejecutable
    if [[ -f "$script_name" ]]; then
        if [[ -x "$script_name" ]]; then
            echo "✓ Script existe y es ejecutable"
        else
            echo "✗ Script existe pero no es ejecutable"
        fi
    else
        echo "✗ Script no encontrado"
    fi
}

# Usar la función
# check_cron_execution "/path/to/script.sh"
```

## **Seguridad en cron**

### **Mejores Prácticas de Seguridad**

```bash
# Restringir acceso a cron
# /etc/cron.allow - usuarios permitidos
# /etc/cron.deny - usuarios denegados

# Permitir solo usuarios específicos
sudo cat > /etc/cron.allow << 'EOF'
root
admin
backup_user
EOF

# Configurar permisos seguros
sudo chmod 600 /etc/cron.allow
sudo chmod 600 /etc/crontab

# Usar rutas absolutas en scripts
# En lugar de: find /tmp -name "*.tmp" -delete
# Usar: /usr/bin/find /tmp -name "*.tmp" -delete

# Validar entradas en scripts
validate_backup_path() {
    local path="$1"
    
    # Verificar que la ruta existe y es un directorio
    if [[ ! -d "$path" ]]; then
        echo "Error: $path no es un directorio válido"
        exit 1
    fi
    
    # Verificar que no sea una ruta peligrosa
    case "$path" in
        /|/bin|/sbin|/usr|/etc)
            echo "Error: Ruta peligrosa detectada: $path"
            exit 1
            ;;
    esac
}
```

### **Monitoreo de Tareas cron**

```bash
#!/bin/bash
# Monitor de ejecución de tareas cron

MONITOR_LOG="/var/log/cron_monitor.log"
ALERT_EMAIL="admin@ejemplo.com"

monitor_cron_jobs() {
    echo "=== Monitor cron - $(date) ===" >> "$MONITOR_LOG"
    
    # Verificar que cronie esté ejecutándose
    if ! systemctl is-active --quiet cronie; then
        echo "ALERTA: cronie no está ejecutándose" >> "$MONITOR_LOG"
        echo "cronie service is down" | mail -s "CRON ALERT" "$ALERT_EMAIL"
    fi
    
    # Verificar tareas fallidas recientes
    local failed_jobs=$(journalctl -u cronie --since "1 hour ago" | grep -i "error\|failed" | wc -l)
    if [[ $failed_jobs -gt 0 ]]; then
        echo "ALERTA: $failed_jobs trabajos cron fallaron en la última hora" >> "$MONITOR_LOG"
        journalctl -u cronie --since "1 hour ago" | grep -i "error\|failed" | mail -s "CRON FAILURES" "$ALERT_EMAIL"
    fi
    
    # Verificar espacio en logs
    local log_size=$(du -m /var/log/cron* 2>/dev/null | awk '{sum+=$1} END {print sum+0}')
    if [[ $log_size -gt 100 ]]; then
        echo "ALERTA: Logs de cron ocupan ${log_size}MB" >> "$MONITOR_LOG"
    fi
}

# Agregar a crontab para ejecutar cada hora:
# 0 * * * * /path/to/monitor_cron_jobs.sh
```

## **Alternativas y Herramientas Relacionadas**

### **systemd Timers como Alternativa**

```bash
# systemd timers son una alternativa moderna a cron

# Crear servicio
sudo cat > /etc/systemd/system/mi-backup.service << 'EOF'
[Unit]
Description=Backup diario
Wants=mi-backup.timer

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_script.sh

[Install]
WantedBy=multi-user.target
EOF

# Crear timer
sudo cat > /etc/systemd/system/mi-backup.timer << 'EOF'
[Unit]
Description=Ejecutar backup diario
Requires=mi-backup.service

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
EOF

# Habilitar y iniciar timer
sudo systemctl enable mi-backup.timer
sudo systemctl start mi-backup.timer

# Ver timers activos
systemctl list-timers
```

### **at para Tareas Únicas**

```bash
# Para tareas que se ejecutan una sola vez
sudo pacman -S at

# Programar tarea para ejecución única
echo "rm /tmp/archivo_temporal.txt" | at now + 1 hour
echo "shutdown -h now" | at 23:30

# Ver tareas programadas
atq

# Eliminar tarea programada
atrm 1  # Elimina trabajo número 1
```

## **Enlaces Relacionados**

- [[systemctl]] - Gestionar servicios que incluyen timers como alternativa a cron
- [[ps]] - Ver procesos de cron en ejecución
- [[grep]] - Buscar en logs de cron
- [[find]] - Comandos comunes usados en tareas cron
- [[rsync]] - Herramienta común para backups programados con cron
- [[tar]] - Crear archivos comprimidos en tareas de backup