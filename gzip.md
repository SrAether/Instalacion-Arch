# gzip - Compresión y Descompresión de Archivos

El comando `gzip` es tu **compresor universal**. Es la herramienta estándar en Linux para comprimir archivos de manera eficiente, reduciendo el espacio de almacenamiento y acelerando las transferencias de datos.

## **¿Qué es gzip?**

`gzip` te permite:
- **Comprimir archivos** reduciendo su tamaño hasta un 90%
- **Descomprimir archivos** .gz de manera rápida
- **Ahorrar espacio** en disco y ancho de banda
- **Acelerar transferencias** de archivos por red

## **¿Por qué es Fundamental en Arch Linux?**

Durante la administración del sistema, `gzip` es esencial para gestionar logs, hacer backups eficientes, distribuir software comprimido y optimizar el almacenamiento del sistema.

### **Sintaxis Básica**

```bash
# Comprimir archivo
gzip archivo.txt

# Descomprimir archivo
gzip -d archivo.txt.gz
# o alternativamente
gunzip archivo.txt.gz

# Ver contenido sin descomprimir
zcat archivo.txt.gz
```

### **Opciones Fundamentales**

```bash
# -d, --decompress     = descomprimir
# -c, --stdout         = escribir a stdout (mantener original)
# -r, --recursive      = procesar directorios recursivamente
# -k, --keep           = mantener archivos originales
# -f, --force          = forzar compresión/sobrescribir
# -v, --verbose        = mostrar información detallada
# -t, --test           = probar integridad del archivo
# -l, --list           = listar información del archivo comprimido
```

## **Compresión Básica**

### **Comprimir Archivos**

```bash
# Comprimir archivo simple (elimina el original)
gzip documento.txt                    # Crea documento.txt.gz

# Comprimir manteniendo el original
gzip -c archivo.txt > archivo.txt.gz

# Comprimir con mejor compresión (más lento)
gzip -9 archivo.txt                   # Máxima compresión

# Comprimir con menos compresión (más rápido)
gzip -1 archivo.txt                   # Mínima compresión

# Comprimir manteniendo archivos originales
gzip -k archivo.txt                   # Mantiene archivo.txt y crea archivo.txt.gz
```

### **Comprimir Múltiples Archivos**

```bash
# Comprimir varios archivos individualmente
gzip archivo1.txt archivo2.txt archivo3.txt

# Comprimir todos los archivos de texto
gzip *.txt

# Comprimir recursivamente un directorio
gzip -r /path/to/directory/

# Comprimir con patrón específico
find /var/log -name "*.log" -exec gzip {} \;
```

## **Descompresión**

### **Descomprimir Archivos**

```bash
# Descomprimir archivo (elimina el .gz)
gzip -d archivo.txt.gz
# o
gunzip archivo.txt.gz

# Descomprimir manteniendo el archivo comprimido
gzip -dk archivo.txt.gz

# Descomprimir a stdout sin eliminar
gzip -dc archivo.txt.gz

# Descomprimir múltiples archivos
gunzip *.gz
```

### **Ver Contenido sin Descomprimir**

```bash
# Ver archivo comprimido completo
zcat archivo.txt.gz

# Ver primeras líneas
zcat archivo.txt.gz | head -10

# Buscar en archivo comprimido
zcat archivo.txt.gz | grep "patrón"

# Contar líneas en archivo comprimido
zcat archivo.txt.gz | wc -l
```

## **Casos de Uso en Arch Linux**

### **Gestión de Logs del Sistema**

```bash
# Comprimir logs antiguos automáticamente
sudo find /var/log -name "*.log" -mtime +7 -exec gzip {} \;

# Comprimir logs de sistema específicos
sudo gzip /var/log/pacman.log.1
sudo gzip /var/log/Xorg.0.log.old

# Rotar y comprimir logs
sudo cp /var/log/messages /var/log/messages.1
sudo gzip /var/log/messages.1

# Buscar en logs comprimidos
zcat /var/log/syslog.*.gz | grep "ERROR"
```

### **Backups y Archivos**

```bash
# Crear backup comprimido
tar cf - /home/usuario | gzip > backup_$(date +%Y%m%d).tar.gz

# Backup de configuraciones del sistema
sudo tar cf - /etc | gzip > sistema_config_$(date +%Y%m%d).tar.gz

# Comprimir backup de base de datos
mysqldump database | gzip > database_backup.sql.gz

# Backup comprimido de directorio específico
tar czf documentos_backup.tar.gz ~/Documentos/
```

### **Administración de Paquetes**

```bash
# Comprimir cache de paquetes antiguos
sudo find /var/cache/pacman/pkg -name "*.pkg.tar.*" -mtime +30 -exec gzip {} \;

# Examinar contenido de paquetes comprimidos
zcat package.pkg.tar.gz | tar -tv

# Extraer archivo específico de paquete comprimido
zcat package.pkg.tar.gz | tar -x specific_file
```

## **Optimización de Compresión**

### **Niveles de Compresión**

```bash
# Compresión rápida (nivel 1)
gzip -1 archivo_grande.txt            # Más rápido, menos compresión

# Compresión balanceada (nivel 6 - default)
gzip archivo.txt                      # Balance entre velocidad y compresión

# Máxima compresión (nivel 9)
gzip -9 archivo.txt                   # Más lento, máxima compresión

# Comparar diferentes niveles
for level in {1..9}; do
    cp archivo.txt "test_$level.txt"
    time gzip -$level "test_$level.txt"
    ls -lh "test_$level.txt.gz"
done
```

### **Optimización para Diferentes Tipos de Archivos**

```bash
# Para archivos de texto (logs, código)
gzip -9 archivo_texto.txt             # Excelente compresión

# Para archivos binarios
gzip -6 archivo_binario               # Compresión moderada

# Para archivos ya comprimidos (imágenes, videos)
gzip -1 archivo.jpg                   # Poca mejora, usar compresión rápida
```

## **Información y Verificación**

### **Listar Información de Archivos Comprimidos**

```bash
# Información básica
gzip -l archivo.txt.gz

# Información detallada
gzip -lv archivo.txt.gz

# Información de múltiples archivos
gzip -l *.gz

# Mostrar ratio de compresión
gzip -lv *.gz | awk 'NR>1 {printf "%-20s %5.1f%%\n", $4, (1-$1/$2)*100}'
```

### **Verificar Integridad**

```bash
# Probar integridad de archivo comprimido
gzip -t archivo.txt.gz

# Verificar múltiples archivos
gzip -tv *.gz

# Verificar con información detallada
for file in *.gz; do
    echo "Verificando $file:"
    gzip -tv "$file" && echo "OK" || echo "ERROR"
done
```

## **Pipelines y Flujos de Datos**

### **Compresión en Pipelines**

```bash
# Comprimir salida de comando
comando | gzip > salida.gz

# Transferir archivos comprimidos por red
tar cf - directorio/ | gzip | ssh usuario@servidor 'cat > backup.tar.gz'

# Backup comprimido a través de SSH
tar czf - /home/usuario | ssh servidor 'cat > /backup/usuario.tar.gz'

# Procesar log en tiempo real
tail -f /var/log/syslog | gzip > syslog_live.gz
```

### **Descompresión en Pipelines**

```bash
# Descomprimir y procesar
zcat archivo.log.gz | grep "ERROR" | wc -l

# Descomprimir múltiples archivos y procesar
zcat *.log.gz | [[awk]] '/ERROR/ {print $1, $2, $NF}'

# Transferir y descomprimir
ssh servidor 'cat backup.tar.gz' | gunzip | tar xf -
```

## **Scripts y Automatización**

### **Script de Rotación de Logs**

```bash
# Script simple para rotar y comprimir logs

# Rotar un log específico
echo "Rotando log..."
cp /var/log/aplicacion.log /var/log/aplicacion.log.1
> /var/log/aplicacion.log

# Comprimir logs antiguos
gzip /var/log/aplicacion.log.1

# Limpiar logs antiguos (más de 30 días)
find /var/log -name "*.gz" -mtime +30 -delete
```

### **Script de Backup Automático**

```bash
# Backup automático simple con compresión

SOURCE="/home/usuario"
DEST="/backup"
DATE=$(date +%Y%m%d)

# Crear backup comprimido
echo "Creando backup..."
tar czf "$DEST/backup_$DATE.tar.gz" "$SOURCE"

# Verificar resultado
if [[ $? -eq 0 ]]; then
    echo "✅ Backup creado: backup_$DATE.tar.gz"
    ls -lh "$DEST/backup_$DATE.tar.gz"
else
    echo "❌ Error al crear backup"
fi
```
        echo "Error al crear backup"
        return 1
    fi
}

# Crear backup comprimido
create_compressed_backup "$BACKUP_SOURCE" "$BACKUP_DEST"

# Limpiar backups antiguos (mantener últimos 7)
find "$BACKUP_DEST" -name "backup_*.tar.gz" -type f -mtime +7 -delete
```

## **Integración con Otros Comandos**

### **Con [[tar]] para Archivos Completos**

```bash
# Crear archivo tar comprimido
tar czf archivo.tar.gz directorio/

# Extraer archivo tar comprimido
tar xzf archivo.tar.gz

# Listar contenido sin extraer
tar tzf archivo.tar.gz

# Extraer archivo específico
tar xzf archivo.tar.gz archivo_específico
```

### **Con [[find]] para Compresión Masiva**

```bash
# Comprimir archivos antiguos
find /var/log -name "*.log" -mtime +7 -exec gzip {} \;

# Comprimir por tamaño
find /tmp -size +100M -name "*.tmp" -exec gzip {} \;

# Comprimir excluyendo ciertos archivos
find /home -name "*.txt" ! -path "*/important/*" -exec gzip {} \;
```

### **Con [[rsync]] para Transferencias Eficientes**

```bash
# Sincronizar archivos comprimidos
rsync -av --compress backup/ servidor:/backup/

# Transferir comprimiendo on-the-fly
rsync -avz directorio/ servidor:/destino/
```

## **Alternativas y Comparaciones**

### **Comparación con Otros Compresores**

```bash
# Comparar gzip vs bzip2 vs xz
echo "Probando compresores en archivo.txt:"

# gzip
cp archivo.txt test_gzip.txt
time gzip test_gzip.txt
ls -lh test_gzip.txt.gz

# bzip2 (mejor compresión, más lento)
cp archivo.txt test_bzip2.txt
time bzip2 test_bzip2.txt
ls -lh test_bzip2.txt.bz2

# xz (mejor compresión, más lento que bzip2)
cp archivo.txt test_xz.txt
time xz test_xz.txt
ls -lh test_xz.txt.xz
```

### **Comandos Relacionados**

```bash
# zcat = cat para archivos .gz
zcat archivo.gz

# zgrep = grep para archivos .gz
zgrep "patrón" archivo.gz

# zless = less para archivos .gz
zless archivo.gz

# zdiff = diff para archivos .gz
zdiff archivo1.gz archivo2.gz
```

## **Monitoreo y Estadísticas**

### **Estadísticas de Compresión**

```bash
# Función para analizar eficiencia de compresión
analyze_compression() {
    local file="$1"
    local original_size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file")
    
    gzip -c "$file" > "${file}.gz"
    local compressed_size=$(stat -f%z "${file}.gz" 2>/dev/null || stat -c%s "${file}.gz")
    
    local ratio=$(echo "scale=2; (1 - $compressed_size / $original_size) * 100" | bc)
    
    echo "Archivo: $file"
    echo "Tamaño original: $(numfmt --to=iec $original_size)"
    echo "Tamaño comprimido: $(numfmt --to=iec $compressed_size)"
    echo "Ratio de compresión: ${ratio}%"
    
    rm "${file}.gz"
}

# Usar la función
analyze_compression "archivo_grande.txt"
```

### **Monitoreo de Espacio Ahorrado**

```bash
# Script para monitorear espacio ahorrado por compresión
#!/bin/bash

calculate_savings() {
    local dir="$1"
    local total_original=0
    local total_compressed=0
    
    for file in "$dir"/*.gz; do
        if [[ -f "$file" ]]; then
            local info=$(gzip -l "$file")
            local original=$(echo "$info" | awk 'NR==2 {print $2}')
            local compressed=$(echo "$info" | awk 'NR==2 {print $1}')
            
            total_original=$((total_original + original))
            total_compressed=$((total_compressed + compressed))
        fi
    done
    
    if [[ $total_original -gt 0 ]]; then
        local savings=$(echo "scale=2; (1 - $total_compressed / $total_original) * 100" | bc)
        echo "Espacio total sin comprimir: $(numfmt --to=iec $total_original)"
        echo "Espacio total comprimido: $(numfmt --to=iec $total_compressed)"
        echo "Espacio ahorrado: ${savings}%"
    fi
}

calculate_savings "/var/log"
```

## **Troubleshooting Común**

### **Problemas de Espacio**

```bash
# Verificar espacio disponible antes de comprimir
df -h /tmp

# Comprimir a directorio diferente si no hay espacio
gzip -c archivo_grande.txt > /otro/directorio/archivo_grande.txt.gz

# Usar compresión en pipeline para archivos muy grandes
cat archivo_muy_grande.txt | gzip > archivo_muy_grande.txt.gz
```

### **Archivos Corruptos**

```bash
# Verificar integridad antes de usar
gzip -t archivo.gz && echo "Archivo OK" || echo "Archivo corrupto"

# Intentar recuperación parcial
zcat archivo_corrupto.gz 2>/dev/null > archivo_recuperado.txt

# Verificar todos los archivos en directorio
find /backup -name "*.gz" -exec gzip -t {} \; -print
```

## **Enlaces Relacionados**

- [[tar]] - Crear archivos comprimidos con múltiples archivos
- [[find]] - Buscar archivos para comprimir
- [[du]] - Ver espacio ocupado antes/después de comprimir
- [[df]] - Verificar espacio disponible para compresión
- [[rsync]] - Transferir archivos con compresión automática
- [[cat]] - Ver archivos antes de comprimir