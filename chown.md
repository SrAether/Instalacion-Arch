# chown - Cambiar Propietario y Grupo

El comando `chown` (Change Owner) es tu **administrador de propiedades**. Te permite cambiar el propietario y grupo de archivos y directorios, estableciendo quién tiene control y acceso a los recursos del sistema.

## **¿Qué es chown?**

`chown` te permite modificar:
- **Propietario** (owner) de archivos y directorios
- **Grupo** (group) de archivos y directorios  
- **Ambos simultáneamente** en una sola operación

## **¿Por qué es Crucial en Arch Linux?**

Durante la instalación y configuración del sistema, necesitarás transferir la propiedad de archivos entre usuarios, corregir propiedades incorrectas y establecer estructuras de permisos apropiadas.

### **Sintaxis Básica**

```bash
# Cambiar solo propietario
chown nuevo_usuario archivo

# Cambiar propietario y grupo
chown nuevo_usuario:nuevo_grupo archivo

# Cambiar solo grupo (usando dos puntos)
chown :nuevo_grupo archivo

# Usar ID numéricos en lugar de nombres
chown 1000:1000 archivo
```

### **Ejemplos Básicos**

```bash
# Cambiar propietario de un archivo
sudo chown juan documento.txt

# Cambiar propietario y grupo
sudo chown juan:users documento.txt

# Cambiar solo grupo manteniendo propietario
sudo chown :wheel script.sh

# Usar punto en lugar de dos puntos (sintaxis alternativa)
sudo chown juan.users documento.txt
```

## **Operaciones Recursivas**

### **Cambiar Propiedades de Directorios**

```bash
# Cambiar recursivamente todo un directorio
sudo chown -R usuario:grupo /home/usuario/

# Cambiar solo archivos en directorio
sudo find /var/www -type f -exec chown www-data:www-data {} \;

# Cambiar solo directorios
sudo find /var/www -type d -exec chown www-data:www-data {} \;
```

### **Preservar Enlaces Simbólicos**

```bash
# No seguir enlaces simbólicos (cambiar el enlace, no el destino)
sudo chown -h usuario:grupo enlace_simbolico

# Cambiar recursivamente sin seguir enlaces
sudo chown -R --no-dereference usuario:grupo directorio/
```

## **Casos de Uso en Arch Linux**

### **Durante la Instalación ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Configurar propietario del directorio home del usuario
sudo chown -R usuario:usuario /home/usuario

# Configurar archivos de configuración del usuario
sudo chown usuario:usuario /home/usuario/.bashrc
sudo chown usuario:usuario /home/usuario/.profile
```

### **Post-Instalación**

```bash
# Transferir archivos de root a usuario
sudo chown -R $USER:$USER ~/Documentos/

# Configurar directorio de proyectos
sudo chown -R desarrollador:desarrolladores /opt/proyectos/

# Configurar servidor web
sudo chown -R www-data:www-data /var/www/html/
```

### **Servicios del Sistema**

```bash
# Configurar logs de aplicación
sudo chown syslog:adm /var/log/mi_aplicacion.log

# Configurar archivos de base de datos
sudo chown postgres:postgres /var/lib/postgresql/

# Configurar directorio de mail
sudo chown mail:mail /var/spool/mail/
```

## **Trabajando con IDs Numéricos**

### **Usar UIDs y GIDs**

```bash
# Ver IDs de usuario y grupo
id usuario
# uid=1000(usuario) gid=1000(usuario) groups=1000(usuario),998(wheel)

# Cambiar usando números
sudo chown 1000:1000 archivo.txt

# Útil cuando el usuario no existe en el sistema actual
sudo chown 1001:1001 archivo_respaldo.txt
```

### **Verificar Cambios**

```bash
# Ver propiedades antes del cambio
ls -la archivo.txt
# -rw-r--r-- 1 root root 1234 Oct 30 12:00 archivo.txt

# Cambiar propietario
sudo chown usuario:users archivo.txt

# Verificar después del cambio
ls -la archivo.txt
# -rw-r--r-- 1 usuario users 1234 Oct 30 12:00 archivo.txt
```

## **Casos Especiales y Avanzados**

### **Copiar Propiedades de Otro Archivo**

```bash
# Usar archivo de referencia
sudo chown --reference=archivo_modelo archivo_destino

# Útil para mantener consistencia
sudo chown --reference=/home/usuario/.bashrc /home/usuario/.profile
```

### **Cambios Condicionales**

```bash
# Cambiar solo si el propietario actual es específico
sudo chown --from=root usuario archivo.txt

# Cambiar solo archivos de cierto propietario
sudo find /tmp -user root -exec chown usuario:users {} \;
```

## **Resolución de Problemas Comunes**

### **Archivos sin Propietario**

```bash
# Encontrar archivos "huérfanos" (sin propietario válido)
sudo find / -nouser -ls 2>/dev/null

# Asignar propietario a archivos huérfanos
sudo find /home -nouser -exec chown usuario:usuario {} \;
```

### **Permisos Denegados**

```bash
# Error: Operation not permitted
# Verificar que tienes permisos de sudo
sudo chown usuario archivo.txt

# Verificar que no esté montado como solo lectura
mount | grep "ro"

# Verificar atributos especiales del archivo
lsattr archivo.txt
```

### **Corrección de Propiedades en Home**

```bash
# Típico problema: archivos de root en directorio de usuario
# Verificar archivos problemáticos
find /home/usuario -not -user usuario -ls

# Corregir propiedades
sudo chown -R usuario:usuario /home/usuario/

# Excluir directorios específicos
sudo find /home/usuario -not -path "/home/usuario/.cache*" -exec chown usuario:usuario {} \;
```

## **Integración con Otros Comandos**

### **Con [[chmod]]**

```bash
# Cambiar propietario y permisos juntos
sudo chown usuario:grupo archivo.txt
sudo chmod 644 archivo.txt

# En una sola línea
sudo chown usuario:grupo archivo.txt && sudo chmod 644 archivo.txt
```

### **Con [[find]]**

```bash
# Buscar y cambiar propietario por tipo de archivo
sudo find /var/www -name "*.php" -exec chown www-data:www-data {} \;

# Cambiar propietario de archivos grandes
sudo find /home -size +100M -exec chown usuario:usuario {} \;
```

### **Con [[rsync]]**

```bash
# Mantener propiedades durante copia
rsync -a --chown=usuario:grupo origen/ destino/

# Cambiar propietario durante sincronización
sudo rsync -a --chown=www-data:www-data /home/usuario/web/ /var/www/html/
```

## **Scripts de Automatización**

### **Script de Configuración de Usuario**

```bash
#!/bin/bash
# Configurar propiedades correctas para nuevo usuario

USUARIO="$1"
if [ -z "$USUARIO" ]; then
    echo "Uso: $0 nombre_usuario"
    exit 1
fi

# Verificar que el usuario existe
if ! id "$USUARIO" &>/dev/null; then
    echo "Usuario $USUARIO no existe"
    exit 1
fi

# Configurar directorio home
sudo chown -R "$USUARIO:$USUARIO" "/home/$USUARIO"

# Configurar archivos de configuración específicos
config_files=(".bashrc" ".profile" ".vimrc" ".ssh")
for file in "${config_files[@]}"; do
    if [ -e "/home/$USUARIO/$file" ]; then
        sudo chown -R "$USUARIO:$USUARIO" "/home/$USUARIO/$file"
    fi
done

echo "Propiedades configuradas para $USUARIO"
```

### **Auditoría de Propiedades**

```bash
#!/bin/bash
# Verificar archivos con propiedades incorrectas

echo "=== Archivos de usuario con propietario root ==="
find /home -user root -ls 2>/dev/null

echo -e "\n=== Archivos sin propietario válido ==="
find /home -nouser -ls 2>/dev/null

echo -e "\n=== Archivos sin grupo válido ==="
find /home -nogroup -ls 2>/dev/null
```

## **Seguridad y Buenas Prácticas**

### **Principios de Seguridad**

```bash
# ✅ Buenas prácticas
# Usar chown con sudo para cambios administrativos
sudo chown root:root /etc/importante.conf

# Verificar cambios críticos
sudo chown root:root /usr/bin/sudo && ls -la /usr/bin/sudo

# ❌ Evitar
# No cambiar propietario de archivos del sistema sin razón
# No hacer chown 777 (confundir con chmod)
```

### **Archivos Críticos del Sistema**

```bash
# Verificar propiedades de archivos críticos
ls -la /etc/passwd    # root:root
ls -la /etc/shadow    # root:shadow  
ls -la /usr/bin/sudo  # root:root
ls -la /etc/sudoers   # root:root
```

## **Casos de Uso Específicos**

### **Configuración de Servidor Web**

```bash
# Apache/Nginx
sudo chown -R www-data:www-data /var/www/html/
sudo chown -R www-data:www-data /var/log/apache2/

# Permisos específicos para uploads
sudo chown www-data:www-data /var/www/html/uploads/
sudo chmod 755 /var/www/html/uploads/
```

### **Base de Datos**

```bash
# PostgreSQL
sudo chown -R postgres:postgres /var/lib/postgresql/
sudo chown postgres:postgres /var/log/postgresql/

# MySQL/MariaDB
sudo chown -R mysql:mysql /var/lib/mysql/
sudo chown mysql:mysql /var/log/mysql/
```

### **Servicios Personalizados**

```bash
# Crear usuario de servicio y configurar archivos
sudo useradd -r -s /bin/false mi_servicio
sudo chown -R mi_servicio:mi_servicio /opt/mi_aplicacion/
sudo chown mi_servicio:mi_servicio /var/log/mi_aplicacion.log
```

## **Troubleshooting Avanzado**

### **Problemas con Montajes**

```bash
# Verificar si el filesystem soporta cambios de propietario
mount | grep /directorio

# Algunos filesystems (FAT32, NTFS) no soportan chown Unix
# Usar opciones de montaje específicas
sudo mount -o uid=1000,gid=1000 /dev/sdX /mnt/punto
```

### **Restaurar Propiedades**

```bash
# Backup de propiedades antes de cambios masivos
find /directorio -printf "%M %u %g %p\n" > propiedades_backup.txt

# Script para restaurar (ejemplo básico)
while read perms user group file; do
    sudo chown "$user:$group" "$file"
done < propiedades_backup.txt
```

## **Enlaces Relacionados**

- [[chmod]] - Cambiar permisos (complementa chown)
- [[ls]] - Ver propietarios actuales antes de cambiarlos
- [[id]] - Verificar usuario y grupo actuales
- [[groups]] - Ver grupos disponibles para asignar
- [[find]] - Buscar archivos para cambiar propietario en lote
- [[stat]] - Ver información detallada de propietario y grupo