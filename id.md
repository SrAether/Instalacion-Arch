# id - Información Completa del Usuario

El comando `id` es tu **tarjeta de identidad digital**. Mientras que [[whoami]] te dice simplemente quién eres, `id` te proporciona tu **credencial completa**: tu ID de usuario, grupos a los que perteneces, y todos los privilegios asociados.

## **¿Qué es id?**

Es un comando que muestra:
- **UID** (User ID): Tu número de identificación único
- **GID** (Group ID): Tu grupo principal
- **Grupos secundarios**: Todos los grupos a los que perteneces
- **Contexto de seguridad**: En sistemas con SELinux/AppArmor

## **¿Por qué es Importante en Arch Linux?**

Durante la configuración post-instalación, `id` te ayuda a verificar que tu usuario tiene los permisos correctos y pertenece a los grupos necesarios para usar el sistema efectivamente.

### **Uso Básico**

```bash
# Mostrar información del usuario actual
id

# Salida típica:
# uid=1000(usuario) gid=1000(usuario) groups=1000(usuario),998(wheel),108(audio),997(video)

# Información de otro usuario
id root
id nombreusuario
```

### **Opciones Específicas**

```bash
# Solo el UID numérico
id -u

# Solo el nombre de usuario
id -un

# Solo el GID del grupo principal
id -g

# Solo el nombre del grupo principal
id -gn

# Solo los grupos secundarios
id -G

# Solo los nombres de los grupos
id -Gn
```

## **Interpretando la Salida**

```bash
id
uid=1000(aether) gid=1000(aether) groups=1000(aether),998(wheel),108(audio),997(video),96(storage)
```

**Desglose:**
- **uid=1000(aether)**: Usuario ID 1000, nombre "aether"
- **gid=1000(aether)**: Grupo principal ID 1000, nombre "aether"
- **groups=**: Lista de todos los grupos:
  - `1000(aether)`: Grupo personal
  - `998(wheel)`: Puede usar [[Sudo]]
  - `108(audio)`: Acceso a dispositivos de audio
  - `997(video)`: Acceso a dispositivos de video
  - `96(storage)`: Acceso a dispositivos de almacenamiento

## **Grupos Importantes en Arch Linux**

| Grupo | Propósito | ¿Por qué lo necesitas? |
|-------|-----------|------------------------|
| **wheel** | Acceso a sudo | Ejecutar comandos como root |
| **audio** | Dispositivos de sonido | Reproducir/grabar audio |
| **video** | Dispositivos de video | Usar cámara, aceleración gráfica |
| **storage** | Dispositivos de almacenamiento | Montar USBs, discos externos |
| **optical** | Unidades ópticas | Usar CD/DVD |
| **network** | Configuración de red | NetworkManager, etc. |
| **power** | Gestión de energía | Suspender, hibernar |

## **Uso en Scripts y Automatización**

```bash
# Verificar si eres root
if [[ $(id -u) -eq 0 ]]; then
    echo "Ejecutándose como root"
else
    echo "Usuario normal"
fi

# Verificar pertenencia a grupo wheel
if id -nG | grep -q wheel; then
    echo "Puedes usar sudo"
else
    echo "No puedes usar sudo"
fi

# Obtener solo el nombre de usuario
current_user=$(id -un)
echo "Usuario actual: $current_user"
```

## **Verificaciones Post-Instalación**

### **Después de Crear Usuario ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Verificar que el usuario fue creado correctamente
id miusuario

# Verificar grupos asignados
id -Gn miusuario

# Verificar acceso a sudo
id -nG miusuario | grep wheel
```

### **Diagnóstico de Permisos**

```bash
# Si no puedes reproducir audio
id -Gn | grep audio

# Si no puedes usar dispositivos USB
id -Gn | grep storage

# Si sudo no funciona
id -Gn | grep wheel
```

## **Diferencias con Comandos Relacionados**

| Comando | Información | Cuándo Usar |
|---------|-------------|-------------|
| **[[whoami]]** | Solo nombre de usuario | Verificación rápida |
| **id** | Información completa | Diagnóstico de permisos |
| **[[groups]]** | Solo grupos | Ver membresías de grupo |
| **getent passwd** | Info del /etc/passwd | Detalles de cuenta |

## **Casos de Uso Prácticos**

### **Verificar Configuración de Usuario**

```bash
# Usuario recién creado debe tener:
id nuevousuario
# uid=1000+ (no 0), gid=1000+, groups incluye wheel
```

### **Debugging de Permisos**

```bash
# Si un programa no funciona, verificar grupos
id -Gn
# Comparar con requisitos del programa
```

### **Auditoría de Seguridad**

```bash
# Verificar que usuarios normales no son root
for user in $(cut -d: -f1 /etc/passwd); do
    if [[ $(id -u "$user" 2>/dev/null) -eq 0 ]] && [[ "$user" != "root" ]]; then
        echo "ALERTA: $user tiene UID 0"
    fi
done
```

## **Información del Sistema de IDs**

### **Rangos de UID/GID en Arch Linux**

- **0**: root (superusuario)
- **1-99**: Usuarios del sistema (daemons, etc.)
- **100-999**: Usuarios de sistema dinámicos
- **1000+**: Usuarios normales

### **Verificar Rangos**

```bash
# Ver configuración de rangos
grep -E '^UID_|^GID_' /etc/login.defs
```

## **Troubleshooting Común**

### **"Permission denied" con dispositivos**
```bash
# Verificar grupos necesarios
id -Gn
# Agregar usuario a grupo faltante:
# sudo usermod -a -G audio,video,storage usuario
```

### **sudo no funciona**
```bash
# Verificar membresía en wheel
id -Gn | grep wheel
# Si no está: sudo usermod -a -G wheel usuario
```

### **Problemas de acceso a archivos**
```bash
# Verificar propietario de archivos problemáticos
ls -la archivo_problematico
# Comparar con tu id
id -u
```

## **Enlaces Relacionados**

- [[whoami]] - Verificación rápida de identidad
- [[groups]] - Ver solo grupos del usuario
- [[Sudo]] - Configurar privilegios elevados que id puede verificar
- [[Usuario root]] - Entender diferencias de permisos que id revela