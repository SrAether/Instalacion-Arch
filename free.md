# free - Información de Memoria

**free** es tu **medidor de memoria** en Arch Linux. Te muestra el uso actual de RAM y swap, siendo fundamental para monitorear el rendimiento del sistema y diagnosticar problemas de memoria.

## **¿Qué es free?**

free te permite:
- **Ver uso de memoria RAM** total, usada y disponible
- **Monitorear memoria swap** (intercambio)
- **Identificar problemas de memoria** y rendimiento
- **Obtener información** en diferentes formatos y unidades

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, free es esencial para monitorear recursos durante la instalación, identificar cuando necesitas más RAM, y diagnosticar problemas de rendimiento del sistema.

### **Uso Básico**

```bash
# Mostrar información de memoria
free

# En formato legible para humanos
free -h

# Actualizar cada X segundos
free -s 2

# Mostrar totales
free -t
```

## **Interpretación de la Salida**

### **Salida Estándar de free**

```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           7.8G        2.1G        3.2G        245M        2.5G        5.1G
Swap:          2.0G          0B        2.0G
```

**Explicación de columnas:**
- **total**: Memoria total instalada
- **used**: Memoria actualmente en uso por procesos
- **free**: Memoria completamente libre
- **shared**: Memoria compartida entre procesos
- **buff/cache**: Memoria usada para buffers y cache (reutilizable)
- **available**: Memoria realmente disponible para nuevos procesos

### **Diferencia entre 'free' y 'available'**

```bash
# 'free' = memoria completamente sin usar
# 'available' = memoria que puede ser liberada inmediatamente

# Ejemplo:
# free: 1.2G      # Solo 1.2G completamente libre
# available: 5.1G # Pero 5.1G está disponible (incluye cache reutilizable)

# El sistema puede liberar buff/cache instantáneamente cuando se necesita
# Por eso 'available' es el número más importante
```

## **Formatos de Salida**

### **Diferentes Unidades**

```bash
# En bytes (por defecto)
free

# En formato legible (K, M, G)
free -h

# En kilobytes
free -k

# En megabytes
free -m

# En gigabytes
free -g

# Ejemplo comparativo:
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           7962        2156        3287         245        2518        5204
Swap:          2047           0        2047

$ free -g
              total        used        free      shared  buff/cache   available
Mem:              7           2           3           0           2           5
Swap:             1           0           1
```

### **Opciones de Visualización**

```bash
# Mostrar totales (suma de RAM + Swap)
free -t

# Salida ancha (más columnas)
free -w

# Solo mostrar memoria física (sin swap)
free | grep "^Mem"

# Solo mostrar swap
free | grep "^Swap"
```

## **Monitoreo Continuo**

### **Monitoreo en Tiempo Real**

```bash
# Actualizar cada 2 segundos
free -h -s 2

# Actualizar 10 veces cada 3 segundos y parar
free -h -s 3 -c 10

# Con timestamp
while true; do
    echo "$(date): $(free -h | grep Mem)"
    sleep 5
done
```

### **Monitoreo Durante Tareas Específicas**

```bash
# Monitorear memoria mientras ejecutas algo específico
# Terminal 1:
free -h -s 1

# Terminal 2:
make -j$(nproc)  # Compilar algo que use mucha memoria

# Observar cómo cambia el uso de memoria
```

## **Análisis de Situaciones Comunes**

### **Sistema con Poca Memoria**

```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           2.0G        1.8G         50M         32M        150M        120M
Swap:          1.0G        800M        200M

# Señales de problema:
# - 'available' muy bajo (< 200M en este ejemplo)
# - Swap en uso (800M usado)
# - 'free' muy bajo

# Acciones recomendadas:
# 1. Cerrar aplicaciones innecesarias
# 2. Identificar procesos que consumen mucha memoria
# 3. Considerar agregar más RAM
```

### **Sistema con Memoria Abundante**

```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:          16.0G        3.2G        8.1G        124M        4.7G       12.4G
Swap:          4.0G          0B        4.0G

# Señales de sistema saludable:
# - 'available' alto (12.4G)
# - Swap sin usar (0B)
# - Buen balance entre used y available
```

### **Sistema con Cache Activo**

```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           8.0G        2.1G        1.2G        156M        4.7G        5.4G
Swap:          2.0G          0B        2.0G

# Situación normal:
# - buff/cache alto (4.7G) = sistema eficiente
# - available alto (5.4G) = memoria disponible cuando se necesite
# - El cache se libera automáticamente si se necesita RAM
```

## **Identificar Problemas de Memoria**

### **Detectar Memory Leaks**

```bash
# Monitorear memoria a lo largo del tiempo
echo "Timestamp,Used,Available" > memoria.log

for i in {1..60}; do
    timestamp=$(date '+%H:%M:%S')
    used=$(free -m | grep Mem | awk '{print $3}')
    available=$(free -m | grep Mem | awk '{print $7}')
    echo "$timestamp,$used,$available" >> memoria.log
    sleep 60
done

# Después revisar si el uso sigue creciendo sin razón
cat memoria.log
```

### **Verificar si Necesitas Más RAM**

```bash
# Indicadores de que necesitas más RAM:

# 1. Swap constantemente en uso
free -h | grep Swap
# Si swap > 0 de forma persistente

# 2. Available memory muy bajo
available=$(free -m | grep Mem | awk '{print $7}')
if [[ $available -lt 500 ]]; then
    echo "⚠️ Memoria disponible baja: ${available}MB"
fi

# 3. Sistema lento con poca memoria libre
total=$(free -m | grep Mem | awk '{print $2}')
available=$(free -m | grep Mem | awk '{print $7}')
percentage=$((available * 100 / total))
echo "Porcentaje de memoria disponible: ${percentage}%"
```

## **Optimización de Memoria**

### **Limpiar Cache Manualmente**

```bash
# ⚠️ Solo hacer si realmente necesitas liberar memoria

# Limpiar page cache
sudo sysctl vm.drop_caches=1

# Limpiar dentries e inodes
sudo sysctl vm.drop_caches=2

# Limpiar todo
sudo sysctl vm.drop_caches=3

# Verificar efecto
free -h

# Nota: El sistema volverá a usar cache automáticamente
# Solo útil para pruebas o situaciones extremas
```

### **Verificar Configuración de Swap**

```bash
# Ver información de swap
swapon --show

# Ver prioridad de swap
cat /proc/swaps

# Verificar swappiness (qué tan agresivo es el uso de swap)
cat /proc/sys/vm/swappiness
# Valor por defecto: 60
# 0 = evitar swap al máximo
# 100 = usar swap agresivamente

# Cambiar swappiness temporalmente
sudo sysctl vm.swappiness=10

# Cambiar permanentemente
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
```

## **Durante la Instalación de Arch**

### **Verificar Memoria Antes de Instalar**

```bash
# En el entorno live de Arch, verificar memoria disponible
free -h

# Para instalación básica necesitas al menos:
# - 512MB para instalación mínima
# - 1GB recomendado
# - 2GB+ para entorno de escritorio

# Si tienes poca memoria, evitar:
# - Múltiples descargas simultáneas
# - Compilación pesada durante instalación
# - Abrir muchos programas a la vez
```

### **Configurar Swap Durante Instalación**

```bash
# Después de particionar, verificar que swap esté activo
swapon --show

# Si creaste partición swap
mkswap /dev/sdaX
swapon /dev/sdaX

# Verificar que funciona
free -h
```

## **Comparación con Otros Comandos**

### **free vs top/htop**

```bash
# free: Vista estática de memoria total del sistema
free -h

# top/htop: Vista dinámica + memoria por proceso
top
htop

# Usar free para:
# - Vista rápida del estado general
# - Scripts y monitoreo automatizado
# - Verificación básica

# Usar top/htop para:
# - Identificar procesos que consumen memoria
# - Monitoreo interactivo
# - Gestión de procesos
```

### **free vs /proc/meminfo**

```bash
# free: Vista resumida y formateada
free -h

# /proc/meminfo: Información detallada
cat /proc/meminfo | head -20

# /proc/meminfo incluye detalles como:
# - MemTotal, MemFree, MemAvailable
# - Buffers, Cached, SwapCached
# - Dirty, Writeback, AnonPages
# - Y muchos más campos específicos
```

## **Scripts Útiles para Monitoreo**

### **Alerta de Memoria Baja**

```bash
check_memory() {
    available=$(free -m | grep Mem | awk '{print $7}')
    threshold=500  # MB
    
    if [[ $available -lt $threshold ]]; then
        echo "⚠️ ALERTA: Memoria disponible baja: ${available}MB"
        echo "Top 5 procesos por memoria:"
        ps aux --sort=-%mem | head -6
        return 1
    else
        echo "✅ Memoria OK: ${available}MB disponibles"
        return 0
    fi
}

check_memory
```

### **Información Resumida**

```bash
memory_summary() {
    echo "=== Resumen de Memoria ==="
    
    # Información básica
    free -h | head -2
    echo
    
    # Porcentajes
    total=$(free -m | grep Mem | awk '{print $2}')
    used=$(free -m | grep Mem | awk '{print $3}')
    available=$(free -m | grep Mem | awk '{print $7}')
    
    used_percent=$((used * 100 / total))
    available_percent=$((available * 100 / total))
    
    echo "Uso: ${used_percent}% | Disponible: ${available_percent}%"
    
    # Estado del swap
    swap_used=$(free -m | grep Swap | awk '{print $3}')
    if [[ $swap_used -gt 0 ]]; then
        echo "⚠️ Swap en uso: ${swap_used}MB"
    else
        echo "✅ Swap sin usar"
    fi
}

memory_summary
```

## **Enlaces Relacionados**

- [[top]] - Ver uso de memoria por proceso
- [[htop]] - Monitor visual de memoria y procesos
- [[ps]] - Información de memoria de procesos específicos
- [[df]] - Espacio en disco (complementa información de memoria)
- [[systemctl]] - Gestionar servicios que pueden consumir memoria
- [[mount]] - Gestionar swap y sistemas de archivos