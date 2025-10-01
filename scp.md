# scp - Copia Segura de Archivos

**scp** (Secure Copy Protocol) es tu **puente digital seguro** para transferir archivos entre sistemas a través de SSH. Funciona como `cp`, pero a través de la red con cifrado completo, siendo fundamental para administración remota y backups seguros en Arch Linux.

## **¿Qué es scp?**

scp te permite:
- **Copiar archivos** de forma segura entre sistemas locales y remotos
- **Transferir directorios completos** con preservación de permisos
- **Autenticar** usando claves SSH o contraseñas
- **Cifrar** todo el tráfico de datos durante la transferencia

## **¿Por qué es Fundamental?**

En administración de sistemas Arch Linux, scp es esencial para:
- Desplegar aplicaciones y configuraciones
- Realizar backups remotos seguros
- Sincronizar archivos entre servidores
- Transferir logs y datos de diagnóstico

### **Verificar Disponibilidad**

```bash
# scp viene incluido con openssh
which scp
ssh -V

# En Arch Linux, instalar si no está disponible
sudo pacman -S openssh

# Verificar que el servicio SSH esté activo (para recibir archivos)
systemctl status sshd
```

## **Sintaxis Básica de scp**

### **Estructura del Comando**

```bash
# Sintaxis general
scp [opciones] origen destino

# Transferencia local a remoto
scp archivo.txt usuario@servidor:/ruta/destino/

# Transferencia remoto a local
scp usuario@servidor:/ruta/archivo.txt /ruta/local/

# Entre dos servidores remotos
scp usuario1@servidor1:/archivo usuario2@servidor2:/destino/
```

### **Formatos de Ruta**

```bash
# Rutas locales (como cualquier archivo local)
/home/usuario/documento.pdf
./archivo.txt
../config/settings.conf

# Rutas remotas (siempre incluyen host)
usuario@192.168.1.100:/home/usuario/archivo.txt
admin@servidor.ejemplo.com:/var/log/sistema.log
root@10.0.0.50:/etc/nginx/nginx.conf
```

## **Operaciones Básicas**

### **Copiar Archivo Único**

```bash
# Enviar archivo local a servidor remoto
scp documento.pdf usuario@192.168.1.100:/home/usuario/Documentos/

# Descargar archivo desde servidor remoto
scp usuario@192.168.1.100:/var/log/syslog.log ./logs/

# Especificar puerto SSH personalizado
scp -P 2022 archivo.txt usuario@servidor:/destino/

# Con nombre diferente en destino
scp config.local usuario@servidor:/etc/app/config.prod
```

### **Copiar Múltiples Archivos**

```bash
# Múltiples archivos al mismo destino
scp archivo1.txt archivo2.txt archivo3.txt usuario@servidor:/destino/

# Usando patrones (wildcards)
scp *.log usuario@servidor:/var/log/aplicacion/
scp config/*.conf usuario@servidor:/etc/aplicacion/

# Archivos con espacios en el nombre
scp "archivo con espacios.txt" usuario@servidor:/destino/
scp 'Mi Documento.pdf' usuario@servidor:/home/usuario/
```

### **Copiar Directorios Completos**

```bash
# Copiar directorio recursivamente (-r)
scp -r /home/usuario/proyecto/ usuario@servidor:/var/www/

# Preservar atributos (-p: permisos, timestamps)
scp -rp /home/usuario/configuraciones/ usuario@servidor:/backup/

# Copiar contenido del directorio (no el directorio mismo)
scp -r /home/usuario/datos/* usuario@servidor:/var/aplicacion/

# Directorio con exclusiones (usando rsync es mejor, pero ejemplo básico)
scp -r --exclude='*.tmp' directorio/ usuario@servidor:/destino/
```

## **Opciones Avanzadas**

### **Control de Transferencia**

```bash
# Modo verbose (-v) para depuración
scp -v archivo.txt usuario@servidor:/destino/

# Modo silencioso (-q)
scp -q *.log usuario@servidor:/logs/

# Límite de ancho de banda (-l en Kbit/s)
scp -l 1000 archivo_grande.zip usuario@servidor:/tmp/

# Compresión durante transferencia (-C)
scp -C archivo_grande.tar usuario@servidor:/backup/

# Usar IPv4 específicamente (-4) o IPv6 (-6)
scp -4 archivo.txt usuario@servidor:/destino/
scp -6 archivo.txt usuario@servidor:/destino/
```

### **Configuración de SSH**

```bash
# Usar archivo de configuración SSH específico
scp -F ~/.ssh/config_personalizado archivo.txt servidor:/destino/

# Especificar clave privada
scp -i ~/.ssh/clave_especial archivo.txt usuario@servidor:/destino/

# Desactivar verificación de host key (NO recomendado para producción)
scp -o StrictHostKeyChecking=no archivo.txt usuario@servidor:/destino/

# Usar agente SSH para autenticación
scp -o PasswordAuthentication=no archivo.txt usuario@servidor:/destino/
```

### **Opciones de Preservación**

```bash
# Preservar todos los atributos (-p)
scp -p archivo.txt usuario@servidor:/destino/
# Preserva: permisos, timestamps de acceso y modificación

# Preservar estructura de directorios con rutas completas
scp --preserve=timestamps archivo.txt usuario@servidor:/destino/

# Verificar integridad durante copia
scp -o CheckHostIP=yes archivo.txt usuario@servidor:/destino/
```

## **Casos de Uso en Arch Linux**

### **Backup de Configuraciones**

```bash
# Backup simple de configuraciones del sistema

BACKUP_SERVER="backup@192.168.1.200"
TIMESTAMP=$(date +%Y%m%d)

echo "=== Backup de Configuraciones del Sistema ==="

# Backup directo de archivos importantes
scp /etc/pacman.conf "$BACKUP_SERVER:/backups/$(hostname)/pacman.conf.$TIMESTAMP"
scp /etc/fstab "$BACKUP_SERVER:/backups/$(hostname)/fstab.$TIMESTAMP"
scp /etc/hosts "$BACKUP_SERVER:/backups/$(hostname)/hosts.$TIMESTAMP"

echo "✅ Backup de configuraciones completado"
```
    
    # Configuraciones de servicios
    sudo cp -r /etc/nginx/ "$temp_dir/" 2>/dev/null
    sudo cp -r /etc/apache2/ "$temp_dir/" 2>/dev/null
    sudo cp -r /etc/ssh/ "$temp_dir/" 2>/dev/null
    
    # Lista de paquetes instalados
    pacman -Qe > "$temp_dir/packages_explicit.txt"
    pacman -Q > "$temp_dir/packages_all.txt"
    
    # Configuraciones de usuario
    cp -r ~/.config "$temp_dir/user_config" 2>/dev/null
    cp ~/.bashrc "$temp_dir/" 2>/dev/null
    cp ~/.zshrc "$temp_dir/" 2>/dev/null
    
    # Comprimir backup
    echo "📦 Comprimiendo backup..."
    tar -czf "/tmp/system_backup_$timestamp.tar.gz" -C "/tmp" "system_backup_$timestamp"
    
    # Transferir a servidor de backup
    echo "🚀 Transfiriendo a servidor de backup..."
    scp -C "/tmp/system_backup_$timestamp.tar.gz" "$backup_server:$backup_path/"
    
    if [[ $? -eq 0 ]]; then
        echo "✅ Backup completado exitosamente"
        echo "📄 Archivo: system_backup_$timestamp.tar.gz"
        
        # Limpiar archivos temporales
        rm -rf "$temp_dir"
        rm "/tmp/system_backup_$timestamp.tar.gz"
    else
        echo "❌ Error durante la transferencia"
        echo "💾 Backup local disponible en: /tmp/system_backup_$timestamp.tar.gz"
    fi
}

backup_system_configs
```

### **Despliegue de Aplicación Web**

```bash
#!/bin/bash
# Script de despliegue automático

deploy_web_application() {
    local app_name="$1"
    local server="$2"
    local deploy_path="$3"
    
    if [[ $# -lt 3 ]]; then
        echo "Uso: deploy_web_application <app_name> <server> <deploy_path>"
        echo "Ejemplo: deploy_web_application mi-app produccion@192.168.1.50 /var/www/mi-app"
        return 1
    fi
    
    echo "=== Desplegando $app_name ==="
    echo "Servidor: $server"
    echo "Ruta: $deploy_path"
    echo
    
    # Verificar que existe el directorio de la aplicación
    if [[ ! -d "./$app_name" ]]; then
        echo "❌ Directorio de aplicación no encontrado: ./$app_name"
        return 1
    fi
    
    # Crear backup del despliegue actual
    echo "📦 Creando backup del despliegue actual..."
    ssh "${server%:*}" "sudo tar -czf /tmp/backup_${app_name}_$(date +%Y%m%d_%H%M%S).tar.gz -C $(dirname $deploy_path) $(basename $deploy_path) 2>/dev/null"
    
    # Preparar archivos para despliegue
    echo "🔧 Preparando archivos de despliegue..."
    
    # Excluir archivos de desarrollo
    rsync -av --exclude='node_modules' \
              --exclude='.git' \
              --exclude='*.log' \
              --exclude='.env' \
              --exclude='dist' \
              "./$app_name/" "/tmp/deploy_$app_name/"
    
    # Construir aplicación si es necesario
    if [[ -f "./$app_name/package.json" ]]; then
        echo "🏗️ Construyendo aplicación..."
        (cd "./$app_name" && npm run build)
        cp -r "./$app_name/dist/"* "/tmp/deploy_$app_name/" 2>/dev/null
    fi
    
    # Transferir archivos
    echo "🚀 Transfiriendo archivos..."
    scp -r "/tmp/deploy_$app_name/"* "$server:$deploy_path/"
    
    # Configurar permisos remotos
    echo "🔐 Configurando permisos..."
    ssh "${server%:*}" "sudo chown -R www-data:www-data $deploy_path && sudo chmod -R 755 $deploy_path"
    
    # Reiniciar servicios si es necesario
    echo "🔄 Reiniciando servicios..."
    ssh "${server%:*}" "sudo systemctl reload nginx && sudo systemctl restart php-fpm" 2>/dev/null
    
    # Limpiar archivos temporales
    rm -rf "/tmp/deploy_$app_name"
    
    echo "✅ Despliegue completado para $app_name"
}

# Uso del script
deploy_web_application "mi-tienda-online" "produccion@192.168.1.50" "/var/www/tienda"
```

### **Sincronización de Logs**

```bash
#!/bin/bash
# Script para recopilar logs de múltiples servidores

collect_logs_from_servers() {
    local log_dir="/tmp/logs_collection_$(date +%Y%m%d_%H%M%S)"
    mkdir -p "$log_dir"
    
    # Lista de servidores
    local servers=(
        "web1@192.168.1.10"
        "web2@192.168.1.11"
        "db@192.168.1.20"
        "cache@192.168.1.30"
    )
    
    echo "=== Recopilando Logs de Servidores ==="
    echo "Directorio local: $log_dir"
    echo
    
    for server in "${servers[@]}"; do
        local hostname="${server##*@}"
        local server_dir="$log_dir/$hostname"
        
        echo "📥 Recopilando de $server..."
        mkdir -p "$server_dir"
        
        # Logs del sistema
        scp -q "$server:/var/log/syslog" "$server_dir/syslog" 2>/dev/null
        scp -q "$server:/var/log/auth.log" "$server_dir/auth.log" 2>/dev/null
        
        # Logs de aplicaciones
        scp -q "$server:/var/log/nginx/access.log" "$server_dir/nginx_access.log" 2>/dev/null
        scp -q "$server:/var/log/nginx/error.log" "$server_dir/nginx_error.log" 2>/dev/null
        
        # Logs de aplicación personalizada
        scp -q "$server:/var/log/aplicacion/*.log" "$server_dir/" 2>/dev/null
        
        # Información del sistema
        ssh "$server" "df -h; free -h; uptime" > "$server_dir/system_info.txt" 2>/dev/null
        
        if [[ $? -eq 0 ]]; then
            echo "  ✅ $server - Completado"
        else
            echo "  ⚠️ $server - Algunos archivos no disponibles"
        fi
    done
    
    # Crear archivo comprimido
    echo "📦 Comprimiendo logs..."
    tar -czf "$log_dir.tar.gz" -C "/tmp" "$(basename $log_dir)"
    
    echo "✅ Logs recopilados en: $log_dir.tar.gz"
    echo "📊 Tamaño total: $(du -sh $log_dir.tar.gz | cut -f1)"
}

collect_logs_from_servers
```

### **Backup de Base de Datos**

```bash
#!/bin/bash
# Script de backup de base de datos a servidor remoto

database_backup_to_remote() {
    local db_name="$1"
    local backup_server="backup@192.168.1.200"
    local backup_path="/backups/databases"
    local timestamp=$(date +%Y%m%d_%H%M%S)
    
    if [[ -z "$db_name" ]]; then
        echo "Uso: database_backup_to_remote <nombre_db>"
        return 1
    fi
    
    echo "=== Backup de Base de Datos: $db_name ==="
    echo "Servidor: $backup_server"
    echo "Timestamp: $timestamp"
    echo
    
    # Crear backup local temporal
    local backup_file="/tmp/${db_name}_backup_$timestamp.sql"
    
    echo "🗃️ Creando dump de base de datos..."
    
    # PostgreSQL
    if command -v pg_dump >/dev/null; then
        pg_dump "$db_name" > "$backup_file"
    
    # MySQL/MariaDB
    elif command -v mysqldump >/dev/null; then
        mysqldump "$db_name" > "$backup_file"
    
    else
        echo "❌ No se encontró pg_dump ni mysqldump"
        return 1
    fi
    
    if [[ $? -ne 0 ]]; then
        echo "❌ Error creando backup de base de datos"
        return 1
    fi
    
    # Comprimir backup
    echo "📦 Comprimiendo backup..."
    gzip "$backup_file"
    backup_file="${backup_file}.gz"
    
    # Transferir a servidor de backup
    echo "🚀 Transfiriendo a servidor de backup..."
    scp -C "$backup_file" "$backup_server:$backup_path/"
    
    if [[ $? -eq 0 ]]; then
        echo "✅ Backup transferido exitosamente"
        echo "📄 Archivo: $(basename $backup_file)"
        echo "📊 Tamaño: $(du -sh $backup_file | cut -f1)"
        
        # Limpiar archivo temporal
        rm "$backup_file"
        
        # Verificar integridad en servidor remoto
        echo "🔍 Verificando integridad..."
        ssh "$backup_server" "gunzip -t $backup_path/$(basename $backup_file)"
        
        if [[ $? -eq 0 ]]; then
            echo "✅ Verificación de integridad exitosa"
        else
            echo "⚠️ Advertencia: Error en verificación de integridad"
        fi
        
    else
        echo "❌ Error durante la transferencia"
        echo "💾 Backup local disponible en: $backup_file"
    fi
}

# Automatizar backups de múltiples bases de datos
backup_all_databases() {
    echo "=== Backup Automático de Todas las Bases de Datos ==="
    
    # Obtener lista de bases de datos PostgreSQL
    if command -v psql >/dev/null; then
        psql -l -t | cut -d'|' -f1 | grep -v template | grep -v postgres | sed 's/ //g' | grep -v '^$' | while read db; do
            database_backup_to_remote "$db"
        done
    fi
    
    # Obtener lista de bases de datos MySQL/MariaDB
    if command -v mysql >/dev/null; then
        mysql -e "SHOW DATABASES;" | grep -v "information_schema\|mysql\|performance_schema\|sys" | tail -n +2 | while read db; do
            database_backup_to_remote "$db"
        done
    fi
}

# Ejemplo de uso
database_backup_to_remote "mi_aplicacion"
```

## **Automatización con Scripts**

### **Script de Sincronización Bidireccional**

```bash
#!/bin/bash
# Script de sincronización inteligente con scp

smart_sync() {
    local local_path="$1"
    local remote_path="$2"
    local direction="$3"  # "up", "down", o "both"
    
    if [[ $# -lt 3 ]]; then
        echo "Uso: smart_sync <ruta_local> <usuario@servidor:ruta_remota> <direccion>"
        echo "Direcciones: up (local->remoto), down (remoto->local), both (bidireccional)"
        return 1
    fi
    
    echo "=== Sincronización Inteligente ==="
    echo "Local: $local_path"
    echo "Remoto: $remote_path"
    echo "Dirección: $direction"
    echo
    
    case "$direction" in
        "up")
            echo "📤 Sincronizando: Local → Remoto"
            scp -rp "$local_path"/* "$remote_path/"
            ;;
        "down")
            echo "📥 Sincronizando: Remoto → Local"
            scp -rp "$remote_path"/* "$local_path/"
            ;;
        "both")
            echo "🔄 Sincronización bidireccional"
            
            # Comparar timestamps (requiere script adicional en servidor)
            echo "⏰ Comparando timestamps..."
            
            # Subir archivos más nuevos localmente
            find "$local_path" -type f -newer "$local_path/.last_sync" 2>/dev/null | while read file; do
                relative_path="${file#$local_path/}"
                echo "📤 Subiendo: $relative_path"
                scp "$file" "$remote_path/$relative_path"
            done
            
            # Descargar archivos más nuevos remotamente (simplificado)
            echo "📥 Descargando archivos remotos más nuevos..."
            scp -rp "$remote_path"/* "$local_path/"
            
            # Marcar tiempo de sincronización
            touch "$local_path/.last_sync"
            ;;
        *)
            echo "❌ Dirección no válida: $direction"
            return 1
            ;;
    esac
    
    echo "✅ Sincronización completada"
}

# Uso del script
smart_sync "/home/usuario/proyecto" "servidor@192.168.1.100:/var/www/proyecto" "up"
```

### **Monitor de Transferencias en Tiempo Real**

```bash
#!/bin/bash
# Monitor de transferencias scp en tiempo real

monitor_scp_transfers() {
    echo "=== Monitor de Transferencias SCP ==="
    echo "Presiona Ctrl+C para salir"
    echo
    
    while true; do
        clear
        echo "Monitor SCP - $(date)"
        echo "========================"
        echo
        
        # Procesos scp activos
        echo "🚀 Procesos SCP activos:"
        ps aux | grep '[s]cp' | while read line; do
            echo "  $line"
        done
        echo
        
        # Conexiones SSH activas (para scp)
        echo "🔗 Conexiones SSH activas:"
        ss -tn | grep ':22' | head -10
        echo
        
        # Uso de ancho de banda
        echo "📊 Uso de red:"
        if command -v vnstat >/dev/null; then
            vnstat -i eth0 -tr 2 | tail -2
        else
            echo "  (instala vnstat para estadísticas detalladas)"
        fi
        
        # Espacio en disco
        echo "💾 Espacio en disco:"
        df -h /tmp | tail -1
        
        sleep 5
    done
}

monitor_scp_transfers
```

## **Configuración y Optimización**

### **Archivo de Configuración SSH para scp**

```bash
# Crear configuración SSH optimizada para scp
mkdir -p ~/.ssh
chmod 700 ~/.ssh

tee ~/.ssh/config << 'EOF'
# Configuración general
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    Compression yes
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h-%p
    ControlPersist 600

# Servidor de desarrollo
Host dev
    HostName 192.168.1.50
    User desarrollador
    Port 22
    IdentityFile ~/.ssh/dev_key
    
# Servidor de producción
Host prod
    HostName servidor.empresa.com
    User admin
    Port 2022
    IdentityFile ~/.ssh/prod_key
    StrictHostKeyChecking yes
    
# Servidor de backup
Host backup
    HostName 192.168.1.200
    User backup
    Port 22
    IdentityFile ~/.ssh/backup_key
    Compression yes
    
# Configuración para transferencias grandes
Host *.bigfiles.com
    Compression no
    TCPKeepAlive yes
    ServerAliveInterval 20
EOF

chmod 600 ~/.ssh/config

# Crear directorio para sockets de control
mkdir -p ~/.ssh/sockets
chmod 700 ~/.ssh/sockets

echo "✅ Configuración SSH optimizada para scp"
```

### **Script de Optimización de Rendimiento**

```bash
#!/bin/bash
# Script para optimizar transferencias scp

optimize_scp_performance() {
    echo "=== Optimización de Rendimiento SCP ==="
    
    # Verificar algoritmos de cifrado disponibles
    echo "🔐 Algoritmos de cifrado soportados:"
    ssh -Q cipher | head -10
    echo
    
    # Verificar algoritmos de compresión
    echo "📦 Compresión disponible:"
    ssh -Q compression
    echo
    
    # Configurar cipher más rápido para LAN
    echo "⚡ Configuración recomendada para LAN:"
    echo "scp -c aes128-ctr -o Compression=no archivo.txt servidor:/destino/"
    echo
    
    # Configuración para WAN con compresión
    echo "🌐 Configuración recomendada para WAN:"
    echo "scp -C -c aes128-ctr archivo.txt servidor:/destino/"
    echo
    
    # Verificar MTU de la interfaz
    echo "🔧 MTU de interfaces de red:"
    ip link show | grep mtu
    echo
    
    # Sugerencias de optimización
    echo "💡 Sugerencias de optimización:"
    echo "1. Para archivos grandes: usar -C (compresión)"
    echo "2. Para LAN: usar -c aes128-ctr sin compresión"
    echo "3. Para múltiples archivos: considerar rsync o tar+ssh"
    echo "4. Usar ControlMaster para conexiones persistentes"
    echo "5. Verificar que no hay límites de ancho de banda"
}

optimize_scp_performance
```

## **Troubleshooting de scp**

### **Problemas Comunes y Soluciones**

```bash
# Diagnóstico completo de problemas scp
troubleshoot_scp() {
    local target_host="$1"
    
    echo "=== Diagnóstico de Problemas SCP ==="
    echo "Host objetivo: $target_host"
    echo
    
    # Verificar conectividad básica
    echo "🌐 Verificando conectividad:"
    if ping -c 3 "$target_host" >/dev/null 2>&1; then
        echo "  ✅ Ping OK"
    else
        echo "  ❌ Sin conectividad ping"
    fi
    
    # Verificar puerto SSH
    echo "🔌 Verificando puerto SSH:"
    if nc -z "$target_host" 22 2>/dev/null; then
        echo "  ✅ Puerto 22 abierto"
    else
        echo "  ❌ Puerto 22 cerrado o filtrado"
    fi
    
    # Verificar autenticación SSH
    echo "🔑 Verificando autenticación:"
    if ssh -o BatchMode=yes -o ConnectTimeout=5 "$target_host" 'echo "Auth OK"' 2>/dev/null; then
        echo "  ✅ Autenticación exitosa"
    else
        echo "  ❌ Error de autenticación"
        echo "    - Verifica credenciales o claves SSH"
        echo "    - Ejecuta: ssh-copy-id $target_host"
    fi
    
    # Verificar espacio en disco
    echo "💾 Verificando espacio en disco:"
    local_space=$(df /tmp | awk 'NR==2 {print $4}')
    echo "  Local (/tmp): $(( local_space / 1024 )) MB disponibles"
    
    if ssh "$target_host" 'df /tmp' >/dev/null 2>&1; then
        remote_space=$(ssh "$target_host" 'df /tmp | awk "NR==2 {print \$4}"')
        echo "  Remoto (/tmp): $(( remote_space / 1024 )) MB disponibles"
    else
        echo "  ❌ No se puede verificar espacio remoto"
    fi
    
    # Verificar permisos
    echo "🔐 Verificando permisos:"
    if ssh "$target_host" 'test -w /tmp' 2>/dev/null; then
        echo "  ✅ Permisos de escritura OK"
    else
        echo "  ❌ Sin permisos de escritura"
    fi
    
    # Test de transferencia simple
    echo "🧪 Test de transferencia:"
    test_file="/tmp/scp_test_$(date +%s).txt"
    echo "Test de scp - $(date)" > "$test_file"
    
    if scp "$test_file" "$target_host:/tmp/" >/dev/null 2>&1; then
        echo "  ✅ Transferencia test exitosa"
        ssh "$target_host" "rm /tmp/$(basename $test_file)" 2>/dev/null
    else
        echo "  ❌ Fallo en transferencia test"
    fi
    
    rm "$test_file" 2>/dev/null
    
    echo
    echo "🔧 Comandos útiles para debugging:"
    echo "  scp -v archivo.txt $target_host:/tmp/     # Modo verbose"
    echo "  ssh -v $target_host                      # Debug SSH"
    echo "  ssh $target_host 'ls -la /destino/'      # Verificar destino"
}

# Función para probar diferentes algoritmos de cifrado
test_cipher_performance() {
    local host="$1"
    local file="$2"
    
    if [[ ! -f "$file" ]]; then
        echo "Archivo no encontrado: $file"
        return 1
    fi
    
    echo "=== Test de Rendimiento de Algoritmos ==="
    
    local ciphers=("aes128-ctr" "aes256-ctr" "chacha20-poly1305@openssh.com" "aes128-gcm@openssh.com")
    
    for cipher in "${ciphers[@]}"; do
        echo "🧪 Probando $cipher:"
        
        time_start=$(date +%s.%N)
        scp -c "$cipher" "$file" "$host:/tmp/test_$(basename $file)" 2>/dev/null
        time_end=$(date +%s.%N)
        
        if [[ $? -eq 0 ]]; then
            duration=$(echo "$time_end - $time_start" | bc)
            echo "  ✅ Completado en ${duration}s"
            
            # Limpiar archivo remoto
            ssh "$host" "rm /tmp/test_$(basename $file)" 2>/dev/null
        else
            echo "  ❌ Error con $cipher"
        fi
    done
}

# Uso de las funciones de troubleshooting
troubleshoot_scp "192.168.1.100"
```

### **Recovery de Transferencias Interrumpidas**

```bash
#!/bin/bash
# Script para reanudar transferencias scp interrumpidas

resume_scp_transfer() {
    local local_file="$1"
    local remote_path="$2"
    
    if [[ $# -lt 2 ]]; then
        echo "Uso: resume_scp_transfer <archivo_local> <usuario@servidor:ruta_remota>"
        return 1
    fi
    
    echo "=== Recuperación de Transferencia SCP ==="
    echo "Archivo local: $local_file"
    echo "Destino remoto: $remote_path"
    echo
    
    # scp no soporta resume nativo, usar rsync como alternativa
    if command -v rsync >/dev/null; then
        echo "📡 Usando rsync para transferencia resumible..."
        rsync -avz --partial --progress "$local_file" "$remote_path"
        
        if [[ $? -eq 0 ]]; then
            echo "✅ Transferencia completada con rsync"
        else
            echo "❌ Error en transferencia con rsync"
        fi
    else
        echo "⚠️ rsync no disponible, reinstentando con scp..."
        echo "💡 Instala rsync para transferencias resumibles: sudo pacman -S rsync"
        
        # Reintentar con scp
        scp -r "$local_file" "$remote_path"
    fi
}

# Script de reintento automático
retry_scp_with_backoff() {
    local max_retries=5
    local delay=1
    local command="$*"
    
    for ((i=1; i<=max_retries; i++)); do
        echo "🔄 Intento $i de $max_retries..."
        
        if eval "$command"; then
            echo "✅ Comando exitoso en intento $i"
            return 0
        else
            echo "❌ Intento $i falló"
            
            if [[ $i -lt $max_retries ]]; then
                echo "⏳ Esperando ${delay}s antes del siguiente intento..."
                sleep $delay
                delay=$((delay * 2))  # Backoff exponencial
            fi
        fi
    done
    
    echo "💥 Todos los intentos fallaron"
    return 1
}

# Uso del retry con backoff
retry_scp_with_backoff scp archivo_grande.zip usuario@servidor:/destino/
```

## **Seguridad en scp**

### **Mejores Prácticas de Seguridad**

```bash
#!/bin/bash
# Script de verificación de seguridad para scp

security_check_scp() {
    echo "=== Verificación de Seguridad SCP ==="
    
    # Verificar configuración SSH
    echo "🔐 Configuración de seguridad SSH:"
    
    # Verificar que PasswordAuthentication esté deshabilitado
    if grep -q "PasswordAuthentication no" /etc/ssh/sshd_config 2>/dev/null; then
        echo "  ✅ PasswordAuthentication deshabilitado"
    else
        echo "  ⚠️ Recomendado: deshabilitar PasswordAuthentication"
    fi
    
    # Verificar que PermitRootLogin esté restringido
    if grep -q "PermitRootLogin no\|PermitRootLogin prohibit-password" /etc/ssh/sshd_config 2>/dev/null; then
        echo "  ✅ PermitRootLogin configurado de forma segura"
    else
        echo "  ⚠️ Recomendado: restringir PermitRootLogin"
    fi
    
    # Verificar claves SSH
    echo "🔑 Verificación de claves SSH:"
    
    if [[ -f ~/.ssh/id_rsa ]]; then
        key_size=$(ssh-keygen -lf ~/.ssh/id_rsa | awk '{print $1}')
        if [[ $key_size -ge 2048 ]]; then
            echo "  ✅ Clave RSA de tamaño adecuado: $key_size bits"
        else
            echo "  ⚠️ Clave RSA pequeña: $key_size bits (recomendado ≥2048)"
        fi
    fi
    
    if [[ -f ~/.ssh/id_ed25519 ]]; then
        echo "  ✅ Clave Ed25519 encontrada (recomendada)"
    fi
    
    # Verificar permisos de archivos SSH
    echo "🔒 Verificación de permisos:"
    
    check_ssh_permissions() {
        local file="$1"
        local expected="$2"
        
        if [[ -f "$file" ]]; then
            actual=$(stat -c "%a" "$file")
            if [[ "$actual" == "$expected" ]]; then
                echo "  ✅ $file: $actual (correcto)"
            else
                echo "  ⚠️ $file: $actual (debería ser $expected)"
            fi
        fi
    }
    
    check_ssh_permissions ~/.ssh/id_rsa 600
    check_ssh_permissions ~/.ssh/id_rsa.pub 644
    check_ssh_permissions ~/.ssh/authorized_keys 600
    check_ssh_permissions ~/.ssh/config 600
    
    # Verificar directorio SSH
    ssh_dir_perms=$(stat -c "%a" ~/.ssh)
    if [[ "$ssh_dir_perms" == "700" ]]; then
        echo "  ✅ ~/.ssh: $ssh_dir_perms (correcto)"
    else
        echo "  ⚠️ ~/.ssh: $ssh_dir_perms (debería ser 700)"
    fi
    
    echo
    echo "🛡️ Recomendaciones de seguridad:"
    echo "1. Usar autenticación por claves SSH únicamente"
    echo "2. Deshabilitar autenticación por contraseña"
    echo "3. Usar claves Ed25519 o RSA ≥2048 bits"
    echo "4. Configurar fail2ban para protección anti-brute force"
    echo "5. Usar firewalls para restringir acceso SSH"
    echo "6. Rotar claves SSH periódicamente"
}

security_check_scp
```

### **Configuración de Claves SSH Seguras**

```bash
#!/bin/bash
# Script para configurar claves SSH seguras

setup_secure_ssh_keys() {
    local key_type="${1:-ed25519}"
    local comment="${2:-$(whoami)@$(hostname)-$(date +%Y%m%d)}"
    
    echo "=== Configuración de Claves SSH Seguras ==="
    echo "Tipo de clave: $key_type"
    echo "Comentario: $comment"
    echo
    
    # Crear directorio SSH si no existe
    mkdir -p ~/.ssh
    chmod 700 ~/.ssh
    
    # Generar nueva clave
    case "$key_type" in
        "ed25519")
            ssh-keygen -t ed25519 -C "$comment" -f ~/.ssh/id_ed25519
            ;;
        "rsa")
            ssh-keygen -t rsa -b 4096 -C "$comment" -f ~/.ssh/id_rsa
            ;;
        "ecdsa")
            ssh-keygen -t ecdsa -b 521 -C "$comment" -f ~/.ssh/id_ecdsa
            ;;
        *)
            echo "❌ Tipo de clave no soportado: $key_type"
            echo "Tipos disponibles: ed25519, rsa, ecdsa"
            return 1
            ;;
    esac
    
    # Configurar permisos
    chmod 600 ~/.ssh/id_*
    chmod 644 ~/.ssh/id_*.pub
    
    # Mostrar clave pública
    echo
    echo "🔑 Clave pública generada:"
    cat ~/.ssh/id_${key_type}.pub
    echo
    
    # Instrucciones para copiar la clave
    echo "📋 Para copiar la clave a un servidor:"
    echo "ssh-copy-id -i ~/.ssh/id_${key_type}.pub usuario@servidor"
    echo
    echo "🔧 Para usar esta clave específica con scp:"
    echo "scp -i ~/.ssh/id_${key_type} archivo.txt usuario@servidor:/destino/"
}

setup_secure_ssh_keys "ed25519" "$(whoami)@$(hostname)-arch-$(date +%Y%m%d)"
```

## **Enlaces Relacionados**

- [[ssh]] - Configuración de SSH para usar con scp
- [[rsync]] - Alternativa avanzada para sincronización
- [[firewall]] - Configurar firewall para permitir SSH/SCP
- [[Usuario root]] - Gestión de permisos para transferencias
- [[ps]] - Monitorear procesos de transferencia scp
- [[df]] - Verificar espacio en disco antes de transferencias