# wc - Contar Líneas, Palabras y Caracteres

**wc** (word count) es tu **contador universal** en Arch Linux. Te permite obtener estadísticas básicas de archivos de texto: líneas, palabras, caracteres y bytes, siendo fundamental para análisis rápido de archivos y verificación de contenido.

## **¿Qué es wc?**

wc te permite:
- **Contar líneas** en archivos de texto
- **Contar palabras** y caracteres
- **Medir tamaño** en bytes
- **Analizar múltiples archivos** simultáneamente

## **¿Por qué es Útil en Arch Linux?**

En Arch Linux, wc es esencial para verificar tamaños de archivos de configuración, contar entradas en logs, analizar scripts, y obtener estadísticas rápidas de cualquier archivo de texto durante la instalación y mantenimiento.

### **Uso Básico**

```bash
# Mostrar todas las estadísticas (líneas, palabras, caracteres)
wc archivo.txt

# Contar solo líneas
wc -l archivo.txt

# Contar solo palabras
wc -w archivo.txt

# Contar solo caracteres
wc -c archivo.txt

# Múltiples archivos
wc archivo1.txt archivo2.txt
```

## **Conteos Específicos**

### **Contar Líneas (-l)**

```bash
# Contar líneas en archivo
wc -l /etc/passwd
# Resultado: 25 /etc/passwd

# Contar líneas de múltiples archivos
wc -l /etc/passwd /etc/group
# Resultado:
# 25 /etc/passwd
# 45 /etc/group
# 70 total

# Solo mostrar el número (sin nombre de archivo)
wc -l < /etc/passwd
# Resultado: 25

# Casos prácticos:
wc -l /var/log/pacman.log    # Líneas en log de pacman
wc -l ~/.bashrc              # Líneas en configuración bash
wc -l *.txt                  # Líneas en todos los archivos .txt
```

### **Contar Palabras (-w)**

```bash
# Contar palabras en archivo
wc -w documento.txt

# Contar palabras en configuración
wc -w /etc/fstab

# Ejemplo de salida:
wc -w /etc/hosts
# Resultado: 12 /etc/hosts

# Útil para:
# - Verificar tamaño de documentos
# - Analizar archivos de configuración
# - Estimar complejidad de scripts
```

### **Contar Caracteres (-c y -m)**

```bash
# Contar bytes (-c)
wc -c archivo.txt

# Contar caracteres multibyte (-m)
wc -m archivo.txt

# Diferencia entre -c y -m:
echo "ñáéí" | wc -c   # Resultado: 9 (bytes UTF-8)
echo "ñáéí" | wc -m   # Resultado: 5 (caracteres)

# Ejemplos prácticos:
wc -c /etc/hostname     # Tamaño del nombre del host
wc -m script.sh         # Caracteres en script
```

## **Casos de Uso Prácticos**

### **Análisis de Archivos de Sistema**

```bash
# Verificar cantidad de usuarios en el sistema
wc -l /etc/passwd
echo "Usuarios registrados: $(wc -l < /etc/passwd)"

# Contar grupos del sistema
wc -l /etc/group

# Analizar tamaño de configuraciones
echo "=== Análisis de Configuraciones ==="
echo "fstab: $(wc -l < /etc/fstab) líneas"
echo "hosts: $(wc -l < /etc/hosts) líneas"
echo "sudoers: $(wc -l < /etc/sudoers) líneas" 2>/dev/null || echo "sudoers: No accesible"

# Verificar archivos de configuración de usuario
echo "bashrc: $(wc -l < ~/.bashrc) líneas"
echo "profile: $(wc -l < ~/.profile) líneas" 2>/dev/null || echo "profile: No existe"
```

### **Análisis de Logs**

```bash
# Contar entradas en logs del sistema
wc -l /var/log/pacman.log
echo "Transacciones de pacman: $(wc -l < /var/log/pacman.log)"

# Analizar logs por tamaño
echo "=== Análisis de Logs ==="
for log in /var/log/*.log; do
    if [[ -r "$log" ]]; then
        printf "%-30s %6d líneas\n" "$(basename "$log")" "$(wc -l < "$log")"
    fi
done 2>/dev/null | sort -k2 -nr | head -10

# Contar mensajes del kernel
dmesg | wc -l
echo "Mensajes del kernel: $(dmesg | wc -l)"
```

### **Análisis de Scripts y Código**

```bash
# Analizar tamaño de scripts
echo "=== Análisis de Scripts ==="
for script in *.sh; do
    if [[ -f "$script" ]]; then
        lines=$(wc -l < "$script")
        words=$(wc -w < "$script")
        echo "$script: $lines líneas, $words palabras"
    fi
done

# Comparar archivos de configuración
echo "=== Comparación de Configuraciones ==="
echo "Original vs Personalizado:"
if [[ -f config.conf.orig && -f config.conf ]]; then
    echo "Original: $(wc -l < config.conf.orig) líneas"
    echo "Actual:   $(wc -l < config.conf) líneas"
fi
```

## **Combinación con Otros Comandos**

### **wc + find**

```bash
# Contar archivos por tipo
find . -name "*.txt" | wc -l
echo "Archivos .txt encontrados: $(find . -name "*.txt" | wc -l)"

# Contar líneas totales en archivos de un tipo
find . -name "*.sh" -exec cat {} \; | wc -l

# Analizar distribución de archivos
echo "=== Distribución de Archivos ==="
echo "Scripts shell: $(find . -name "*.sh" | wc -l)"
echo "Archivos Python: $(find . -name "*.py" | wc -l)"
echo "Archivos de configuración: $(find . -name "*.conf" | wc -l)"
```

### **wc + grep**

```bash
# Contar líneas que contienen patrón específico
grep "error" /var/log/application.log | wc -l
echo "Errores en log: $(grep -c "error" /var/log/application.log)"

# Contar líneas no vacías
grep -v "^$" archivo.txt | wc -l

# Contar líneas con comentarios
grep "^#" script.sh | wc -l
echo "Líneas comentadas: $(grep -c "^#" script.sh)"

# Análisis de logs específicos
echo "=== Análisis de Log de Pacman ==="
echo "Instalaciones: $(grep "installed" /var/log/pacman.log | wc -l)"
echo "Actualizaciones: $(grep "upgraded" /var/log/pacman.log | wc -l)"
echo "Eliminaciones: $(grep "removed" /var/log/pacman.log | wc -l)"
```

### **wc + sort**

```bash
# Ordenar archivos por número de líneas
wc -l *.txt | sort -n

# Encontrar archivos más largos
wc -l *.conf 2>/dev/null | sort -nr | head -5

# Ordenar por palabras
wc -w *.md | sort -k1 -nr | head -10
```

## **Estadísticas Completas**

### **Análisis Completo de Archivos**

```bash
# Mostrar todas las estadísticas
wc archivo.txt
# Resultado: 150 1200 8500 archivo.txt
#           líneas palabras bytes nombre

# Función para análisis completo
analyze_file() {
    local file="$1"
    
    if [[ ! -f "$file" ]]; then
        echo "❌ Archivo no encontrado: $file"
        return 1
    fi
    
    echo "=== Análisis de $file ==="
    local lines=$(wc -l < "$file")
    local words=$(wc -w < "$file")
    local chars=$(wc -m < "$file")
    local bytes=$(wc -c < "$file")
    
    echo "Líneas:     $lines"
    echo "Palabras:   $words"
    echo "Caracteres: $chars"
    echo "Bytes:      $bytes"
    
    if [[ $lines -gt 0 ]]; then
        echo "Promedio palabras/línea: $((words / lines))"
    fi
    
    if [[ $words -gt 0 ]]; then
        echo "Promedio caracteres/palabra: $((chars / words))"
    fi
}

# Uso: analyze_file ~/.bashrc
```

### **Comparación de Archivos**

```bash
# Comparar estadísticas de múltiples archivos
compare_files() {
    echo "=== Comparación de Archivos ==="
    printf "%-20s %8s %8s %8s\n" "Archivo" "Líneas" "Palabras" "Bytes"
    echo "=================================================="
    
    for file in "$@"; do
        if [[ -f "$file" ]]; then
            local stats=($(wc -l -w -c "$file"))
            printf "%-20s %8d %8d %8d\n" "$(basename "$file")" "${stats[0]}" "${stats[1]}" "${stats[2]}"
        fi
    done
}

# Uso: compare_files ~/.bashrc ~/.profile /etc/bash.bashrc
```

## **Procesamiento desde Entrada Estándar**

### **Conteo en Pipelines**

```bash
# Contar líneas de la salida de comandos
ls -1 | wc -l
echo "Archivos en directorio actual: $(ls -1 | wc -l)"

# Contar procesos en ejecución
ps aux | wc -l
echo "Procesos totales: $(ps aux | tail -n +2 | wc -l)"

# Contar paquetes instalados
pacman -Q | wc -l
echo "Paquetes instalados: $(pacman -Q | wc -l)"

# Contar conexiones de red
ss -tuln | wc -l
echo "Conexiones activas: $(ss -tuln | tail -n +2 | wc -l)"
```

### **Análisis de Salida de Comandos**

```bash
# Analizar salida de comandos del sistema
echo "=== Estadísticas del Sistema ==="

# Información de hardware
echo "Dispositivos de bloque: $(lsblk -l | tail -n +2 | wc -l)"
echo "Módulos del kernel: $(lsmod | tail -n +2 | wc -l)"
echo "Dispositivos PCI: $(lspci | wc -l)"
echo "Dispositivos USB: $(lsusb | wc -l)"

# Información de red
echo "Interfaces de red: $(ip link show | grep "^[0-9]" | wc -l)"

# Información de sistema de archivos
echo "Puntos de montaje: $(mount | wc -l)"
```

## **Scripts de Utilidad**

### **Monitor de Crecimiento de Archivos**

```bash
# Monitorear crecimiento de archivos simple

# Ver crecimiento de un archivo
echo "Líneas antes: $(wc -l < /var/log/pacman.log)"
sleep 10
echo "Líneas después: $(wc -l < /var/log/pacman.log)"

# Comparar tamaños
echo "Bytes antes: $(wc -c < archivo.txt)"
# [realizar operación que modifique archivo]
echo "Bytes después: $(wc -c < archivo.txt)"
```

### **Análisis de Directorio**

```bash
# Análisis simple de archivos de directorio

# Ver tamaño de archivos de configuración
echo "Líneas en archivos de configuración:"
wc -l /etc/fstab /etc/hosts /etc/hostname 2>/dev/null

# Contar tipos de archivo
echo "Scripts en directorio actual:"
find . -name "*.sh" | wc -l

echo "Archivos Python:"
find . -name "*.py" | wc -l

echo "Archivos de configuración:"
find . -name "*.conf" | wc -l
```
```

### **Comparador de Versiones de Archivos**

```bash
# Comparar versiones de archivos simple

# Comparar líneas en dos versiones
echo "Versión original: $(wc -l < archivo_v1.txt) líneas"
echo "Versión nueva: $(wc -l < archivo_v2.txt) líneas"

# Comparar palabras
echo "Palabras original: $(wc -w < archivo_v1.txt)"
echo "Palabras nueva: $(wc -w < archivo_v2.txt)"

# Ver diferencia
echo "Diferencia: $(($(wc -l < archivo_v2.txt) - $(wc -l < archivo_v1.txt))) líneas"
```
        echo "⚠️ Archivo se redujo en ${line_diff#-} líneas"
    else
        echo "📏 Mismo número de líneas"
    fi
}

# Uso: compare_versions config.conf.orig config.conf
```

## **Troubleshooting**

### **Problemas con Encoding**

```bash
# Si wc -c y wc -m dan resultados diferentes inesperados:

# Verificar encoding del archivo
file archivo.txt

# Ver caracteres problemáticos
od -c archivo.txt | head -5

# Convertir encoding si es necesario
iconv -f latin1 -t utf-8 archivo.txt | wc -m

# Para archivos con caracteres de control:
tr -d '\0' < archivo.txt | wc -l  # Eliminar caracteres nulos
```

### **Archivos Muy Grandes**

```bash
# Para archivos extremadamente grandes, wc puede tardar:

# Verificar tamaño primero
ls -lh archivo_gigante.log

# Si es necesario, usar estimación por muestra
head -1000 archivo_gigante.log | wc -l
# Multiplicar por factor estimado

# Para seguimiento en tiempo real de archivos grandes:
tail -f archivo_gigante.log | wc -l  # Contar líneas mientras crece
```

### **Archivos Binarios**

```bash
# wc puede dar resultados inesperados con archivos binarios

# Verificar si es archivo de texto
file archivo_sospechoso

# Para archivos binarios, wc -c sigue siendo útil para tamaño
wc -c programa_binario

# Pero wc -l y wc -w no tienen sentido para binarios
```

## **Optimización y Mejores Prácticas**

### **Eficiencia con Múltiples Archivos**

```bash
# Eficiente: Una sola llamada a wc
wc -l *.txt

# Menos eficiente: Múltiples llamadas
for file in *.txt; do
    wc -l "$file"
done

# Para procesamiento masivo, usar find con xargs:
find . -name "*.txt" -print0 | xargs -0 wc -l
```

### **Verificación de Integridad**

```bash
# Usar wc para verificación básica de integridad
verify_file_integrity() {
    local file="$1"
    local expected_lines="$2"
    
    local actual_lines=$(wc -l < "$file")
    
    if [[ $actual_lines -eq $expected_lines ]]; then
        echo "✅ $file: Integridad correcta ($actual_lines líneas)"
    else
        echo "⚠️ $file: Líneas esperadas $expected_lines, encontradas $actual_lines"
    fi
}

# Uso: verify_file_integrity config.txt 50
```

### **Monitoreo Automatizado**

```bash
# Script para monitoreo regular de archivos críticos
monitor_critical_files() {
    local files=(
        "/etc/passwd"
        "/etc/group"
        "/etc/sudoers"
        "/etc/fstab"
    )
    
    echo "=== Monitoreo de Archivos Críticos ==="
    echo "Fecha: $(date)"
    
    for file in "${files[@]}"; do
        if [[ -r "$file" ]]; then
            local lines=$(wc -l < "$file")
            echo "$(basename "$file"): $lines líneas"
        else
            echo "$(basename "$file"): No accesible"
        fi
    done
}
```

## **Comparación con Alternativas**

### **wc vs du**

```bash
# wc: Contar contenido (líneas, palabras, caracteres)
wc archivo.txt

# du: Tamaño en disco
du -h archivo.txt

# Usar wc para: Análisis de contenido de texto
# Usar du para: Uso de espacio en disco
```

### **wc vs grep -c**

```bash
# wc -l: Contar todas las líneas
wc -l archivo.txt

# grep -c: Contar líneas que coinciden con patrón
grep -c "patrón" archivo.txt

# Equivalencias:
wc -l archivo.txt
grep -c ".*" archivo.txt  # Cuenta todas las líneas (incluso vacías)
grep -c "." archivo.txt   # Cuenta líneas no vacías
```

## **Enlaces Relacionados**

- [[cut]] - Extraer campos específicos de archivos para contar
- [[grep]] - Filtrar contenido antes de contar con wc
- [[sort]] - Ordenar resultados de conteos
- [[uniq]] - Contar ocurrencias únicas después de sort
- [[du]] - Medir tamaño de archivos en disco (complementa wc)
- [[find]] - Buscar archivos para procesar con wc