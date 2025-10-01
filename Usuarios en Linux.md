# Usuarios en Linux - Conceptos Fundamentales

Los **usuarios** son la base del sistema de seguridad y permisos en Linux. Comprender los tipos de usuarios, sus roles y cómo gestionarlos es fundamental para la administración segura de cualquier sistema Arch Linux.

## **¿Qué son los Usuarios en Linux?**

En Linux, cada proceso y archivo pertenece a un usuario específico. Los usuarios determinan:
- **Qué archivos** pueden acceder y modificar
- **Qué comandos** pueden ejecutar
- **Qué recursos del sistema** pueden utilizar
- **Qué permisos** tienen sobre el sistema

## **Tipos de Usuarios**

### **1. Usuario Root (Superusuario)**

```bash
# El usuario root tiene UID 0 y permisos absolutos
whoami                    # Mostrar usuario actual
id                       # Mostrar UID, GID y grupos

# Características del usuario root:
# - UID: 0
# - Puede hacer cualquier cosa en el sistema
# - No está sujeto a restricciones de permisos
# - Puede modificar archivos de sistema críticos
# - Puede instalar/desinstalar software
# - Puede crear, modificar y eliminar usuarios
```

**Para más información detallada sobre root, consulta: [[Usuario root]]**

### **2. Usuarios del Sistema (System Users)**

```bash
# Usuarios creados para servicios y demonios
# Ejemplos en /etc/passwd:
grep -E '^(daemon|bin|sys|sync|games|man|lp|mail|news|uucp|proxy|www-data|backup|list|irc|gnats|nobody)' /etc/passwd

# Características:
# - UID típicamente < 1000 (en Arch Linux)
# - No tienen shell de login (usualmente /usr/bin/nologin o /bin/false)
# - Ejecutan servicios específicos
# - No tienen directorio home o es mínimo
# - Principio de menor privilegio
```

#### **Usuarios de Sistema Comunes**

```bash
# Ver usuarios del sistema
awk -F: '$3 < 1000 {print $1, $3, $7}' /etc/passwd

# Ejemplos importantes:
# daemon - Procesos del sistema
# bin - Ejecutables del sistema  
# sys - Archivos de sistema
# mail - Sistema de correo
# http - Servidor web (en Arch Linux)
# nobody - Usuario sin privilegios para servicios
```

### **3. Usuarios Regulares (Normal Users)**

```bash
# Usuarios humanos reales del sistema
# Características:
# - UID >= 1000 (en Arch Linux)
# - Tienen directorio home (/home/username)
# - Shell de login funcional (/bin/bash, /bin/zsh, etc.)
# - Pueden usar sudo si están configurados
# - Sujetos a permisos y restricciones

# Ver usuarios regulares
awk -F: '$3 >= 1000 && $1 != "nobody" {print $1, $3, $6, $7}' /etc/passwd
```

## **Archivo /etc/passwd - Base de Datos de Usuarios**

### **Estructura de /etc/passwd**

```bash
# Ver contenido de /etc/passwd
cat /etc/passwd

# Formato: username:x:UID:GID:GECOS:home:shell
# Ejemplo: juan:x:1000:1000:Juan Pérez,,,:/home/juan:/bin/bash

# Campos explicados:
# 1. username  - Nombre del usuario
# 2. x         - Contraseña (almacenada en /etc/shadow)
# 3. UID       - User ID (identificador numérico)
# 4. GID       - Group ID primario
# 5. GECOS     - Información adicional (nombre completo, etc.)
# 6. home      - Directorio home del usuario
# 7. shell     - Shell de login
```

### **Análisis de Usuarios**

```bash
# Contar tipos de usuarios
echo "=== Análisis de Usuarios del Sistema ==="
echo "Total de usuarios: $(wc -l < /etc/passwd)"
echo "Usuarios del sistema (UID < 1000): $(awk -F: '$3 < 1000' /etc/passwd | wc -l)"
echo "Usuarios regulares (UID >= 1000): $(awk -F: '$3 >= 1000 && $1 != "nobody"' /etc/passwd | wc -l)"
echo

# Usuarios con shell de login
echo "Usuarios con shell de login:"
grep -E '/bin/(bash|zsh|fish|sh)$' /etc/passwd

# Usuarios sin shell (servicios)
echo "Usuarios de servicio (sin shell):"
grep -E '/(nologin|false)$' /etc/passwd | head -10
```

## **Identificadores de Usuario (UID) y Grupo (GID)**

### **Rangos de UID en Arch Linux**

```bash
# Verificar rangos de UID configurados
grep -E '^(UID_MIN|UID_MAX|SYS_UID_MIN|SYS_UID_MAX)' /etc/login.defs

# Rangos típicos en Arch Linux:
# 0         - root
# 1-999     - usuarios del sistema
# 1000+     - usuarios regulares

# Ver distribución de UIDs
echo "=== Distribución de UIDs ==="
awk -F: '{
    if ($3 == 0) system++
    else if ($3 < 1000) service++
    else regular++
}
END {
    print "Root (UID 0):", system+0
    print "Sistema (UID 1-999):", service+0  
    print "Regulares (UID 1000+):", regular+0
}' /etc/passwd
```

### **Gestión de UIDs**

```bash
# Encontrar próximo UID disponible
next_uid() {
    local max_uid=$(awk -F: '$3 >= 1000 && $3 < 65534 {print $3}' /etc/passwd | sort -n | tail -1)
    echo $((max_uid + 1))
}

# Verificar UID duplicados (problema de seguridad)
check_duplicate_uids() {
    echo "=== Verificación de UIDs Duplicados ==="
    awk -F: '{uid[$3]++; user[$3]=user[$3] $1 " "} END {
        for (u in uid) {
            if (uid[u] > 1) {
                print "UID " u " duplicado: " user[u]
            }
        }
    }' /etc/passwd
}

check_duplicate_uids
```

## **Grupos de Usuarios**

### **Archivo /etc/group**

```bash
# Ver grupos del sistema
cat /etc/group

# Formato: groupname:x:GID:members
# Ejemplo: wheel:x:998:juan,maria

# Grupos importantes en Arch Linux:
echo "=== Grupos Importantes ==="
grep -E '^(wheel|sudo|audio|video|optical|storage|network|users)' /etc/group
```

### **Grupos Especiales**

```bash
# wheel - Usuarios que pueden usar sudo
echo "Miembros del grupo wheel (sudo):"
getent group wheel

# audio - Acceso a dispositivos de audio  
echo "Miembros del grupo audio:"
getent group audio

# video - Acceso a dispositivos de video
echo "Miembros del grupo video:"
getent group video

# storage - Acceso a dispositivos de almacenamiento
echo "Miembros del grupo storage:"
getent group storage

# network - Gestión de red
echo "Miembros del grupo network:"
getent group network
```

## **Creación y Gestión de Usuarios**

### **Crear Usuarios**

```bash
# Crear usuario básico
sudo useradd -m -s /bin/bash nuevo_usuario

# Crear usuario con información completa
sudo useradd -m -s /bin/bash -c "Nombre Completo" -G wheel,audio,video nuevo_usuario

# Crear usuario del sistema (para servicios)
sudo useradd -r -s /usr/bin/nologin -d /var/lib/servicio usuario_servicio

# Establecer contraseña
sudo passwd nuevo_usuario

# Parámetros importantes de useradd:
# -m = crear directorio home
# -s = shell de login
# -c = comentario (nombre completo)
# -G = grupos suplementarios
# -r = usuario del sistema
# -d = directorio home personalizado
```

### **Modificar Usuarios**

```bash
# Cambiar shell del usuario
sudo usermod -s /bin/zsh usuario

# Agregar usuario a grupos
sudo usermod -a -G wheel,docker usuario

# Cambiar directorio home
sudo usermod -d /home/nuevo_home -m usuario

# Cambiar UID (cuidado con archivos existentes)
sudo usermod -u 1500 usuario

# Bloquear cuenta de usuario
sudo usermod -L usuario

# Desbloquear cuenta
sudo usermod -U usuario
```

### **Eliminar Usuarios**

```bash
# Eliminar usuario (mantener home)
sudo userdel usuario

# Eliminar usuario y directorio home
sudo userdel -r usuario

# Eliminar usuario y todos sus archivos
sudo userdel -r usuario
sudo find / -user usuario -delete 2>/dev/null
```

## **Información de Usuarios**

### **Comandos de Consulta**

```bash
# Información del usuario actual
whoami                   # Nombre del usuario
id                      # UID, GID y grupos
groups                  # Grupos del usuario actual
finger $USER            # Información detallada (si está instalado)

# Información de otro usuario
id usuario
groups usuario
getent passwd usuario   # Info de /etc/passwd
getent shadow usuario   # Info de /etc/shadow (solo root)

# Usuarios actualmente conectados
who                     # Usuarios con sesión
w                       # Usuarios con actividad detallada
last                    # Historial de logins
lastlog                 # Último login de cada usuario
```

### **Script de Auditoría de Usuarios**

```bash
#!/bin/bash
# Auditoría completa de usuarios del sistema

user_audit() {
    echo "=========================================="
    echo "AUDITORÍA DE USUARIOS - $(date)"
    echo "=========================================="
    
    # Estadísticas generales
    echo "=== ESTADÍSTICAS GENERALES ==="
    echo "Total de usuarios: $(wc -l < /etc/passwd)"
    echo "Usuarios con shell: $(grep -E '/bin/(bash|zsh|fish|sh)$' /etc/passwd | wc -l)"
    echo "Usuarios de sistema: $(awk -F: '$3 < 1000' /etc/passwd | wc -l)"
    echo "Usuarios regulares: $(awk -F: '$3 >= 1000 && $1 != "nobody"' /etc/passwd | wc -l)"
    echo
    
    # Usuarios con permisos especiales
    echo "=== USUARIOS CON PERMISOS ESPECIALES ==="
    echo "Miembros de wheel (sudo):"
    getent group wheel | cut -d: -f4 | tr ',' '\n' | sort
    echo
    
    # Usuarios sin contraseña (problema de seguridad)
    echo "=== VERIFICACIONES DE SEGURIDAD ==="
    local no_pass=$(sudo awk -F: '$2 == "" {print $1}' /etc/shadow 2>/dev/null)
    if [[ -n "$no_pass" ]]; then
        echo "⚠️ Usuarios sin contraseña:"
        echo "$no_pass"
    else
        echo "✅ Todos los usuarios tienen contraseña"
    fi
    echo
    
    # Usuarios con shell de login
    echo "=== USUARIOS CON ACCESO AL SISTEMA ==="
    awk -F: '$3 >= 1000 && $7 ~ /(bash|zsh|fish|sh)$/ {
        printf "%-15s UID:%-6s GID:%-6s Home:%-20s Shell:%s\n", $1, $3, $4, $6, $7
    }' /etc/passwd
    echo
    
    # Directorios home
    echo "=== VERIFICACIÓN DE DIRECTORIOS HOME ==="
    awk -F: '$3 >= 1000 && $6 != "" {
        printf "%-15s %s", $1, $6
        system("test -d " $6 " && echo \" ✅\" || echo \" ❌\"")
    }' /etc/passwd
    echo
    
    # Usuarios activos recientemente
    echo "=== ACTIVIDAD RECIENTE ==="
    echo "Últimos logins:"
    last -n 10 | grep -v "^$"
    
    echo "=========================================="
}

user_audit
```

## **Seguridad de Usuarios**

### **Mejores Prácticas**

```bash
# 1. Usar sudo en lugar de login directo como root
echo "Configuración sudo para usuario:"
echo "usuario ALL=(ALL:ALL) ALL" | sudo tee -a /etc/sudoers.d/usuario

# 2. Configurar política de contraseñas
sudo vim /etc/security/pwquality.conf
# minlen = 8
# dcredit = -1    # Al menos 1 dígito
# ucredit = -1    # Al menos 1 mayúscula
# lcredit = -1    # Al menos 1 minúscula
# ocredit = -1    # Al menos 1 carácter especial

# 3. Configurar expiración de contraseñas
sudo chage -M 90 usuario        # Expira en 90 días
sudo chage -W 7 usuario         # Avisar 7 días antes
sudo chage -I 30 usuario        # Cuenta inactiva después de 30 días

# 4. Deshabilitar usuarios innecesarios
for user in games news uucp; do
    sudo usermod -s /usr/bin/nologin $user 2>/dev/null
done
```

### **Monitoreo de Seguridad**

```bash
#!/bin/bash
# Monitor de seguridad de usuarios

security_check() {
    echo "=== VERIFICACIÓN DE SEGURIDAD DE USUARIOS ==="
    
    # Usuarios con UID 0 (solo debe ser root)
    echo "Usuarios con UID 0:"
    awk -F: '$3 == 0 {print $1}' /etc/passwd
    echo
    
    # Usuarios sin contraseña
    echo "Usuarios sin contraseña:"
    sudo awk -F: '$2 == "" {print $1}' /etc/shadow 2>/dev/null || echo "No se puede verificar"
    echo
    
    # Cuentas bloqueadas
    echo "Cuentas bloqueadas:"
    sudo awk -F: '$2 ~ /^!/ {print $1}' /etc/shadow 2>/dev/null | head -5
    echo
    
    # Usuarios con shell de login no estándar
    echo "Usuarios con shells no estándar:"
    awk -F: '$7 !~ /(bash|zsh|fish|sh|nologin|false)$/ && $7 != "" {print $1, $7}' /etc/passwd
    echo
    
    # Verificar permisos de archivos críticos
    echo "Permisos de archivos críticos:"
    ls -l /etc/passwd /etc/shadow /etc/group /etc/gshadow 2>/dev/null
    echo
    
    # Usuarios con directorios home con permisos incorrectos
    echo "Verificando permisos de directorios home:"
    for home in /home/*; do
        if [[ -d "$home" ]]; then
            perms=$(stat -c "%a" "$home")
            if [[ "$perms" != "755" && "$perms" != "750" && "$perms" != "700" ]]; then
                echo "⚠️ $home tiene permisos $perms (debería ser 755, 750, o 700)"
            fi
        fi
    done
}

security_check
```

## **Casos de Uso Prácticos**

### **Usuario para Aplicación Web**

```bash
# Crear usuario para servidor web
sudo useradd -r -s /usr/bin/nologin -d /var/www -c "Servidor Web" www-app

# Crear directorio de la aplicación
sudo mkdir -p /var/www/mi-app
sudo chown www-app:www-app /var/www/mi-app
sudo chmod 755 /var/www/mi-app

# Configurar servicio systemd para ejecutar como este usuario
sudo tee /etc/systemd/system/mi-app.service << 'EOF'
[Unit]
Description=Mi Aplicación Web
After=network.target

[Service]
Type=simple
User=www-app
Group=www-app
WorkingDirectory=/var/www/mi-app
ExecStart=/usr/bin/node server.js
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

### **Usuario de Backup**

```bash
# Crear usuario especializado para backups
sudo useradd -m -s /bin/bash -c "Usuario de Backup" -G storage backup

# Configurar directorio de backups
sudo mkdir -p /backup
sudo chown backup:backup /backup
sudo chmod 750 /backup

# Configurar clave SSH para backups automatizados
sudo -u backup ssh-keygen -t ed25519 -f /home/backup/.ssh/id_backup -N ""
sudo -u backup cat /home/backup/.ssh/id_backup.pub | sudo -u backup tee /home/backup/.ssh/authorized_keys

# Script de backup específico para este usuario
sudo -u backup tee /home/backup/backup_script.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backup/$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"
rsync -av /home/ "$BACKUP_DIR/home/"
rsync -av /etc/ "$BACKUP_DIR/etc/"
tar czf "$BACKUP_DIR/var_log.tar.gz" /var/log/
EOF

sudo chmod +x /home/backup/backup_script.sh
```

## **Troubleshooting de Usuarios**

### **Problemas Comunes**

```bash
# Usuario no puede hacer login
check_login_issues() {
    local username="$1"
    
    echo "=== Diagnóstico de problemas de login para $username ==="
    
    # Verificar que el usuario existe
    if ! getent passwd "$username" >/dev/null; then
        echo "❌ Usuario $username no existe"
        return 1
    fi
    
    # Verificar shell
    local shell=$(getent passwd "$username" | cut -d: -f7)
    echo "Shell configurado: $shell"
    if [[ ! -x "$shell" ]]; then
        echo "❌ Shell $shell no es ejecutable"
    fi
    
    # Verificar directorio home
    local home=$(getent passwd "$username" | cut -d: -f6)
    echo "Directorio home: $home"
    if [[ ! -d "$home" ]]; then
        echo "❌ Directorio home no existe"
    fi
    
    # Verificar estado de la cuenta
    local status=$(sudo passwd -S "$username" 2>/dev/null | awk '{print $2}')
    echo "Estado de la cuenta: $status"
    
    # Verificar grupos
    echo "Grupos: $(groups "$username")"
    
    # Verificar último login
    echo "Último login:"
    last "$username" | head -3
}

# check_login_issues "usuario_problema"
```

### **Recuperación de Acceso**

```bash
# Resetear contraseña de usuario
reset_user_password() {
    local username="$1"
    
    echo "Reseteando contraseña para $username..."
    sudo passwd "$username"
}

# Reparar directorio home
repair_home_directory() {
    local username="$1"
    local home_dir="/home/$username"
    
    echo "Reparando directorio home para $username..."
    
    # Crear directorio si no existe
    if [[ ! -d "$home_dir" ]]; then
        sudo mkdir -p "$home_dir"
    fi
    
    # Copiar archivos de skeleton
    sudo cp -r /etc/skel/. "$home_dir/"
    
    # Configurar propietario y permisos
    sudo chown -R "$username:$username" "$home_dir"
    sudo chmod 755 "$home_dir"
    
    echo "Directorio home reparado"
}

# repair_home_directory "usuario"
```

## **Enlaces Relacionados**

- **[[Usuario root]]** - Información detallada sobre el superusuario
- **[[sudo]]** - Ejecutar comandos con privilegios elevados
- **[[passwd]]** - Gestionar contraseñas de usuarios
- **[[su]]** - Cambiar de usuario
- **[[id]]** - Ver información de usuario actual
- **[[groups]]** - Ver grupos de un usuario
- **[[chmod]]** - Cambiar permisos de archivos
- **[[chown]]** - Cambiar propietario de archivos