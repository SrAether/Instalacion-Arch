# ssh - Secure Shell para Administración Remota

El comando `ssh` es tu **puente seguro** hacia otros sistemas. Es la herramienta fundamental para la administración remota, permitiendo conectarse de forma segura a servidores y ejecutar comandos en sistemas remotos como si estuvieras físicamente frente a ellos.

## **¿Qué es SSH?**

`ssh` te permite:
- **Conectarte remotamente** a servidores de forma segura
- **Ejecutar comandos** en sistemas remotos
- **Transferir archivos** de manera cifrada
- **Crear túneles seguros** para servicios

## **¿Por qué es Fundamental en Arch Linux?**

SSH es esencial para administrar servidores remotos, acceder a sistemas sin interfaz gráfica, automatizar tareas en múltiples máquinas y mantener la seguridad en comunicaciones de red.

### **Sintaxis Básica**

```bash
# Conexión básica
ssh usuario@servidor

# Conexión con puerto específico
ssh -p 2222 usuario@servidor

# Ejecutar comando remoto
ssh usuario@servidor "comando"

# Conexión con clave específica
ssh -i ~/.ssh/clave_privada usuario@servidor
```

### **Opciones Fundamentales**

```bash
# -p PORT          = especificar puerto
# -i KEYFILE       = usar clave privada específica
# -L LOCAL:REMOTE  = túnel local
# -R REMOTE:LOCAL  = túnel remoto
# -X               = habilitar X11 forwarding
# -v               = modo verbose (debug)
# -N               = no ejecutar comando remoto
# -f               = ejecutar en background
# -C               = comprimir datos
```

## **Conexiones Básicas**

### **Conectarse a Servidores**

```bash
# Conexión simple
ssh root@192.168.1.100

# Conexión con usuario específico
ssh juan@servidor.ejemplo.com

# Conexión con puerto no estándar
ssh -p 2222 usuario@servidor

# Conexión con IP y usuario
ssh usuario@203.0.113.10

# Conexión verbose para debugging
ssh -v usuario@servidor
```

### **Ejecutar Comandos Remotos**

```bash
# Comando simple
ssh servidor "uptime"

# Comando con variables
ssh servidor "df -h | grep /"

# Múltiples comandos
ssh servidor "cd /var/log && ls -la && df -h"

# Comando interactivo
ssh -t servidor "sudo systemctl status apache2"

# Script remoto
ssh servidor 'bash -s' < script_local.sh
```

## **Configuración SSH en Arch Linux**

### **Instalar y Configurar SSH Server**

```bash
# Instalar OpenSSH
sudo pacman -S openssh

# Habilitar servicio SSH
sudo systemctl enable sshd
sudo systemctl start sshd

# Verificar estado del servicio
sudo systemctl status sshd

# Ver puerto en uso
sudo ss -tlnp | grep :22
```

### **Configuración del Servidor SSH**

```bash
# Editar configuración principal
sudo nano /etc/ssh/sshd_config

# Configuración segura básica
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Aplicar configuración segura con sed
sudo sed -i '
    s/#Port 22/Port 2222/
    s/#PermitRootLogin yes/PermitRootLogin no/
    s/#PasswordAuthentication yes/PasswordAuthentication no/
    s/#PubkeyAuthentication yes/PubkeyAuthentication yes/
    s/#AuthorizedKeysFile/AuthorizedKeysFile/
' /etc/ssh/sshd_config

# Reiniciar servicio para aplicar cambios
sudo systemctl restart sshd
```

### **Configuración del Cliente SSH**

```bash
# Crear directorio SSH del usuario
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# Crear archivo de configuración del cliente
nano ~/.ssh/config

# Ejemplo de configuración del cliente
cat > ~/.ssh/config << 'EOF'
# Servidor de desarrollo
Host dev
    HostName dev.empresa.com
    User developer
    Port 2222
    IdentityFile ~/.ssh/dev_key

# Servidor de producción
Host prod
    HostName prod.empresa.com
    User admin
    Port 22
    IdentityFile ~/.ssh/prod_key
    
# Configuración por defecto
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    Compression yes
EOF

chmod 600 ~/.ssh/config
```

## **Autenticación con Claves SSH**

### **Generar Claves SSH**

```bash
# Generar clave RSA (recomendado para compatibilidad)
ssh-keygen -t rsa -b 4096 -C "tu_email@ejemplo.com"

# Generar clave Ed25519 (más moderna y segura)
ssh-keygen -t ed25519 -C "tu_email@ejemplo.com"

# Generar clave con nombre específico
ssh-keygen -t rsa -b 4096 -f ~/.ssh/servidor_produccion

# Generar clave sin passphrase (para automatización)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/automation_key -N ""
```

### **Configurar Claves Públicas**

```bash
# Copiar clave pública al servidor (método recomendado)
ssh-copy-id usuario@servidor

# Copiar clave específica
ssh-copy-id -i ~/.ssh/clave_especifica.pub usuario@servidor

# Copiar manualmente si ssh-copy-id no está disponible
cat ~/.ssh/id_rsa.pub | ssh usuario@servidor "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Configurar permisos correctos en el servidor
ssh usuario@servidor "chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
```

### **Gestión de Claves SSH**

```bash
# Listar claves cargadas en el agente SSH
ssh-add -l

# Agregar clave al agente SSH
ssh-add ~/.ssh/clave_privada

# Agregar todas las claves del directorio
ssh-add ~/.ssh/id_*

# Eliminar clave del agente
ssh-add -d ~/.ssh/clave_privada

# Eliminar todas las claves del agente
ssh-add -D

# Iniciar agente SSH automáticamente
eval "$(ssh-agent -s)"
```

## **Transferencia de Archivos**

### **SCP - Copia Segura**

```bash
# Copiar archivo local a servidor
scp archivo.txt usuario@servidor:/ruta/destino/

# Copiar desde servidor a local
scp usuario@servidor:/ruta/archivo.txt ./

# Copiar directorio completo
scp -r directorio/ usuario@servidor:/ruta/destino/

# Copiar con puerto específico
scp -P 2222 archivo.txt usuario@servidor:/destino/

# Copiar múltiples archivos
scp archivo1.txt archivo2.txt usuario@servidor:/destino/

# Copiar con preservación de permisos
scp -p archivo.txt usuario@servidor:/destino/
```

### **SFTP - FTP Seguro**

```bash
# Conectar via SFTP
sftp usuario@servidor

# Comandos dentro de sesión SFTP
sftp> ls                    # listar archivos remotos
sftp> lls                   # listar archivos locales
sftp> pwd                   # directorio remoto actual
sftp> lpwd                  # directorio local actual
sftp> get archivo.txt       # descargar archivo
sftp> put archivo.txt       # subir archivo
sftp> mget *.txt           # descargar múltiples archivos
sftp> mput *.txt           # subir múltiples archivos
sftp> mkdir nueva_carpeta   # crear directorio remoto
sftp> rmdir carpeta        # eliminar directorio remoto
sftp> bye                  # salir
```

## **Túneles SSH**

### **Port Forwarding (Túneles Locales)**

```bash
# Túnel local - acceder servicio remoto localmente
ssh -L 8080:localhost:80 usuario@servidor
# Ahora http://localhost:8080 accede al puerto 80 del servidor

# Túnel para base de datos
ssh -L 3306:localhost:3306 usuario@db-server
# Ahora puedes conectar a MySQL localmente en puerto 3306

# Túnel para servicio web interno
ssh -L 8888:internal-server:8080 usuario@gateway
# Accede a internal-server:8080 via gateway

# Túnel en background
ssh -f -N -L 9000:localhost:9000 usuario@servidor
```

### **Reverse Port Forwarding (Túneles Remotos)**

```bash
# Túnel reverso - exponer servicio local al servidor remoto
ssh -R 8080:localhost:80 usuario@servidor
# El servidor puede acceder a tu puerto 80 local via su puerto 8080

# Exponer servicio local a red remota
ssh -R 3000:localhost:3000 usuario@servidor
# El servidor y su red pueden acceder a tu servicio local en puerto 3000

# Túnel reverso persistente
ssh -f -N -R 2222:localhost:22 usuario@servidor
# El servidor puede hacer SSH de vuelta a tu máquina via puerto 2222
```

### **SOCKS Proxy**

```bash
# Crear proxy SOCKS5
ssh -D 1080 usuario@servidor
# Configura aplicaciones para usar proxy SOCKS5 en localhost:1080

# Proxy SOCKS en background
ssh -f -N -D 1080 usuario@servidor

# Usar con curl a través del proxy
curl --socks5 localhost:1080 http://sitio-bloqueado.com

# Usar con navegador
# Configurar Firefox o Chrome para usar SOCKS5 proxy en localhost:1080
```

## **Administración Remota**

### **Comandos de Sistema Remotos**

```bash
# Verificar estado del sistema
ssh servidor "uptime && free -h && df -h"

# Monitorear procesos
ssh servidor "top -b -n 1"

# Gestionar servicios
ssh servidor "sudo systemctl status nginx"
ssh servidor "sudo systemctl restart apache2"

# Ver logs remotos
ssh servidor "sudo journalctl -f"
ssh servidor "tail -f /var/log/syslog"

# Información del sistema
ssh servidor "uname -a && cat /etc/os-release"
```

### **Mantenimiento Remoto**

```bash
# Actualización de sistema remoto (Arch Linux)
ssh servidor "sudo pacman -Syu"

# Limpiar cache de paquetes
ssh servidor "sudo pacman -Sc"

# Verificar espacio en disco
ssh servidor "df -h && du -sh /var/log/* | sort -h"

# Backup remoto
ssh servidor "sudo tar czf backup-$(date +%Y%m%d).tar.gz /etc /home"

# Reinicio remoto
ssh servidor "sudo reboot"
```

### **Scripts de Administración**

```bash
#!/bin/bash
# Script de monitoreo de múltiples servidores

SERVERS=(
    "web1.empresa.com"
    "web2.empresa.com"
    "db.empresa.com"
)

check_server() {
    local server="$1"
    echo "=== Verificando $server ==="
    
    ssh "$server" "
        echo 'Uptime:' && uptime
        echo 'Memoria:' && free -h | grep Mem
        echo 'Disco:' && df -h / | tail -1
        echo 'Load Average:' && cat /proc/loadavg
        echo
    " 2>/dev/null || echo "ERROR: No se puede conectar a $server"
}

# Verificar todos los servidores
for server in "${SERVERS[@]}"; do
    check_server "$server"
done
```

## **Configuración Avanzada**

### **Configuración de Seguridad**

```bash
# Archivo de configuración del servidor más seguro
sudo tee /etc/ssh/sshd_config << 'EOF'
# Puerto no estándar
Port 2222

# Protocolo SSH versión 2 únicamente
Protocol 2

# Autenticación
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

# Límites de conexión
MaxAuthTries 3
MaxStartups 10:30:100
ClientAliveInterval 300
ClientAliveCountMax 2

# Cifrado fuerte
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group16-sha512

# Usuarios permitidos
AllowUsers usuario1 usuario2

# Deshabilitar forwarding por defecto
AllowTcpForwarding no
X11Forwarding no
AllowAgentForwarding no
EOF

sudo systemctl restart sshd
```

### **Fail2Ban para Protección SSH**

```bash
# Instalar fail2ban
sudo pacman -S fail2ban

# Configurar para SSH
sudo tee /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = ssh,2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
EOF

# Habilitar y iniciar fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Ver estado de jails
sudo fail2ban-client status sshd
```

## **Automatización con SSH**

### **Scripts de Deployment**

```bash
#!/bin/bash
# Script de deployment automático

SERVERS=("web1" "web2" "web3")
APP_DIR="/var/www/app"
BACKUP_DIR="/backup"

deploy_to_server() {
    local server="$1"
    echo "Deployando a $server..."
    
    # Crear backup
    ssh "$server" "sudo cp -r $APP_DIR $BACKUP_DIR/app-backup-$(date +%Y%m%d-%H%M%S)"
    
    # Subir nueva versión
    rsync -avz --delete ./app/ "$server:$APP_DIR/"
    
    # Reiniciar servicios
    ssh "$server" "sudo systemctl restart nginx && sudo systemctl restart php-fpm"
    
    # Verificar estado
    if ssh "$server" "curl -s http://localhost/health" > /dev/null; then
        echo "✓ Deployment exitoso en $server"
    else
        echo "✗ Error en deployment en $server"
        return 1
    fi
}

# Deployar a todos los servidores
for server in "${SERVERS[@]}"; do
    deploy_to_server "$server" || echo "Error en $server"
done
```

### **Monitoreo Automatizado**

```bash
#!/bin/bash
# Script de monitoreo con alertas

SERVERS_FILE="/etc/servers.list"
ALERT_EMAIL="admin@empresa.com"

check_server_health() {
    local server="$1"
    
    # Verificar conectividad
    if ! ssh -o ConnectTimeout=10 "$server" "echo 'OK'" >/dev/null 2>&1; then
        echo "CRITICAL: $server - No se puede conectar via SSH"
        return 1
    fi
    
    # Verificar carga del sistema
    local load=$(ssh "$server" "cut -d' ' -f1 /proc/loadavg")
    if (( $(echo "$load > 5.0" | bc -l) )); then
        echo "WARNING: $server - Carga alta: $load"
    fi
    
    # Verificar espacio en disco
    local disk_usage=$(ssh "$server" "df / | awk 'NR==2 {print \$5}' | sed 's/%//'")
    if [[ $disk_usage -gt 90 ]]; then
        echo "CRITICAL: $server - Disco lleno: ${disk_usage}%"
    fi
    
    # Verificar memoria
    local mem_usage=$(ssh "$server" "free | awk 'NR==2{printf \"%.0f\", \$3/\$2*100}'")
    if [[ $mem_usage -gt 95 ]]; then
        echo "WARNING: $server - Memoria alta: ${mem_usage}%"
    fi
}

# Monitorear todos los servidores
while IFS= read -r server; do
    [[ "$server" =~ ^#.*$ ]] && continue  # Saltar comentarios
    check_server_health "$server"
done < "$SERVERS_FILE"
```

## **Troubleshooting SSH**

### **Problemas de Conexión Comunes**

```bash
# Debug de conexión SSH
ssh -vvv usuario@servidor

# Verificar configuración del cliente
ssh -F /dev/null -o UserKnownHostsFile=/dev/null usuario@servidor

# Probar con diferentes opciones de autenticación
ssh -o PasswordAuthentication=yes usuario@servidor
ssh -o PubkeyAuthentication=no usuario@servidor

# Verificar puerto y servicio en servidor remoto
nmap -p 22 servidor
telnet servidor 22
```

### **Problemas de Permisos**

```bash
# Verificar permisos en el cliente
ls -la ~/.ssh/
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/config

# Verificar permisos en el servidor
ssh servidor "ls -la ~/.ssh/ && cat ~/.ssh/authorized_keys"
ssh servidor "chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
```

### **Problemas de Firewall**

```bash
# Verificar reglas de firewall
sudo iptables -L | grep ssh
sudo ufw status | grep ssh

# Permitir SSH en firewall
sudo ufw allow ssh
sudo ufw allow 2222/tcp  # Puerto personalizado

# Verificar que el servicio esté escuchando
sudo netstat -tlnp | grep :22
sudo ss -tlnp | grep :22
```

## **Integración con Otros Comandos**

### **Con [[rsync]] para Sincronización**

```bash
# Sincronizar directorios via SSH
rsync -avz directorio/ usuario@servidor:/destino/

# Backup incremental via SSH
rsync -avz --delete ~/Documentos/ servidor:~/backup/documentos/

# Sincronización con progreso
rsync -avz --progress archivos/ servidor:/backup/
```

### **Con [[find]] para Operaciones Remotas**

```bash
# Buscar archivos en servidor remoto
ssh servidor "find /var/log -name '*.log' -mtime +7"

# Ejecutar comando en archivos encontrados
ssh servidor "find /tmp -name '*.tmp' -delete"

# Buscar y comprimir archivos remotos
ssh servidor "find /backup -name '*.sql' -exec gzip {} \;"
```

### **Con [[tar]] y [[gzip]] para Backups**

```bash
# Backup remoto comprimido
ssh servidor "tar czf - /home/usuario" > backup-usuario.tar.gz

# Restaurar backup remoto
cat backup.tar.gz | ssh servidor "tar xzf - -C /"

# Backup incremental
ssh servidor "tar czf backup-$(date +%Y%m%d).tar.gz --newer-mtime='1 day ago' /home"
```

## **Enlaces Relacionados**

- [[systemctl]] - Gestionar servicios SSH remotamente
- [[firewall]] - Configurar reglas para SSH
- [[rsync]] - Sincronizar archivos via SSH
- [[tar]] - Crear backups remotos
- [[gzip]] - Comprimir transferencias SSH
- [[find]] - Buscar archivos en servidores remotos