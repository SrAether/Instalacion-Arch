# uniq - Filtrado de Líneas Únicas

El comando `uniq` es tu **detector de duplicados**. Trabaja con datos ordenados para identificar, contar y filtrar líneas únicas o repetidas, siendo esencial para análisis de datos y eliminación de duplicados.

## **¿Qué es uniq?**

`uniq` te permite:
- **Eliminar líneas duplicadas** consecutivas
- **Contar ocurrencias** de líneas repetidas
- **Mostrar solo líneas únicas** o solo duplicados
- **Comparar campos específicos** para determinar unicidad

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, `uniq` es crucial para análisis de logs, procesamiento de listas de paquetes, limpieza de datos de configuración y generación de reportes estadísticos.

### **Sintaxis Básica**

```bash
# Eliminar duplicados consecutivos
uniq archivo.txt

# Contar ocurrencias
uniq -c archivo.txt

# Mostrar solo líneas únicas
uniq -u archivo.txt

# Mostrar solo líneas duplicadas
uniq -d archivo.txt
```

### **Opciones Fundamentales**

```bash
# -c, --count             = contar ocurrencias
# -d, --repeated          = mostrar solo líneas duplicadas
# -u, --unique            = mostrar solo líneas únicas
# -i, --ignore-case       = ignorar mayúsculas/minúsculas
# -f, --skip-fields=N     = saltar N campos
# -s, --skip-chars=N      = saltar N caracteres
# -w, --check-chars=N     = comparar solo N caracteres
```

## **Uso Básico**

### **Eliminar Duplicados**

```bash
# IMPORTANTE: uniq solo elimina duplicados consecutivos
# Necesitas ordenar primero para eliminar todos los duplicados

# Datos de ejemplo
cat > ejemplo.txt << 'EOF'
manzana
banana
manzana
naranja
banana
manzana
EOF

# Solo elimina duplicados consecutivos
uniq ejemplo.txt
# manzana
# banana  
# manzana
# naranja
# banana
# manzana

# Para eliminar TODOS los duplicados:
sort ejemplo.txt | uniq
# banana
# manzana
# naranja
```

### **Contar Ocurrencias**

```bash
# Contar líneas duplicadas
sort ejemplo.txt | uniq -c
#   3 manzana
#   2 banana
#   1 naranja

# Ordenar por frecuencia (más común primero)
sort ejemplo.txt | uniq -c | sort -nr
#   3 manzana
#   2 banana
#   1 naranja

# Ordenar por frecuencia (menos común primero)
sort ejemplo.txt | uniq -c | sort -n
#   1 naranja
#   2 banana
#   3 manzana
```

## **Casos de Uso en Arch Linux**

### **Análisis de Logs del Sistema**

```bash
# Contar tipos de mensajes de error
grep ERROR /var/log/syslog | awk '{print $5}' | sort | uniq -c | sort -nr

# Servicios más activos en journalctl
journalctl --since="1 hour ago" | awk '{print $5}' | sort | uniq -c | sort -nr | head -10

# IPs únicas en logs de acceso
awk '{print $1}' /var/log/access.log | sort | uniq | wc -l

# Contar códigos de estado HTTP
awk '{print $9}' /var/log/access.log | sort | uniq -c | sort -nr

# Errores únicos (sin contar frecuencia)
grep ERROR /var/log/syslog | sort | uniq -u | head -10
```

### **Gestión de Paquetes**

```bash
# Repositorios únicos de paquetes instalados
pacman -Q | awk '{print $1}' | cut -d'-' -f1 | sort | uniq | wc -l

# Dependencias más comunes
pacman -Qi | grep "Depends On" | tr ',' '\n' | awk '{print $1}' | sort | uniq -c | sort -nr | head -20

# Grupos de paquetes únicos
pacman -Qg | awk '{print $1}' | sort | uniq

# Paquetes huérfanos únicos
pacman -Qdt | awk '{print $1}' | sort | uniq
```

### **Monitoreo del Sistema**

```bash
# Usuarios únicos con procesos activos
ps aux | awk '{print $1}' | sort | uniq

# Comandos únicos en el historial
history | awk '{print $2}' | sort | uniq -c | sort -nr | head -20

# TTYs activos únicos
who | awk '{print $2}' | sort | uniq

# Tipos de archivos únicos en directorio
find /var/log -type f -name "*.*" | awk -F. '{print $NF}' | sort | uniq -c | sort -nr
```

## **Opciones Avanzadas**

### **Filtrado por Campos Específicos**

```bash
# Ignorar campos específicos para comparación
# /etc/passwd tiene formato: usuario:x:uid:gid:info:home:shell

# Usuarios únicos por shell (ignorar otros campos)
sort /etc/passwd | uniq -f 6

# Crear datos de ejemplo para demostrar campos
cat > usuarios.txt << 'EOF'
juan:1000:usuario
maria:1001:usuario  
pedro:1002:admin
ana:1003:usuario
carlos:1004:admin
EOF

# Roles únicos (campo 3)
awk -F: '{print $3}' usuarios.txt | sort | uniq
# admin
# usuario

# Contar usuarios por rol
awk -F: '{print $3}' usuarios.txt | sort | uniq -c
#   2 admin
#   3 usuario
```

### **Ignorar Caracteres y Casos**

```bash
# Ignorar mayúsculas/minúsculas
cat > nombres.txt << 'EOF'
Juan
JUAN
juan
Maria
MARIA
Pedro
EOF

# Sin ignorar caso
sort nombres.txt | uniq
# JUAN
# MARIA
# Juan
# Maria
# Pedro
# juan

# Ignorando caso
sort nombres.txt | uniq -i
# JUAN
# MARIA  
# Pedro

# Ignorar primeros N caracteres
echo -e "001-archivo\n002-archivo\n003-documento" | uniq -s 4
# 001-archivo
# 003-documento
```

### **Comparar Solo Primeros Caracteres**

```bash
# Comparar solo primeros N caracteres
echo -e "archivo1.txt\narchivo2.txt\ndocumento1.pdf" | uniq -w 7
# archivo1.txt
# documento1.pdf

# Útil para agrupar archivos similares
ls /var/log/ | uniq -w 3 -c | sort -nr
```

## **Análisis Estadístico**

### **Scripts de Análisis Completo**

```bash
#!/bin/bash
# Análisis estadístico completo con uniq

analyze_file() {
    local file="$1"
    local field="${2:-1}"  # Campo a analizar (por defecto el primero)
    
    echo "=== Análisis estadístico de $file ==="
    echo "Analizando campo $field"
    echo
    
    # Preparar datos
    if [[ $field -eq 1 ]]; then
        data=$(cat "$file")
    else
        data=$(awk -v f="$field" '{print $f}' "$file")
    fi
    
    # Estadísticas básicas
    local total_lines=$(echo "$data" | wc -l)
    local unique_lines=$(echo "$data" | sort | uniq | wc -l)
    local duplicate_lines=$(echo "$data" | sort | uniq -d | wc -l)
    
    echo "Total de líneas: $total_lines"
    echo "Líneas únicas: $unique_lines"
    echo "Líneas con duplicados: $duplicate_lines"
    echo "Porcentaje de duplicación: $(echo "scale=2; ($total_lines - $unique_lines) * 100 / $total_lines" | bc)%"
    echo
    
    # Top 10 más frecuentes
    echo "=== Top 10 más frecuentes ==="
    echo "$data" | sort | uniq -c | sort -nr | head -10
    echo
    
    # Elementos únicos (aparecen solo una vez)
    echo "=== Elementos únicos (aparecen solo una vez) ==="
    echo "$data" | sort | uniq -u | head -10
    echo
    
    # Elementos más duplicados
    echo "=== Elementos más duplicados ==="
    echo "$data" | sort | uniq -c | sort -nr | awk '$1 > 1 {print $0}' | head -10
}

# Función específica para análisis de logs
analyze_log() {
    local log_file="$1"
    
    echo "=== Análisis de Log: $log_file ==="
    
    # Análisis por hora
    echo "Actividad por hora:"
    awk '{print $3}' "$log_file" | awk -F: '{print $1":00"}' | sort | uniq -c | sort -k2n
    echo
    
    # Servicios más activos
    echo "Servicios más activos:"
    awk '{print $5}' "$log_file" | sed 's/\[.*\]://' | sort | uniq -c | sort -nr | head -10
    echo
    
    # Niveles de log únicos
    echo "Niveles de log:"
    grep -oE '\b(DEBUG|INFO|WARN|ERROR|FATAL)\b' "$log_file" | sort | uniq -c | sort -nr
}

# Ejemplos de uso
# analyze_file "/etc/passwd" 7  # Analizar shells
# analyze_log "/var/log/syslog"
```

### **Análisis de Patrones de Uso**

```bash
#!/bin/bash
# Análisis de patrones con uniq

# Análisis de comandos del historial
analyze_command_usage() {
    echo "=== Análisis de Uso de Comandos ==="
    
    # Comandos más usados
    echo "Comandos más utilizados:"
    history | awk '{print $2}' | sort | uniq -c | sort -nr | head -15
    echo
    
    # Comandos únicos (usados solo una vez)
    echo "Comandos usados solo una vez:"
    history | awk '{print $2}' | sort | uniq -u | head -10
    echo
    
    # Patrones de comandos (primeras 3 letras)
    echo "Patrones de comandos (3 primeras letras):"
    history | awk '{print substr($2,1,3)}' | sort | uniq -c | sort -nr | head -10
}

# Análisis de archivos por extensión
analyze_file_types() {
    local directory="$1"
    
    echo "=== Análisis de Tipos de Archivo en $directory ==="
    
    # Extensiones más comunes
    echo "Extensiones más comunes:"
    find "$directory" -type f -name "*.*" | awk -F. '{print tolower($NF)}' | sort | uniq -c | sort -nr | head -15
    echo
    
    # Archivos sin extensión
    local no_extension=$(find "$directory" -type f ! -name "*.*" | wc -l)
    echo "Archivos sin extensión: $no_extension"
    echo
    
    # Extensiones únicas (solo un archivo)
    echo "Extensiones raras (solo un archivo):"
    find "$directory" -type f -name "*.*" | awk -F. '{print tolower($NF)}' | sort | uniq -u | head -10
}

# Análisis de usuarios y grupos
analyze_users_groups() {
    echo "=== Análisis de Usuarios y Grupos ==="
    
    # Shells más usados
    echo "Shells más utilizados:"
    awk -F: '{print $7}' /etc/passwd | sort | uniq -c | sort -nr
    echo
    
    # Grupos más usados
    echo "Grupos más utilizados:"
    awk -F: '{print $4}' /etc/passwd | sort | uniq -c | sort -nr | head -10
    echo
    
    # UIDs únicos vs compartidos
    echo "Análisis de UIDs:"
    local unique_uids=$(awk -F: '{print $3}' /etc/passwd | sort | uniq | wc -l)
    local total_users=$(wc -l < /etc/passwd)
    echo "UIDs únicos: $unique_uids de $total_users usuarios"
    
    # UIDs compartidos (si los hay)
    local shared_uids=$(awk -F: '{print $3}' /etc/passwd | sort | uniq -d)
    if [[ -n "$shared_uids" ]]; then
        echo "UIDs compartidos encontrados:"
        echo "$shared_uids"
    fi
}

# Ejecutar análisis
analyze_command_usage
echo
analyze_file_types "/home"
echo  
analyze_users_groups
```

## **Integración con Pipelines**

### **Con [[sort]] para Análisis Completo**

```bash
# Pipeline típico: sort -> uniq -> sort
comando | sort | uniq -c | sort -nr

# Análisis de errores más comunes
grep ERROR /var/log/syslog | awk '{for(i=6;i<=NF;i++) printf "%s ", $i; print ""}' | sort | uniq -c | sort -nr | head -10

# Top IPs por conexiones
netstat -tuln | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr

# Análisis de tipos MIME en logs web
awk '{print $7}' /var/log/access.log | awk -F. '{print $NF}' | sort | uniq -c | sort -nr
```

### **Con [[awk]] para Procesamiento Avanzado**

```bash
# Combinar awk y uniq para análisis por campos
ps aux | awk '{print $1, $3}' | sort | uniq -c | sort -nr

# Análisis de memoria por usuario
ps aux | awk '{user[$1]+=$4} END {for (u in user) print user[u], u}' | sort -nr

# Estadísticas de tamaño de archivo
ls -la | awk '{print $5}' | sort -n | uniq -c

# Análisis temporal de logs
awk '{print $3}' /var/log/syslog | awk -F: '{print $1}' | sort | uniq -c | sort -k2n
```

### **Con [[grep]] para Filtrado Específico**

```bash
# Errores únicos por servicio
grep ERROR /var/log/syslog | awk '{print $5}' | sort | uniq -c | sort -nr

# Tipos de conexión de red únicos
netstat -tuln | grep LISTEN | awk '{print $1}' | sort | uniq -c

# Usuarios con sesiones SSH únicas
grep "sshd" /var/log/auth.log | grep "Accepted" | awk '{print $9}' | sort | uniq

# Comandos sudo únicos por usuario
grep sudo /var/log/auth.log | awk '{print $6, $14}' | sort | uniq -c | sort -nr
```

## **Casos de Uso Especializados**

### **Limpieza de Listas de Configuración**

```bash
# Limpiar listas de hosts/IPs
clean_host_list() {
    local input_file="$1"
    local output_file="$2"
    
    # Eliminar comentarios, líneas vacías y duplicados
    grep -v '^#' "$input_file" | grep -v '^$' | sort | uniq > "$output_file"
    
    echo "Lista limpia guardada en $output_file"
    echo "Líneas originales: $(wc -l < "$input_file")"
    echo "Líneas únicas: $(wc -l < "$output_file")"
}

# Consolidar múltiples archivos de configuración
merge_configs() {
    local pattern="$1"
    local output="$2"
    
    cat $pattern | grep -v '^#' | grep -v '^$' | sort | uniq > "$output"
    echo "Configuraciones consolidadas en $output"
}

# Ejemplos:
# clean_host_list "/etc/hosts.backup" "/etc/hosts.clean"
# merge_configs "/etc/conf.d/*.conf" "/etc/consolidated.conf"
```

### **Análisis de Duplicados en Sistema**

```bash
#!/bin/bash
# Encontrar y analizar archivos duplicados por nombre

find_duplicate_names() {
    local search_path="$1"
    
    echo "=== Archivos con nombres duplicados en $search_path ==="
    
    # Encontrar nombres de archivo duplicados
    find "$search_path" -type f -printf '%f\n' | sort | uniq -d > /tmp/duplicate_names.txt
    
    if [[ -s /tmp/duplicate_names.txt ]]; then
        echo "Nombres de archivo duplicados encontrados:"
        cat /tmp/duplicate_names.txt
        echo
        
        # Mostrar ubicaciones de cada duplicado
        while read -r duplicate_name; do
            echo "Ubicaciones de '$duplicate_name':"
            find "$search_path" -type f -name "$duplicate_name" -ls
            echo
        done < /tmp/duplicate_names.txt
    else
        echo "No se encontraron nombres de archivo duplicados"
    fi
    
    rm -f /tmp/duplicate_names.txt
}

# Análisis de contenido duplicado (por tamaño)
find_duplicate_sizes() {
    local search_path="$1"
    
    echo "=== Archivos con tamaños duplicados en $search_path ==="
    
    # Encontrar archivos con mismo tamaño
    find "$search_path" -type f -exec ls -l {} \; | awk '{print $5, $NF}' | sort | uniq -d -w 10
}

# Ejecutar análisis
# find_duplicate_names "/home"
# find_duplicate_sizes "/var/log"
```

## **Optimización y Mejores Prácticas**

### **Performance con Archivos Grandes**

```bash
# Para archivos muy grandes, usar LC_ALL=C
export LC_ALL=C
sort archivo_grande.txt | uniq -c | sort -nr

# Pipeline optimizado para memoria
sort -u archivo_grande.txt > archivo_sin_duplicados.txt

# Procesar en chunks para archivos gigantes
split_and_uniq() {
    local large_file="$1"
    local chunk_size="1000000"  # 1M líneas por chunk
    
    split -l "$chunk_size" "$large_file" "chunk_"
    
    for chunk in chunk_*; do
        sort "$chunk" | uniq > "${chunk}.uniq"
        rm "$chunk"
    done
    
    sort -m chunk_*.uniq | uniq > "${large_file}.uniq"
    rm chunk_*.uniq
}
```

### **Verificación de Resultados**

```bash
# Verificar que uniq funcionó correctamente
verify_uniq() {
    local original="$1"
    local processed="$2"
    
    echo "Archivo original: $(wc -l < "$original") líneas"
    echo "Archivo procesado: $(wc -l < "$processed") líneas"
    echo "Duplicados eliminados: $(($(wc -l < "$original") - $(wc -l < "$processed")))"
    
    # Verificar que no hay duplicados en resultado
    local remaining_dupes=$(sort "$processed" | uniq -d | wc -l)
    if [[ $remaining_dupes -eq 0 ]]; then
        echo "✓ Verificación exitosa: no quedan duplicados"
    else
        echo "✗ Error: aún existen $remaining_dupes duplicados"
    fi
}
```

## **Enlaces Relacionados**

- [[sort]] - Ordenar datos antes de usar uniq
- [[awk]] - Procesar campos específicos con uniq
- [[grep]] - Filtrar datos antes de usar uniq
- [[cut]] - Extraer campos para análisis con uniq
- [[wc]] - Contar resultados de uniq
- [[comm]] - Comparar archivos línea por línea