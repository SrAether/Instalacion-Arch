# top - Monitor de Procesos en Tiempo Real

**top** es tu **monitor en vivo** del sistema. Te muestra en tiempo real qué procesos están consumiendo más recursos (CPU y memoria), siendo fundamental para identificar problemas de rendimiento en Arch Linux.

## **¿Qué es top?**

top te permite:
- **Ver procesos activos** en tiempo real
- **Monitorear uso de CPU y memoria** por proceso
- **Identificar procesos problemáticos** que consumen muchos recursos
- **Gestionar procesos** directamente desde la interfaz

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, top es esencial para diagnosticar problemas de rendimiento, identificar procesos que ralentizan el sistema, y gestionar recursos especialmente en sistemas con memoria limitada.

### **Uso Básico**

```bash
# Ejecutar top (presiona 'q' para salir)
top

# Actualizar cada 2 segundos
top -d 2

# Mostrar solo procesos de un usuario
top -u usuario

# Mostrar solo un número específico de procesos
top -n 1

# Modo batch (para scripts)
top -b -n 1
```

## **Interfaz de top**

### **Información del Sistema (Parte Superior)**

```
top - 15:30:45 up 2 days,  3:42,  2 users,  load average: 0.85, 0.65, 0.45
Tasks: 245 total,   1 running, 244 sleeping,   0 stopped,   0 zombie
%Cpu(s):  12.5 us,  2.3 sy,  0.0 ni, 84.7 id,  0.5 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   7952.1 total,   2130.4 free,   3891.2 used,   1930.5 buff/cache
MiB Swap:   8191.0 total,   8191.0 free,      0.0 used.   3642.6 avail Mem
```

**Interpretación:**
- **load average**: Promedio de carga del sistema (1, 5, 15 minutos)
- **Tasks**: Total de procesos y su estado
- **%Cpu(s)**: Porcentaje de uso de CPU por categoría
- **Mem**: Uso de memoria física
- **Swap**: Uso de memoria de intercambio

### **Lista de Procesos**

```
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 user      20   0  123456   45678   12345 S   5.3   2.1   0:45.67 firefox
 5678 root      20   0   78901   23456    7890 R   3.2   1.1   1:23.45 systemd
```

**Columnas importantes:**
- **PID**: ID del proceso
- **USER**: Usuario propietario
- **%CPU**: Porcentaje de CPU usado
- **%MEM**: Porcentaje de memoria usado
- **TIME+**: Tiempo total de CPU usado
- **COMMAND**: Nombre del comando/programa

## **Comandos Interactivos Dentro de top**

### **Navegación y Visualización**

```bash
# Teclas dentro de top:

# Ayuda
h o ?

# Salir
q

# Actualizar inmediatamente
Espacio

# Ordenar por:
P    # CPU (por defecto)
M    # Memoria
T    # Tiempo de ejecución
N    # PID
u    # Filtrar por usuario

# Cambiar prioridad de proceso
r    # Luego ingresa PID y nuevo valor nice

# Terminar proceso
k    # Luego ingresa PID

# Cambiar intervalo de actualización
s    # Luego ingresa segundos
```

### **Personalización de la Vista**

```bash
# Dentro de top:

# Mostrar/ocultar columnas específicas
f    # Seleccionar campos a mostrar

# Cambiar entre vista de procesos y hilos
H

# Mostrar ruta completa de comandos
c

# Mostrar árbol de procesos
V

# Alternar entre modos de vista
1    # Vista de CPU individual vs combinada

# Resaltar columna de ordenación
x

# Colores
z
```

## **Casos de Uso Prácticos**

### **Identificar Procesos que Consumen CPU**

```bash
# 1. Ejecutar top
top

# 2. Presionar 'P' para ordenar por CPU
# 3. Los procesos con mayor %CPU aparecerán arriba

# Ver solo procesos que usan más del 5% CPU
top | awk '$9 > 5.0'  # (en modo batch)

# Ejemplo de salida:
#   PID USER      %CPU COMMAND
#  1234 user      25.3 firefox
#  5678 user      15.2 chromium
```

### **Monitorear Uso de Memoria**

```bash
# 1. Ejecutar top
top

# 2. Presionar 'M' para ordenar por memoria
# 3. Los procesos con mayor %MEM aparecerán arriba

# Ver información resumida de memoria
top -b -n 1 | head -5

# Ejemplo cuando hay poco memoria:
# Si "avail Mem" es bajo, el sistema podría estar lento
# Si hay uso de Swap, indica escasez de RAM
```

### **Diagnosticar Sistema Lento**

```bash
# Pasos para diagnosticar:

# 1. Ver load average
top | head -1
# Si load average > número de CPUs, el sistema está sobrecargado

# 2. Verificar procesos con alta CPU
top
# Presionar 'P' y buscar procesos con %CPU alto

# 3. Verificar memoria
top
# Presionar 'M' y verificar si hay poco "avail Mem"

# 4. Buscar procesos zombie
top
# En la línea "Tasks", verificar si hay procesos zombie

# 5. Ver procesos en estado "D" (uninterruptible sleep)
# Estos pueden indicar problemas de I/O
```

## **Variantes y Alternativas**

### **top vs htop**

```bash
# top - comando básico incluido en el sistema
top

# htop - versión mejorada (necesita instalarse)
sudo pacman -S htop
htop

# Ventajas de htop:
# - Interfaz más colorida y amigable
# - Navegación con flechas
# - Búsqueda de procesos
# - Gestión más fácil de procesos
```

### **Comandos Relacionados**

```bash
# ps - snapshot de procesos
ps aux

# pgrep - buscar procesos por nombre
pgrep firefox

# pkill - terminar procesos por nombre
pkill firefox

# jobs - procesos en segundo plano del shell actual
jobs

# nohup - ejecutar comando inmune a desconexión
nohup comando &
```

## **Gestión de Procesos desde top**

### **Cambiar Prioridad de Procesos**

```bash
# Dentro de top:
# 1. Presionar 'r'
# 2. Ingresar PID del proceso
# 3. Ingresar nuevo valor nice (-20 a 19)
#    -20 = mayor prioridad
#    19 = menor prioridad

# Ejemplo: reducir prioridad de un proceso que consume mucha CPU
# r -> PID -> 10 (menor prioridad)
```

### **Terminar Procesos**

```bash
# Dentro de top:
# 1. Presionar 'k'
# 2. Ingresar PID del proceso
# 3. Confirmar señal (por defecto TERM)

# Señales comunes:
# 15 (TERM) - terminación normal
# 9 (KILL) - terminación forzada
# 1 (HUP) - reiniciar proceso
```

## **top en Scripts y Automatización**

### **Modo Batch para Scripts**

```bash
# Ejecutar top una vez y salir
top -b -n 1

# Obtener top 5 procesos por CPU
top -b -n 1 | head -12 | tail -5

# Obtener información específica
top -b -n 1 | awk 'NR==3 {print "CPU: " $2 " user, " $4 " idle"}'

# Guardar snapshot del sistema
top -b -n 1 > /tmp/system_snapshot.txt
```

### **Monitoreo Simple**

```bash
# Ver procesos que consumen más del 10% CPU
check_high_cpu() {
    echo "=== Procesos con alta CPU (>10%) ==="
    top -b -n 1 | awk '$9 > 10 {print $1, $2, $9"% CPU", $12}'
}

# Ver uso total de memoria
check_memory() {
    echo "=== Estado de Memoria ==="
    top -b -n 1 | head -5 | tail -2
}

# Ejecutar verificaciones
check_high_cpu
echo
check_memory
```

## **Interpretación de la Información**

### **Load Average**

```bash
# Load average muestra la carga promedio del sistema:
# 0.00-1.00 = Sistema sin carga a moderadamente cargado
# 1.00-2.00 = Sistema cargado pero manejable
# >2.00     = Sistema sobrecargado (en sistemas de 1 CPU)

# Para sistemas multi-core, multiplica por número de cores
# Ejemplo: Sistema de 4 cores puede manejar load de hasta ~4.00
```

### **Uso de CPU**

```bash
# %Cpu(s): 12.5 us, 2.3 sy, 0.0 ni, 84.7 id, 0.5 wa, 0.0 hi, 0.0 si, 0.0 st

# us (user)     = tiempo en procesos de usuario
# sy (system)   = tiempo en kernel/sistema  
# ni (nice)     = tiempo en procesos con prioridad alterada
# id (idle)     = tiempo inactivo
# wa (wait)     = tiempo esperando I/O
# hi (hardware) = tiempo en interrupciones hardware
# si (software) = tiempo en interrupciones software
# st (steal)    = tiempo robado por hipervisor (VMs)
```

### **Uso de Memoria**

```bash
# MiB Mem: 7952.1 total, 2130.4 free, 3891.2 used, 1930.5 buff/cache

# total      = memoria total instalada
# free       = memoria completamente libre
# used       = memoria usada por procesos
# buff/cache = memoria usada para buffers y cache (reutilizable)
# avail Mem  = memoria disponible para nuevos procesos
```

## **Solución de Problemas Comunes**

### **Sistema Lento**

```bash
# 1. Verificar load average
# Si es > número de CPUs, hay sobrecarga

# 2. Buscar procesos con alta CPU
# Ordenar por CPU ('P') y verificar procesos arriba

# 3. Verificar memoria disponible
# Si "avail Mem" es muy bajo, hay escasez de memoria

# 4. Verificar I/O wait
# Si 'wa' es alto, hay problemas de disco
```

### **Memoria Insuficiente**

```bash
# Señales de memoria insuficiente:
# - Uso de Swap > 0
# - "avail Mem" muy bajo
# - Procesos grandes en top de memoria

# Soluciones:
# 1. Cerrar aplicaciones innecesarias
# 2. Identificar memory leaks
# 3. Considerar agregar más RAM
```

### **CPU al 100%**

```bash
# Buscar procesos problemáticos:
# 1. Ordenar por CPU en top
# 2. Identificar si es normal o un problema
# 3. Si es un proceso colgado, terminarlo
# 4. Si es uso legítimo, considerar optimización
```

## **Enlaces Relacionados**

- [[htop]] - Versión mejorada de top
- [[ps]] - Información estática de procesos
- [[kill]] - Terminar procesos identificados en top
- [[jobs]] - Gestionar trabajos en segundo plano
- [[systemctl]] - Gestionar servicios del sistema
- [[free]] - Información detallada de memoria
- [[pgrep]] - Buscar procesos por nombre