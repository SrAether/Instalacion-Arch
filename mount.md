# mount - Montar Sistemas de Archivos

**mount** es tu herramienta para **conectar sistemas de archivos** al árbol de directorios de Arch Linux. Te permite acceder a particiones, dispositivos USB, discos duros y recursos de red montándolos en puntos específicos del sistema.

## **¿Qué es mount?**

mount te permite:
- **Montar particiones** y dispositivos de almacenamiento
- **Acceder a sistemas de archivos** diferentes (ext4, NTFS, FAT32, etc.)
- **Conectar recursos de red** (NFS, CIFS/SMB)
- **Gestionar puntos de montaje** temporales y permanentes

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, mount es esencial para la instalación (montar particiones root y boot), acceder a dispositivos externos, y gestionar almacenamiento adicional.

### **Sintaxis Básica**

```bash
# Montar dispositivo en punto de montaje
sudo mount dispositivo punto_de_montaje

# Montar con tipo de sistema de archivos específico
sudo mount -t tipo dispositivo punto_de_montaje

# Ver montajes actuales
mount

# Desmontar
sudo umount punto_de_montaje
```

## **Montaje Básico**

### **Ver Sistemas de Archivos Montados**

```bash
# Ver todos los montajes
mount

# Ver montajes en formato más legible
mount | column -t

# Ver solo sistemas de archivos específicos
mount -t ext4
mount -t vfat

# Ver información específica
findmnt
findmnt /
findmnt /home
```

### **Identificar Dispositivos Disponibles**

```bash
# Ver todos los dispositivos de bloque
lsblk

# Ver particiones
fdisk -l

# Ver dispositivos con información de filesystem
blkid

# Ejemplo de salida de lsblk:
# NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
# sda      8:0    0   50G  0 disk 
# ├─sda1   8:1    0  512M  0 part /boot
# ├─sda2   8:2    0    2G  0 part [SWAP]
# └─sda3   8:3    0 47.5G  0 part /
# sdb      8:16   1    8G  0 disk 
# └─sdb1   8:17   1    8G  0 part 
```

## **Montaje de Dispositivos Comunes**

### **USB y Dispositivos Extraíbles**

```bash
# 1. Conectar USB y identificarlo
lsblk

# 2. Crear punto de montaje
sudo mkdir -p /mnt/usb

# 3. Montar USB (generalmente FAT32 o NTFS)
sudo mount /dev/sdb1 /mnt/usb

# 4. Acceder a archivos
ls /mnt/usb

# 5. Desmontar cuando termines
sudo umount /mnt/usb
```

### **Particiones Windows (NTFS)**

```bash
# Montar partición NTFS
sudo mount -t ntfs-3g /dev/sda4 /mnt/windows

# O con opciones específicas
sudo mount -t ntfs-3g -o uid=1000,gid=1000,umask=0022 /dev/sda4 /mnt/windows

# Las opciones uid/gid te dan ownership como tu usuario
# umask controla permisos de archivos
```

### **Discos Duros Adicionales**

```bash
# 1. Identificar nuevo disco
sudo fdisk -l

# 2. Crear punto de montaje
sudo mkdir -p /mnt/disco2

# 3. Montar disco ext4
sudo mount /dev/sdc1 /mnt/disco2

# 4. Hacer montaje permanente (editar fstab)
echo "/dev/sdc1 /mnt/disco2 ext4 defaults 0 2" | sudo tee -a /etc/fstab
```

## **Durante la Instalación de Arch**

### **Montaje de Particiones del Sistema**

```bash
# Secuencia típica durante instalación de Arch:

# 1. Montar partición root
mount /dev/sda3 /mnt

# 2. Crear directorio para boot
mkdir /mnt/boot

# 3. Montar partición boot (si es separada)
mount /dev/sda1 /mnt/boot

# 4. Si tienes home separado
mkdir /mnt/home
mount /dev/sda4 /mnt/home

# 5. Verificar estructura
lsblk
findmnt /mnt
```

### **Montaje para chroot**

```bash
# Después de instalar sistema base, para chroot:

# 1. Montar sistemas especiales
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys

# 2. Entrar al sistema instalado
arch-chroot /mnt

# Esto te permite configurar el sistema desde dentro
```

## **Opciones de Montaje Importantes**

### **Opciones Comunes**

```bash
# Montaje de solo lectura
sudo mount -o ro /dev/sdb1 /mnt/readonly

# Montaje con permisos específicos
sudo mount -o uid=1000,gid=1000 /dev/sdb1 /mnt/usb

# Montaje sin ejecutables (seguridad)
sudo mount -o noexec /dev/sdb1 /mnt/safe

# Montaje sin dispositivos especiales
sudo mount -o nodev /dev/sdb1 /mnt/secure

# Múltiples opciones
sudo mount -o rw,noexec,nodev,uid=1000 /dev/sdb1 /mnt/mixed
```

### **Opciones por Tipo de Sistema de Archivos**

```bash
# Para NTFS:
sudo mount -t ntfs-3g -o uid=1000,gid=1000,dmask=022,fmask=133 /dev/sda4 /mnt/windows

# Para FAT32:
sudo mount -t vfat -o uid=1000,gid=1000,umask=0022 /dev/sdb1 /mnt/fat32

# Para ext4 con opciones específicas:
sudo mount -t ext4 -o defaults,noatime /dev/sdc1 /mnt/ext4
```

## **Configuración Permanente con fstab**

### **Archivo /etc/fstab**

```bash
# Ver fstab actual
cat /etc/fstab

# Formato de fstab:
# dispositivo  punto_montaje  tipo  opciones  dump  pass

# Ejemplo típico:
# UUID=1234-5678 /boot vfat defaults 0 2
# UUID=abcd-efgh / ext4 defaults 0 1
# UUID=wxyz-1234 /home ext4 defaults 0 2
# UUID=5678-abcd none swap defaults 0 0
```

### **Agregar Montajes Permanentes**

```bash
# 1. Obtener UUID del dispositivo
blkid /dev/sdb1

# 2. Crear punto de montaje
sudo mkdir -p /mnt/datos

# 3. Agregar línea a fstab
echo "UUID=tu-uuid-aqui /mnt/datos ext4 defaults 0 2" | sudo tee -a /etc/fstab

# 4. Probar configuración sin reiniciar
sudo mount -a

# 5. Verificar que funciona
df -h /mnt/datos
```

### **Verificar fstab**

```bash
# Probar configuración de fstab
sudo mount -a

# Si hay errores, aparecerán aquí
# Corregir antes de reiniciar

# Ver qué se montó
findmnt

# Verificar montajes específicos
findmnt /boot
findmnt /home
```

## **Montaje de Recursos de Red**

### **NFS (Network File System)**

```bash
# 1. Instalar cliente NFS
sudo pacman -S nfs-utils

# 2. Crear punto de montaje
sudo mkdir -p /mnt/nfs

# 3. Montar recurso NFS
sudo mount -t nfs servidor.ejemplo.com:/ruta/compartida /mnt/nfs

# 4. Para montaje permanente en fstab:
# servidor.ejemplo.com:/ruta/compartida /mnt/nfs nfs defaults 0 0
```

### **SMB/CIFS (Windows Share)**

```bash
# 1. Instalar cliente SMB
sudo pacman -S cifs-utils

# 2. Crear punto de montaje
sudo mkdir -p /mnt/windows-share

# 3. Montar recurso SMB
sudo mount -t cifs //servidor/compartida /mnt/windows-share -o username=usuario

# 4. Con credenciales en archivo
echo "username=usuario" > ~/.smbcredentials
echo "password=contraseña" >> ~/.smbcredentials
chmod 600 ~/.smbcredentials

sudo mount -t cifs //servidor/compartida /mnt/windows-share -o credentials=/home/usuario/.smbcredentials
```

## **Troubleshooting**

### **Problemas Comunes de Montaje**

```bash
# Dispositivo ocupado
# Error: mount: /dev/sdb1: device is busy

# Solución: verificar qué lo está usando
lsof /dev/sdb1
fuser -v /dev/sdb1

# Terminar procesos que lo usan
sudo fuser -k /dev/sdb1

# O identificar punto de montaje actual
findmnt /dev/sdb1
```

### **Permisos Incorrectos**

```bash
# Si no puedes escribir en dispositivo montado:

# 1. Verificar permisos del punto de montaje
ls -ld /mnt/usb

# 2. Remontar con opciones de usuario
sudo umount /mnt/usb
sudo mount -o uid=$(id -u),gid=$(id -g) /dev/sdb1 /mnt/usb

# 3. Para NTFS específicamente:
sudo mount -t ntfs-3g -o uid=$(id -u),gid=$(id -g),umask=0022 /dev/sdb1 /mnt/usb
```

### **Sistema de Archivos Corrupto**

```bash
# Si hay errores de montaje por corrupción:

# 1. Verificar sistema de archivos
sudo fsck /dev/sdb1

# 2. Para NTFS usar ntfsfix
sudo ntfsfix /dev/sdb1

# 3. Montar en modo solo lectura para recuperar datos
sudo mount -o ro /dev/sdb1 /mnt/recovery
```

## **Comandos Relacionados**

### **umount - Desmontar**

```bash
# Desmontar por punto de montaje
sudo umount /mnt/usb

# Desmontar por dispositivo
sudo umount /dev/sdb1

# Forzar desmontaje (cuidado)
sudo umount -f /mnt/problemático

# Lazy unmount (desmontar cuando sea posible)
sudo umount -l /mnt/ocupado
```

### **findmnt - Información de Montajes**

```bash
# Ver árbol de montajes
findmnt

# Información específica
findmnt /
findmnt /boot

# En formato tabla
findmnt -D

# Buscar por tipo
findmnt -t ext4
```

### **lsblk - Dispositivos de Bloque**

```bash
# Ver todos los dispositivos
lsblk

# Con información de filesystem
lsblk -f

# Con UUIDs
lsblk -o NAME,UUID,MOUNTPOINT

# Información específica
lsblk /dev/sda
```

## **Mejores Prácticas**

### **Organización de Puntos de Montaje**

```bash
# Estructura recomendada:
/mnt/          # Montajes temporales
/mnt/usb/      # Dispositivos USB
/mnt/external/ # Discos externos
/mnt/network/  # Recursos de red
/media/        # Montajes automáticos (algunos entornos de escritorio)
```

### **Seguridad en Montajes**

```bash
# Para dispositivos externos, usar opciones restrictivas:
sudo mount -o noexec,nodev,nosuid /dev/sdb1 /mnt/external

# Para recursos de red, usar ro si no necesitas escribir:
sudo mount -o ro -t nfs servidor:/compartida /mnt/nfs

# Siempre desmontar dispositivos extraíbles antes de retirarlos:
sudo umount /mnt/usb
# Esperar a que termine antes de retirar USB
```

## **Enlaces Relacionados**

- [[lsblk]] - Ver dispositivos de bloque
- [[df]] - Ver espacio en dispositivos montados
- [[du]] - Analizar uso de espacio en montajes
- [[fdisk]] - Gestionar particiones antes de montar
- [[systemctl]] - Gestionar servicios de montaje automático
- [[fstab]] - Configuración permanente de montajes