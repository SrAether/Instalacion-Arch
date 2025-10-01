# umask - Control de Permisos por Defecto

El comando `umask` es tu **guardián silencioso de permisos**. Define automáticamente qué permisos se asignan por defecto a los archivos y directorios que creas, siendo fundamental para la seguridad del sistema y la gestión de accesos.

## **¿Qué es umask?**

`umask` te permite:
- **Establecer permisos por defecto** para archivos y directorios nuevos
- **Controlar la seguridad** automática del sistema
- **Definir políticas de acceso** para usuarios y grupos
- **Prevenir exposiciones accidentales** de datos

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, umask es esencial para mantener la seguridad del sistema, especialmente en entornos multiusuario, servidores web y cuando se ejecutan servicios que crean archivos automáticamente.

### **Conceptos Fundamentales**

```bash
# Ver umask actual
umask

# Ver umask en formato simbólico
umask -S

# Ver umask en formato octal
umask -p
```

### **Cómo Funciona umask**

```bash
# Permisos máximos:
# Archivos: 666 (rw-rw-rw-)
# Directorios: 777 (rwxrwxrwx)

# umask SUSTRAE permisos de los máximos
# Ejemplo: umask 022
# Archivos: 666 - 022 = 644 (rw-r--r--)
# Directorios: 777 - 022 = 755 (rwxr-xr-x)
```

## **Configuración de umask**

### **Establecer umask Temporalmente**

```bash
# Establecer umask restrictivo
umask 077                    # Solo propietario tiene permisos

# Establecer umask estándar para usuarios
umask 022                    # Propietario: completo, otros: solo lectura

# Establecer umask para grupos de trabajo
umask 002                    # Propietario y grupo: completo, otros: solo lectura

# Establecer umask muy permisivo (no recomendado)
umask 000                    # Todos los permisos para todos

# Verificar resultado
umask -S
```

### **umask Permanente para Usuario**

```bash
# Agregar a ~/.bashrc
echo "umask 027" >> ~/.bashrc

# Agregar a ~/.zshrc (si usas zsh)
echo "umask 027" >> ~/.zshrc

# Agregar a ~/.profile (para todos los shells)
echo "umask 027" >> ~/.profile

# Aplicar inmediatamente
source ~/.bashrc
```

### **umask Global del Sistema**

```bash
# Configuración global en /etc/profile
sudo tee -a /etc/profile << 'EOF'
# umask por defecto del sistema
umask 022
EOF

# Configuración en /etc/bash.bashrc
sudo tee -a /etc/bash.bashrc << 'EOF'
# umask para bash
umask 022
EOF

# Para usuarios específicos en /etc/passwd
# (no recomendado, mejor usar archivos de configuración)
```

## **Valores Comunes de umask**

### **umask para Usuarios Regulares**

```bash
# umask 022 - Configuración estándar
umask 022
# Archivos: 644 (rw-r--r--) - Propietario lee/escribe, otros solo leen
# Directorios: 755 (rwxr-xr-x) - Propietario ejecuta, otros leen/ejecutan

# umask 027 - Más restrictivo
umask 027  
# Archivos: 640 (rw-r-----) - Grupo puede leer, otros sin acceso
# Directorios: 750 (rwxr-x---) - Grupo puede navegar, otros sin acceso

# umask 077 - Muy restrictivo
umask 077
# Archivos: 600 (rw-------) - Solo propietario tiene acceso
# Directorios: 700 (rwx------) - Solo propietario tiene acceso
```

### **umask para Servidores y Servicios**

```bash
# umask 002 - Para colaboración en grupos
umask 002
# Archivos: 664 (rw-rw-r--) - Propietario y grupo escriben
# Directorios: 775 (rwxrwxr-x) - Propietario y grupo escriben/ejecutan

# umask 007 - Grupo de trabajo exclusivo
umask 007
# Archivos: 660 (rw-rw----) - Solo propietario y grupo
# Directorios: 770 (rwxrwx---) - Solo propietario y grupo

# umask 177 - Para servicios críticos
umask 177
# Archivos: 600 (rw-------) - Solo propietario
# Directorios: 600 (rw-------) - Solo propietario (sin ejecución para otros)
```

## **Casos de Uso en Arch Linux**

### **Configuración para Desarrolladores**

```bash
# umask para proyectos de desarrollo
umask 002

# Crear estructura de proyecto
mkdir -p ~/proyectos/mi-app/{src,docs,tests}
touch ~/proyectos/mi-app/README.md

# Verificar permisos creados
ls -la ~/proyectos/mi-app/
# drwxrwxr-x - directorios
# -rw-rw-r-- - archivos

# Para proyectos privados
umask 077
mkdir ~/proyectos/privado
touch ~/proyectos/privado/secreto.txt
ls -la ~/proyectos/privado/
# drwx------ - solo propietario
# -rw------- - solo propietario
```

### **Configuración para Servidor Web**

```bash
# umask para Apache/Nginx
sudo tee /etc/systemd/system/nginx.service.d/umask.conf << 'EOF'
[Service]
UMask=0022
EOF

# umask para usuario www-data
sudo tee -a /home/www-data/.bashrc << 'EOF'
umask 022
EOF

# umask para scripts de deployment
#!/bin/bash
umask 022  # Asegurar permisos correctos para web
cp -r /tmp/app/* /var/www/html/

# Verificar permisos de archivos web
ls -la /var/www/html/
```

### **Configuración para Servicios del Sistema**

```bash
# umask para systemd services
sudo mkdir -p /etc/systemd/system/mi-servicio.service.d/
sudo tee /etc/systemd/system/mi-servicio.service.d/umask.conf << 'EOF'
[Service]
UMask=0027
EOF

# umask para cron jobs
# En crontab, agregar:
UMASK=022
0 2 * * * /usr/local/bin/backup.sh

# umask para scripts de backup
#!/bin/bash
umask 077  # Backups solo para root
tar czf "/backup/sistema-$(date +%Y%m%d).tar.gz" /etc /home
```

## **Testing y Verificación de umask**

### **Script de Testing**

```bash
# Probar diferentes valores de umask

echo "=== Probando umask 022 ==="
umask 022
touch test_022.txt
ls -l test_022.txt

echo "=== Probando umask 077 ==="
umask 077
touch test_077.txt
ls -l test_077.txt

# Limpiar
rm test_*.txt
```
    touch archivo_test.txt
    mkdir directorio_test
    
    # Mostrar permisos resultantes
    echo "umask actual: $(umask)"
    echo "Archivo:     $(ls -l archivo_test.txt | awk '{print $1}')"
    echo "Directorio:  $(ls -ld directorio_test | awk '{print $1}')"
    echo "Archivo octal:     $(stat -c '%a' archivo_test.txt)"
    echo "Directorio octal:  $(stat -c '%a' directorio_test)"
    echo
    
    # Limpiar
    cd /tmp
    rm -rf "$test_dir"
}

# Probar diferentes valores
for mask in 022 027 077 002 007; do
    test_umask "$mask"
done
```

### **Verificación de Configuración Actual**

```bash
#!/bin/bash
# Script para verificar configuración umask del sistema

echo "=== Estado actual de umask ==="
echo "umask actual: $(umask)"
echo "umask simbólico: $(umask -S)"

echo -e "\n=== Archivos de configuración ==="
echo "~/.bashrc:"
grep -n umask ~/.bashrc 2>/dev/null || echo "  No configurado"

echo "/etc/profile:"
sudo grep -n umask /etc/profile 2>/dev/null || echo "  No configurado"

echo "/etc/bash.bashrc:"
sudo grep -n umask /etc/bash.bashrc 2>/dev/null || echo "  No configurado"

echo -e "\n=== Prueba de creación ==="
test_file="/tmp/umask_verification_$$"
touch "$test_file"
mkdir "${test_file}_dir"

echo "Archivo creado: $(ls -l $test_file | awk '{print $1, $3, $4, $9}')"
echo "Directorio creado: $(ls -ld ${test_file}_dir | awk '{print $1, $3, $4, $9}')"

# Limpiar
rm -f "$test_file"
rmdir "${test_file}_dir"
```

## **umask por Usuario y Servicio**

### **Configuración Específica por Usuario**

```bash
# Usuario desarrollador (permisivo para colaboración)
sudo tee -a /home/developer/.bashrc << 'EOF'
# umask para desarrollo colaborativo
umask 002
EOF

# Usuario admin (restrictivo)
sudo tee -a /home/admin/.bashrc << 'EOF'
# umask restrictivo para administración
umask 077
EOF

# Usuario web (balance entre seguridad y funcionalidad)
sudo tee -a /home/www-data/.bashrc << 'EOF'
# umask para servidor web
umask 022
EOF
```

### **umask para Diferentes Servicios**

```bash
# Servicio de base de datos
sudo tee /etc/systemd/system/postgresql.service.d/umask.conf << 'EOF'
[Service]
UMask=0077
EOF

# Servicio de aplicación web
sudo tee /etc/systemd/system/webapp.service.d/umask.conf << 'EOF'
[Service]
UMask=0022
EOF

# Servicio de backup
sudo tee /etc/systemd/system/backup.service.d/umask.conf << 'EOF'
[Service]
UMask=0077
EOF

# Recargar configuración systemd
sudo systemctl daemon-reload
```

## **umask Dinámico y Condicional**

### **umask Basado en Condiciones**

```bash
# En ~/.bashrc - umask dinámico basado en hora
if [[ $(date +%H) -ge 18 ]] || [[ $(date +%H) -lt 8 ]]; then
    # Horario no laboral - más restrictivo
    umask 077
else
    # Horario laboral - colaborativo
    umask 022
fi

# umask basado en directorio actual
set_umask_by_directory() {
    case "$PWD" in
        /home/*/private/* | /home/*/confidential/*)
            umask 077
            ;;
        /home/*/projects/* | /home/*/shared/*)
            umask 002
            ;;
        *)
            umask 022
            ;;
    esac
}

# Ejecutar al cambiar directorio
cd() {
    builtin cd "$@"
    set_umask_by_directory
}
```

### **umask para Scripts**

```bash
#!/bin/bash
# Script con manejo inteligente de umask

# Guardar umask original
original_umask=$(umask)

# Establecer umask específico para este script
umask 027

# Función para restaurar umask al salir
cleanup() {
    umask "$original_umask"
    echo "umask restaurado a: $original_umask"
}

# Configurar trap para limpiar al salir
trap cleanup EXIT

# Trabajo del script con umask restrictivo
echo "Trabajando con umask $(umask)..."
mkdir -p /tmp/script_work
touch /tmp/script_work/archivo_seguro.txt

echo "Permisos del archivo creado:"
ls -la /tmp/script_work/archivo_seguro.txt
```

## **Monitoreo y Auditoría de umask**

### **Script de Auditoría**

```bash
#!/bin/bash
# Auditoría de configuración umask en el sistema

echo "=== Auditoría de umask del Sistema ==="
echo "Fecha: $(date)"
echo

# Verificar configuración global
echo "--- Configuración Global ---"
echo "/etc/profile:"
sudo grep -n "umask" /etc/profile 2>/dev/null || echo "  No configurado"

echo "/etc/bash.bashrc:"
sudo grep -n "umask" /etc/bash.bashrc 2>/dev/null || echo "  No configurado"

echo "/etc/zsh/zshrc:"
sudo grep -n "umask" /etc/zsh/zshrc 2>/dev/null || echo "  No configurado"

# Verificar configuración por usuario
echo -e "\n--- Configuración por Usuario ---"
for user_home in /home/*; do
    [[ -d "$user_home" ]] || continue
    username=$(basename "$user_home")
    
    echo "Usuario: $username"
    for config_file in .bashrc .zshrc .profile; do
        config_path="$user_home/$config_file"
        if [[ -f "$config_path" ]]; then
            umask_lines=$(grep -n "umask" "$config_path" 2>/dev/null)
            if [[ -n "$umask_lines" ]]; then
                echo "  $config_file: $umask_lines"
            fi
        fi
    done
    echo
done

# Verificar servicios systemd
echo "--- Servicios con umask Personalizado ---"
for service_dir in /etc/systemd/system/*.d/; do
    [[ -d "$service_dir" ]] || continue
    service_name=$(basename "${service_dir%.*}")
    
    for conf_file in "$service_dir"/*.conf; do
        [[ -f "$conf_file" ]] || continue
        if grep -q "UMask" "$conf_file" 2>/dev/null; then
            echo "Servicio: $service_name"
            grep -n "UMask" "$conf_file"
        fi
    done
done
```

### **Monitoreo en Tiempo Real**

```bash
#!/bin/bash
# Monitor de cambios en archivos con permisos inusuales

# Función para verificar permisos de archivos nuevos
monitor_new_files() {
    local watch_dir="$1"
    local expected_umask="$2"
    
    inotifywait -m -r -e create "$watch_dir" --format '%w%f' |
    while read file; do
        if [[ -f "$file" ]]; then
            perms=$(stat -c '%a' "$file")
            expected_perms=$(printf "%03o" $((0666 & ~0$expected_umask)))
            
            if [[ "$perms" != "$expected_perms" ]]; then
                echo "ALERTA: Archivo $file tiene permisos $perms, esperado $expected_perms"
                echo "Archivo: $file, Propietario: $(stat -c '%U:%G' "$file")"
            fi
        fi
    done
}

# Monitorear directorios importantes
monitor_new_files "/var/www" "022" &
monitor_new_files "/home" "077" &

wait
```

## **Troubleshooting umask**

### **Problemas Comunes**

```bash
# Verificar por qué umask no se aplica
echo "=== Debugging umask ==="

# 1. Verificar umask actual
echo "umask actual: $(umask)"

# 2. Verificar herencia de procesos
echo "umask del shell padre: $(ps -o pid,umask $$ 2>/dev/null || echo 'No disponible')"

# 3. Verificar archivos de configuración cargados
echo "Archivos de configuración:"
echo "  ~/.bashrc existe: $([[ -f ~/.bashrc ]] && echo "Sí" || echo "No")"
echo "  ~/.profile existe: $([[ -f ~/.profile ]] && echo "Sí" || echo "No")"

# 4. Probar creación de archivo
test_file="/tmp/umask_debug_$$"
touch "$test_file"
echo "Archivo de prueba: $(ls -l $test_file)"
rm -f "$test_file"

# 5. Verificar si algún programa está cambiando umask
echo "Procesos actuales con umask personalizado:"
ps axo pid,umask,comm 2>/dev/null | grep -v "0022\|0002" || echo "Ninguno encontrado"
```

### **Restaurar Configuración**

```bash
#!/bin/bash
# Script para restaurar configuración umask por defecto

echo "Restaurando configuración umask por defecto..."

# Backup de archivos de configuración
backup_dir="/tmp/umask_backup_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$backup_dir"

# Respaldar configuraciones existentes
for file in ~/.bashrc ~/.zshrc ~/.profile; do
    if [[ -f "$file" ]] && grep -q "umask" "$file"; then
        cp "$file" "$backup_dir/"
        echo "Respaldado: $file -> $backup_dir/"
    fi
done

# Remover configuraciones umask personalizadas
for file in ~/.bashrc ~/.zshrc ~/.profile; do
    if [[ -f "$file" ]]; then
        sed -i '/^umask/d' "$file"
        echo "Removido umask de: $file"
    fi
done

# Establecer umask estándar
echo "umask 022" >> ~/.bashrc
echo "umask estándar (022) configurado en ~/.bashrc"

echo "Backup guardado en: $backup_dir"
echo "Por favor, reinicia el shell o ejecuta: source ~/.bashrc"
```

## **Integración con Otros Comandos**

### **Con [[chmod]] y [[chown]]**

```bash
# umask establece permisos por defecto, chmod los modifica después
umask 077
touch archivo_privado.txt     # Creado con 600
chmod 644 archivo_privado.txt # Modificado a 644

# Verificar relación umask-chmod
echo "umask: $(umask)"
echo "archivo con umask: $(stat -c '%a' <(touch /tmp/test$$ && echo /tmp/test$$))"
```

### **Con [[find]] para Auditoría**

```bash
# Buscar archivos con permisos inusuales
find /home -type f ! -perm 644 ! -perm 600 ! -perm 755 -ls

# Buscar directorios con permisos inusuales
find /var/www -type d ! -perm 755 ! -perm 750 -ls

# Encontrar archivos que no coinciden con umask esperado
find /tmp -type f -newermt "1 hour ago" ! -perm 644 -ls
```

### **Con Scripts de Backup**

```bash
#!/bin/bash
# Backup con umask seguro
umask 077  # Solo propietario puede leer backups

backup_dir="/backup/$(date +%Y%m%d)"
mkdir -p "$backup_dir"

tar czf "$backup_dir/sistema.tar.gz" /etc /home
echo "Backup creado con permisos: $(stat -c '%a' $backup_dir/sistema.tar.gz)"
```

## **Enlaces Relacionados**

- [[chmod]] - Modificar permisos después de la creación
- [[chown]] - Cambiar propietario y grupo de archivos
- [[ls]] - Ver permisos de archivos y directorios
- [[stat]] - Ver información detallada de permisos
- [[find]] - Buscar archivos por permisos
- [[systemctl]] - Configurar umask para servicios