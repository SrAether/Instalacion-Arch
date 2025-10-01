# tar - Archivador y Compresor

El comando `tar` (Tape ARchiver) es tu **maestro empaquetador**. Es una herramienta fundamental para crear archivos comprimidos, hacer respaldos, distribuir software y gestionar colecciones de archivos como una unidad cohesiva.

## **¿Qué es tar?**

`tar` te permite:
- **Empaquetar múltiples archivos** en un solo archivo
- **Comprimir archivos** usando diferentes algoritmos
- **Extraer archivos** de paquetes existentes
- **Listar contenido** de archivos tar sin extraer

## **¿Por qué es Esencial en Arch Linux?**

Durante la instalación y administración del sistema, `tar` es fundamental para descomprimir software descargado, crear respaldos del sistema, y gestionar paquetes que no están en los repositorios oficiales.

### **Sintaxis Básica**

```bash
# Estructura básica
tar [opciones] archivo.tar [archivos/directorios]

# Opciones principales:
# c = crear archivo
# x = extraer archivo  
# t = listar contenido
# v = verbose (mostrar detalles)
# f = especificar archivo
# z = comprimir con gzip
# j = comprimir con bzip2
# J = comprimir con xz
```

### **Operaciones Fundamentales**

```bash
# Crear archivo tar simple
tar -cf archivo.tar directorio/

# Crear archivo tar comprimido con gzip
tar -czf archivo.tar.gz directorio/

# Extraer archivo tar
tar -xf archivo.tar

# Extraer archivo tar comprimido
tar -xzf archivo.tar.gz

# Listar contenido sin extraer
tar -tf archivo.tar
```

## **Creación de Archivos**

### **Crear Archives**

```bash
# Crear tar simple
tar -cf backup.tar /home/usuario/Documentos/

# Crear tar con compresión gzip (más común)
tar -czf backup.tar.gz /home/usuario/Documentos/

# Crear tar con compresión bzip2 (mejor compresión)
tar -cjf backup.tar.bz2 /home/usuario/Documentos/

# Crear tar con compresión xz (mejor compresión moderna)
tar -cJf backup.tar.xz /home/usuario/Documentos/

# Verbose para ver progreso
tar -czvf backup.tar.gz /home/usuario/Documentos/
```

### **Opciones de Creación Avanzadas**

```bash
# Excluir archivos/directorios específicos
tar -czf backup.tar.gz --exclude="*.tmp" --exclude="cache" /home/usuario/

# Crear desde lista de archivos
echo -e "/etc/fstab\n/etc/hosts\n/etc/hostname" | tar -czf config.tar.gz -T -

# Preservar permisos y propiedades
tar -czpf backup.tar.gz /home/usuario/

# Mostrar progreso con tamaño
tar -czf backup.tar.gz --checkpoint=1000 --checkpoint-action=echo /home/usuario/
```

## **Extracción de Archivos**

### **Extraer Archives**

```bash
# Extraer en directorio actual
tar -xzf archivo.tar.gz

# Extraer en directorio específico
tar -xzf archivo.tar.gz -C /destino/

# Extraer archivos específicos
tar -xzf backup.tar.gz home/usuario/documento.txt

# Extraer con verbose para ver progreso
tar -xzvf archivo.tar.gz

# Extraer preservando propiedades (como root)
sudo tar -xzpf backup.tar.gz -C /
```

### **Extracción Selectiva**

```bash
# Extraer solo archivos que coincidan con patrón
tar -xzf backup.tar.gz --wildcards "*.conf"

# Extraer excluyendo ciertos archivos
tar -xzf backup.tar.gz --exclude="*.log"

# Extraer solo directorios específicos
tar -xzf backup.tar.gz home/usuario/Documentos/

# Extraer renombrando el directorio base
tar -xzf backup.tar.gz --transform 's/^home/nueva_ubicacion/'
```

## **Casos de Uso en Arch Linux**

### **Durante la Instalación**

```bash
# Extraer paquetes de software descargados
tar -xzf software-1.0.tar.gz
cd software-1.0/
make && sudo make install

# Restaurar configuraciones guardadas
tar -xzf config_respaldo.tar.gz -C /
```

### **Gestión del Sistema**

```bash
# Crear respaldo del sistema
sudo tar -czf /backup/sistema_$(date +%Y%m%d).tar.gz \
    --exclude=/proc --exclude=/sys --exclude=/dev \
    --exclude=/tmp --exclude=/backup /

# Respaldo de directorio home
tar -czf ~/backup_documentos_$(date +%Y%m%d).tar.gz ~/Documentos/

# Respaldo de configuraciones
sudo tar -czf config_backup.tar.gz /etc/
```

### **Desarrollo y Proyectos**

```bash
# Empaquetar proyecto para distribución
tar -czf mi_proyecto_v1.0.tar.gz --exclude=".git" --exclude="*.o" proyecto/

# Crear respaldo antes de cambios importantes
tar -czf proyecto_backup_$(date +%H%M).tar.gz proyecto/
```

## **Compresión y Algoritmos**

### **Comparación de Métodos**

```bash
# Sin compresión (más rápido)
tar -cf archivo.tar directorio/

# gzip - balance entre velocidad y compresión
tar -czf archivo.tar.gz directorio/

# bzip2 - mejor compresión, más lento
tar -cjf archivo.tar.bz2 directorio/

# xz - mejor compresión moderna
tar -cJf archivo.tar.xz directorio/

# Comparar tamaños
ls -lh archivo.tar*
```

### **Niveles de Compresión**

```bash
# Compresión rápida (menos compresión)
tar -czf archivo.tar.gz --gzip-compress=1 directorio/

# Compresión máxima (más lenta)
tar -czf archivo.tar.gz --gzip-compress=9 directorio/

# Con bzip2 y nivel específico
BZIP2=-9 tar -cjf archivo.tar.bz2 directorio/

# Con xz y nivel específico
XZ_OPT=-9 tar -cJf archivo.tar.xz directorio/
```

## **Verificación y Listado**

### **Listar Contenido**

```bash
# Listar contenido básico
tar -tf archivo.tar.gz

# Listar con detalles (como ls -la)
tar -tvf archivo.tar.gz

# Buscar archivos específicos en el tar
tar -tzf archivo.tar.gz | grep "\.conf$"

# Listar solo directorios
tar -tf archivo.tar.gz | grep "/$"
```

### **Verificar Integridad**

```bash
# Verificar que el archivo tar no está corrupto
tar -tzf archivo.tar.gz > /dev/null && echo "OK" || echo "Corrupto"

# Comparar archivos en tar con sistema
tar -df archivo.tar

# Verificar checksums si están disponibles
tar -xzf archivo.tar.gz
sha256sum -c checksums.txt
```

## **Operaciones Avanzadas**

### **Actualizar Archives**

```bash
# Agregar archivos a tar existente (solo tar sin comprimir)
tar -rf archivo.tar nuevo_archivo.txt

# Actualizar archivo si es más nuevo
tar -uf archivo.tar directorio/

# Para archivos comprimidos, necesitas recrear
tar -czf temp.tar.gz -T <(tar -tf archivo.tar.gz) nuevo_archivo.txt
mv temp.tar.gz archivo.tar.gz
```

### **División de Archives Grandes**

```bash
# Dividir archivo grande en partes
tar -czf - directorio/ | split -b 100M - archivo.tar.gz.part_

# Reunir partes divididas
cat archivo.tar.gz.part_* | tar -xzf -

# Usar con GNU tar para división automática
tar -czf archivo.tar.gz --multi-volume --tape-length=100M directorio/
```

## **Integración con Otros Comandos**

### **Con [[find]]**

```bash
# Crear tar de archivos encontrados con find
find /etc -name "*.conf" -print0 | tar -czf configs.tar.gz --null -T -

# Respaldar archivos modificados recientemente
find /home/usuario -mtime -7 -type f -print0 | tar -czf recientes.tar.gz --null -T -
```

### **Con [[rsync]] y [[ssh]]**

```bash
# Transferir y extraer remotamente
tar -czf - directorio/ | ssh usuario@servidor "cd /destino && tar -xzf -"

# Respaldo remoto comprimido
tar -czf - /home/usuario/ | ssh backup@servidor "cat > backup_$(date +%Y%m%d).tar.gz"
```

### **Con [[grep]] y filtros**

```bash
# Buscar texto dentro de archivos en tar
tar -xzf archivo.tar.gz -O | grep "patron"

# Extraer solo archivos que contienen cierto texto
tar -tzf backup.tar.gz | xargs tar -xzf backup.tar.gz
```

## **Respaldos y Automatización**

### **Scripts de Respaldo**

```bash
# Script simple de respaldo automático

FECHA=$(date +%Y%m%d)
ORIGEN="/home/usuario"
DESTINO="/backup"
ARCHIVO="backup_home_$FECHA.tar.gz"

# Crear respaldo básico
tar -czf "$DESTINO/$ARCHIVO" "$ORIGEN"

# Verificar resultado
if [ $? -eq 0 ]; then
    echo "✅ Respaldo exitoso: $ARCHIVO"
    # Limpiar respaldos antiguos
    find "$DESTINO" -name "backup_home_*.tar.gz" -mtime +7 -delete
else
    echo "❌ Error en respaldo"
fi
```

### **Respaldo Incremental**

```bash
# Respaldo incremental simple

# Crear backup completo inicial
tar -czf backup_full.tar.gz -g snapshot.snar /home/usuario/

# Crear backup incremental
tar -czf backup_incr_$(date +%Y%m%d).tar.gz -g snapshot.snar /home/usuario/

# Restaurar
tar -xzf backup_full.tar.gz
tar -xzf backup_incr_20231030.tar.gz
```

## **Troubleshooting Común**

### **Errores de Permisos**

```bash
# Error: Cannot write to 'archivo.tar': Permission denied
# Usar sudo para escribir en ubicaciones del sistema
sudo tar -czf /backup/sistema.tar.gz /etc/

# Error al extraer por permisos
# Extraer en directorio propio o usar sudo
tar -xzf archivo.tar.gz -C ~/temp/
```

### **Archives Corruptos**

```bash
# Verificar si está corrupto
tar -tzf archivo.tar.gz

# Intentar recuperar con gzip
gunzip -t archivo.tar.gz

# Extraer lo que se pueda
tar -xzf archivo.tar.gz --ignore-failed-read
```

### **Problemas de Espacio**

```bash
# Verificar espacio antes de extraer
tar -tzf archivo.tar.gz | wc -l  # Número de archivos
df -h .  # Espacio disponible

# Extraer en ubicación con más espacio
tar -xzf archivo.tar.gz -C /tmp/
```

## **Optimización y Performance**

### **Mejorar Velocidad**

```bash
# Usar múltiples cores para compresión (con pigz)
tar -cf - directorio/ | pigz > archivo.tar.gz

# Para xz paralelo
tar -cf - directorio/ | pxz > archivo.tar.xz

# Reducir I/O con buffer
tar -czf archivo.tar.gz --blocking-factor=20 directorio/
```

### **Monitorear Progreso**

```bash
# Con pv (pipe viewer)
tar -czf - directorio/ | pv > archivo.tar.gz

# Con tar progress
tar -czf archivo.tar.gz --checkpoint=1000 --checkpoint-action=echo directorio/

# Estimar progreso con du
du -sh directorio/  # Ver tamaño antes de comprimir
```

## **Enlaces Relacionados**

- [[find]] - Buscar archivos para incluir en archives tar
- [[gzip]] - Comprimir individual de archivos (tar usa gzip internamente)
- [[rsync]] - Alternativa para respaldos incrementales
- [[cp]] - Copiar archivos que luego se pueden empaquetar con tar
- [[mv]] - Mover archives tar creados
- [[chmod]] - Ajustar permisos de archives tar creados
- [[chown]] - Cambiar propietario de archives tar