El comando `cp` es tu **duplicadora digital**. Cuando necesitas hacer copias exactas de archivos o directorios, `cp` es la herramienta que toma algo que ya existe y crea una réplica perfecta en la ubicación que especifiques.

---
### ¿Qué Hace CP? 📋
**CP** significa **"Copy"** (Copiar). Su función es crear duplicados exactos de archivos o directorios, manteniendo el original intacto mientras crea una copia en una nueva ubicación.

### Sintaxis Básica
```bash
cp [opciones] origen destino
cp [opciones] origen1 origen2 origen3... directorio_destino
```

### Uso Básico - Copiar Archivos
```bash
# Copiar un archivo a la misma ubicación con nuevo nombre
cp archivo.txt archivo_copia.txt

# Copiar archivo a otro directorio
cp archivo.txt /home/usuario/Documentos/

# Copiar archivo a otro directorio con nuevo nombre
cp archivo.txt /home/usuario/Documentos/nuevo_nombre.txt
```

### Copiar Múltiples Archivos
```bash
# Copiar varios archivos a un directorio
cp archivo1.txt archivo2.txt archivo3.txt ~/Documentos/

# Copiar archivos con patrón
cp *.txt ~/backup/
cp imagen*.jpg ~/fotos/backup/
```

### Copiar Directorios
#### **Opción `-r` (Recursive) - Indispensable para Directorios**
```bash
# Copiar directorio completo con todo su contenido
cp -r mi-proyecto/ mi-proyecto-backup/

# Copiar directorio a otra ubicación
cp -r ~/Documentos/proyecto/ /backup/proyectos/
```

### En el Contexto de la Instalación de Arch Linux

#### **Hacer Backup de Configuraciones Importantes**
```bash
# Backup de archivos críticos antes de modificar
cp /etc/fstab /etc/fstab.backup
cp /etc/mkinitcpio.conf /etc/mkinitcpio.conf.backup
cp ~/.bashrc ~/.bashrc.backup
```

#### **Copiar Configuraciones de Referencia**
```bash
# Copiar configuraciones de ejemplo
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
cp /etc/locale.gen /etc/locale.gen.backup
```

#### **Durante la Post-Instalación**
```bash
# Copiar dotfiles personalizados
cp ~/dotfiles/.bashrc ~/
cp ~/dotfiles/.vimrc ~/
cp -r ~/dotfiles/.config/ ~/
```

#### **Backup del Sistema Instalado**
```bash
# Hacer backup de configuraciones importantes
cp -r /etc/ /backup/system-config-$(date +%Y%m%d)/
cp /boot/grub/grub.cfg /backup/grub-backup.cfg
```

### Opciones Importantes

#### **`-r` o `-R` (Recursive) - Para Directorios**
```bash
# Necesario para copiar directorios y su contenido
cp -r directorio_origen/ directorio_destino/
```

#### **`-i` (Interactive) - Confirmar Sobrescritura**
```bash
# Pregunta antes de sobrescribir archivos existentes
cp -i archivo.txt /destino/
# ¿Sobrescribir '/destino/archivo.txt'? (y/n)
```

#### **`-u` (Update) - Solo Copiar si es Más Nuevo**
```bash
# Solo copia si el archivo origen es más nuevo que el destino
cp -u archivo_modificado.txt /backup/
```

#### **`-v` (Verbose) - Mostrar Qué Se Está Copiando**
```bash
# Muestra cada archivo que se copia
cp -rv directorio_origen/ directorio_destino/
# 'directorio_origen/archivo1.txt' -> 'directorio_destino/archivo1.txt'
# 'directorio_origen/archivo2.txt' -> 'directorio_destino/archivo2.txt'
```

#### **`-p` (Preserve) - Mantener Atributos**
```bash
# Preserva propietario, permisos, timestamps
cp -p archivo_importante.conf /etc/backup/
```

#### **`-a` (Archive) - Combinación Perfecta**
```bash
# Equivale a -dR --preserve=all (recursivo + preservar todo)
cp -a directorio_completo/ backup_completo/
```

### Patrones de Uso Avanzados

#### **Backup con Estructura de Fechas**
```bash
# Crear backup con fecha actual
BACKUP_DIR="/backup/$(date +%Y-%m-%d)"
mkdir -p "$BACKUP_DIR"
cp -a ~/proyectos/ "$BACKUP_DIR/"
```

#### **Copiar Solo Ciertos Tipos de Archivo**
```bash
# Copiar solo archivos de configuración
cp ~/.{bashrc,vimrc,gitconfig} ~/dotfiles/

# Copiar archivos por extensión
cp ~/Documentos/*.pdf ~/backup/pdfs/
cp ~/proyectos/*/{*.py,*.js,*.md} ~/código-backup/
```

#### **Crear Estructura de Directorios Igual**
```bash
# Copiar estructura preservando jerarquía
cp -a /etc/systemd/ ~/backup/systemd-config/
cp -a ~/.config/ ~/dotfiles/config-backup/
```

### Casos de Uso Específicos

#### **1. Gestión de Dotfiles**
```bash
# Copiar configuraciones personales a repositorio dotfiles
cp ~/.bashrc ~/dotfiles/bash/
cp ~/.vimrc ~/dotfiles/vim/
cp -r ~/.config/i3/ ~/dotfiles/i3/
```

#### **2. Backup Antes de Actualizaciones**
```bash
# Backup completo antes de actualización mayor
BACKUP_DATE=$(date +%Y%m%d_%H%M%S)
cp -a /etc/ /backup/etc-backup-$BACKUP_DATE/
cp -a ~/.config/ /backup/user-config-$BACKUP_DATE/
```

#### **3. Distribución de Configuraciones**
```bash
# Distribuir configuración a múltiples usuarios
for user in usuario1 usuario2 usuario3; do
    cp template.bashrc /home/$user/.bashrc
    cp -r template-config/ /home/$user/.config/
done
```

#### **4. Preparar Entorno de Desarrollo**
```bash
# Copiar plantillas de proyecto
cp -r ~/plantillas/proyecto-python/ ~/nuevos-proyectos/mi-app/
cp ~/plantillas/.{gitignore,readme-template.md} ~/nuevos-proyectos/mi-app/
```

### Combinando CP con Otros Comandos

#### **Verificar Copia Exitosa**
```bash
# Copiar y verificar integridad
cp archivo_importante.iso /backup/
diff archivo_importante.iso /backup/archivo_importante.iso
```

#### **Copiar con Compresión**
```bash
# Copiar y comprimir simultáneamente
cp -a directorio_grande/ /backup/
tar -czf backup-$(date +%Y%m%d).tar.gz /backup/directorio_grande/
```

#### **Copiar con Progreso**
```bash
# Para archivos grandes, usar rsync con progreso
rsync -avh --progress archivo_grande.iso /backup/
```

### Diferencias con Otros Comandos

#### **CP vs MV**
```bash
# CP: crea copia, mantiene original
cp archivo.txt /backup/          # archivo.txt sigue existiendo

# MV: mueve archivo, elimina original
mv archivo.txt /backup/          # archivo.txt ya no existe en origen
```

#### **CP vs RSYNC**
```bash
# CP: copia completa siempre
cp -a directorio/ backup/

# RSYNC: sincronización inteligente (solo cambios)
rsync -av directorio/ backup/
```

### Errores Comunes y Soluciones

❌ **Error: Intentar copiar directorio sin -r**
```bash
cp mi-directorio/ /backup/       # Error: omitting directory
```
✅ **Solución:**
```bash
cp -r mi-directorio/ /backup/    # Usar -r para directorios
```

❌ **Error: Sobrescribir sin querer**
```bash
cp archivo.txt /destino/archivo_importante.txt  # Sobrescribe sin avisar
```
✅ **Solución:**
```bash
cp -i archivo.txt /destino/archivo_importante.txt  # Pide confirmación
```

❌ **Error: Espacio insuficiente**
```bash
cp archivo_gigante.iso /dev/lleno/   # No space left on device
```
✅ **Solución:**
```bash
df -h /destino/                      # Verificar espacio antes
du -h archivo_gigante.iso           # Ver tamaño del archivo
```

❌ **Error: Permisos insuficientes**
```bash
cp archivo.txt /etc/               # Permission denied
```
✅ **Solución:**
```bash
sudo cp archivo.txt /etc/          # Usar sudo si es necesario
```

### Buenas Prácticas

#### **1. Verificar Destino Antes de Copiar**
```bash
ls -la /destino/               # Ver qué hay en destino
df -h /destino/                # Verificar espacio disponible
```

#### **2. Usar Opciones Apropiadas**
```bash
# Para backup completo
cp -a origen/ destino/         # Preserva todo

# Para copia interactiva
cp -i archivo destino/         # Pide confirmación

# Para ver progreso
cp -rv directorio/ destino/    # Muestra qué se copia
```

#### **3. Nombrar Copias Claramente**
```bash
# Incluir fecha en nombre de backup
cp config.conf config.conf.backup-$(date +%Y%m%d)

# Usar nombres descriptivos
cp proyecto/ proyecto-backup-pre-refactor/
```

---
### Enlaces Relacionados
- [[mv]] - Mover archivos (diferente de copiar)
- [[ls]] - Verificar que los archivos se copiaron
- [[rm]] - Eliminar copias innecesarias
- [[mkdir]] - Crear directorios destino antes de copiar
- [[pwd]] - Verificar ubicación antes de copiar
- [[cat]] - Verificar contenido de archivos copiados