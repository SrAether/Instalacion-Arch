Una **ruta absoluta** es como dar una dirección postal completa, con país, ciudad, calle y número. En GNU/Linux, es una ruta que **siempre comienza desde la raíz del sistema** (`/`) y especifica la ubicación exacta de un archivo o directorio, sin importar dónde te encuentres actualmente.

---
### ¿Qué Define una Ruta Absoluta? 🌍
Una ruta absoluta **siempre** tiene estas características:
1. **Comienza con `/`** (la raíz del sistema de archivos)
2. **Es única e inequívoca** - siempre apunta al mismo lugar
3. **Funciona desde cualquier ubicación** - no depende de tu directorio actual

### Ejemplos de Rutas Absolutas
```bash
/home/usuario/Documentos/proyecto.txt
/etc/hostname
/usr/bin/ls
/var/log/messages
/mnt/usb/archivo.pdf
/boot/efi/EFI/BOOT/BOOTX64.EFI
```

Todas estas rutas comienzan con `/` y te llevarán al mismo lugar sin importar si estás en `/home/usuario`, `/tmp`, o cualquier otro directorio.

### Ventajas de las Rutas Absolutas
#### **1. Precisión Total**
```bash
# Esto SIEMPRE funcionará, estés donde estés
cat /etc/hostname
ls /home/usuario/Documentos
cd /usr/local/bin
```

#### **2. Claridad en Scripts**
```bash
#!/bin/bash
# Un script siempre sabe exactamente dónde buscar
CONFIG_FILE="/etc/myapp/config.conf"
LOG_DIR="/var/log/myapp"
```

#### **3. Sin Ambigüedad**
Si le dices a alguien "ve a `/home/usuario/proyecto/datos.txt`", sabrá exactamente dónde ir, independientemente de su ubicación actual.

### En el Contexto de la Instalación de Arch Linux

#### **Durante el Particionado ([[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]])**
```bash
# Las rutas de dispositivos son siempre absolutas
cfdisk /dev/sda
mkfs.ext4 /dev/sda1
mount /dev/sda1 /mnt
```

#### **Al Montar Particiones**
```bash
# Puntos de montaje siempre con rutas absolutas
mount /dev/sda1 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda2 /mnt/boot/efi
```

#### **Durante la Instalación ([[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]])**
```bash
# pacstrap necesita la ruta absoluta del punto de montaje
pacstrap /mnt base linux linux-firmware

# genfstab también usa rutas absolutas
genfstab -U /mnt >> /mnt/etc/fstab
```

#### **En Configuración del Sistema ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**
```bash
# Archivos de configuración tienen rutas absolutas conocidas
nano /etc/locale.gen
echo "LANG=es_ES.UTF-8" > /etc/locale.conf
ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
```

### Estructura Típica del Sistema de Archivos
```
/                    (raíz - el punto de partida de toda ruta absoluta)
├── boot/           (archivos de arranque)
├── etc/            (archivos de configuración del sistema)
├── home/           (directorios personales de usuarios)
│   └── usuario/    (tu directorio personal)
├── mnt/            (punto de montaje temporal - usado en instalación)
├── usr/            (programas y librerías de usuario)
│   ├── bin/        (comandos ejecutables)
│   └── local/      (software instalado localmente)
├── var/            (datos variables - logs, cachés)
└── tmp/            (archivos temporales)
```

### Cuándo Usar Rutas Absolutas
✅ **Úsalas cuando:**
- Escribes scripts que deben funcionar desde cualquier lugar
- Referencias archivos de sistema importantes (`/etc/`, `/usr/`, `/var/`)
- Trabajas con dispositivos (`/dev/`)
- Configuras puntos de montaje
- Necesitas precision total sin ambigüedad

❌ **Evítalas cuando:**
- Trabajas en tu propio directorio personal (usa [[Ruta relativa|rutas relativas]])
- Quieres que tu código sea portable entre usuarios
- Las rutas son muy largas y repetitivas

### Comparación Visual
```bash
# Estás en: /home/usuario/Documentos/proyectos

# RUTA ABSOLUTA (funciona desde cualquier lugar)
cat /home/usuario/Documentos/proyectos/archivo.txt

# RUTA RELATIVA (solo funciona desde el directorio actual)
cat archivo.txt
```

---
### Enlaces Relacionados
- [[Ruta relativa]] - Rutas que dependen de tu ubicación actual
- [[pwd]] - Comando que siempre muestra una ruta absoluta
- [[cd]] - Cambiar directorios usando rutas absolutas o relativas
- [[ls]] - Listar contenido usando rutas absolutas