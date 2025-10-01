# lsblk - Listar Dispositivos de Bloque

**lsblk** es tu **explorador de dispositivos** de almacenamiento en Arch Linux. Te muestra todos los dispositivos de bloque (discos, particiones, USB) en un formato de árbol fácil de entender.

## **¿Qué es lsblk?**

lsblk te permite:
- **Ver todos los dispositivos** de almacenamiento conectados
- **Identificar particiones** y su estructura
- **Ver puntos de montaje** de cada dispositivo
- **Obtener información** de tamaño, tipo y formato

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, lsblk es esencial durante la instalación para identificar discos y particiones, y en el uso diario para gestionar dispositivos de almacenamiento externos.

### **Uso Básico**

```bash
# Listar todos los dispositivos de bloque
lsblk

# Con información de sistemas de archivos
lsblk -f

# Con información detallada
lsblk -a

# Dispositivo específico
lsblk /dev/sda
```

## **Interpretación de la Salida**

### **Vista Estándar**

```bash
$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   50G  0 disk 
├─sda1   8:1    0  512M  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0 47.5G  0 part /
sdb      8:16   1    8G  0 disk 
└─sdb1   8:17   1    8G  0 part /mnt/usb
sr0     11:0    1 1024M  0 rom  
```

**Explicación de columnas:**
- **NAME**: Nombre del dispositivo/partición
- **MAJ:MIN**: Números mayor y menor del dispositivo
- **RM**: Removible (1=sí, 0=no)
- **SIZE**: Tamaño del dispositivo
- **RO**: Solo lectura (1=sí, 0=no)
- **TYPE**: Tipo (disk, part, rom, etc.)
- **MOUNTPOINT**: Donde está montado

### **Vista con Sistemas de Archivos**

```bash
$ lsblk -f
NAME   FSTYPE LABEL    UUID                                 MOUNTPOINT
sda                                                         
├─sda1 vfat   BOOT     1234-ABCD                            /boot
├─sda2 swap             wxyz-5678                            [SWAP]
└─sda3 ext4   ROOT     abcd-1234-5678-efgh                  /
sdb                                                         
└─sdb1 ntfs   USB_DATA efgh-9012                            /mnt/usb
```

**Información adicional:**
- **FSTYPE**: Tipo de sistema de archivos (ext4, ntfs, vfat, etc.)
- **LABEL**: Etiqueta del sistema de archivos
- **UUID**: Identificador único del sistema de archivos

## **Opciones Útiles**

### **Información Detallada**

```bash
# Todos los dispositivos, incluso vacíos
lsblk -a

# Información de permisos
lsblk -m

# Mostrar propietarios
lsblk -o NAME,SIZE,OWNER,GROUP,MODE

# Salida en formato JSON
lsblk -J

# Salida sin cabeceras
lsblk -n
```

### **Columnas Personalizadas**

```bash
# Ver columnas disponibles
lsblk --help | grep -A 20 "Available columns"

# Mostrar columnas específicas
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT

# Ejemplo con más información
lsblk -o NAME,SIZE,TYPE,FSTYPE,LABEL,UUID,MOUNTPOINT

# Para dispositivos extraíbles específicamente
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT | grep -E "(disk|part)" | grep -v "├─"
```

## **Casos de Uso Durante Instalación de Arch**

### **Identificar Discos para Instalación**

```bash
# Ver todos los discos disponibles
lsblk

# Identificar el disco principal (generalmente sda)
# Ejemplo de decisión:
# sda (50G) - Disco principal para instalar Arch
# sdb (8G)  - USB de instalación
# sdc (1TB) - Disco adicional para datos

# Verificar que no hay montajes importantes
lsblk -f | grep -v "SWAP\|sr0"
```

### **Después del Particionado**

```bash
# Verificar que las particiones se crearon correctamente
lsblk /dev/sda

# Ejemplo de particionado típico:
# sda      50G  disk
# ├─sda1  512M  part  (futura /boot)
# ├─sda2    2G  part  (futura swap)
# └─sda3 47.5G  part  (futura /)

# Antes de formatear, verificar que las particiones son correctas
echo "¿Proceder con formateo? (Verificar particiones arriba)"
```

### **Verificar Montajes Durante Instalación**

```bash
# Después de montar particiones para instalación
lsblk -f

# Verificar estructura esperada:
# sda1 -> /mnt/boot
# sda3 -> /mnt
# sda2 -> [SWAP]

# Si algo no está correcto, corregir antes de continuar
```

## **Gestión de Dispositivos Externos**

### **USB y Dispositivos Extraíbles**

```bash
# Antes de conectar USB
lsblk

# Conectar USB y verificar
lsblk
# Nuevo dispositivo aparecerá (generalmente sdb, sdc, etc.)

# Identificar particiones del USB
lsblk /dev/sdb

# Ver formato del USB
lsblk -f /dev/sdb

# Ejemplo típico de USB:
# sdb      8G  disk
# └─sdb1   8G  part  ntfs  USB_DATA  /mnt/usb
```

### **Múltiples Discos Duros**

```bash
# Sistema con múltiples discos
lsblk

# Ejemplo de sistema con 3 discos:
# sda - SSD 256GB (sistema)
# sdb - HDD 1TB (datos)
# sdc - HDD 2TB (backup)

# Ver información específica de cada disco
lsblk -f /dev/sda  # Sistema
lsblk -f /dev/sdb  # Datos
lsblk -f /dev/sdc  # Backup
```

## **Información Específica de Dispositivos**

### **Detalles de un Dispositivo**

```bash
# Información completa de un disco
lsblk -a /dev/sda

# Con todos los atributos disponibles
lsblk -o +MODEL,SERIAL,REV /dev/sda

# Información de tamaño en diferentes unidades
lsblk -b /dev/sda  # En bytes
lsblk -h /dev/sda  # Formato legible
```

### **Filtrar por Tipo de Dispositivo**

```bash
# Solo discos principales (no particiones)
lsblk -d

# Solo dispositivos extraíbles
lsblk | grep " 1 "

# Solo dispositivos montados
lsblk | grep -v "^$" | grep -E "/|SWAP"

# Dispositivos sin montar
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT | grep -v "/\|SWAP\|sr0"
```

## **Solución de Problemas**

### **Dispositivo No Aparece**

```bash
# Verificar que el dispositivo está conectado
lsblk -a

# Si USB no aparece, verificar conexión física
dmesg | tail -20

# Forzar re-escaneo de dispositivos SCSI
echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan

# Verificar en nivel más bajo
cat /proc/partitions
```

### **Información de Dispositivo Corrupto**

```bash
# Si lsblk muestra información extraña
lsblk -f /dev/sdb

# Verificar con otras herramientas
fdisk -l /dev/sdb
blkid /dev/sdb

# Para dispositivos con problemas de tabla de particiones
parted /dev/sdb print
```

### **Dispositivo Ocupado**

```bash
# Si no puedes desmontar dispositivo
lsof /dev/sdb1

# Ver qué procesos lo están usando
fuser -v /dev/sdb1

# Información desde lsblk para debug
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,MAJ:MIN /dev/sdb
```

## **Comparación con Otros Comandos**

### **lsblk vs fdisk**

```bash
# lsblk: Vista de árbol, fácil de leer
lsblk

# fdisk: Información técnica detallada
sudo fdisk -l

# Usar lsblk para:
# - Vista general rápida
# - Verificar montajes
# - Identificar dispositivos

# Usar fdisk para:
# - Información detallada de particiones
# - Modificar tablas de particiones
# - Información técnica específica
```

### **lsblk vs df**

```bash
# lsblk: Todos los dispositivos (montados y no montados)
lsblk

# df: Solo sistemas de archivos montados con uso de espacio
df -h

# lsblk muestra estructura física
# df muestra uso de espacio lógico
```

### **lsblk vs blkid**

```bash
# lsblk: Vista estructurada con montajes
lsblk -f

# blkid: Lista UUIDs y tipos de sistemas de archivos
blkid

# lsblk para navegación visual
# blkid para scripts que necesitan UUIDs específicos
```

## **Uso en Scripts**

### **Obtener Información Específica**

```bash
# Obtener dispositivos extraíbles
removable_devices=$(lsblk -rno NAME,RM | awk '$2==1 {print $1}')

# Dispositivos sin montar
unmounted=$(lsblk -rno NAME,MOUNTPOINT | awk '$2=="" {print $1}')

# Obtener UUID de un dispositivo
uuid=$(lsblk -rno UUID /dev/sda1)

# Verificar si dispositivo está montado
if lsblk -rno MOUNTPOINT /dev/sdb1 | grep -q "/"; then
    echo "Dispositivo montado"
else
    echo "Dispositivo no montado"
fi
```

### **Monitoreo Simple**

```bash
# Función para mostrar cambios en dispositivos
watch_devices() {
    echo "Monitoreando dispositivos (Ctrl+C para salir)..."
    while true; do
        clear
        echo "=== Dispositivos de Bloque - $(date) ==="
        lsblk -f
        echo
        echo "Presiona Ctrl+C para salir"
        sleep 5
    done
}

watch_devices
```

## **Información Avanzada**

### **Atributos Extendidos**

```bash
# Información de hardware
lsblk -o NAME,SIZE,MODEL,SERIAL,VENDOR

# Información de E/O
lsblk -o NAME,SIZE,RQ-SIZE,MIN-IO,OPT-IO

# Información de alineación
lsblk -o NAME,SIZE,ALIGNMENT,PHY-SEC,LOG-SEC

# Información de estado
lsblk -o NAME,SIZE,STATE,RAND,ROT
```

### **Formato de Salida Personalizado**

```bash
# Solo nombres de dispositivos
lsblk -rno NAME

# Información tabular sin árbol
lsblk -l

# En formato par=valor
lsblk -P

# Para parsing en scripts
lsblk -rno NAME,SIZE,TYPE,MOUNTPOINT | while read name size type mount; do
    echo "Dispositivo: $name, Tamaño: $size, Tipo: $type, Montado en: $mount"
done
```

## **Enlaces Relacionados**

- [[mount]] - Montar dispositivos identificados con lsblk
- [[fdisk]] - Particionar dispositivos mostrados por lsblk
- [[df]] - Ver uso de espacio en dispositivos montados
- [[blkid]] - Obtener UUIDs y información de sistemas de archivos
- [[free]] - Información de memoria (complementa información de almacenamiento)
- [[du]] - Analizar uso de espacio en dispositivos montados