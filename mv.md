El comando `mv` es tu **transportador y renombrador** de archivos. Funciona como una mudanza: puede mover archivos de una ubicación a otra, o simplemente cambiarles el nombre en el mismo lugar. A diferencia de [[cp]], `mv` no crea una copia; **mueve** el archivo original.

---
### ¿Qué Hace MV? 🚚
**MV** significa **"Move"** (Mover). Sus dos funciones principales son:
1. **Mover archivos/directorios** de una ubicación a otra
2. **Renombrar archivos/directorios** en la misma ubicación

### Sintaxis Básica
```bash
mv [opciones] origen destino
mv [opciones] origen1 origen2 origen3... directorio_destino
```

### Renombrar Archivos y Directorios
```bash
# Renombrar un archivo
mv archivo_viejo.txt archivo_nuevo.txt

# Renombrar un directorio
mv directorio_viejo/ directorio_nuevo/

# Cambiar extensión de archivo
mv documento.txt documento.md
mv imagen.jpeg imagen.jpg
```

### Mover Archivos a Otros Directorios
```bash
# Mover archivo a otro directorio (mantiene el nombre)
mv archivo.txt ~/Documentos/

# Mover archivo a otro directorio con nuevo nombre
mv archivo.txt ~/Documentos/nuevo_nombre.txt

# Mover múltiples archivos a un directorio
mv archivo1.txt archivo2.txt archivo3.txt ~/backup/
```

### En el Contexto de la Instalación de Arch Linux

#### **Organizar Archivos de Configuración**
```bash
# Mover configuraciones temporales a su lugar definitivo
mv /tmp/mi-config.conf /etc/
mv ~/descargas/dotfiles/.bashrc ~/
mv ~/descargas/wallpapers/ ~/Imágenes/
```

#### **Reorganizar Estructura del Sistema**
```bash
# Mover logs a ubicación correcta
mv /tmp/*.log /var/log/
mv /home/usuario/config/ /home/usuario/.config/mi-app/
```

#### **Backup y Restauración**
```bash
# Mover configuración actual para hacer backup
mv /etc/pacman.conf /etc/pacman.conf.backup
mv ~/.bashrc ~/.bashrc.old

# Restaurar desde backup
mv ~/.bashrc.backup ~/.bashrc
```

#### **Limpiar Archivos de Instalación**
```bash
# Mover archivos temporales de instalación
mv /tmp/arch-install-* ~/Documentos/instalacion-logs/
mv *.iso ~/ISOs/
```

### Opciones Importantes

#### **`-i` (Interactive) - Confirmar Sobrescritura**
```bash
# Pregunta antes de sobrescribir archivos existentes
mv -i archivo.txt /destino/
# ¿Sobrescribir '/destino/archivo.txt'? (y/n)
```

#### **`-n` (No-clobber) - No Sobrescribir**
```bash
# No sobrescribe si el archivo destino ya existe
mv -n archivo.txt /destino/
```

#### **`-u` (Update) - Solo Mover si es Más Nuevo**
```bash
# Solo mueve si el archivo origen es más nuevo
mv -u archivo_modificado.txt /destino/
```

#### **`-v` (Verbose) - Mostrar Operaciones**
```bash
# Muestra qué archivos se están moviendo
mv -v *.txt ~/Documentos/
# 'archivo1.txt' -> '/home/usuario/Documentos/archivo1.txt'
# 'archivo2.txt' -> '/home/usuario/Documentos/archivo2.txt'
```

### Casos de Uso Específicos

#### **1. Organización de Archivos por Tipo**
```bash
# Organizar archivos por extensión
mkdir -p ~/organizados/{imagenes,documentos,videos,audio}
mv *.{jpg,png,gif} ~/organizados/imagenes/
mv *.{pdf,doc,txt} ~/organizados/documentos/
mv *.{mp4,avi,mkv} ~/organizados/videos/
mv *.{mp3,wav,flac} ~/organizados/audio/
```

#### **2. Limpieza de Directorio de Descargas**
```bash
# Mover archivos antiguos a archivo
mkdir -p ~/archivo/descargas-$(date +%Y-%m)
mv ~/Descargas/*.{iso,zip,tar.gz} ~/archivo/descargas-$(date +%Y-%m)/
```

#### **3. Rotación de Logs**
```bash
# Rotar archivos de log
mv /var/log/mi-app.log /var/log/mi-app.log.1
mv /var/log/mi-app.log.1 /var/log/mi-app.log.2
```

#### **4. Gestión de Dotfiles**
```bash
# Mover dotfiles a repositorio de gestión
mv ~/.bashrc ~/dotfiles/bash/bashrc
mv ~/.vimrc ~/dotfiles/vim/vimrc
mv ~/.config/i3/ ~/dotfiles/i3/
```

### Patrones de Renombrado Avanzados

#### **Renombrado Masivo con Scripts**
```bash
# Renombrar múltiples archivos agregando prefijo
for file in *.txt; do
    mv "$file" "backup_$file"
done

# Cambiar extensiones masivamente
for file in *.jpeg; do
    mv "$file" "${file%.jpeg}.jpg"
done
```

#### **Usando Variables en Renombrado**
```bash
# Renombrar con fecha actual
DATE=$(date +%Y%m%d)
mv proyecto/ proyecto-backup-$DATE/
mv config.conf config-$DATE.conf
```

#### **Normalizar Nombres de Archivos**
```bash
# Convertir espacios a guiones
for file in *\ *; do
    mv "$file" "${file// /-}"
done

# Convertir a minúsculas
for file in *; do
    mv "$file" "$(echo $file | tr '[:upper:]' '[:lower:]')"
done
```

### Combinando MV con Otros Comandos

#### **Mover y Verificar**
```bash
# Mover y listar destino para verificar
mv archivo.txt ~/Documentos/ && ls ~/Documentos/archivo.txt
```

#### **Mover con Backup Automático**
```bash
# Si existe, hacer backup antes de mover
if [ -f /destino/archivo.txt ]; then
    mv /destino/archivo.txt /destino/archivo.txt.backup
fi
mv archivo.txt /destino/
```

#### **Crear Directorio y Mover**
```bash
# Crear directorio destino si no existe y mover
mkdir -p ~/proyectos/nuevo-proyecto
mv archivos-proyecto/* ~/proyectos/nuevo-proyecto/
```

### Diferencias Importantes con Otros Comandos

#### **MV vs CP**
```bash
# MV: mueve (elimina original)
mv archivo.txt /destino/         # archivo.txt ya no existe en origen

# CP: copia (mantiene original)
cp archivo.txt /destino/         # archivo.txt sigue en origen
```

#### **MV vs RENAME**
```bash
# MV: comando estándar POSIX
mv archivo.txt nuevo_nombre.txt

# RENAME: comando específico de algunas distribuciones
rename 's/\.txt$/\.md/' *.txt    # Cambia .txt a .md en múltiples archivos
```

### Operaciones con Directorios

#### **Mover Directorios Completos**
```bash
# Mover directorio con todo su contenido
mv directorio_origen/ ~/nuevo_destino/

# Renombrar directorio
mv mi_proyecto/ mi_proyecto_v2/
```

#### **Fusionar Directorios**
```bash
# Mover contenido de un directorio a otro
mv directorio_temporal/* directorio_principal/
rmdir directorio_temporal/  # Eliminar directorio vacío
```

### Casos Especiales y Trucos

#### **Mover Archivos Ocultos**
```bash
# Mover archivos que empiezan con punto
mv .* ~/backup/           # ⚠️ Cuidado: incluye . y ..
mv .[^.]* ~/backup/       # ✅ Mejor: excluye . y ..
```

#### **Mover Archivos con Caracteres Especiales**
```bash
# Usar comillas para nombres con espacios
mv "archivo con espacios.txt" "nuevo archivo.txt"

# Escapar caracteres especiales
mv archivo\$especial.txt archivo_especial.txt
```

#### **Intercambiar Nombres de Archivos**
```bash
# Intercambiar nombres usando archivo temporal
mv archivo1.txt temp_file
mv archivo2.txt archivo1.txt
mv temp_file archivo2.txt
```

### Errores Comunes y Soluciones

❌ **Error: Mover directorio dentro de sí mismo**
```bash
mv /home/usuario/ /home/usuario/backup/  # Error: cannot move directory
```
✅ **Solución:**
```bash
mv /home/usuario/* /backup/usuario/      # Mover contenido, no directorio padre
```

❌ **Error: Destino no es directorio para múltiples archivos**
```bash
mv archivo1 archivo2 archivo3 destino_que_no_es_directorio
```
✅ **Solución:**
```bash
mkdir -p destino_directorio/
mv archivo1 archivo2 archivo3 destino_directorio/
```

❌ **Error: Sobrescribir sin querer**
```bash
mv archivo_importante.txt archivo_critico.txt  # Sobrescribe sin avisar
```
✅ **Solución:**
```bash
mv -i archivo_importante.txt archivo_critico.txt  # Pide confirmación
```

❌ **Error: Permisos insuficientes**
```bash
mv archivo.txt /etc/                     # Permission denied
```
✅ **Solución:**
```bash
sudo mv archivo.txt /etc/                # Usar sudo si es necesario
```

### Buenas Prácticas

#### **1. Usar Confirmación para Operaciones Críticas**
```bash
mv -i archivo_importante.* /nueva_ubicacion/
```

#### **2. Verificar Destino Antes de Mover**
```bash
ls -la /destino/                         # Ver qué hay en destino
df -h /destino/                          # Verificar espacio
```

#### **3. Hacer Backup Antes de Renombrar Archivos Críticos**
```bash
cp archivo_critico.conf archivo_critico.conf.backup
mv archivo_critico.conf archivo_critico.new.conf
```

#### **4. Usar Rutas Absolutas para Operaciones Importantes**
```bash
mv /home/usuario/importante.txt /backup/importante.txt
```

---
### Enlaces Relacionados
- [[cp]] - Copiar archivos (mantiene original)
- [[rm]] - Eliminar archivos movidos incorrectamente
- [[ls]] - Verificar resultado de operaciones de movimiento
- [[mkdir]] - Crear directorios destino antes de mover
- [[pwd]] - Verificar ubicación actual antes de mover
- [[find]] - Encontrar archivos para mover en lote
