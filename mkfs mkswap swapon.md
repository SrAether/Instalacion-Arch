# **mkfs - Creación de Sistemas de Archivos**

## **¿Qué es mkfs?**

`mkfs` (make file system) es una familia de comandos utilizados para crear sistemas de archivos en particiones de disco. Es el paso que sigue después del particionado con herramientas como `cfdisk` o `fdisk`, y es esencial en la instalación de Arch Linux para preparar las particiones donde se instalará el sistema.

En Arch Linux, mkfs es fundamental para formatear la partición EFI (FAT32), la partición raíz (ext4 o Btrfs), y cualquier partición adicional como `/home` antes de proceder con la instalación del sistema.

## **Sintaxis Básica**

```bash
mkfs.<tipo> [opciones] dispositivo
```

### **Tipos de Sistema de Archivos Comunes**

- **mkfs.fat**: Para sistemas de archivos FAT (EFI, USB)
- **mkfs.ext4**: Para sistema de archivos ext4 (Linux tradicional)
- **mkfs.btrfs**: Para sistema de archivos Btrfs (moderno)
- **mkfs.xfs**: Para sistema de archivos XFS (alta performance)
- **mkfs.ntfs**: Para sistema de archivos NTFS (Windows)

## **Uso en Instalación de Arch Linux**

### **Formateo para Sistema UEFI**

```bash
# Esquema típico después de cfdisk:
# /dev/sda1 - 512M - EFI System
# /dev/sda2 - 2G   - Linux swap
# /dev/sda3 - resto - Linux filesystem

# 1. Formatear partición EFI (obligatorio para UEFI)
mkfs.fat -F32 /dev/sda1

# 2. Formatear partición raíz con ext4
mkfs.ext4 /dev/sda3

# 3. Configurar swap (ver sección mkswap)
mkswap /dev/sda2
```

### **Formateo para Sistema BIOS/Legacy**

```bash
# Esquema típico para BIOS:
# /dev/sda1 - 2G   - Linux swap
# /dev/sda2 - resto - Linux filesystem

# 1. Formatear partición raíz
mkfs.ext4 /dev/sda2

# 2. Configurar swap
mkswap /dev/sda1
```

## **mkfs.fat - Sistemas de Archivos FAT**

### **Sintaxis y Opciones**

```bash
# Sintaxis básica
mkfs.fat [opciones] dispositivo

# Opciones principales
# -F32: Crear FAT32 (requerido para EFI)
# -F16: Crear FAT16
# -n: Etiqueta del volumen
# -v: Modo verboso
```

### **Ejemplos Prácticos**

```bash
# Formatear partición EFI (estándar para Arch Linux)
mkfs.fat -F32 /dev/sda1

# Con etiqueta personalizada
mkfs.fat -F32 -n "EFI-ARCH" /dev/sda1

# Verificar resultado
blkid /dev/sda1
# Salida: /dev/sda1: LABEL="EFI-ARCH" UUID="xxxx" TYPE="vfat"
```

### **Requisitos para EFI**

```bash
# La partición EFI debe ser:
# ✅ FAT32 (no FAT16)
# ✅ Mínimo 512MB (recomendado)
# ✅ Tipo de partición: EFI System (ef00 en fdisk)
# ✅ Montada en /boot/efi o /boot

# Verificar que es EFI válida
file -s /dev/sda1
# Debe mostrar: FAT (32 bit)
```

## **mkfs.ext4 - Sistema de Archivos ext4**

### **Sintaxis y Opciones**

```bash
# Sintaxis básica
mkfs.ext4 [opciones] dispositivo

# Opciones importantes
# -L: Etiqueta del sistema de archivos
# -b: Tamaño de bloque (1024, 2048, 4096)
# -i: Bytes por inode
# -m: Porcentaje reservado para root
# -F: Forzar creación (sobrescribir)
```

### **Ejemplos para Arch Linux**

```bash
# Formateo básico para partición raíz
mkfs.ext4 /dev/sda3

# Con etiqueta personalizada
mkfs.ext4 -L "ArchLinux" /dev/sda3

# Optimizado para SSD (menos espacio reservado)
mkfs.ext4 -m 1 -L "ArchRoot" /dev/sda3

# Con verificación de bloques dañados
mkfs.ext4 -c /dev/sda3

# Verificar resultado
tune2fs -l /dev/sda3 | head -10
```

### **Optimizaciones Comunes**

```bash
# Para SSD: reducir espacio reservado
mkfs.ext4 -m 1 /dev/sda3  # Solo 1% para root (vs 5% default)

# Para particiones grandes: más inodes
mkfs.ext4 -i 8192 /dev/sda3  # Un inode cada 8KB

# Para mejor rendimiento: bloques grandes
mkfs.ext4 -b 4096 /dev/sda3  # Bloques de 4KB
```

## **mkfs.btrfs - Sistema de Archivos Btrfs**

### **Sintaxis y Opciones**

```bash
# Sintaxis básica
mkfs.btrfs [opciones] dispositivo

# Opciones principales
# -L: Etiqueta del sistema de archivos
# -f: Forzar creación
# -d: Nivel RAID para datos
# -m: Nivel RAID para metadatos
```

### **Ejemplos para Arch Linux**

```bash
# Formateo básico para Btrfs
mkfs.btrfs /dev/sda3

# Con etiqueta personalizada
mkfs.btrfs -L "ArchBtrfs" /dev/sda3

# Forzar en partición con datos existentes
mkfs.btrfs -f /dev/sda3

# Para múltiples dispositivos (RAID)
mkfs.btrfs -d raid1 -m raid1 /dev/sda3 /dev/sdb3

# Verificar resultado
btrfs filesystem show
```

---

# **mkswap - Configuración de Memoria de Intercambio**

## **¿Qué es mkswap?**

`mkswap` es el comando utilizado para configurar una partición o archivo como espacio de intercambio (swap). El swap actúa como memoria virtual cuando la RAM física se agota, permitiendo que el sistema continúe funcionando aunque con menor rendimiento.

En Arch Linux, mkswap es esencial para preparar la partición swap después del particionado, especialmente importante en sistemas con RAM limitada.

## **Sintaxis Básica**

```bash
mkswap [opciones] dispositivo
```

### **Opciones Principales**

- **-L**: Etiqueta para el swap
- **-U**: UUID personalizado
- **-c**: Verificar bloques dañados
- **-f**: Forzar creación
- **-p**: Tamaño de página

## **Configuración de Swap en Arch Linux**

### **Swap en Partición Dedicada**

```bash
# Después de crear partición swap con cfdisk
# Tipo: Linux swap (82 en MBR, 8200 en GPT)

# 1. Crear área de swap
mkswap /dev/sda2

# 2. Con etiqueta personalizada
mkswap -L "ArchSwap" /dev/sda2

# 3. Verificar resultado
blkid /dev/sda2
# Salida: /dev/sda2: LABEL="ArchSwap" UUID="xxxx" TYPE="swap"
```

### **Tamaños Recomendados de Swap**

```bash
# Guías generales para tamaño de swap:

# RAM <= 2GB:     Swap = 2x RAM
# RAM 2-8GB:      Swap = RAM
# RAM 8-64GB:     Swap = 0.5x RAM (mínimo 4GB)
# RAM > 64GB:     Swap = 4-8GB (o deshabilitado)

# Ejemplos prácticos:
# Sistema con 4GB RAM:    Swap = 4GB
# Sistema con 8GB RAM:    Swap = 4-8GB  
# Sistema con 16GB RAM:   Swap = 2-4GB
# Sistema con 32GB RAM:   Swap = 4GB o sin swap
```

### **Swap File (Alternativa a Partición)**

```bash
# Crear archivo de swap (alternativa flexible)

# 1. Crear archivo de swap (4GB ejemplo)
dd if=/dev/zero of=/swapfile bs=1M count=4096

# 2. Establecer permisos correctos
chmod 600 /swapfile

# 3. Configurar como swap
mkswap /swapfile

# 4. Activar (ver sección swapon)
swapon /swapfile

# 5. Verificar
free -h
```

## **Consideraciones Especiales**

### **Swap en Btrfs**

```bash
# ⚠️ IMPORTANTE: Swap file en Btrfs requiere configuración especial

# 1. Crear archivo sin compresión ni CoW
truncate -s 0 /swapfile
chattr +C /swapfile
btrfs property set /swapfile compression none

# 2. Crear archivo del tamaño deseado
dd if=/dev/zero of=/swapfile bs=1M count=4096

# 3. Configurar permisos y formato
chmod 600 /swapfile
mkswap /swapfile
```

### **Swap Encriptado**

```bash
# Para sistemas con encriptación completa

# 1. El swap debe estar dentro del volumen encriptado
# O configurar swap encriptado separadamente

# En /etc/crypttab:
# swap /dev/sda2 /dev/urandom swap,cipher=aes-xts-plain64,size=512

# En /etc/fstab:
# /dev/mapper/swap none swap defaults 0 0
```

---

# **swapon - Activación de Memoria de Intercambio**

## **¿Qué es swapon?**

`swapon` es el comando utilizado para activar el espacio de intercambio (swap) previamente configurado con `mkswap`. Es el paso final para hacer que el swap esté disponible para el sistema.

En la instalación de Arch Linux, swapon se ejecuta después de mkswap para activar inmediatamente el swap y permitir que el instalador use esta memoria adicional.

## **Sintaxis Básica**

```bash
swapon [opciones] dispositivo
swapoff [opciones] dispositivo  # Para desactivar
```

### **Opciones Principales**

- **-a**: Activar todos los swaps en /etc/fstab
- **-p**: Establecer prioridad
- **-s**: Mostrar estadísticas de swap
- **-v**: Modo verboso

## **Activación de Swap**

### **Activación Manual**

```bash
# Activar swap en partición
swapon /dev/sda2

# Activar swap file
swapon /swapfile

# Activar con prioridad específica (0-32767, mayor = más prioritario)
swapon -p 1000 /dev/sda2

# Verificar que está activo
swapon -s
# O usar:
free -h
cat /proc/swaps
```

### **Activación Automática en /etc/fstab**

```bash
# Configurar en /etc/fstab para activación automática

# Para partición swap:
UUID=xxxx-xxxx-xxxx none swap defaults 0 0

# Para swap file:
/swapfile none swap defaults 0 0

# Con prioridad específica:
UUID=xxxx-xxxx-xxxx none swap defaults,pri=1000 0 0

# Activar todos los swaps configurados
swapon -a
```

### **Verificación de Swap Activo**

```bash
# Métodos para verificar swap activo

# 1. Comando swapon
swapon -s
# Salida:
# Filename      Type      Size    Used    Priority
# /dev/sda2     partition 2097148 0       -2

# 2. Comando free
free -h
# Salida incluye línea Swap con tamaño total

# 3. Archivo /proc/swaps
cat /proc/swaps

# 4. Información detallada del sistema
cat /proc/meminfo | grep -i swap
```

## **Gestión Avanzada de Swap**

### **Múltiples Dispositivos Swap**

```bash
# Configurar múltiples swaps con prioridades

# Swap rápido (SSD) - prioridad alta
swapon -p 1000 /dev/nvme0n1p2

# Swap lento (HDD) - prioridad baja  
swapon -p 500 /dev/sda2

# El kernel usa el de mayor prioridad primero
swapon -s
```

### **Desactivación de Swap**

```bash
# Desactivar swap específico
swapoff /dev/sda2

# Desactivar todo el swap
swapoff -a

# Útil antes de redimensionar particiones
# O para mantenimiento del sistema
```

### **Configuración de Swappiness**

```bash
# Controlar cuándo el kernel usa swap

# Ver configuración actual
cat /proc/sys/vm/swappiness
# Default: 60 (0-100, mayor = más swap)

# Configurar temporalmente
echo 10 > /proc/sys/vm/swappiness

# Configurar permanentemente en /etc/sysctl.conf
echo "vm.swappiness=10" >> /etc/sysctl.conf

# Valores recomendados:
# 1-10:  Para sistemas con mucha RAM (desktop)
# 10-40: Para sistemas balanceados  
# 60+:   Para sistemas con poca RAM (servers)
```

## **Flujo Completo en Instalación de Arch**

### **Proceso Paso a Paso**

```bash
# Flujo completo después de particionar con cfdisk

# 1. Formatear particiones
mkfs.fat -F32 /dev/sda1      # EFI
mkfs.ext4 /dev/sda3          # Root

# 2. Configurar swap
mkswap /dev/sda2             # Crear swap
swapon /dev/sda2             # Activar swap

# 3. Verificar configuración
lsblk -f                     # Ver todas las particiones
free -h                      # Confirmar swap activo
swapon -s                    # Detalles del swap

# 4. Montar para instalación
mount /dev/sda3 /mnt         # Root
mkdir /mnt/boot              # Directorio boot
mount /dev/sda1 /mnt/boot    # EFI

# 5. Continuar con pacstrap
pacstrap /mnt base linux linux-firmware
```

### **Configuración en fstab**

```bash
# Después de genfstab, verificar /etc/fstab

# Generar fstab (incluye swap automáticamente)
genfstab -U /mnt >> /mnt/etc/fstab

# Verificar contenido
cat /mnt/etc/fstab

# Debe incluir línea similar a:
# UUID=xxxx-xxxx none swap defaults 0 0
```

## **Troubleshooting**

### **Problemas Comunes**

```bash
# Error: "swapon: /dev/sda2: read swap header failed"
# Solución: Reformatear swap
mkswap /dev/sda2
swapon /dev/sda2

# Error: "Device or resource busy"
# Solución: Verificar si ya está activo
swapon -s
# Si está activo, desactivar primero
swapoff /dev/sda2

# Error: Swap no se activa al arrancar
# Solución: Verificar /etc/fstab
grep swap /etc/fstab
# Verificar UUID correcto
blkid /dev/sda2
```

### **Verificación de Rendimiento**

```bash
# Monitorear uso de swap
watch free -h

# Estadísticas detalladas
vmstat 1

# Ver qué procesos usan swap
for file in /proc/*/status ; do awk '/VmSwap|Name/{printf $2 " " $3}END{ print ""}' $file; done | sort -k 2 -n
```

### **Optimización de Swap**

```bash
# Para mejor rendimiento:

# 1. Usar SSD para swap si es posible
# 2. Configurar swappiness apropiadamente
# 3. Considerar zswap para compresión
echo 1 > /sys/module/zswap/parameters/enabled

# 4. Para workstations: swap pequeño o sin swap
# 5. Para servers: swap = RAM para hibernación
```

## **Comparación de Opciones**

### **Partición vs Archivo Swap**

| Aspecto | Partición Swap | Archivo Swap |
|---------|----------------|--------------|
| **Rendimiento** | Ligeramente mejor | Muy bueno |
| **Flexibilidad** | Fijo al crear | Redimensionable |
| **Hibernación** | Soporte completo | Soporte limitado |
| **Configuración** | Simple | Más pasos |
| **Encriptación** | Automática (LVM) | Manual |
| **Recomendado para** | Instalación nueva | Sistema existente |

### **Cuándo Usar Swap**

```bash
# ✅ Usar swap cuando:
# - RAM < 8GB
# - Necesitas hibernación
# - Workloads con picos de memoria
# - Sistema servidor con muchos procesos

# ❌ Considerar sin swap cuando:
# - RAM > 16GB y uso predecible
# - Solo SSD pequeño disponible
# - Rendimiento crítico (trading, gaming)
# - Sistema embebido con almacenamiento limitado
```

## **Integración con Hibernación**

### **Configuración para Hibernar**

```bash
# Para habilitar hibernación, swap debe ser >= RAM

# 1. Swap suficientemente grande
mkswap -L "HibernateSwap" /dev/sda2
swapon /dev/sda2

# 2. Configurar kernel para hibernación
# En /etc/default/grub:
# GRUB_CMDLINE_LINUX_DEFAULT="resume=UUID=xxxx-xxxx"

# 3. Configurar initramfs
# En /etc/mkinitcpio.conf:
# HOOKS=(... resume ...)

# 4. Regenerar initramfs
mkinitcpio -p linux

# 5. Actualizar GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

---

**Los comandos mkfs, mkswap y swapon son fundamentales en la preparación del sistema de archivos durante la instalación de Arch Linux, transformando particiones vacías en sistemas de archivos funcionales listos para la instalación del sistema operativo.**