# **fdisk - Gestión de Particiones de Disco**

## **¿Qué es fdisk?**

`fdisk` es una utilidad de línea de comandos para crear, eliminar, redimensionar y administrar particiones en discos duros. Es una herramienta fundamental durante la instalación de Arch Linux para preparar el disco donde se instalará el sistema.

En Arch Linux, fdisk es esencial para particionar el disco antes de la instalación, permitiendo crear las particiones necesarias para el sistema de archivos raíz, swap, y en sistemas UEFI, la partición EFI.

## **Sintaxis Básica**

```bash
fdisk [opciones] dispositivo
```

### **Parámetros Principales**

- **dispositivo**: Ruta al dispositivo de disco (ej: `/dev/sda`, `/dev/nvme0n1`)
- **-l**: Listar particiones de todos los discos o uno específico
- **-s**: Mostrar tamaño de una partición o disco
- **-u**: Cambiar unidades de visualización

## **Comandos Interactivos de fdisk**

Cuando entras a fdisk de forma interactiva, tienes estos comandos:

### **Comandos de Información**
- **p**: Imprimir tabla de particiones
- **l**: Listar tipos de partición conocidos
- **F**: Listar espacio libre sin particionar

### **Comandos de Modificación**
- **n**: Crear nueva partición
- **d**: Eliminar partición
- **t**: Cambiar tipo de partición
- **a**: Activar/desactivar bandera booteable

### **Comandos de Control**
- **w**: Escribir cambios y salir
- **q**: Salir sin guardar cambios
- **m**: Mostrar menú de ayuda

## **Ejemplos Prácticos en Arch Linux**

### **Listar Discos y Particiones**

```bash
# Ver todos los discos y particiones
fdisk -l

# Ver particiones de un disco específico
fdisk -l /dev/sda

# Ver solo información básica
lsblk
```

### **Crear Particiones para Arch Linux**

```bash
# Iniciar fdisk en el disco principal
fdisk /dev/sda

# Dentro de fdisk, crear esquema típico:
# 1. Borrar particiones existentes (si es necesario)
d   # Eliminar partición (repetir para cada una)

# 2. Crear partición EFI (sistemas UEFI)
n   # Nueva partición
p   # Primaria
1   # Número de partición
    # [Enter] para primer sector por defecto
+512M   # Tamaño para EFI

# 3. Cambiar tipo a EFI
t   # Cambiar tipo
1   # Partición 1
ef  # Tipo EFI (EF00)

# 4. Crear partición swap
n   # Nueva partición
p   # Primaria
2   # Número de partición
    # [Enter] para primer sector
+2G # Tamaño del swap

# 5. Cambiar tipo a swap
t   # Cambiar tipo
2   # Partición 2
82  # Tipo Linux swap

# 6. Crear partición raíz
n   # Nueva partición
p   # Primaria
3   # Número de partición
    # [Enter] para primer sector
    # [Enter] para usar todo el espacio restante

# 7. Verificar y guardar
p   # Imprimir tabla de particiones
w   # Escribir cambios y salir
```

### **Esquemas de Particionado Comunes**

```bash
# Esquema UEFI típico para Arch Linux:
# /dev/sda1 - 512M - EFI System Partition (ESP)
# /dev/sda2 - 2G   - Linux swap
# /dev/sda3 - resto - Linux filesystem (/)

# Esquema BIOS/Legacy:
# /dev/sda1 - 2G   - Linux swap
# /dev/sda2 - resto - Linux filesystem (/)
```

### **Verificación de Particiones**

```bash
# Verificar tabla de particiones creada
fdisk -l /dev/sda

# Ver estructura con lsblk
lsblk /dev/sda

# Verificar tipos de partición
blkid /dev/sda1
blkid /dev/sda2
blkid /dev/sda3
```

## **Casos de Uso en Instalación de Arch**

### **Preparación para Instalación UEFI**

```bash
# 1. Verificar que el sistema sea UEFI
ls /sys/firmware/efi/efivars

# 2. Identificar disco de instalación
fdisk -l

# 3. Particionar disco
fdisk /dev/sda
# Crear particiones según esquema UEFI

# 4. Formatear particiones
mkfs.fat -F32 /dev/sda1  # EFI
mkswap /dev/sda2         # Swap
mkfs.ext4 /dev/sda3      # Raíz
```

### **Preparación para Instalación BIOS**

```bash
# 1. Particionar disco
fdisk /dev/sda
# Crear particiones según esquema BIOS

# 2. Formatear particiones
mkswap /dev/sda1         # Swap
mkfs.ext4 /dev/sda2      # Raíz
```

### **Redimensionar Particiones**

```bash
# Eliminar y recrear partición (CUIDADO: Respaldar datos)
fdisk /dev/sda
# d - eliminar partición
# n - crear nueva con tamaño diferente
# (mantener mismo sector de inicio para preservar datos)
```

## **Gestión de Tipos de Partición**

### **Tipos Comunes**

```bash
# En fdisk, comando 't' para cambiar tipos:
# 83 - Linux filesystem
# 82 - Linux swap
# ef - EFI System
# fd - Linux RAID
# 8e - Linux LVM
```

### **Verificar y Cambiar Tipos**

```bash
# Ver tipos actuales
fdisk -l /dev/sda

# Cambiar tipo de partición
fdisk /dev/sda
t   # Cambiar tipo
2   # Número de partición
82  # Nuevo tipo (swap)
```

## **Troubleshooting**

### **Problemas Comunes**

```bash
# Error: Dispositivo ocupado
# Solución: Desmontar particiones
umount /dev/sda1
umount /dev/sda2

# Error: No se pueden escribir cambios
# Solución: Verificar permisos y que no esté en uso
sudo fdisk /dev/sda

# Error: Tabla de particiones corrupta
# Solución: Recrear tabla (PERDERÁS DATOS)
fdisk /dev/sda
# o (crear nueva tabla vacía)
```

### **Verificación de Integridad**

```bash
# Verificar tabla de particiones
fdisk -l /dev/sda

# Verificar filesystem
fsck /dev/sda1

# Ver información detallada del disco
hdparm -I /dev/sda
```

### **Recuperación de Particiones**

```bash
# Listar particiones perdidas con testdisk (si está instalado)
testdisk /dev/sda

# O usar herramientas de recuperación
photorec /dev/sda
```

## **Diferencias con Otras Herramientas**

### **fdisk vs cfdisk**

```bash
# fdisk: Interfaz de comandos
fdisk /dev/sda

# cfdisk: Interfaz visual/menú
cfdisk /dev/sda  # Más amigable para principiantes
```

### **fdisk vs parted**

```bash
# fdisk: Para discos MBR y GPT básico
fdisk /dev/sda

# parted: Más potente, mejor para GPT
parted /dev/sda
```

## **Buenas Prácticas**

### **Antes de Particionar**

```bash
# 1. Respaldar datos importantes
# 2. Verificar disco objetivo
lsblk
fdisk -l

# 3. Planificar esquema de particiones
# - EFI: 512M
# - Swap: 1-2x RAM (máximo 8GB)
# - Raíz: Resto del espacio
```

### **Durante el Particionado**

```bash
# 1. Usar 'p' frecuentemente para verificar
# 2. NO usar 'w' hasta estar seguro
# 3. Anotar el esquema de particiones
# 4. Verificar alineación de sectores
```

### **Después del Particionado**

```bash
# 1. Verificar tabla de particiones
fdisk -l /dev/sda

# 2. Notificar al kernel de cambios
partprobe /dev/sda

# 3. Formatear particiones inmediatamente
mkfs.ext4 /dev/sda3
mkswap /dev/sda2
```

## **Automatización Simple**

### **Script de Particionado Básico**

```bash
# Script para automatizar particionado UEFI
#!/bin/bash

DISK="/dev/sda"

echo "Creando tabla de particiones GPT..."
(
echo g      # crear tabla GPT
echo n      # nueva partición
echo 1      # número de partición
echo        # primer sector (por defecto)
echo +512M  # tamaño EFI
echo t      # cambiar tipo
echo 1      # tipo EFI
echo n      # nueva partición
echo 2      # número de partición
echo        # primer sector
echo +2G    # tamaño swap
echo t      # cambiar tipo
echo 2      # partición 2
echo 19     # tipo swap
echo n      # nueva partición
echo 3      # número de partición
echo        # primer sector
echo        # último sector (resto)
echo p      # imprimir tabla
echo w      # escribir cambios
) | fdisk $DISK
```

## **Seguridad y Precauciones**

### **Advertencias Importantes**

- ⚠️ **fdisk modifica permanentemente las particiones**
- ⚠️ **Siempre respalda datos importantes antes de particionar**
- ⚠️ **Verifica que estás modificando el disco correcto**
- ⚠️ **No uses 'w' hasta estar completamente seguro**

### **Verificaciones de Seguridad**

```bash
# Confirmar disco objetivo
lsblk
echo "¿Estás seguro que quieres modificar este disco?"
read -p "Continuar? (y/N): " confirm

# Verificar que no hay particiones montadas
mount | grep /dev/sda
```

## **Integración con la Instalación de Arch**

### **Flujo Típico de Instalación**

```bash
# 1. Particionar con fdisk
fdisk /dev/sda

# 2. Formatear particiones
mkfs.fat -F32 /dev/sda1  # EFI
mkswap /dev/sda2         # Swap
mkfs.ext4 /dev/sda3      # Raíz

# 3. Montar para instalación
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/sda2

# 4. Continuar con pacstrap
pacstrap /mnt base linux linux-firmware
```

---

**fdisk es una herramienta fundamental para la preparación de discos en Arch Linux. Su dominio es esencial para una instalación exitosa del sistema.**