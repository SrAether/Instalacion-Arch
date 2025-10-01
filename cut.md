# cut - Extraer Columnas y Campos de Texto

**cut** es tu **extractor de precisión** en Arch Linux. Te permite extraer columnas específicas, campos delimitados, o rangos de caracteres de archivos de texto, siendo fundamental para procesamiento de datos tabulares y análisis de configuraciones.

## **¿Qué es cut?**

cut te permite:
- **Extraer columnas específicas** por posición de caracteres
- **Seleccionar campos** separados por delimitadores
- **Procesar archivos CSV** y datos tabulares
- **Filtrar información específica** de archivos de configuración

## **¿Por qué es Útil en Arch Linux?**

En Arch Linux, cut es esencial para extraer información específica de archivos como `/etc/passwd`, `/proc/cpuinfo`, salidas de comandos como `ps` o `ls`, y procesar datos de configuración de forma precisa.

### **Uso Básico**

```bash
# Extraer caracteres por posición
cut -c 1-5 archivo.txt

# Extraer campos por delimitador
cut -d: -f1,3 /etc/passwd

# Extraer múltiples rangos
cut -c 1-3,7-9 archivo.txt

# Desde entrada estándar
ps aux | cut -c 1-11
```

## **Extracción por Caracteres**

### **Posiciones Específicas**

```bash
# Extraer caracteres individuales
cut -c 1 archivo.txt      # Solo primer carácter
cut -c 5 archivo.txt      # Solo quinto carácter
cut -c 1,3,5 archivo.txt  # Caracteres 1, 3 y 5

# Extraer rangos
cut -c 1-10 archivo.txt   # Caracteres 1 al 10
cut -c 5-15 archivo.txt   # Caracteres 5 al 15
cut -c 1-5,10-15 archivo.txt  # Múltiples rangos

# Desde el inicio o hasta el final
cut -c -5 archivo.txt     # Desde inicio hasta carácter 5
cut -c 10- archivo.txt    # Desde carácter 10 hasta el final
```

### **Ejemplos Prácticos con Caracteres**

```bash
# Extraer hora de timestamps
date | cut -c 12-19
# Resultado: 14:30:25

# Extraer información de ls -l
ls -l | cut -c 1-10
# Resultado: Permisos de archivos
# drwxr-xr-x
# -rw-r--r--

# Extraer primeros caracteres de múltiples líneas
cut -c 1-20 /etc/fstab
# Muestra primeros 20 caracteres de cada línea del fstab

# Extraer extensiones de archivos (método simple)
ls *.txt | cut -c 1-10  # Primeros 10 caracteres de nombres
```

## **Extracción por Campos**

### **Delimitadores Comunes**

```bash
# Usar dos puntos como delimitador (común en /etc/passwd)
cut -d: -f1 /etc/passwd   # Extraer nombres de usuario

# Usar espacio como delimitador
cut -d' ' -f1 archivo.txt

# Usar tabulación como delimitador (por defecto)
cut -f1,3 archivo.tsv

# Usar coma como delimitador (archivos CSV)
cut -d, -f2,4 datos.csv

# Usar punto y coma
cut -d';' -f1-3 archivo.txt
```

### **Selección de Campos**

```bash
# Campo individual
cut -d: -f1 /etc/passwd   # Solo primer campo

# Múltiples campos específicos
cut -d: -f1,3,5 /etc/passwd  # Campos 1, 3 y 5

# Rango de campos
cut -d: -f1-3 /etc/passwd    # Campos 1 al 3
cut -d: -f3-6 /etc/passwd    # Campos 3 al 6

# Desde el inicio o hasta el final
cut -d: -f-3 /etc/passwd     # Desde campo 1 hasta 3
cut -d: -f3- /etc/passwd     # Desde campo 3 hasta el final

# Todos los campos excepto algunos
cut -d: -f1,3- /etc/passwd   # Campo 1 y desde 3 hasta el final
```

## **Casos de Uso Prácticos**

### **Análisis de /etc/passwd**

```bash
# Extraer nombres de usuario
cut -d: -f1 /etc/passwd

# Extraer usuarios y sus UIDs
cut -d: -f1,3 /etc/passwd

# Extraer usuarios y sus shells
cut -d: -f1,7 /etc/passwd

# Usuarios del sistema (UID < 1000)
cut -d: -f1,3 /etc/passwd | awk -F: '$2 < 1000 {print $1}'

# Ejemplo de salida:
# root:0
# bin:1
# daemon:2
# usuario:1000
```

### **Procesamiento de Logs**

```bash
# Extraer timestamps de logs
cut -c 1-20 /var/log/pacman.log

# Extraer solo la acción de pacman.log
cut -d' ' -f4- /var/log/pacman.log

# Análisis de logs de acceso web (si los tienes)
# Formato típico: IP - - [timestamp] "método URL" código tamaño
cut -d' ' -f1 access.log     # IPs
cut -d' ' -f7 access.log     # URLs solicitadas

# Extraer información de ps
ps aux | cut -c 1-11,65-     # Usuario y comando
ps aux | cut -c 12-16        # PID solamente
```

### **Análisis de Hardware**

```bash
# Información de CPU
cut -d: -f2 /proc/cpuinfo | head -5

# Información de memoria
cut -d: -f2 /proc/meminfo | head -3

# Sistemas de archivos montados
cut -d' ' -f1,2 /proc/mounts | head -10

# Ejemplo de salida de /proc/cpuinfo:
# processor	: 0
# vendor_id	: GenuineIntel
# cpu family	: 6
# model		: 142
```

### **Procesamiento de Archivos CSV**

```bash
# Archivo CSV de ejemplo: nombre,edad,ciudad,email
# Juan,25,Madrid,juan@email.com
# María,30,Barcelona,maria@email.com

# Extraer solo nombres
cut -d, -f1 usuarios.csv

# Extraer nombres y ciudades
cut -d, -f1,3 usuarios.csv

# Extraer todo excepto email
cut -d, -f1-3 usuarios.csv

# Con header incluido:
head -1 usuarios.csv && tail -n +2 usuarios.csv | cut -d, -f1,3
```

## **Combinación con Otros Comandos**

### **cut + sort**

```bash
# Extraer y ordenar usuarios por UID
cut -d: -f1,3 /etc/passwd | sort -t: -k2 -n

# Extraer extensiones y contar
ls -1 | cut -d. -f2- | sort | uniq -c

# Ordenar por campos extraídos
ps aux | cut -c 1-11,17-24 | sort -k2 -n  # Ordenar por uso de CPU
```

### **cut + uniq**

```bash
# Extraer y contar campos únicos
cut -d: -f7 /etc/passwd | sort | uniq -c  # Contar shells únicos

# Extraer y mostrar valores únicos
cut -d, -f3 datos.csv | sort | uniq  # Ciudades únicas en CSV

# Eliminar duplicados en campos específicos
cut -d: -f1 /etc/passwd | sort | uniq  # Usuarios únicos
```

### **cut + grep**

```bash
# Buscar y extraer información específica
grep "root" /etc/passwd | cut -d: -f1,3,7

# Extraer información de procesos específicos
ps aux | grep "firefox" | cut -c 1-11,17-24

# Filtrar y extraer de logs
grep "error" /var/log/application.log | cut -c 1-20  # Timestamps de errores
```

## **Opciones Avanzadas**

### **Especificar Delimitador de Salida**

```bash
# Cambiar delimitador de salida
cut -d: -f1,3 /etc/passwd --output-delimiter=' '
# Resultado: root 0 (en lugar de root:0)

# Usar delimitador personalizado en salida
cut -d, -f1,3 datos.csv --output-delimiter=' | '
# Resultado: Juan | Madrid

# Útil para reformatear datos:
cut -d: -f1,5 /etc/passwd --output-delimiter=': '
# usuario: Nombre Completo
```

### **Manejo de Líneas sin Delimitador**

```bash
# Por defecto, cut imprime líneas sin delimitador tal como están
echo "sin delimitador" | cut -d: -f2
# Resultado: sin delimitador (línea completa)

# Suprimir líneas sin delimitador
echo -e "con:delimitador\nsin delimitador" | cut -d: -f2 -s
# Resultado: solo "delimitador" (suprime "sin delimitador")

# Útil para filtrar líneas que no coinciden con formato esperado
cut -d: -f1 /etc/passwd -s  # Solo líneas con : (todas en este caso)
```

### **Procesamiento de Archivos con Encoding**

```bash
# Para archivos con caracteres especiales
# cut cuenta bytes, no caracteres necesariamente

# Verificar encoding del archivo primero
file archivo.txt

# Para archivos UTF-8 con caracteres multibyte, usar:
# Método 1: Convertir a ASCII si es posible
iconv -f UTF-8 -t ASCII//IGNORE archivo.txt | cut -c 1-10

# Método 2: Usar herramientas que manejan UTF-8 correctamente
# (cut moderno generalmente maneja UTF-8 bien)
```

## **Scripts y Automatización**

### **Función para Análisis de Archivos Delimitados**

```bash
# Análisis simple de archivos delimitados

# Ver estructura de /etc/passwd
echo "Usuarios del sistema (primeros 5):"
head -5 /etc/passwd | cut -d: -f1,3,7

# Analizar archivo CSV simple
echo "Header del CSV:"
head -1 datos.csv

echo "Primeros datos:"
tail -n +2 datos.csv | head -3 | cut -d, -f1,3
```
```

### **Extractor de Información del Sistema**

```bash
# Extracción simple de información del sistema

# Usuarios con UID >= 1000 (usuarios humanos)
echo "Usuarios del sistema:"
cut -d: -f1,3 /etc/passwd | awk -F: '$2 >= 1000 {print $1}' | head -5

# Shells únicos
echo "Shells disponibles:"
cut -d: -f7 /etc/passwd | sort | uniq -c

# Información básica de CPU
echo "CPU:"
grep "model name" /proc/cpuinfo | head -1 | cut -d: -f2

# Memoria total
echo "Memoria:"
grep "MemTotal" /proc/meminfo | cut -d: -f2
```
```

### **Procesador de CSV Simple**

```bash
#!/bin/bash
# csv_processor.sh - Procesador simple de CSV

csv_extract() {
    local file="$1"
    local fields="$2"
    local has_header="${3:-true}"
    
    if [[ ! -f "$file" ]]; then
        echo "❌ Archivo CSV no encontrado: $file"
        return 1
    fi
    
    echo "=== Procesando CSV: $file ==="
    
    if [[ "$has_header" == "true" ]]; then
        echo "--- Header ---"
        head -1 "$file" | cut -d, -f"$fields"
        echo
        echo "--- Datos ---"
        tail -n +2 "$file" | cut -d, -f"$fields" | head -10
    else
        echo "--- Datos (sin header) ---"
        cut -d, -f"$fields" "$file" | head -10
    fi
    
    echo -e "\n--- Estadísticas ---"
    local total_lines=$(wc -l < "$file")
    echo "Total de líneas: $total_lines"
    
    if [[ "$has_header" == "true" ]]; then
        echo "Líneas de datos: $((total_lines - 1))"
    fi
}

# Uso: csv_extract "datos.csv" "1,3,5" true
```

## **Troubleshooting**

### **Problemas Comunes**

```bash
# Problema: cut no encuentra el delimitador esperado
# Verificar qué delimitadores tiene realmente el archivo
head -3 archivo.txt | od -c
# Puede mostrar espacios, tabs, o caracteres inesperados

# Problema: Caracteres extraños en la salida
# Verificar encoding del archivo
file archivo.txt
locale  # Verificar configuración de locale

# Problema: Campos no alineados como se espera
# El archivo puede tener espacios o tabs mezclados
# Solución: Normalizar delimitadores primero
tr -s ' ' < archivo.txt | cut -d' ' -f2  # Comprimir espacios múltiples
```

### **Archivos con Formato Inconsistente**

```bash
# Si el archivo tiene líneas con diferente número de campos:

# Verificar estructura del archivo
awk -F: '{print NF}' archivo.txt | sort | uniq -c
# Muestra cuántos campos tiene cada tipo de línea

# Filtrar solo líneas con número esperado de campos
awk -F: 'NF==7 {print}' /etc/passwd | cut -d: -f1,3

# Manejar campos faltantes con valores por defecto
awk -F: '{print ($1 ? $1 : "N/A"), ($3 ? $3 : "0")}' archivo.txt
```

### **Problemas con Espacios en Blanco**

```bash
# Si los campos tienen espacios al inicio/final:

# Ver espacios literalmente
cut -d: -f2 /etc/passwd | sed 's/ /[SPACE]/g'

# Limpiar espacios automáticamente
cut -d: -f2 /etc/passwd | sed 's/^ *//; s/ *$//'

# O usar awk que maneja espacios mejor
awk -F: '{print $1, $3}' /etc/passwd  # Automáticamente limpia espacios
```

## **Comparación con Alternativas**

### **cut vs awk**

```bash
# cut: Simple, rápido para extracciones básicas
cut -d: -f1,3 /etc/passwd

# awk: Más potente, programable
awk -F: '{print $1, $3}' /etc/passwd

# Usar cut cuando:
# - Extracciones simples de campos
# - Procesamiento rápido de archivos grandes
# - Scripts simples sin lógica compleja

# Usar awk cuando:
# - Necesitas lógica condicional
# - Cálculos o transformaciones
# - Formateo complejo de salida
```

### **cut vs sed**

```bash
# cut: Para campos estructurados
cut -d: -f1 /etc/passwd

# sed: Para patrones y sustituciones
sed 's/:.*//' /etc/passwd  # Equivalente al cut anterior

# cut es mejor para:
# - Datos tabulares bien estructurados
# - Extracciones por posición fija
# - Simplicidad y legibilidad

# sed es mejor para:
# - Patrones complejos
# - Sustituciones y transformaciones
# - Procesamiento de texto no estructurado
```

## **Optimización y Mejores Prácticas**

### **Eficiencia con Archivos Grandes**

```bash
# cut es muy eficiente con archivos grandes
# No carga todo el archivo en memoria

# Para archivos muy grandes, combinar con head/tail
head -1000 archivo_gigante.csv | cut -d, -f1,3  # Solo primeras 1000 líneas

# Usar con pipelines eficientes
grep "patrón" archivo_grande.txt | cut -d: -f2 | sort | uniq
```

### **Verificación de Datos**

```bash
# Siempre verificar estructura antes de procesar
verify_structure() {
    local file="$1"
    local delimiter="$2"
    
    echo "=== Verificación de Estructura ==="
    echo "Archivo: $file"
    echo "Delimitador: '$delimiter'"
    
    # Verificar primeras líneas
    echo -e "\n--- Primeras 3 líneas ---"
    head -3 "$file"
    
    # Contar campos por línea
    echo -e "\n--- Campos por línea (primeras 10) ---"
    head -10 "$file" | awk -F"$delimiter" '{print "Línea " NR ": " NF " campos"}'
}

verify_structure /etc/passwd ":"
```

### **Funciones de Utilidad**

```bash
# Función para extraer campo por nombre (CSV con header)
extract_column() {
    local file="$1"
    local column_name="$2"
    
    # Obtener número de columna
    local column_num=$(head -1 "$file" | tr ',' '\n' | grep -n "^$column_name$" | cut -d: -f1)
    
    if [[ -z "$column_num" ]]; then
        echo "❌ Columna '$column_name' no encontrada"
        return 1
    fi
    
    echo "Extrayendo columna '$column_name' (posición $column_num)"
    tail -n +2 "$file" | cut -d, -f"$column_num"
}

# Uso: extract_column "usuarios.csv" "email"
```

## **Enlaces Relacionados**

- [[awk]] - Procesamiento más avanzado de campos y patrones
- [[sed]] - Edición y transformación de flujos de texto
- [[sort]] - Ordenar datos extraídos con cut
- [[uniq]] - Encontrar valores únicos en campos extraídos
- [[grep]] - Filtrar líneas antes de extraer campos
- [[tr]] - Transformar caracteres y delimitadores