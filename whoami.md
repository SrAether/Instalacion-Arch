El comando `whoami` es tu **identificador de usuario**. Es un comando simple pero fundamental que te dice exactamente qué usuario estás ejecutando en el sistema. Es especialmente útil durante la instalación de Arch Linux para verificar permisos, cambios de usuario y configuración del sistema.

---
### ¿Qué Hace WHOAMI? 👤
**WHOAMI** significa **"Who Am I"** (¿Quién soy yo?). Su función es:
1. **Mostrar** el nombre del usuario actual
2. **Verificar** identidad en el sistema
3. **Confirmar** cambios de usuario (su, sudo)
4. **Debuggear** problemas de permisos

### Sintaxis Básica
```bash
whoami
```

### Uso Básico
```bash
# Mostrar usuario actual
whoami

# En usuario normal
$ whoami
usuario

# Como root
# whoami
root

# Después de sudo
$ sudo whoami
root
```

### En el Contexto de la Instalación de Arch Linux

#### **Durante la Instalación desde ISO**
```bash
# Al arrancar desde ISO de Arch
whoami
# Respuesta: root

# Confirmar que tienes permisos de administrador
whoami
# Esto es importante porque necesitas root para particionar, formatear, etc.
```

#### **Después de Crear Usuario**
```bash
# Verificar que estás en el usuario correcto
whoami
# Respuesta: tu_usuario

# Cambiar a root y verificar
su -
whoami
# Respuesta: root

# Verificar sudo funciona
sudo whoami
# Respuesta: root
```

#### **En Scripts de Instalación**
```bash
# Verificar permisos antes de operaciones críticas
if [[ $(whoami) != "root" ]]; then
    echo "Este script necesita permisos de root"
    exit 1
fi

# Proceder con comandos que requieren root
pacstrap /mnt base linux linux-firmware
```

#### **Post-Instalación: Verificar Configuración**
```bash
# Verificar que no estás siempre como root
whoami
# Debería ser tu usuario normal

# Verificar que sudo está configurado
sudo whoami
# Debería responder: root
```

### Casos de Uso Específicos

#### **1. Verificar Cambios de Usuario**
```bash
# Usuario normal
$ whoami
usuario

# Cambiar a root
$ su -
Password: 
# whoami
root

# Volver a usuario normal
# exit
$ whoami
usuario
```

#### **2. En Scripts de Automatización**
```bash
#!/bin/bash
# install_aur_package.sh

# No debe ejecutarse como root
if [[ $(whoami) == "root" ]]; then
    echo "ERROR: No ejecutes este script como root"
    echo "Los paquetes AUR deben instalarse como usuario normal"
    exit 1
fi

# Proceder con instalación de AUR
cd /tmp
git clone https://aur.archlinux.org/paquete.git
cd paquete
makepkg -si
```

#### **3. Verificar Configuración de Sudo**
```bash
# Verificar que sudo funciona
sudo whoami

# Si responde "root", sudo está configurado correctamente
# Si pide contraseña infinitamente o da error, revisar /etc/sudoers
```

#### **4. Debug de Problemas de Permisos**
```bash
# Verificar usuario antes de operación
echo "Usuario actual: $(whoami)"
echo "Intentando acceder a archivo:"
cat /etc/shadow

# Si falla, verificar si necesitas sudo
sudo cat /etc/shadow
echo "Después de sudo: $(whoami)"  # Esto no cambia, sigue siendo tu usuario
```

### Comparación con Comandos Relacionados

#### **WHOAMI vs WHO**
```bash
# whoami: solo tu usuario
$ whoami
usuario

# who: todos los usuarios conectados
$ who
usuario  pts/0        2024-01-15 10:30 (192.168.1.100)
root     tty1         2024-01-15 09:15
```

#### **WHOAMI vs ID**
```bash
# whoami: solo nombre de usuario
$ whoami
usuario

# id: información completa de usuario
$ id
uid=1000(usuario) gid=1000(usuario) groups=1000(usuario),998(wheel),987(audio),984(video)
```

#### **WHOAMI vs $USER**
```bash
# whoami: comando que consulta al sistema
$ whoami
usuario

# $USER: variable de entorno (puede ser modificada)
$ echo $USER
usuario

# $USER puede ser diferente de whoami en algunos casos
$ USER="falso"
$ echo $USER
falso
$ whoami
usuario  # Sigue siendo el real
```

### Scripts Útiles con WHOAMI

#### **Verificador de Permisos**
```bash
#!/bin/bash
# check_permissions.sh

current_user=$(whoami)

echo "=== Verificación de Permisos ==="
echo "Usuario actual: $current_user"

# Verificar si es root
if [[ $current_user == "root" ]]; then
    echo "✅ Tienes permisos de administrador"
    echo "⚠️  Cuidado: estás ejecutando como root"
else
    echo "📝 Usuario normal: $current_user"
    
    # Verificar sudo
    if sudo -n whoami 2>/dev/null; then
        echo "✅ Sudo configurado (sin contraseña)"
    elif sudo whoami 2>/dev/null; then
        echo "✅ Sudo configurado (con contraseña)"
    else
        echo "❌ Sudo no configurado"
    fi
fi

# Verificar grupos importantes
echo -e "\n=== Grupos del Usuario ==="
groups $current_user | tr ' ' '\n' | grep -E '^(wheel|sudo|admin|audio|video|storage|optical|network)$' | while read group; do
    echo "✅ Grupo: $group"
done
```

#### **Script de Instalación Segura**
```bash
#!/bin/bash
# safe_install.sh

# Verificar que no se ejecuta como root
if [[ $(whoami) == "root" ]]; then
    echo "❌ Este script no debe ejecutarse como root"
    echo "💡 Ejecuta: su tu_usuario"
    echo "💡 Luego: ./safe_install.sh"
    exit 1
fi

echo "✅ Ejecutando como usuario: $(whoami)"

# Operaciones que requieren usuario normal
echo "Clonando repositorio..."
git clone https://github.com/usuario/proyecto.git

# Operaciones que requieren sudo
echo "Instalando dependencias (se pedirá contraseña)..."
sudo pacman -S git base-devel

echo "✅ Instalación completada como usuario: $(whoami)"
```

#### **Monitor de Cambios de Usuario**
```bash
#!/bin/bash
# user_monitor.sh

echo "Monitoreando cambios de usuario..."
echo "Usuario inicial: $(whoami)"

current_user=$(whoami)

while true; do
    new_user=$(whoami)
    
    if [[ $new_user != $current_user ]]; then
        echo "$(date): Usuario cambió de '$current_user' a '$new_user'"
        current_user=$new_user
    fi
    
    sleep 1
done
```

### Casos Especiales y Troubleshooting

#### **Problema: Siempre Root Después de Instalación**
```bash
# Verificar usuario actual
whoami
# Si siempre responde "root", tienes problema

# Verificar si tienes usuario normal creado
cat /etc/passwd | grep "/home"

# Si no existe, crear usuario
useradd -m -G wheel tu_usuario
passwd tu_usuario

# Cambiar a usuario normal
su - tu_usuario
whoami  # Ahora debería responder tu_usuario
```

#### **Problema: Sudo No Funciona**
```bash
# Verificar usuario actual
whoami

# Intentar sudo
sudo whoami

# Si falla, verificar configuración
su -  # Cambiar a root
visudo  # Editar configuración sudo

# Asegurar que línea está descomentada:
# %wheel ALL=(ALL) ALL

# Verificar que usuario está en grupo wheel
usermod -aG wheel tu_usuario
```

#### **Problema: Usuario Incorrecto en Scripts**
```bash
# Variable $USER puede estar mal
echo "Variable USER: $USER"
echo "Comando whoami: $(whoami)"

# Si son diferentes, limpiar entorno
unset USER
export USER=$(whoami)

# O mejor, usar whoami directamente en scripts
current_user=$(whoami)
```

### Integración con Otros Comandos

#### **Con SU (Switch User)**
```bash
# Antes de cambiar
$ whoami
usuario

# Cambiar a root
$ su -
Password:
# whoami
root

# Cambiar a otro usuario específico
# su - otrousuario
$ whoami
otrousuario
```

#### **Con SUDO**
```bash
# Tu usuario no cambia con sudo
$ whoami
usuario

# Comando ejecutado como root, pero tu sesión sigue igual
$ sudo whoami
root

$ whoami  # Sigue siendo tu usuario
usuario
```

#### **Con SYSTEMCTL y Servicios**
```bash
# Verificar usuario antes de operaciones de sistema
echo "Configurando servicio como usuario: $(whoami)"

if [[ $(whoami) != "root" ]]; then
    echo "Usando sudo para operaciones de sistema..."
    sudo systemctl enable servicio
else
    echo "Ejecutando directamente como root..."
    systemctl enable servicio
fi
```

### En Chroot Durante Instalación

#### **Verificar Usuario en Chroot**
```bash
# Desde ISO (antes de chroot)
whoami
# Respuesta: root

# Después de arch-chroot /mnt
arch-chroot /mnt
whoami
# Respuesta: root (pero en el nuevo sistema)

# Verificar que estás en el sistema correcto
pwd
# Respuesta: /

ls /home
# Debería mostrar directorios de usuarios del nuevo sistema
```

### Variables Relacionadas y Alternativas

#### **Variables de Entorno de Usuario**
```bash
# Comando whoami
whoami

# Variables relacionadas
echo $USER       # Nombre de usuario
echo $HOME       # Directorio home
echo $UID        # ID numérico del usuario
echo $LOGNAME    # Nombre de login

# ID completo del usuario
id -un           # Equivalente a whoami
id -u            # Solo UID numérico
```

#### **Información Extendida de Usuario**
```bash
# Información básica
whoami

# Información completa
id

# Información del sistema de usuarios
getent passwd $(whoami)

# Grupos del usuario
groups $(whoami)

# Último login
last $(whoami) | head -5
```

### Buenas Prácticas

#### **1. Verificar Usuario en Scripts Críticos**
```bash
#!/bin/bash
# Siempre verificar usuario al inicio de scripts importantes

current_user=$(whoami)
echo "Ejecutando como: $current_user"

# Parar si no es el usuario correcto
if [[ $current_user == "root" ]] && [[ $ALLOW_ROOT != "yes" ]]; then
    echo "❌ No ejecutar como root"
    exit 1
fi
```

#### **2. Usar en Logs para Trazabilidad**
```bash
# Incluir usuario en logs
echo "$(date): Usuario $(whoami) ejecutó operación X" >> ~/mi_log.txt

# En scripts de sistema
logger "Usuario $(whoami) ejecutó script de instalación"
```

#### **3. Verificar Antes de Operaciones Peligrosas**
```bash
# Antes de eliminar archivos importantes
if [[ $(whoami) == "root" ]]; then
    echo "⚠️  Ejecutando como root - confirmar operación"
    read -p "¿Continuar? (yes/no): " confirm
    [[ $confirm == "yes" ]] || exit 1
fi
```

#### **4. Combinar con Verificación de Directorio**
```bash
# Verificar usuario Y ubicación
echo "Usuario: $(whoami)"
echo "Directorio: $(pwd)"

# Asegurar que no estás en sistema como usuario equivocado
if [[ $(whoami) == "root" ]] && [[ $(pwd) == "/home/"* ]]; then
    echo "⚠️  Root en directorio de usuario - verificar"
fi
```

---
### Enlaces Relacionados
- [[Usuario root]] - Configuración y gestión del usuario root
- [[Sudo]] - Configurar sudo para ejecutar comandos como root
- [[su]] - Cambiar de usuario temporalmente
- [[id]] - Ver información completa del usuario actual
- [[groups]] - Ver grupos del usuario actual
- [[passwd]] - Cambiar contraseña del usuario



