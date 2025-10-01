El comando `rm` es tu **eliminador** de archivos y directorios. Es una herramienta poderosa pero peligrosa: una vez que eliminas algo con `rm`, **no hay papelera de reciclaje**. Los archivos se borran permanentemente del sistema.

---
### ¿Qué Hace RM? 🗑️
**RM** significa **"Remove"** (Eliminar). Su función es eliminar archivos y directorios del sistema de archivos de forma permanente.

⚠️ **ADVERTENCIA CRÍTICA**: `rm` elimina archivos PERMANENTEMENTE. No hay función "deshacer".

### Sintaxis Básica
```bash
rm [opciones] archivo(s)
rm [opciones] directorio(s)
```

### Eliminar Archivos Básico
```bash
# Eliminar un archivo
rm archivo.txt

# Eliminar múltiples archivos
rm archivo1.txt archivo2.txt archivo3.txt

# Eliminar archivos con patrón
rm *.tmp
rm backup_*
rm archivo?.txt    # archivo1.txt, archivo2.txt, etc.
```

### En el Contexto de la Instalación de Arch Linux

#### **Limpieza Post-Instalación**
```bash
# Eliminar archivos temporales de instalación
rm /tmp/arch-install.log
rm /tmp/*.tmp
rm /var/cache/pacman/pkg/*.pkg.tar.xz.old

# Limpiar descargas de paquetes
rm ~/archiso-*.iso
rm ~/firmware-*.tar.gz
```

#### **Limpiar Configuraciones de Prueba**
```bash
# Eliminar configuraciones experimentales
rm ~/.config/test-config.conf
rm /etc/systemd/system/experimental.service
rm ~/.bashrc.testing
```

#### **Mantenimiento del Sistema**
```bash
# Limpiar logs antiguos
rm /var/log/*.log.old
rm ~/.cache/*/*.log

# Eliminar archivos de backup automático
rm ~/.vimrc~
rm ~/.bashrc.bak
```

#### **Liberar Espacio en Disco**
```bash
# Limpiar caché de aplicaciones
rm -rf ~/.cache/thumbnails/*
rm ~/.local/share/Trash/files/*
rm /tmp/*
```

### Opciones Críticas de Seguridad

#### **`-i` (Interactive) - SIEMPRE RECOMENDADO**
```bash
# Pregunta antes de eliminar cada archivo
rm -i archivo.txt
# rm: ¿eliminar archivo regular 'archivo.txt'? (y/n)

# Especialmente importante con patrones
rm -i *.txt
```

#### **`-I` (Interactive-once) - Para Múltiples Archivos**
```bash
# Pregunta una vez si hay más de 3 archivos
rm -I *.log
# rm: ¿eliminar 15 argumentos? (y/n)
```

#### **`-f` (Force) - ⚠️ PELIGROSO**
```bash
# Elimina sin preguntar, incluso archivos protegidos
rm -f archivo_protegido.txt

# ⚠️ NUNCA uses: rm -rf / 
# ⚠️ Esto eliminaría TODO el sistema
```

### Trabajar con Directorios

#### **`-r` o `-R` (Recursive) - Para Directorios**
```bash
# Eliminar directorio vacío (también funciona rmdir)
rm -r directorio_vacio/

# Eliminar directorio con contenido
rm -r directorio_con_archivos/

# Eliminar múltiples directorios
rm -r dir1/ dir2/ dir3/
```

#### **Combinación Común (¡PELIGROSA!)**
```bash
# ⚠️ EXTREMADAMENTE PELIGROSO
rm -rf directorio/          # Elimina recursivamente, sin preguntar

# ✅ VERSIÓN SEGURA
rm -ri directorio/          # Elimina recursivamente, preguntando
```

### Eliminar Archivos Específicos

#### **Por Tipo de Archivo**
```bash
# Eliminar archivos temporales
rm *.tmp *.temp *.cache

# Eliminar archivos de backup
rm *.bak *.backup *~

# Eliminar archivos compilados
rm *.o *.obj *.class

# Eliminar archivos de log
rm *.log *.log.*
```

#### **Por Antigüedad (con find)**
```bash
# Eliminar archivos más antiguos de 30 días
find /tmp -type f -mtime +30 -exec rm {} \;

# Eliminar archivos más antiguos de 7 días en descargas
find ~/Descargas -type f -mtime +7 -exec rm -i {} \;
```

#### **Por Tamaño**
```bash
# Eliminar archivos grandes (más de 100MB)
find . -type f -size +100M -exec rm -i {} \;

# Eliminar archivos vacíos
find . -type f -empty -delete
```

### Casos de Uso Específicos en Arch Linux

#### **1. Limpiar Caché de Pacman**
```bash
# Eliminar paquetes antiguos del caché
sudo pacman -Sc                      # Limpieza estándar
rm /var/cache/pacman/pkg/*.pkg.tar.xz.old

# Limpiar completamente (cuidado)
sudo pacman -Scc                     # Elimina TODO el caché
```

#### **2. Eliminar Kernels Antiguos**
```bash
# Listar kernels instalados
ls /boot/vmlinuz-*

# Eliminar kernel específico (cuidado)
sudo rm /boot/vmlinuz-linux-lts-old
sudo rm /boot/initramfs-linux-lts-old.img
```

#### **3. Limpiar Configuraciones Huérfanas**
```bash
# Eliminar configuraciones de paquetes desinstalados
find ~/.config -name "*app-desinstalada*" -exec rm -ri {} \;

# Limpiar enlaces simbólicos rotos
find /usr/bin -type l ! -exec test -e {} \; -exec rm {} \;
```

#### **4. Eliminar Archivos de Instalación**
```bash
# Después de instalación exitosa
rm ~/archiso-latest.iso
rm -r ~/arch-installation-scripts/
rm /tmp/arch-install-*
```

### Patrones Avanzados de Eliminación

#### **Eliminar Excepto Ciertos Archivos**
```bash
# Eliminar todo excepto archivos específicos
rm !(importante.txt|config.conf)

# Usando find para mayor control
find . -type f ! -name "importante.txt" ! -name "*.conf" -delete
```

#### **Eliminar Archivos con Nombres Especiales**
```bash
# Archivos que empiezan con guión
rm -- -archivo-extraño.txt
rm ./-archivo-con-guion.txt

# Archivos con espacios
rm "archivo con espacios.txt"
rm archivo\ con\ espacios.txt
```

#### **Eliminar Archivos Ocultos**
```bash
# Eliminar archivos ocultos específicos
rm .archivo_oculto

# Eliminar todos los archivos ocultos (CUIDADO)
rm .*                    # ⚠️ Incluye . y .. (problemático)
rm .[^.]*               # ✅ Mejor: excluye . y ..
```

### Seguridad y Recuperación

#### **Crear Función RM Segura (Recomendado)**
```bash
# Agregar a ~/.bashrc
alias rm='rm -i'                    # Siempre pregunta
alias rmd='rm -ri'                  # Para directorios

# Función que mueve a "papelera" en lugar de eliminar
trash() {
    mkdir -p ~/.local/share/Trash/files
    mv "$@" ~/.local/share/Trash/files/
}
```

#### **Backup Antes de Eliminar**
```bash
# Función segura que hace backup
safe_rm() {
    mkdir -p ~/rm_backup/$(date +%Y%m%d)
    cp -r "$@" ~/rm_backup/$(date +%Y%m%d)/
    rm -ri "$@"
}
```

#### **Verificar Antes de Eliminar**
```bash
# Ver qué se va a eliminar
ls -la archivo*                     # Ver archivos que coinciden
find . -name "*.tmp" -ls            # Ver archivos temporales

# Contar archivos antes de eliminar
ls *.log | wc -l                    # ¿Cuántos logs hay?
```

### Recuperación de Archivos Eliminados

#### **Herramientas de Recuperación**
```bash
# Instalar herramientas de recuperación
sudo pacman -S testdisk photorec

# Usar extundelete para ext4
sudo pacman -S extundelete
sudo extundelete /dev/sda1 --restore-file archivo_eliminado.txt

# Para archivos recién eliminados, detener escritura inmediatamente
sudo sync                           # Sincronizar buffers
sudo mount -o remount,ro /          # Montar como solo lectura
```

#### **Prevención de Pérdida de Datos**
```bash
# Configurar snapshots con Btrfs
sudo pacman -S snapper
sudo snapper create-config /

# Usar timeshift para backups automáticos
sudo pacman -S timeshift
```

### Comandos Peligrosos a EVITAR

❌ **NUNCA ejecutes estos comandos:**
```bash
rm -rf /                            # Elimina TODO el sistema
rm -rf /*                           # Elimina todo en la raíz
rm -rf $HOME                        # Elimina tu directorio personal
rm -rf ~/.                          # Elimina archivos de configuración
sudo rm -rf /*                      # Destrucción total con permisos de root
```

❌ **Variables peligrosas:**
```bash
# Si $VAR está vacía, esto se convierte en "rm -rf /"
rm -rf $VAR/                        # ⚠️ Verificar variable primero

# ✅ Versión segura:
if [ -n "$VAR" ] && [ -d "$VAR" ]; then
    rm -rf "$VAR/"
fi
```

### Errores Comunes y Soluciones

❌ **Error: Archivo protegido contra escritura**
```bash
rm archivo_protegido.txt
# rm: cannot remove 'archivo_protegido.txt': Permission denied
```
✅ **Solución:**
```bash
chmod +w archivo_protegido.txt && rm archivo_protegido.txt
# O usar force (cuidado):
rm -f archivo_protegido.txt
```

❌ **Error: Directorio no vacío**
```bash
rm directorio/
# rm: cannot remove 'directorio/': Is a directory
```
✅ **Solución:**
```bash
rm -r directorio/                   # Para directorios con contenido
rmdir directorio/                   # Solo para directorios vacíos
```

❌ **Error: Archivo en uso**
```bash
rm archivo_en_uso.log
# rm: cannot remove 'archivo_en_uso.log': Device or resource busy
```
✅ **Solución:**
```bash
lsof archivo_en_uso.log            # Ver qué proceso lo usa
# Cerrar el proceso o:
sudo fuser -k archivo_en_uso.log   # Terminar procesos que lo usan
```

### Alternativas Más Seguras

#### **1. Usar Papelera de Reciclaje**
```bash
# Instalar trash-cli
sudo pacman -S trash-cli

# Usar en lugar de rm
trash archivo.txt                   # Mueve a papelera
trash-list                          # Ver archivos en papelera
trash-restore                       # Restaurar archivos
trash-empty                         # Vaciar papelera
```

#### **2. Mover en Lugar de Eliminar**
```bash
# Crear directorio de "eliminados"
mkdir -p ~/.deleted/$(date +%Y%m%d)

# Mover en lugar de eliminar
mv archivo_viejo.txt ~/.deleted/$(date +%Y%m%d)/
```

#### **3. Usar Herramientas de Backup**
```bash
# rsync con --backup-dir
rsync -av --backup-dir=~/backup/ origen/ destino/

# cp con backup automático
cp --backup=numbered archivo.txt archivo.txt.backup
```

### Buenas Prácticas

#### **1. SIEMPRE usar -i en scripts importantes**
```bash
#!/bin/bash
# En scripts, usar confirmación
rm -i "$file_to_delete"
```

#### **2. Verificar antes de ejecutar**
```bash
# Ver qué archivos se eliminarían
ls *.tmp                           # Ver archivos primero
read -p "¿Eliminar estos archivos? (y/n): " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    rm *.tmp
fi
```

#### **3. Usar aliases de seguridad**
```bash
# En ~/.bashrc
alias rm='rm -i'
alias del='rm -i'
alias remove='rm -ri'

# Para expertos que quieren fuerza real
alias rmf='command rm -f'
alias rmrf='command rm -rf'
```

#### **4. Documentar eliminaciones importantes**
```bash
# Crear log de eliminaciones importantes
echo "$(date): Eliminé directorio $DIR" >> ~/.rm_log
rm -rf "$DIR"
```

---
### Enlaces Relacionados
- [[mv]] - Mover archivos (alternativa a eliminar + recrear)
- [[cp]] - Hacer copias antes de eliminar
- [[ls]] - Verificar archivos antes de eliminar
- [[find]] - Encontrar archivos para eliminar en lote
- [[chmod]] - Cambiar permisos antes de eliminar


