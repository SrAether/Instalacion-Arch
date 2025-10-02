# **Btrfs - Sistema de Archivos Avanzado y Flexible**

## **¿Qué es Btrfs?**

`Btrfs` (B-Tree File System, pronunciado "Butter FS") es un sistema de archivos moderno de nueva generación diseñado para Linux. Desarrollado por Oracle, Facebook, Intel y otros, está diseñado para abordar las limitaciones de los sistemas de archivos tradicionales como ext4, proporcionando características avanzadas como snapshots, compresión transparente, verificación de integridad y administración flexible del almacenamiento.

En Arch Linux, Btrfs es una excelente opción para usuarios que buscan características avanzadas como snapshots automáticos del sistema, compresión para ahorrar espacio y la flexibilidad de subvolúmenes para organizar mejor su sistema.

## **Características Principales de Btrfs**

### **🔄 Copy-on-Write (CoW)**
```bash
# Btrfs nunca modifica datos en su lugar
# Cuando cambias un archivo:
# 1. Crea una copia en nueva ubicación
# 2. Modifica la copia
# 3. Actualiza punteros solo si tiene éxito
# 4. Marca el espacio antiguo como libre

# Ventajas:
# ✅ Resistente a corrupción por fallos de energía
# ✅ Base para snapshots eficientes
# ✅ Integridad de datos mejorada
```

### **📸 Snapshots (Instantáneas)**
```bash
# Tomar snapshot del sistema antes de actualización
btrfs subvolume snapshot / /.snapshots/antes-actualizacion-$(date +%Y%m%d)

# Los snapshots son casi instantáneos y ocupan poco espacio inicial
# Solo consumen espacio por los cambios (CoW)

# Ventajas:
# ✅ Respaldo instantáneo del sistema
# ✅ Recuperación rápida ante problemas
# ✅ Versionado de archivos y configuraciones
```

### **📁 Subvolúmenes**
```bash
# Subvolúmenes = "particiones virtuales" dentro de Btrfs
# Comparten espacio dinámicamente pero actúan independientemente

# Ejemplo de estructura típica:
# / (subvol @)      - Sistema raíz
# /home (subvol @home) - Directorios de usuario
# /var (subvol @var)   - Logs y datos variables
# /.snapshots (subvol @snapshots) - Snapshots del sistema

# Ventajas:
# ✅ Flexibilidad en asignación de espacio
# ✅ Snapshots independientes por subvolumen
# ✅ Administración granular del sistema
```

### **🛡️ Integridad de Datos**
```bash
# Btrfs calcula checksums para todos los datos y metadatos
# Detecta automáticamente corrupción silenciosa

# Verificar integridad del sistema de archivos
btrfs scrub start /
btrfs scrub status /

# Ventajas:
# ✅ Detección automática de corrupción
# ✅ Reparación automática (con RAID)
# ✅ Confiabilidad mejorada
```

### **🗜️ Compresión Transparente**
```bash
# Compresión automática sin intervención del usuario
# Algoritmos soportados: zstd, lzo, zlib

# Montar con compresión
mount -o compress=zstd /dev/sda3 /mnt

# Ventajas:
# ✅ Ahorro significativo de espacio
# ✅ Transparente para aplicaciones
# ✅ Mejor rendimiento en SSDs
```

## **Instalación y Configuración en Arch Linux**

### **Instalación de Herramientas Btrfs**

```bash
# Durante la instalación de Arch (en chroot)
pacstrap /mnt base linux linux-firmware btrfs-progs

# En sistema ya instalado
sudo pacman -S btrfs-progs

# Herramientas adicionales útiles
sudo pacman -S snapper            # Gestor automático de snapshots
sudo pacman -S grub-btrfs         # Integración GRUB con snapshots
```

### **Creación del Sistema de Archivos**

```bash
# Formatear partición como Btrfs
mkfs.btrfs /dev/sda3

# Con etiqueta personalizada
mkfs.btrfs -L "ArchLinux" /dev/sda3

# Para múltiples dispositivos (RAID)
mkfs.btrfs -d raid1 -m raid1 /dev/sda3 /dev/sdb3
```

### **Configuración de Subvolúmenes para Arch Linux**

#### **Estructura Básica (@/@home)**

```bash
# 1. Montar partición Btrfs temporalmente
mount /dev/sda3 /mnt

# 2. Crear subvolúmenes básicos
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home

# 3. Desmontar y remontar con estructura correcta
umount /mnt

# 4. Montar subvolúmenes con opciones optimizadas
mount -o noatime,compress=zstd,subvol=@ /dev/sda3 /mnt
mkdir /mnt/home
mount -o noatime,compress=zstd,subvol=@home /dev/sda3 /mnt/home
```

#### **Estructura Avanzada (Múltiples Subvolúmenes)**

```bash
# 1. Montar partición Btrfs
mount /dev/sda3 /mnt

# 2. Crear estructura completa de subvolúmenes
btrfs subvolume create /mnt/@              # Sistema raíz
btrfs subvolume create /mnt/@home          # Directorios usuario
btrfs subvolume create /mnt/@var           # Datos variables (/var)
btrfs subvolume create /mnt/@tmp           # Archivos temporales
btrfs subvolume create /mnt/@opt           # Software opcional
btrfs subvolume create /mnt/@root          # Directorio root
btrfs subvolume create /mnt/@srv           # Servicios
btrfs subvolume create /mnt/@snapshots     # Directorio snapshots

# 3. Desmontar
umount /mnt

# 4. Montar estructura completa
mount -o noatime,compress=zstd,subvol=@ /dev/sda3 /mnt

# Crear directorios
mkdir -p /mnt/{home,var,tmp,opt,root,srv,.snapshots}

# Montar subvolúmenes
mount -o noatime,compress=zstd,subvol=@home /dev/sda3 /mnt/home
mount -o noatime,compress=zstd,subvol=@var /dev/sda3 /mnt/var
mount -o noatime,compress=zstd,subvol=@tmp /dev/sda3 /mnt/tmp
mount -o noatime,compress=zstd,subvol=@opt /dev/sda3 /mnt/opt
mount -o noatime,compress=zstd,subvol=@root /dev/sda3 /mnt/root
mount -o noatime,compress=zstd,subvol=@srv /dev/sda3 /mnt/srv
mount -o noatime,compress=zstd,subvol=@snapshots /dev/sda3 /mnt/.snapshots
```

## **Opciones de Montaje Btrfs**

### **Opciones de Rendimiento**

```bash
# noatime: No actualizar tiempo de acceso (mejor rendimiento)
mount -o noatime /dev/sda3 /mnt

# ssd: Optimizaciones para discos SSD
mount -o ssd /dev/sda3 /mnt

# discard: TRIM automático para SSDs
mount -o discard /dev/sda3 /mnt

# space_cache=v2: Cache de espacio libre mejorado
mount -o space_cache=v2 /dev/sda3 /mnt
```

### **Opciones de Compresión**

```bash
# zstd: Mejor balance rendimiento/compresión (recomendado)
mount -o compress=zstd /dev/sda3 /mnt

# lzo: Compresión más rápida, menos eficiente
mount -o compress=lzo /dev/sda3 /mnt

# zlib: Compresión más lenta, más eficiente
mount -o compress=zlib /dev/sda3 /mnt

# Niveles de compresión zstd (1-15)
mount -o compress=zstd:3 /dev/sda3 /mnt
```

### **Opciones de Subvolúmenes**

```bash
# Montar subvolumen específico
mount -o subvol=@ /dev/sda3 /mnt
mount -o subvol=@home /dev/sda3 /mnt/home

# Por ID de subvolumen
mount -o subvolid=256 /dev/sda3 /mnt

# Combinando opciones
mount -o noatime,compress=zstd,ssd,subvol=@ /dev/sda3 /mnt
```

## **Gestión de Subvolúmenes**

### **Operaciones Básicas**

```bash
# Crear subvolumen
btrfs subvolume create /path/nuevo_subvolumen

# Listar subvolúmenes
btrfs subvolume list /

# Mostrar información detallada
btrfs subvolume show /home

# Eliminar subvolumen
btrfs subvolume delete /path/subvolumen

# Establecer subvolumen por defecto
btrfs subvolume set-default 256 /
```

### **Snapshots de Subvolúmenes**

```bash
# Crear snapshot de solo lectura
btrfs subvolume snapshot -r / /.snapshots/root-$(date +%Y%m%d-%H%M)

# Crear snapshot escribible
btrfs subvolume snapshot /home /.snapshots/home-backup

# Crear snapshot de sistema completo antes de actualización
btrfs subvolume snapshot / /.snapshots/pre-update-$(date +%Y%m%d)
btrfs subvolume snapshot /home /.snapshots/home-pre-update-$(date +%Y%m%d)

# Restaurar desde snapshot
# 1. Arrancar desde live USB
# 2. Montar partición Btrfs
mount /dev/sda3 /mnt
# 3. Mover subvolumen actual
mv /mnt/@ /mnt/@.broken
# 4. Restaurar snapshot
btrfs subvolume snapshot /mnt/.snapshots/root-20241002-1200 /mnt/@
```

## **Administración de Btrfs**

### **Información del Sistema de Archivos**

```bash
# Mostrar información general
btrfs filesystem show

# Uso del espacio
btrfs filesystem usage /

# Estadísticas detalladas
btrfs filesystem df /

# Información de dispositivos
btrfs device stats /
```

### **Mantenimiento y Verificación**

```bash
# Verificar integridad (scrub)
btrfs scrub start /
btrfs scrub status /

# Balancear el sistema de archivos
btrfs balance start /

# Desfragmentar (usar con precaución)
btrfs filesystem defragment -r -v /

# Redimensionar sistema de archivos
btrfs filesystem resize +10G /
btrfs filesystem resize max /
```

### **Gestión de Cuotas**

```bash
# Habilitar cuotas
btrfs quota enable /

# Crear grupo de cuotas
btrfs qgroup create 1/100 /

# Asignar límite
btrfs qgroup limit 50G 1/100 /

# Mostrar uso de cuotas
btrfs qgroup show /
```

## **Configuración en /etc/fstab**

### **Entrada Básica**

```bash
# /etc/fstab para configuración básica @/@home
UUID=xxx-xxx-xxx / btrfs noatime,compress=zstd,subvol=@ 0 0
UUID=xxx-xxx-xxx /home btrfs noatime,compress=zstd,subvol=@home 0 0
```

### **Configuración Avanzada**

```bash
# /etc/fstab completo para estructura avanzada
UUID=xxx-xxx-xxx / btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@ 0 0
UUID=xxx-xxx-xxx /home btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@home 0 0
UUID=xxx-xxx-xxx /var btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@var 0 0
UUID=xxx-xxx-xxx /tmp btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@tmp 0 0
UUID=xxx-xxx-xxx /opt btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@opt 0 0
UUID=xxx-xxx-xxx /root btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@root 0 0
UUID=xxx-xxx-xxx /.snapshots btrfs noatime,compress=zstd,ssd,space_cache=v2,subvol=@snapshots 0 0
```

### **Obtener UUID**

```bash
# Obtener UUID de la partición
blkid /dev/sda3

# O usar lsblk
lsblk -f /dev/sda3
```

## **Snapshots Automáticos con Snapper**

### **Instalación y Configuración**

```bash
# Instalar snapper
sudo pacman -S snapper

# Crear configuración para raíz
sudo snapper -c root create-config /

# Crear configuración para home
sudo snapper -c home create-config /home

# Configurar snapshots automáticos
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

### **Configuración de Snapper**

```bash
# Editar configuración de snapshots
sudo nano /etc/snapper/configs/root

# Configuraciones importantes:
# TIMELINE_CREATE="yes"           # Crear snapshots automáticos
# TIMELINE_CLEANUP="yes"          # Limpiar snapshots antiguos  
# NUMBER_LIMIT="50"               # Máximo 50 snapshots
# TIMELINE_LIMIT_HOURLY="10"      # 10 snapshots por hora
# TIMELINE_LIMIT_DAILY="10"       # 10 snapshots diarios
# TIMELINE_LIMIT_WEEKLY="0"       # Sin snapshots semanales
# TIMELINE_LIMIT_MONTHLY="10"     # 10 snapshots mensuales
```

### **Uso de Snapper**

```bash
# Listar snapshots
snapper list

# Crear snapshot manual
snapper create --description "Antes de instalar nuevo software"

# Comparar snapshots
snapper status 1..2

# Restaurar archivo desde snapshot
snapper undochange 1..2

# Restaurar sistema completo (requiere reinicio)
snapper rollback
```

## **Integración con GRUB**

### **Configuración de grub-btrfs**

```bash
# Instalar grub-btrfs
sudo pacman -S grub-btrfs

# Habilitar detección automática de snapshots
sudo systemctl enable --now grub-btrfsd

# Regenerar configuración GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### **Arranque desde Snapshots**

```bash
# Después de configurar grub-btrfs:
# 1. Los snapshots aparecen en el menú GRUB
# 2. Seleccionar "Advanced options for Arch Linux"
# 3. Elegir snapshot para arrancar
# 4. Si funciona, hacer permanente con snapper rollback
```

## **Comparación con ext4**

### **Tabla Comparativa**

| Característica | Btrfs | ext4 |
|----------------|-------|------|
| **Snapshots** | ✅ Nativo, eficiente | ❌ Requiere LVM |
| **Compresión** | ✅ Transparente (zstd/lzo/zlib) | ❌ No soportado |
| **Checksums** | ✅ Automático datos+metadatos | ❌ Solo metadatos |
| **Subvolúmenes** | ✅ Nativo, flexible | ❌ No soportado |
| **RAID** | ✅ Nativo (0,1,5,6,10) | ❌ Requiere mdadm |
| **Dedup** | ✅ En desarrollo | ❌ No soportado |
| **Redimensionar** | ✅ En línea | ✅ Limitado |
| **Estabilidad** | 🟡 Muy buena | 🟢 Excelente |
| **Rendimiento** | 🟡 Bueno | 🟢 Excelente |
| **Madurez** | 🟡 Moderna | 🟢 Muy madura |
| **Complejidad** | 🔴 Alta | 🟢 Baja |
| **Recuperación** | 🟡 Buena | 🟢 Excelente |

### **Cuándo Usar Cada Sistema**

#### **Usar Btrfs cuando:**
```bash
# ✅ Quieres snapshots automáticos del sistema
# ✅ Necesitas compresión para ahorrar espacio
# ✅ Trabajas con VMs o containers
# ✅ Sistema de desarrollo con cambios frecuentes
# ✅ Tienes conocimientos avanzados de Linux
# ✅ Valoras características modernas sobre estabilidad absoluta
```

#### **Usar ext4 cuando:**
```bash
# ✅ Priorizas estabilidad y simplicidad
# ✅ Sistema de producción crítico
# ✅ Hardware con recursos limitados
# ✅ Nuevo en Linux
# ✅ Necesitas máximo rendimiento
# ✅ Recuperación de datos es prioritaria
```

## **Troubleshooting y Problemas Comunes**

### **Problemas de Espacio**

```bash
# "No space left on device" pero df muestra espacio libre
# Causa: Metadatos llenos

# Solución: Balancear el sistema de archivos
btrfs balance start -dusage=50 /
btrfs balance start -musage=50 /

# Para casos severos
btrfs balance start -dusage=100 /
```

### **Problemas de Rendimiento**

```bash
# Sistema lento con Btrfs
# Posibles causas y soluciones:

# 1. Fragmentación excesiva
btrfs filesystem defragment -r /

# 2. Sin compresión
# Remontar con compresión
mount -o remount,compress=zstd /

# 3. Subvolúmenes sin optimizar
# Verificar opciones de montaje en /etc/fstab
```

### **Corrupción y Recuperación**

```bash
# Verificar y reparar sistema de archivos
btrfs check /dev/sda3                    # Solo lectura
btrfs check --repair /dev/sda3           # ¡Peligroso! Solo como último recurso

# Recuperar desde snapshot
mount /dev/sda3 /mnt
btrfs subvolume list /mnt
btrfs subvolume snapshot /mnt/.snapshots/snapshot_bueno /mnt/@_recuperado
```

### **Problemas de Montaje**

```bash
# Error: "mount: wrong fs type, bad option, bad superblock"
# Verificar que btrfs-progs está instalado
pacman -S btrfs-progs

# Error: "No such device"
# Actualizar UUID en /etc/fstab
blkid /dev/sda3
```

## **Optimizaciones Avanzadas**

### **SSD Optimizations**

```bash
# Configuración óptima para SSDs
mount -o noatime,ssd,discard,compress=zstd,space_cache=v2 /dev/sda3 /

# En /etc/fstab:
UUID=xxx / btrfs noatime,ssd,discard,compress=zstd,space_cache=v2,subvol=@ 0 0
```

### **Configuración de Compresión por Directorio**

```bash
# Habilitar compresión en directorio específico
btrfs property set /home/usuario/documentos compression zstd

# Verificar configuración
btrfs property get /home/usuario/documentos compression

# Aplicar a archivos existentes
btrfs filesystem defragment -r -czstd /home/usuario/documentos
```

### **Automatización de Mantenimiento**

```bash
# Script de mantenimiento semanal
#!/bin/bash
# /etc/cron.weekly/btrfs-maintenance

echo "=== Mantenimiento Btrfs $(date) ==="

# Scrub del sistema de archivos
echo "Iniciando scrub..."
btrfs scrub start /
btrfs scrub status /

# Balance ligero
echo "Ejecutando balance..."
btrfs balance start -dusage=20 -musage=20 /

# Desfragmentación de directorios críticos
echo "Desfragmentando..."
btrfs filesystem defragment /var/log
btrfs filesystem defragment /home

echo "Mantenimiento completado"
```

## **Migración desde ext4 a Btrfs**

### **Preparación para Migración**

```bash
# 1. Respaldo completo del sistema
rsync -aAXv / /backup/sistema-completo/ --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found}

# 2. Verificar integridad de respaldo
diff -r / /backup/sistema-completo/

# 3. Crear partición adicional o usar disco externo para Btrfs
```

### **Proceso de Migración**

```bash
# Método 1: Instalación limpia (recomendado)
# 1. Instalar Arch Linux con Btrfs desde cero
# 2. Restaurar datos de usuario desde respaldo

# Método 2: Conversión in-place (riesgoso)
# ⚠️ Solo para usuarios muy experimentados
# btrfs-convert /dev/sda3  # Convierte ext4 a btrfs
# mount /dev/sda3 /mnt
# btrfs subvolume snapshot /mnt /mnt/@
```

## **Monitoreo y Logs**

### **Monitoreo de Salud**

```bash
# Script de monitoreo de salud
#!/bin/bash
# /usr/local/bin/btrfs-health-check

echo "=== Estado de Btrfs $(date) ==="

# Información general
btrfs filesystem show
echo

# Uso del espacio
btrfs filesystem usage /
echo

# Estado de dispositivos
btrfs device stats /
echo

# Estado de scrub
btrfs scrub status /
echo

# Subvolúmenes y snapshots
echo "=== Subvolúmenes ==="
btrfs subvolume list /
echo

echo "=== Snapshots recientes ==="
snapper list | tail -10
```

### **Logs del Sistema**

```bash
# Ver logs relacionados con Btrfs
journalctl -u btrfs-*
dmesg | grep -i btrfs

# Monitorear errores en tiempo real
journalctl -f | grep -i btrfs
```

---

**Btrfs es un sistema de archivos potente y moderno que ofrece características avanzadas ideales para usuarios experimentados de Arch Linux que valoran la flexibilidad, los snapshots automáticos y las funciones de integridad de datos.**