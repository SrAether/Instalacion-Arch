# **cfdisk - Editor de Particiones Interactivo**

## **¿Qué es cfdisk?**

`cfdisk` es un editor de particiones interactivo basado en texto que proporciona una interfaz visual amigable para crear, eliminar y modificar particiones de disco. Es una alternativa más intuitiva a `fdisk`, especialmente útil para usuarios que prefieren una interfaz de menú en lugar de comandos de texto.

En Arch Linux, cfdisk es la herramienta recomendada para principiantes en el particionado de discos durante la instalación, ya que combina la potencia de las herramientas de línea de comandos con una interfaz visual clara y fácil de navegar.

## **Ventajas de cfdisk sobre fdisk**

### **Interfaz Visual**
- **📊 Vista de tabla:** Muestra todas las particiones en formato tabular
- **🎯 Navegación intuitiva:** Uso de flechas para navegar
- **📋 Menú de opciones:** Comandos claramente etiquetados
- **⚡ Vista en tiempo real:** Cambios visibles inmediatamente

### **Facilidad de Uso**
- **🎮 Control simple:** Flechas arriba/abajo para seleccionar particiones
- **📝 Etiquetas claras:** Comandos con nombres descriptivos
- **✅ Confirmación visual:** Estados de partición claramente indicados
- **🛡️ Seguridad:** No escribe cambios hasta confirmación explícita

## **Sintaxis y Uso Básico**

```bash
# Sintaxis básica
cfdisk [dispositivo]

# Ejemplos comunes
cfdisk /dev/sda    # Disco SATA principal
cfdisk /dev/nvme0n1 # Disco NVMe
cfdisk             # Seleccionar disco interactivamente
```

## **Interfaz de cfdisk**

### **Elementos de la Pantalla**

```
┌─ Dispositivo: /dev/sda ────────────────────────────────────────────────┐
│                                                                        │
│     Dispositivo       Boot      Inicio        Fin      Sectores  Tamaño│
│ /dev/sda1                          2048       1050623      1048576   512M│
│ /dev/sda2                       1050624       5244927      4194304     2G│
│ /dev/sda3                       5244928      62914559     57669632  27.5G│
│                                                                        │
│                        [ Espacio libre: 37.5 GB ]                     │
│                                                                        │
└────────────────────────────────────────────────────────────────────────┘
    [  Nuevo  ] [  Salir  ] [  Ayuda  ] [  Escribir ] [  Abandonar ]
```

### **Navegación y Controles**

- **↑/↓**: Navegar entre particiones
- **Enter**: Seleccionar opción del menú
- **Tab**: Cambiar entre particiones y menú
- **q**: Salir sin guardar
- **h**: Mostrar ayuda

## **Creación de Particiones Paso a Paso**

### **Esquema Típico para Arch Linux UEFI**

```bash
# 1. Iniciar cfdisk
cfdisk /dev/sda

# 2. Crear tabla de particiones GPT (si es necesario)
# Seleccionar: [ gpt ] cuando aparezca el selector de tipo

# 3. Crear partición EFI
# Navegar a espacio libre → [ Nuevo ]
# Tamaño: 512M
# Tipo: EFI System

# 4. Crear partición Swap
# Navegar a espacio libre → [ Nuevo ]
# Tamaño: 2G (o 1-2x tu RAM)
# Tipo: Linux swap

# 5. Crear partición raíz
# Navegar a espacio libre → [ Nuevo ]
# Tamaño: [resto del espacio]
# Tipo: Linux filesystem

# 6. Escribir cambios
# [ Escribir ] → confirmar con "yes"
```

### **Esquema para BIOS/Legacy**

```bash
# 1. Iniciar cfdisk
cfdisk /dev/sda

# 2. Crear tabla MBR/DOS (si es necesario)
# Seleccionar: [ dos ] cuando aparezca

# 3. Crear partición Swap
# [ Nuevo ] → 2G → Linux swap

# 4. Crear partición raíz
# [ Nuevo ] → [resto] → Linux filesystem

# 5. Marcar partición raíz como booteable
# Seleccionar partición raíz → [ Booteable ]

# 6. Escribir cambios
# [ Escribir ] → "yes"
```

## **Tipos de Partición Disponibles**

### **Sistemas UEFI (GPT)**

| Tipo | Código | Uso en Arch Linux |
|------|--------|------------------|
| EFI System | ef00 | Partición de arranque UEFI |
| Linux filesystem | 8300 | Sistema de archivos raíz y home |
| Linux swap | 8200 | Memoria de intercambio |
| Linux extended boot | ea00 | /boot extendido (casos especiales) |

### **Sistemas BIOS (MBR)**

| Tipo | Código | Uso en Arch Linux |
|------|--------|------------------|
| Linux | 83 | Sistema de archivos raíz y home |
| Linux swap | 82 | Memoria de intercambio |
| Extended | 05 | Partición extendida (para más de 4 particiones) |

## **Ejemplos Prácticos Detallados**

### **Instalación Básica UEFI**

```bash
# Objetivo: Crear esquema básico para Arch Linux
# Disco: 30GB
# Esquema: EFI (512M) + Swap (2G) + Root (resto)

cfdisk /dev/sda

# Pasos en cfdisk:
# 1. [Nuevo] → 512M → Cambiar tipo a "EFI System"
# 2. [Nuevo] → 2G → Cambiar tipo a "Linux swap"  
# 3. [Nuevo] → [resto] → "Linux filesystem" (por defecto)
# 4. [Escribir] → "yes"
# 5. [Salir]
```

### **Instalación con Home Separado**

```bash
# Objetivo: Separar /home del sistema
# Disco: 100GB
# Esquema: EFI (512M) + Swap (4G) + Root (30G) + Home (resto)

cfdisk /dev/sda

# Pasos detallados:
# 1. EFI: [Nuevo] → 512M → Tipo: EFI System
# 2. Swap: [Nuevo] → 4G → Tipo: Linux swap
# 3. Root: [Nuevo] → 30G → Tipo: Linux filesystem
# 4. Home: [Nuevo] → [resto] → Tipo: Linux filesystem
# 5. [Escribir] → "yes"
```

### **Dual Boot con Windows**

```bash
# Escenario: Windows ya instalado, agregar Arch Linux
# Supuesto: Windows ocupa los primeros 100GB

cfdisk /dev/sda

# Situación típica encontrada:
# /dev/sda1 - 100M - Windows Recovery
# /dev/sda2 - 16M  - Microsoft reserved  
# /dev/sda3 - 100G - Windows (C:)
# [Espacio libre: 400GB]

# Crear particiones para Arch:
# 1. [Nuevo] en espacio libre → 2G → Linux swap
# 2. [Nuevo] en espacio libre → [resto] → Linux filesystem
# 3. [Escribir] → "yes"

# Nota: EFI ya existe (compartida con Windows)
```

## **Modificación de Particiones Existentes**

### **Cambiar Tipos de Partición**

```bash
# En cfdisk:
# 1. Navegar a la partición
# 2. Seleccionar [Tipo]
# 3. Elegir nuevo tipo de la lista
# 4. [Escribir] para confirmar

# Tipos comunes a cambiar:
# - Linux filesystem → Linux swap
# - Microsoft basic data → Linux filesystem
```

### **Eliminar Particiones**

```bash
# En cfdisk:
# 1. Navegar a la partición a eliminar
# 2. Seleccionar [Eliminar]
# 3. Confirmar
# 4. [Escribir] para aplicar cambios

# ⚠️ ADVERTENCIA: Esto eliminará todos los datos
```

### **Redimensionar Particiones**

```bash
# cfdisk no puede redimensionar particiones directamente
# Proceso necesario:
# 1. Respaldar datos de la partición
# 2. Eliminar partición en cfdisk
# 3. Crear nueva partición con tamaño deseado
# 4. Restaurar datos

# Alternativa: usar parted/gparted para redimensionar
```

## **Integración con Instalación de Arch**

### **Flujo Completo de Instalación**

```bash
# 1. Verificar tipo de firmware
ls /sys/firmware/efi/efivars  # UEFI si existe

# 2. Identificar discos
lsblk

# 3. Particionar con cfdisk
cfdisk /dev/sda

# 4. Verificar particiones creadas
lsblk /dev/sda

# 5. Formatear particiones
mkfs.fat -F32 /dev/sda1      # EFI
mkswap /dev/sda2             # Swap
mkfs.ext4 /dev/sda3          # Root

# 6. Montar para instalación
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/sda2

# 7. Continuar instalación
pacstrap /mnt base linux linux-firmware
```

### **Verificación Post-Particionado**

```bash
# Verificar tabla de particiones
fdisk -l /dev/sda

# Verificar estructura con lsblk
lsblk -f /dev/sda

# Verificar que las particiones son detectadas
ls /dev/sda*

# Verificar tipos de partición
blkid /dev/sda1
blkid /dev/sda2
blkid /dev/sda3
```

## **Troubleshooting y Problemas Comunes**

### **Problemas de Acceso**

```bash
# Error: No se puede abrir el dispositivo
# Causa: Permisos o dispositivo en uso

# Solución 1: Usar sudo
sudo cfdisk /dev/sda

# Solución 2: Verificar que no esté montado
umount /dev/sda*
cfdisk /dev/sda

# Solución 3: Verificar nombre correcto del dispositivo
lsblk  # Confirmar nombre del disco
```

### **Problemas de Tabla de Particiones**

```bash
# Error: Tabla de particiones no reconocida
# Solución: Crear nueva tabla

# En cfdisk, cuando aparezca selector:
# - Para UEFI: seleccionar "gpt"
# - Para BIOS: seleccionar "dos"

# ⚠️ Esto eliminará todas las particiones existentes
```

### **Problemas de Espacio**

```bash
# Error: No hay espacio suficiente
# Verificar espacio disponible en cfdisk

# Soluciones:
# 1. Eliminar particiones innecesarias
# 2. Reducir tamaño de otras particiones
# 3. Usar disco diferente

# Ver uso actual del disco
df -h
lsblk -f
```

### **Errores al Escribir**

```bash
# Error: No se pueden escribir cambios
# Causas comunes:

# 1. Dispositivo en uso
umount /dev/sda*
swapoff /dev/sda2  # si swap está activo

# 2. Permisos insuficientes
sudo cfdisk /dev/sda

# 3. Disco de solo lectura
# Verificar hardware o controladora RAID
```

## **Comparación con Otras Herramientas**

### **cfdisk vs fdisk**

| Aspecto | cfdisk | fdisk |
|---------|--------|-------|
| **Interfaz** | Visual/Menú | Comandos de texto |
| **Facilidad** | Principiantes | Usuarios avanzados |
| **Velocidad** | Moderada | Rápida (expertos) |
| **Funciones** | Básicas | Completas |
| **Automatización** | No | Sí (scripts) |

### **cfdisk vs parted**

| Aspecto | cfdisk | parted |
|---------|--------|--------|
| **Redimensionar** | No | Sí |
| **Tipos soportados** | MBR, GPT | MBR, GPT, otros |
| **Interfaz** | Solo visual | Comandos + GUI |
| **Complejidad** | Simple | Avanzada |

### **Cuándo Usar Cada Herramienta**

```bash
# Usar cfdisk cuando:
# - Eres principiante en Linux
# - Instalación básica de Arch Linux
# - Prefieres interfaz visual
# - Operaciones simples de particionado

# Usar fdisk cuando:
# - Necesitas automatizar con scripts
# - Operaciones avanzadas
# - Recursos del sistema limitados
# - Experiencia en línea de comandos

# Usar parted cuando:
# - Necesitas redimensionar particiones
# - Trabajas con múltiples tipos de tabla
# - Operaciones complejas de disco
```

## **Consejos y Mejores Prácticas**

### **Antes de Particionar**

```bash
# 1. Respaldar datos importantes
# 2. Verificar disco objetivo
lsblk
fdisk -l

# 3. Planificar esquema de particiones
# Esquema recomendado para Arch Linux:
# - EFI: 512MB (UEFI systems)
# - Swap: 1-2x RAM (máximo 8GB)
# - Root: 20-30GB mínimo
# - Home: Resto (si se separa)

# 4. Verificar tipo de firmware
ls /sys/firmware/efi/efivars  # UEFI si existe
```

### **Durante el Particionado**

```bash
# 1. Verificar constantemente la tabla
# Usar [Vista previa] o revisar columnas

# 2. No escribir hasta estar seguro
# Revisar todas las particiones antes de [Escribir]

# 3. Anotar el esquema creado
# Ejemplo:
# /dev/sda1 - 512M - EFI System
# /dev/sda2 - 2G   - Linux swap  
# /dev/sda3 - 28G  - Linux filesystem (/)
```

### **Después del Particionado**

```bash
# 1. Verificar particiones creadas
lsblk /dev/sda
fdisk -l /dev/sda

# 2. Formatear inmediatamente
mkfs.fat -F32 /dev/sda1  # EFI
mkswap /dev/sda2         # Swap
mkfs.ext4 /dev/sda3      # Root

# 3. Probar montaje
mount /dev/sda3 /mnt     # Debe funcionar sin errores
```

### **Configuraciones Recomendadas**

```bash
# Esquema mínimo (20GB+):
# EFI: 512M
# Swap: 2G
# Root: Resto

# Esquema estándar (50GB+):  
# EFI: 512M
# Swap: 4G
# Root: 30G
# Home: Resto

# Esquema avanzado (100GB+):
# EFI: 512M
# Boot: 1G (opcional)
# Swap: 8G
# Root: 30G
# Home: Resto
```

## **Automatización y Scripts**

### **Verificación Pre-Particionado**

```bash
#!/bin/bash
# Verificar sistema antes de usar cfdisk

echo "=== Verificación del Sistema ==="

# Verificar tipo de firmware
if [[ -d /sys/firmware/efi/efivars ]]; then
    echo "✅ Sistema UEFI detectado"
    echo "   → Usar tabla GPT en cfdisk"
else
    echo "✅ Sistema BIOS detectado"
    echo "   → Usar tabla DOS/MBR en cfdisk"
fi

# Mostrar discos disponibles
echo -e "\n=== Discos Disponibles ==="
lsblk -d -o NAME,SIZE,MODEL

# Verificar particiones montadas
echo -e "\n=== Particiones Montadas ==="
mount | grep "^/dev/" | cut -d' ' -f1,3

echo -e "\n🎯 Listo para usar cfdisk"
echo "   Comando: cfdisk /dev/sdX"
```

### **Verificación Post-Particionado**

```bash
#!/bin/bash
# Verificar particiones después de cfdisk

DISK="$1"

if [[ -z "$DISK" ]]; then
    echo "Uso: $0 /dev/sdX"
    exit 1
fi

echo "=== Verificación de Particiones ==="
echo "Disco: $DISK"
echo

# Mostrar tabla de particiones
echo "--- Tabla de Particiones ---"
fdisk -l "$DISK"

echo -e "\n--- Estructura con lsblk ---"
lsblk "$DISK"

echo -e "\n--- IDs de Partición ---"
blkid "${DISK}"*

echo -e "\n✅ Verificación completada"
echo "   Siguiente paso: formatear particiones"
```

## **Seguridad y Precauciones**

### **⚠️ Advertencias Importantes**

- **Respaldo obligatorio:** Siempre respalda datos importantes antes de particionar
- **Verificación de disco:** Confirma que estás modificando el disco correcto
- **No escribir prematuramente:** Revisa todo antes de usar [Escribir]
- **Particiones activas:** Desmonta y desactiva swap antes de modificar

### **Procedimiento Seguro**

```bash
# 1. Verificar disco objetivo
lsblk
echo "¿Modificar $DISK? (y/N)"
read -r confirm

# 2. Desmontar particiones existentes
umount "${DISK}"* 2>/dev/null
swapoff "${DISK}"* 2>/dev/null

# 3. Respaldar tabla de particiones actual
sfdisk -d "$DISK" > "partition_backup_$(date +%Y%m%d).txt"

# 4. Usar cfdisk
cfdisk "$DISK"

# 5. Verificar resultado
fdisk -l "$DISK"
```

---

**cfdisk es la herramienta ideal para principiantes en el particionado de discos durante la instalación de Arch Linux, combinando potencia y facilidad de uso en una interfaz visual intuitiva.**