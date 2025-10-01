# awk - Procesador de Texto Avanzado

El comando `awk` es tu **procesador de texto inteligente**. Es un lenguaje de programación completo especializado en el procesamiento de texto estructurado, ideal para extraer, transformar y analizar datos de archivos de configuración, logs y salidas de comandos.

## **¿Qué es awk?**

`awk` te permite:
- **Procesar texto línea por línea** con patrones y acciones
- **Extraer campos específicos** de datos estructurados
- **Realizar cálculos** y operaciones matemáticas
- **Generar reportes** formatados a partir de datos

## **¿Por qué es Poderoso en Arch Linux?**

Durante la administración del sistema, `awk` es excepcional para procesar archivos de configuración, analizar logs del sistema, generar estadísticas y transformar salidas de comandos en información útil.

### **Sintaxis Básica**

```bash
# Estructura básica
awk 'patrón { acción }' archivo

# Procesar entrada estándar
comando | awk 'patrón { acción }'

# Usar separador de campo específico
awk -F':' '{ print $1 }' /etc/passwd
```

### **Conceptos Fundamentales**

```bash
# Variables automáticas importantes:
# $0 = línea completa
# $1, $2, $3... = campos (columnas)
# NR = número de línea actual
# NF = número de campos en línea actual
# FS = separador de campo (por defecto espacio/tab)
```

## **Operaciones Básicas**

### **Imprimir y Extraer Campos**

```bash
# Imprimir línea completa
awk '{ print $0 }' archivo.txt

# Imprimir primer campo
awk '{ print $1 }' archivo.txt

# Imprimir múltiples campos
awk '{ print $1, $3 }' archivo.txt

# Con separador personalizado
awk -F':' '{ print $1, $3 }' /etc/passwd

# Imprimir con formato personalizado
awk '{ print "Usuario: " $1 " ID: " $3 }' /etc/passwd
```

### **Filtrado con Patrones**

```bash
# Líneas que contienen texto específico
awk '/root/ { print $0 }' /etc/passwd

# Líneas que NO contienen texto
awk '!/^#/ { print $0 }' archivo.conf

# Comparaciones numéricas
awk '$3 > 1000 { print $1, $3 }' /etc/passwd

# Múltiples condiciones
awk '$3 > 1000 && $1 ~ /^user/ { print $1 }' /etc/passwd
```

## **Casos de Uso en Arch Linux**

### **Análisis del Sistema**

```bash
# Usuarios normales (UID > 1000)
awk -F':' '$3 > 1000 { print $1, $3 }' /etc/passwd

# Procesos que consumen más memoria
ps aux | awk '$4 > 5.0 { print $2, $1, $4, $11 }'

# Espacio en disco por partición
df -h | awk 'NR > 1 { print $6 ": " $5 }'

# Interfaces de red activas
ip a | awk '/^[0-9]+:/ { gsub(/:/, "", $2); print $2 }'
```

### **Procesamiento de Logs**

```bash
# Errores en logs con timestamp
awk '/error/ { print $1, $2, $0 }' /var/log/syslog

# Contar tipos de eventos en logs
journalctl --no-pager | awk '{ print $5 }' | sort | uniq -c

# IPs más activas en logs de acceso
awk '{ print $1 }' /var/log/access.log | sort | uniq -c | sort -nr

# Estadísticas de horario de eventos
awk '{ print substr($3, 1, 2) }' /var/log/auth.log | sort | uniq -c
```

### **Configuración del Sistema**

```bash
# Servicios habilitados
systemctl list-units | awk '/enabled/ { print $1 }'

# Montajes activos sin filesystems especiales
mount | awk '!/^(proc|sys|dev)/ { print $1, $3, $5 }'

# Variables de entorno importantes
env | awk -F'=' '/^(PATH|HOME|USER)/ { print $1 ": " $2 }'
```

## **Operaciones Matemáticas**

### **Cálculos y Estadísticas**

```bash
# Sumar valores de una columna
ps aux | awk '{ sum += $4 } END { print "Memoria total: " sum "%" }'

# Promedio de una columna
awk '{ sum += $3; count++ } END { print "Promedio: " sum/count }' numeros.txt

# Encontrar máximo y mínimo
awk 'BEGIN { max = 0; min = 999999 } 
     { if ($1 > max) max = $1; if ($1 < min) min = $1 } 
     END { print "Max: " max ", Min: " min }' datos.txt

# Contar líneas que cumplen condición
ps aux | awk '$3 > 1.0 { count++ } END { print count " procesos con >1% CPU" }'
```

### **Formateo de Números**

```bash
# Formatear números con printf
awk '{ printf "%.2f MB\n", $1/1024 }' tamaños_kb.txt

# Formatear como porcentajes
awk '{ printf "%s: %.1f%%\n", $1, $2*100 }' datos.txt

# Formatear con separadores de miles
awk '{ printf "%\047d\n", $1 }' numeros.txt
```

## **Programación Avanzada en awk**

### **Variables y Estructuras de Control**

```bash
# Variables personalizadas
awk 'BEGIN { contador = 0 } 
     /patrón/ { contador++ } 
     END { print "Encontrado " contador " veces" }' archivo.txt

# Condicionales
awk '{ if ($3 > 1000) print $1 " es usuario normal"; 
       else print $1 " es usuario del sistema" }' /etc/passwd

# Bucles
awk '{ for (i=1; i<=NF; i++) print i ": " $i }' archivo.txt

# Arrays asociativos
awk '{ count[$1]++ } END { for (ip in count) print ip, count[ip] }' access.log
```

### **Funciones Incorporadas**

```bash
# Funciones de string
awk '{ print length($0) }' archivo.txt              # Longitud de línea
awk '{ print toupper($1) }' archivo.txt             # Mayúsculas
awk '{ print substr($0, 1, 10) }' archivo.txt       # Substring
awk '{ gsub(/old/, "new"); print }' archivo.txt     # Reemplazar texto

# Funciones matemáticas
awk '{ print sqrt($1) }' numeros.txt                # Raíz cuadrada
awk '{ print int($1) }' decimales.txt               # Parte entera
awk 'BEGIN { print rand() }'                        # Número aleatorio
```

## **Scripts awk Complejos**

### **Análisis de Logs de Sistema**

```bash
# Script para analizar logs de autenticación
awk '
BEGIN { 
    failed = 0; success = 0 
}
/Failed password/ { 
    failed++; 
    failed_ips[$11]++ 
}
/Accepted password/ { 
    success++; 
    users[$9]++ 
}
END {
    print "=== Estadísticas de Autenticación ==="
    print "Intentos fallidos: " failed
    print "Accesos exitosos: " success
    print "\n=== IPs con fallos ==="
    for (ip in failed_ips) 
        print ip ": " failed_ips[ip]
    print "\n=== Usuarios exitosos ==="
    for (user in users) 
        print user ": " users[user]
}' /var/log/auth.log
```

### **Monitor de Recursos del Sistema**

```bash
# Script para monitorear procesos
ps aux | awk '
BEGIN {
    print "=== Monitor de Procesos ==="
    total_mem = 0
    total_cpu = 0
}
NR > 1 {
    total_mem += $4
    total_cpu += $3
    if ($4 > 5.0) {
        high_mem[NR] = sprintf("%s: %.1f%%", $11, $4)
    }
    if ($3 > 10.0) {
        high_cpu[NR] = sprintf("%s: %.1f%%", $11, $3)
    }
}
END {
    print "Memoria total usada: " total_mem "%"
    print "CPU total usado: " total_cpu "%"
    print "\n=== Procesos con alta memoria (>5%) ==="
    for (i in high_mem) print high_mem[i]
    print "\n=== Procesos con alta CPU (>10%) ==="
    for (i in high_cpu) print high_cpu[i]
}'
```

## **Integración con Otros Comandos**

### **Con [[grep]] y [[find]]**

```bash
# Buscar y procesar archivos grandes
find /var -name "*.log" -exec awk '/error/ { print FILENAME ": " $0 }' {} \;

# Combinar grep y awk
grep "Failed" /var/log/auth.log | awk '{ print $11 }' | sort | uniq -c

# Procesar resultados de find
find /home -type f -name "*.txt" | awk -F'/' '{ print $NF }' | sort
```

### **Con [[ps]] y comandos del sistema**

```bash
# Top procesos por memoria
ps aux | awk 'NR > 1 { print $4, $11 }' | sort -nr | head -10

# Análisis de red
netstat -tulpn | awk '$1 == "tcp" && $4 ~ /:80$/ { print $7 }'

# Espacio de directorios
du -sh /home/* | awk '{ print $2 ": " $1 }' | sort
```

### **Con [[sed]] para transformaciones**

```bash
# Combinar awk y sed para limpieza de datos
awk '{ print $1, $3 }' archivo.txt | sed 's/,//g' | awk '{ sum += $2 } END { print sum }'
```

## **Casos de Uso Específicos**

### **Administración de Red**

```bash
# Análisis de conexiones activas
ss -tuln | awk 'NR > 1 { split($4, a, ":"); ports[a[2]]++ } 
                END { for (p in ports) print "Puerto " p ": " ports[p] " conexiones" }'

# Estadísticas de interfaces
cat /proc/net/dev | awk 'NR > 2 { 
    printf "%-10s RX: %10d bytes TX: %10d bytes\n", substr($1,1,length($1)-1), $2, $10 
}'
```

### **Monitoreo de Performance**

```bash
# CPU por core
cat /proc/loadavg | awk '{ 
    print "Load Average: 1min=" $1 " 5min=" $2 " 15min=" $3 
    print "Procesos activos: " $4 
}'

# Memoria detallada
cat /proc/meminfo | awk '
/^MemTotal/ { total = $2 }
/^MemFree/ { free = $2 }
/^MemAvailable/ { available = $2 }
END {
    used = total - free
    printf "Memoria Total: %.1f GB\n", total/1024/1024
    printf "Memoria Usada: %.1f GB (%.1f%%)\n", used/1024/1024, (used/total)*100
    printf "Memoria Disponible: %.1f GB\n", available/1024/1024
}'
```

## **Optimización y Mejores Prácticas**

### **Performance**

```bash
# Para archivos grandes, usar condiciones tempranas
awk 'NR > 1000 { exit } /patrón/ { print }' archivo_enorme.txt

# Usar variables predefinidas cuando sea posible
awk -v usuario="$USER" '$1 == usuario { print }' /etc/passwd

# Minimizar operaciones en loops
awk '{ print $1 }' archivo.txt   # Mejor que: awk '{ for(i=1;i<=1;i++) print $i }'
```

### **Debugging**

```bash
# Mostrar número de línea para debug
awk '{ print NR ": " $0 }' archivo.txt

# Verificar número de campos
awk '{ print NF, $0 }' archivo.txt

# Imprimir variables importantes
awk '{ print "NR=" NR " NF=" NF " FS=[" FS "]" }' archivo.txt
```

## **Troubleshooting Común**

### **Problemas con Separadores**

```bash
# Si los campos no se separan correctamente
awk -F'\t' '{ print $1 }' archivo.txt        # Para tabs
awk -F',' '{ print $1 }' archivo.csv         # Para CSV
awk -F'[[:space:]]+' '{ print $1 }' archivo.txt  # Múltiples espacios
```

### **Caracteres Especiales**

```bash
# Escapar caracteres especiales en patrones
awk '/\$/ { print }' archivo.txt              # Buscar símbolo $
awk -F'\\|' '{ print $1 }' archivo.txt       # Separador |
```

## **Enlaces Relacionados**

- [[grep]] - Buscar patrones simples antes de procesar con awk
- [[sed]] - Editor de flujo que complementa las capacidades de awk
- [[find]] - Buscar archivos para procesar con awk
- [[cat]] - Mostrar archivos que se procesan con awk
- [[sort]] - Ordenar salidas de awk
- [[uniq]] - Eliminar duplicados en salidas de awk