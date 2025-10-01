# groups - Ver Grupos del Usuario

El comando `groups` es tu **lista de membresías**. Te muestra todos los grupos a los que perteneces, que son como "clubes de permisos" que determinan qué puedes y no puedes hacer en el sistema.

## **¿Qué es groups?**

Es un comando simple que muestra:
- **Todos los grupos** a los que pertenece un usuario
- **Grupo principal** (el primero en la lista)
- **Grupos secundarios** (el resto)

## **¿Por qué es Importante en Arch Linux?**

Los grupos controlan el acceso a recursos del sistema. Saber a qué grupos perteneces te ayuda a entender por qué puedes o no puedes realizar ciertas acciones.

### **Uso Básico**

```bash
# Ver tus grupos
groups

# Salida típica:
# aether wheel audio video storage optical

# Ver grupos de otro usuario
groups root
groups nombreusuario
```

### **Interpretando la Salida**

```bash
groups
# aether wheel audio video storage optical power
```

- **Primer grupo (aether)**: Tu grupo principal
- **wheel**: Puedes usar [[Sudo]]
- **audio**: Acceso a dispositivos de sonido
- **video**: Acceso a dispositivos de video/gráficos
- **storage**: Puedes montar dispositivos USB
- **optical**: Acceso a CD/DVD
- **power**: Gestión de energía (suspender/hibernar)

## **Grupos Críticos en Arch Linux**

### **Grupos de Permisos Administrativos**

| Grupo | Función | Ejemplo de Uso |
|-------|---------|----------------|
| **wheel** | sudo/su access | `sudo pacman -S firefox` |
| **adm** | Logs del sistema | Ver `/var/log/` |
| **sys** | Dispositivos de sistema | Acceso a `/dev/` especiales |

### **Grupos de Hardware**

| Grupo | Hardware | Ejemplo de Uso |
|-------|----------|----------------|
| **audio** | Tarjetas de sonido | Reproducir música |
| **video** | Tarjetas gráficas | Aceleración 3D, cámara |
| **input** | Dispositivos de entrada | Teclados especiales |
| **storage** | Discos y USBs | Montar pendrive |
| **optical** | CD/DVD/Blu-ray | Reproducir/grabar discos |
| **scanner** | Escáneres | Usar SANE |
| **lp** | Impresoras | Imprimir documentos |

### **Grupos de Red**

| Grupo | Función | Ejemplo de Uso |
|-------|---------|----------------|
| **network** | NetworkManager | Configurar Wi-Fi |
| **wireshark** | Captura de paquetes | Análisis de red |

## **Verificaciones Post-Instalación**

### **Usuario Recién Creado**

```bash
# Verificar que tienes los grupos básicos
groups

# Deberías ver al menos:
# - tu_usuario (grupo principal)
# - wheel (para sudo)
# - audio (para sonido)
# - video (para gráficos)
# - storage (para USBs)
```

### **Diagnóstico de Problemas**

```bash
# No puedes reproducir audio
groups | grep audio
# Si no aparece: sudo usermod -a -G audio tu_usuario

# No puedes usar sudo
groups | grep wheel  
# Si no aparece: sudo usermod -a -G wheel tu_usuario

# No puedes montar USBs
groups | grep storage
# Si no aparece: sudo usermod -a -G storage tu_usuario
```

## **Gestión de Grupos**

### **Agregar Usuario a Grupos (como root)**

```bash
# Agregar a un grupo (manteniendo otros)
sudo usermod -a -G nuevo_grupo usuario

# Agregar a múltiples grupos
sudo usermod -a -G audio,video,storage usuario

# ⚠️ NUNCA hagas esto (sobrescribe todos los grupos):
# usermod -G nuevo_grupo usuario
```

### **Crear y Eliminar Grupos**

```bash
# Crear grupo personalizado
sudo groupadd mi_grupo

# Eliminar grupo
sudo groupdel mi_grupo

# Ver todos los grupos del sistema
cat /etc/group
```

## **Diferencias con Comandos Relacionados**

| Comando | Información | Salida |
|---------|-------------|--------|
| **groups** | Solo grupos | `aether wheel audio video` |
| **[[id]]** | Completa con UIDs | `uid=1000(aether) gid=1000(aether) groups=...` |
| **[[whoami]]** | Solo usuario | `aether` |

## **Uso en Scripts**

```bash
# Verificar pertenencia a grupo específico
if groups | grep -q wheel; then
    echo "Puedes usar sudo"
else
    echo "No tienes permisos de sudo"
fi

# Verificar múltiples grupos
required_groups="audio video storage"
for group in $required_groups; do
    if ! groups | grep -q "$group"; then
        echo "Falta grupo: $group"
    fi
done
```

## **Contexto en el Curso**

### **Durante Configuración de Usuario ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Después de crear usuario, verificar grupos
groups nuevo_usuario

# Agregar a grupos esenciales
usermod -a -G wheel,audio,video,storage nuevo_usuario
```

### **Troubleshooting Post-Instalación**

```bash
# Si algo no funciona, verificar grupos primero
groups
```

## **Grupos Específicos de Arch Linux**

### **Grupos del Sistema**

```bash
# Ver grupos de servicios del sistema
groups systemd-network
groups systemd-resolve

# Grupos de servidores web
groups http  # nginx/apache
```

### **Grupos de Desarrollo**

```bash
# Para desarrollo
groups | grep -E 'docker|libvirt|vboxusers'
```

## **Troubleshooting Común**

### **"Permission denied" al acceder a dispositivos**
```bash
# Verificar grupos de hardware
groups | grep -E 'audio|video|storage|input'
```

### **sudo pide contraseña constantemente**
```bash
# Verificar que estás en wheel
groups | grep wheel
```

### **No puedes configurar red**
```bash
# Verificar acceso a NetworkManager
groups | grep network
```

### **Aplicaciones gráficas no funcionan**
```bash
# Verificar acceso a video
groups | grep video
```

## **Diferencias entre Sesiones**

```bash
# Los cambios de grupo requieren logout/login
# Verificar grupos activos en la sesión actual:
groups

# vs grupos según /etc/group:
getent group | grep tu_usuario
```

## **Enlaces Relacionados**

- [[id]] - Información más detallada incluyendo UIDs y GIDs
- [[whoami]] - Identificación básica de usuario
- [[Sudo]] - Configurar acceso que el grupo wheel proporciona
- [[Usuario root]] - Entender diferencias de permisos entre usuarios y grupos