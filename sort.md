# sort - Ordenamiento de Datos

El comando `sort` es tu **organizador de datos**. Te permite ordenar líneas de texto de manera alfabética, numérica, por fechas y con múltiples criterios, siendo fundamental para el procesamiento y análisis de información.

## **¿Qué es sort?**

`sort` te permite:
- **Ordenar texto** alfabética y numéricamente
- **Eliminar duplicados** mientras ordenas
- **Ordenar por campos específicos** en archivos estructurados
- **Combinar archivos ordenados**

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, `sort` es esencial para organizar salidas de comandos, procesar logs, generar reportes ordenados y preparar datos para análisis con otras herramientas.

### **Sintaxis Básica**

```bash
# Ordenar archivo
sort archivo.txt

# Ordenar entrada estándar
comando | sort

# Ordenar y guardar resultado
sort archivo.txt > archivo_ordenado.txt

# Ordenar in-place (modificar archivo original)
sort -o archivo.txt archivo.txt
```

### **Opciones Fundamentales**

```bash
# -n, --numeric-sort       = ordenar numéricamente
# -r, --reverse           = orden inverso
# -u, --unique            = eliminar duplicados
# -k, --key=KEYDEF        = ordenar por campo específico
# -t, --field-separator   = especificar separador de campos
# -f, --ignore-case       = ignorar mayúsculas/minúsculas
# -h, --human-numeric-sort = ordenar números legibles (1K, 2M, 3G)
# -M, --month-sort        = ordenar por nombres de meses
# -V, --version-sort      = ordenar por números de versión
```

## **Ordenamiento Básico**

### **Ordenamiento Alfabético**

```bash
# Ordenar alfabéticamente (por defecto)
sort nombres.txt

# Ordenar ignorando mayúsculas/minúsculas
sort -f nombres.txt

# Ordenar en orden inverso
sort -r nombres.txt

# Ordenar y eliminar duplicados
sort -u lista.txt

# Ejemplo con datos:
echo -e "banana\napple\nCherry\ndate" | sort
# apple
# Cherry
# banana
# date

echo -e "banana\napple\nCherry\ndate" | sort -f
# apple
# banana
# Cherry
# date
```

### **Ordenamiento Numérico**

```bash
# Ordenar números correctamente
echo -e "10\n2\n1\n20" | sort -n
# 1
# 2
# 10
# 20

# Sin -n (orden alfabético):
echo -e "10\n2\n1\n20" | sort
# 1
# 10
# 2
# 20

# Ordenar números decimales
echo -e "3.14\n2.71\n1.41" | sort -n

# Ordenar con unidades legibles
echo -e "1K\n2M\n512\n1G" | sort -h
# 512
# 1K
# 2M
# 1G
```

## **Casos de Uso en Arch Linux**

### **Análisis de Logs del Sistema**

```bash
# Ordenar logs por timestamp
sort /var/log/pacman.log | tail -20

# Contar y ordenar errores por frecuencia
grep ERROR /var/log/syslog | sort | uniq -c | sort -nr

# Ordenar logs de acceso por IP
awk '{print $1}' /var/log/access.log | sort | uniq -c | sort -nr

# Análisis de tamaños de archivo en logs
ls -la /var/log/*.log | awk '{print $5, $9}' | sort -nr
```

### **Gestión de Paquetes**

```bash
# Listar paquetes instalados ordenados
pacman -Q | sort

# Paquetes por tamaño (mayor a menor)
pacman -Qi | grep "Installed Size" | sort -hr

# Repositorios ordenados por cantidad de paquetes
pacman -Sl | awk '{print $1}' | sort | uniq -c | sort -nr

# Dependencias ordenadas alfabéticamente
pacman -Qi package | grep "Depends On" | tr ' ' '\n' | sort
```

### **Monitoreo del Sistema**

```bash
# Procesos ordenados por uso de memoria
ps aux | sort -k4 -nr | head -20

# Usuarios ordenados por cantidad de procesos
ps aux | awk '{print $1}' | sort | uniq -c | sort -nr

# Directorios ordenados por tamaño
du -h /home/* | sort -hr

# Archivos más grandes del sistema
find /var -type f -exec ls -la {} \; 2>/dev/null | sort -k5 -nr | head -10
```

## **Ordenamiento por Campos**

### **Usar Delimitadores Específicos**

```bash
# Archivo CSV ordenado por segunda columna
sort -t, -k2 datos.csv

# /etc/passwd ordenado por UID (campo 3)
sort -t: -k3 -n /etc/passwd

# Ordenar por múltiples campos
sort -t: -k3 -n -k1 /etc/passwd  # Por UID, luego por nombre

# Logs con timestamp ordenados
sort -k1,2 /var/log/syslog  # Por los primeros dos campos (fecha y hora)
```

### **Ejemplos Prácticos con Campos**

```bash
# Crear archivo de prueba
cat > empleados.txt << 'EOF'
Juan,30,5000
Ana,25,4500
Pedro,35,5500
Maria,28,4800
Carlos,32,5200
EOF

# Ordenar por edad (campo 2)
sort -t, -k2 -n empleados.txt

# Ordenar por salario (campo 3) descendente
sort -t, -k3 -nr empleados.txt

# Ordenar por nombre (campo 1) ignorando mayúsculas
sort -t, -k1 -f empleados.txt

# Ordenamiento múltiple: por edad, luego por salario
sort -t, -k2 -n -k3 -nr empleados.txt
```

### **Rangos de Campos**

```bash
# Ordenar solo por el primer carácter del campo 1
sort -k1.1,1.1 archivo.txt

# Ordenar por los caracteres 2-5 del primer campo
sort -k1.2,1.5 archivo.txt

# Ordenar por el campo 2 completo, luego por caracteres 1-3 del campo 1
sort -k2 -k1.1,1.3 archivo.txt

# Ejemplo con fechas en formato DD/MM/YYYY
echo -e "15/03/2024\n01/12/2023\n20/03/2024" | sort -t/ -k3 -k2 -k1 -n
```

## **Ordenamiento Especializado**

### **Ordenamiento por Fechas y Versiones**

```bash
# Ordenar por meses
echo -e "Jan\nMar\nFeb\nDec" | sort -M

# Ordenar fechas en logs
grep "2024" /var/log/syslog | sort -k1M -k2n

# Ordenar versiones de software
echo -e "1.10.2\n1.2.3\n1.10.10\n2.0.1" | sort -V

# Ordenar paquetes por versión
pacman -Q | sort -k2 -V
```

### **Ordenamiento con Números Legibles**

```bash
# Ordenar tamaños de archivo
ls -lh | sort -k5 -h

# Ordenar salida de du
du -h /var/log/* | sort -hr

# Ordenar memoria de procesos
ps aux | sort -k6 -hr  # Por RSS (memoria física)

# Ejemplo:
echo -e "1.5G\n300M\n2.1G\n50K\n800M" | sort -h
# 50K
# 300M
# 800M
# 1.5G
# 2.1G
```

## **Combinación con Otros Comandos**

### **Con [[uniq]] para Análisis**

```bash
# Contar ocurrencias únicas
sort archivo.txt | uniq -c

# Mostrar solo elementos únicos (que aparecen una vez)
sort archivo.txt | uniq -u

# Mostrar solo duplicados
sort archivo.txt | uniq -d

# Análisis de IPs en logs
awk '{print $1}' /var/log/access.log | sort | uniq -c | sort -nr | head -10
```

### **Con [[head]] y [[tail]]**

```bash
# Top 10 procesos por memoria
ps aux | sort -k4 -nr | head -10

# Últimos 5 archivos modificados
ls -lt | head -6 | tail -5

# Archivos más pequeños en directorio
ls -la | sort -k5 -n | head -10

# Usuarios con más procesos
ps aux | awk '{print $1}' | sort | uniq -c | sort -nr | head -5
```

### **Con [[awk]] para Análisis Avanzado**

```bash
# Análisis de uso de CPU por usuario
ps aux | awk '{user[$1]+=$3} END {for (u in user) print user[u], u}' | sort -nr

# Estadísticas de tamaño de archivos por extensión
find /home -type f -name "*.*" | awk -F. '{ext[tolower($NF)]++} END {for (e in ext) print ext[e], e}' | sort -nr

# Análisis de logs por hora
awk '{print $4}' /var/log/access.log | awk -F: '{print $2}' | sort | uniq -c | sort -nr
```

## **Scripts de Procesamiento**

### **Script de Análisis de Logs**

```bash
# Análisis simple de logs del sistema

LOG_FILE="/var/log/syslog"

echo "=== Análisis de Logs ==="
echo "Archivo analizado: $LOG_FILE"
echo

# Top 10 servicios más activos
echo "=== Servicios Más Activos ==="
awk '{print $5}' "$LOG_FILE" | sort | uniq -c | sort -nr | head -10

# Errores comunes
echo "=== Errores Más Comunes ==="
grep -i error "$LOG_FILE" | sort | uniq -c | sort -nr | head -5
```
```

### **Script de Organización de Archivos**

```bash
# Organizar archivos por diferentes criterios

echo "=== Archivos ordenados por tamaño ==="
ls -lh | sort -k5 -hr

echo "=== Archivos ordenados por fecha ==="
ls -lt

echo "=== Archivos ordenados por nombre ==="
ls -1 | sort

echo "=== Archivos ordenados por extensión ==="
ls -1 | sort -t. -k2
```
            echo "Archivos ordenados por fecha:"
            find "$directory" -type f -exec ls -lt {} \; | sort -k6,7 | head -20
            ;;
        "type")
            echo "Archivos agrupados por tipo:"
            find "$directory" -type f -name "*.*" | awk -F. '{print tolower($NF)}' | sort | uniq -c | sort -nr
            ;;
        "name")
            echo "Archivos ordenados alfabéticamente:"
            find "$directory" -type f | sort | head -20
            ;;
        *)
            echo "Criterio desconocido. Use: size, date, type, name"
            return 1
            ;;
    esac
}

# Análisis de espacio por directorio
analyze_directory_space() {
    local base_dir="$1"
    
    echo "=== Análisis de espacio en $base_dir ==="
    
    # Subdirectorios ordenados por tamaño
    echo "Subdirectorios por tamaño:"
    du -h "$base_dir"/* 2>/dev/null | sort -hr | head -15
    echo
    
    # Archivos más grandes
    echo "Archivos más grandes:"
    find "$base_dir" -type f -exec ls -lh {} \; 2>/dev/null | sort -k5 -hr | head -10
    echo
    
    # Tipos de archivo por espacio usado
    echo "Espacio por tipo de archivo:"
    find "$base_dir" -type f -name "*.*" | while read -r file; do
        ext="${file##*.}"
        size=$(stat -c%s "$file" 2>/dev/null || echo 0)
        echo "$size ${ext,,}"
    done | awk '{ext[$2]+=$1} END {for (e in ext) printf "%.1f MB %s\n", ext[e]/1024/1024, e}' | sort -nr | head -10
}

# Ejemplos de uso
organize_files "/home/usuario/Downloads" "size"
echo
analyze_directory_space "/var/log"
```

## **Ordenamiento Avanzado**

### **Múltiples Criterios y Estabilidad**

```bash
# Ordenamiento estable (mantiene orden relativo)
sort -s -k2 archivo.txt

# Múltiples campos con diferentes direcciones
sort -k1,1 -k2nr -k3 archivo.txt  # Campo 1 ascendente, campo 2 numérico descendente, campo 3 ascendente

# Ordenar por diferentes tipos en el mismo comando
sort -k1,1d -k2,2n archivo.txt  # Campo 1 alfabético, campo 2 numérico

# Ejemplo complejo con /etc/passwd
# Ordenar por shell, luego por UID numérico descendente
sort -t: -k7,7 -k3,3nr /etc/passwd
```

### **Ordenamiento de Archivos Grandes**

```bash
# Ordenar archivos grandes eficientemente
sort -T /tmp --parallel=4 archivo_grande.txt

# Combinar archivos ya ordenados
sort -m archivo1_ordenado.txt archivo2_ordenado.txt > combinado.txt

# Verificar si archivo está ordenado
sort -c archivo.txt && echo "Archivo ya ordenado" || echo "Archivo no ordenado"

# Ordenar y verificar
sort archivo.txt | tee archivo_ordenado.txt | wc -l
```

## **Casos de Uso Especializados**

### **Análisis de Configuraciones**

```bash
# Ordenar variables de entorno
env | sort

# Configuraciones únicas en archivos
grep -h "^[^#]" /etc/*.conf | sort -u

# Ordenar opciones de kernel
cat /proc/cmdline | tr ' ' '\n' | sort

# Módulos del kernel ordenados
lsmod | sort -k1
```

### **Análisis de Red y Seguridad**

```bash
# IPs únicas en logs de acceso
awk '{print $1}' /var/log/access.log | sort -u

# Puertos más utilizados
netstat -tuln | awk '/LISTEN/ {print $4}' | awk -F: '{print $NF}' | sort -n | uniq -c | sort -nr

# Conexiones por estado
netstat -tun | awk 'NR>2 {print $6}' | sort | uniq -c | sort -nr

# Análisis de intentos de login fallidos
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr
```

## **Optimización y Performance**

### **Configuración para Archivos Grandes**

```bash
# Configurar memoria y paralelismo
export LC_ALL=C  # Usar locale C para mayor velocidad
sort --parallel=8 -S 1G archivo_muy_grande.txt

# Usar directorio temporal rápido (SSD)
sort -T /dev/shm archivo.txt  # Usar RAM como temp

# Comprimir archivos temporales
sort --compress-program=gzip archivo.txt
```

### **Tips de Rendimiento**

```bash
# Para archivos muy grandes, usar sort externa
split -l 1000000 archivo_gigante.txt temp_
for file in temp_*; do
    sort "$file" > "${file}.sorted"
    rm "$file"
done
sort -m temp_*.sorted > archivo_gigante_sorted.txt
rm temp_*.sorted

# Pipeline optimizado para análisis frecuente
mkfifo sort_pipe
sort < sort_pipe > resultado.txt &
comando_que_genera_datos > sort_pipe
```

## **Enlaces Relacionados**

- [[uniq]] - Eliminar duplicados de datos ordenados
- [[awk]] - Procesar campos antes de ordenar
- [[grep]] - Filtrar datos antes de ordenar
- [[head]] - Ver primeros resultados ordenados
- [[tail]] - Ver últimos resultados ordenados
- [[cut]] - Extraer campos específicos para ordenar