# find - Buscar Archivos y Directorios

El comando `find` es tu **detective de archivos**. Es una herramienta extraordinariamente poderosa que puede localizar archivos y directorios en todo el sistema basándose en una amplia variedad de criterios, desde nombres hasta permisos, tamaños y fechas.

## **¿Qué es find?**

Es un comando que te permite:
- **Buscar archivos por nombre, tipo, tamaño, fecha**
- **Ejecutar acciones** en los archivos encontrados
- **Filtrar resultados** con criterios complejos
- **Navegar recursivamente** por directorios

## **¿Por qué es Esencial en Arch Linux?**

Durante la instalación y administración del sistema, `find` te ayuda a localizar archivos de configuración, limpiar archivos temporales, encontrar logs, y automatizar tareas de mantenimiento.

### **Sintaxis Básica**

```bash
# Estructura básica
find [directorio] [criterios] [acciones]

# Buscar archivo por nombre
find /home -name "archivo.txt"

# Buscar en directorio actual
find . -name "*.conf"
```

### **Búsquedas por Nombre**

```bash
# Buscar archivo exacto
find /etc -name "fstab"

# Buscar con wildcards (insensible a mayúsculas)
find /etc -iname "*.CONF"

# Buscar que contenga texto en el nombre
find /usr -name "*python*"

# Buscar que empiece/termine con algo
find /home -name "config.*"
find /var -name "*.log"
```

### **Búsquedas por Tipo**

```bash
# Solo archivos regulares
find /etc -type f -name "*.conf"

# Solo directorios
find /home -type d -name "Documents"

# Enlaces simbólicos
find /usr -type l

# Tipos disponibles:
# f = archivo regular
# d = directorio
# l = enlace simbólico
# c = dispositivo de caracteres
# b = dispositivo de bloques
```

## **Búsquedas Avanzadas**

### **Por Tamaño**

```bash
# Archivos mayores a 100MB
find /var -size +100M

# Archivos menores a 1KB
find /tmp -size -1k

# Archivos de exactamente 512 bytes
find . -size 512c

# Unidades: c=bytes, k=KB, M=MB, G=GB
```

### **Por Fecha y Tiempo**

```bash
# Archivos modificados en últimas 24 horas
find /home -mtime -1

# Archivos modificados hace más de 7 días
find /tmp -mtime +7

# Archivos accedidos hoy
find /var -atime 0

# Archivos modificados en últimos 30 minutos
find /var/log -mmin -30
```

### **Por Permisos**

```bash
# Archivos con permisos específicos
find /home -perm 644

# Archivos ejecutables por el propietario
find /usr/bin -perm -u+x

# Archivos escribibles por grupo
find /tmp -perm -g+w

# Archivos SUID (setuid)
find /usr -perm -4000
```

## **Contexto en Arch Linux**

### **Durante la Instalación**

```bash
# Encontrar dispositivos de disco
find /dev -name "sd*"

# Buscar archivos de configuración
find /etc -name "*.conf" | head -10
```

### **Mantenimiento del Sistema**

```bash
# Encontrar archivos grandes
find / -size +1G 2>/dev/null

# Limpiar archivos temporales antiguos
find /tmp -mtime +7 -delete

# Encontrar archivos de log grandes
find /var/log -name "*.log" -size +100M
```

### **Configuración Post-Instalación**

```bash
# Encontrar archivos de configuración de red
find /etc -name "*network*"

# Buscar binarios instalados
find /usr/bin -name "firefox*"
```

## **Acciones con Archivos Encontrados**

### **Ejecutar Comandos**

```bash
# Mostrar información detallada
find /etc -name "*.conf" -exec ls -la {} \;

# Hacer backup de archivos encontrados
find /home -name "*.txt" -exec cp {} /backup/ \;

# Cambiar permisos
find /var/www -name "*.php" -exec chmod 644 {} \;
```

### **Acciones de Limpieza**

```bash
# Eliminar archivos temporales
find /tmp -name "*.tmp" -delete

# Eliminar archivos vacíos
find /home -empty -delete

# Eliminar directorios vacíos
find /var -type d -empty -delete
```

### **Acciones con Confirmación**

```bash
# Preguntar antes de eliminar
find /tmp -name "*.log" -ok rm {} \;

# Usar xargs para mejor rendimiento
find /var -name "*.old" | xargs rm -f
```

## **Combinando Criterios**

### **Operadores Lógicos**

```bash
# AND (implícito)
find /home -name "*.txt" -size +1k

# OR explícito
find /etc \( -name "*.conf" -o -name "*.cfg" \)

# NOT
find /tmp ! -name "*.tmp"

# Combinaciones complejas
find /var -type f \( -name "*.log" -o -name "*.old" \) -size +10M
```

### **Búsquedas por Usuario/Grupo**

```bash
# Archivos de usuario específico
find /home -user juan

# Archivos de grupo específico
find /var -group wheel

# Archivos sin propietario (huérfanos)
find / -nouser 2>/dev/null

# Archivos sin grupo
find / -nogroup 2>/dev/null
```

## **Optimización y Rendimiento**

### **Limitar Profundidad**

```bash
# Buscar solo en nivel superior
find /etc -maxdepth 1 -name "*.conf"

# Buscar hasta 3 niveles
find /home -maxdepth 3 -name "Documents"

# Excluir directorios específicos
find / -path "/proc" -prune -o -name "*.conf" -print
```

### **Optimizar Búsquedas**

```bash
# Usar locate para búsquedas de nombres (más rápido)
locate archivo.txt

# Actualizar base de datos de locate
sudo updatedb

# find es mejor para criterios complejos
find /var -mtime -1 -size +100M
```

## **Casos de Uso Prácticos**

### **Administración del Sistema**

```bash
# Encontrar archivos de configuración modificados recientemente
find /etc -name "*.conf" -mtime -7

# Buscar archivos grandes que consumen espacio
find / -type f -size +500M 2>/dev/null | head -10

# Encontrar archivos SUID (potencial riesgo de seguridad)
find / -perm -4000 -ls 2>/dev/null
```

### **Desarrollo y Debugging**

```bash
# Encontrar archivos de código fuente
find /home/proyecto -name "*.py" -o -name "*.cpp"

# Buscar archivos que contengan texto específico
find /etc -name "*.conf" -exec grep -l "localhost" {} \;

# Encontrar binarios compilados
find . -type f -executable
```

### **Limpieza y Mantenimiento**

```bash
# Limpiar cachés antiguos
find ~/.cache -mtime +30 -delete

# Encontrar duplicados por tamaño
find /home -type f -printf "%s %p\n" | sort -n

# Limpiar logs antiguos
find /var/log -name "*.old" -mtime +30 -delete
```

## **Integración con Otras Herramientas**

### **Con [[grep]]**

```bash
# Buscar archivos y filtrar contenido
find /etc -name "*.conf" | xargs grep "server"
```

### **Con [[tar]]**

```bash
# Hacer backup de archivos encontrados
find /home -name "*.txt" | tar -czf backup.tar.gz -T -
```

### **Con [[chmod]] y [[chown]]**

```bash
# Cambiar permisos en lote
find /var/www -type f -exec chmod 644 {} \;
find /var/www -type d -exec chmod 755 {} \;
```

## **Troubleshooting**

### **Permisos Denegados**

```bash
# Redirigir errores para búsquedas en todo el sistema
find / -name "archivo" 2>/dev/null

# O usar sudo para acceso completo
sudo find / -name "archivo"
```

### **Búsquedas Lentas**

```bash
# Limitar directorios de búsqueda
find /home /etc -name "archivo"  # En lugar de find /

# Usar criterios más específicos
find /var -name "*.log" -mtime -1  # En lugar de find / -name "*.log"
```

## **Enlaces Relacionados**

- [[ls]] - Listar archivos encontrados con find
- [[rm]] - Eliminar archivos encontrados por find
- [[cp]] - Copiar archivos localizados con find
- [[mv]] - Mover archivos encontrados por find
- [[chmod]] - Cambiar permisos de archivos encontrados
- [[grep]] - Buscar contenido en archivos encontrados por find