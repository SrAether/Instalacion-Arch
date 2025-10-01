# kill - Terminar Procesos

**kill** es tu herramienta para **terminar procesos** en Arch Linux. Te permite enviar señales a procesos para terminarlos, reiniciarlos o cambiar su comportamiento de forma controlada.

## **¿Qué es kill?**

kill te permite:
- **Terminar procesos** que no responden o consumen demasiados recursos
- **Enviar señales** a procesos para controlar su comportamiento
- **Gestionar procesos** desde la línea de comandos
- **Forzar la terminación** de aplicaciones colgadas

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, kill es esencial para gestionar procesos problemáticos, cerrar aplicaciones que no responden, y controlar servicios cuando systemctl no es suficiente.

### **Sintaxis Básica**

```bash
# Terminar proceso por PID
kill PID

# Enviar señal específica
kill -SEÑAL PID

# Terminar proceso por nombre
killall nombre_proceso

# Forzar terminación
kill -9 PID

# Terminar múltiples procesos
kill PID1 PID2 PID3
```

## **Señales Principales**

### **Señales Más Usadas**

```bash
# TERM (15) - Terminación normal (por defecto)
kill PID
kill -15 PID
kill -TERM PID

# KILL (9) - Terminación forzada (no se puede ignorar)
kill -9 PID
kill -KILL PID

# HUP (1) - Reiniciar/recargar configuración
kill -1 PID
kill -HUP PID

# INT (2) - Interrupción (como Ctrl+C)
kill -2 PID
kill -INT PID

# STOP (19) - Pausar proceso
kill -19 PID
kill -STOP PID

# CONT (18) - Continuar proceso pausado
kill -18 PID
kill -CONT PID
```

### **Lista Completa de Señales**

```bash
# Ver todas las señales disponibles
kill -l

# Salida típica:
# 1) SIGHUP   2) SIGINT   3) SIGQUIT  4) SIGILL   5) SIGTRAP
# 6) SIGABRT  7) SIGBUS   8) SIGFPE   9) SIGKILL 10) SIGUSR1
#11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM
#16) SIGSTKFLT 17) SIGCHLD 18) SIGCONT 19) SIGSTOP 20) SIGTSTP

# Información específica de una señal
kill -l 9    # Muestra: KILL
kill -l TERM # Muestra: 15
```

## **Encontrar Procesos para Terminar**

### **Usar ps para Encontrar PID**

```bash
# Ver todos los procesos
ps aux

# Buscar proceso específico
ps aux | grep firefox
ps aux | grep nombre_proceso

# Obtener solo el PID
ps aux | grep firefox | awk '{print $2}'

# Ejemplo de salida:
# USER  PID %CPU %MEM    VSZ   RSS TTY STAT START   TIME COMMAND
# user 1234  5.2  8.1 2456789 567890 ?  Sl  14:30   2:45 firefox
```

### **Usar pgrep para Buscar por Nombre**

```bash
# Encontrar PID por nombre de proceso
pgrep firefox
pgrep -f nombre_completo_comando

# Encontrar procesos de un usuario
pgrep -u usuario

# Mostrar nombre y PID
pgrep -l firefox

# Ejemplo de salida:
# 1234 firefox
# 1235 firefox
# 1236 firefox
```

### **Usar top/htop para Identificar Procesos**

```bash
# En top: identificar procesos problemáticos
top
# Buscar procesos con alta CPU o memoria

# En htop: más fácil de navegar
htop
# Usar F3 para buscar, F9 para terminar directamente
```

## **Métodos de Terminación**

### **Terminación Normal (TERM)**

```bash
# Envía señal TERM (15) - permite limpieza
kill 1234

# El proceso puede:
# - Guardar archivos abiertos
# - Cerrar conexiones de red
# - Limpiar archivos temporales
# - Ejecutar rutinas de cierre

# Ejemplo: cerrar Firefox correctamente
pgrep firefox | head -1 | xargs kill
```

### **Terminación Forzada (KILL)**

```bash
# Envía señal KILL (9) - terminación inmediata
kill -9 1234

# Úsala cuando:
# - El proceso no responde a TERM
# - Necesitas terminación inmediata
# - El proceso está colgado

# ⚠️ Cuidado: No permite limpieza, puede causar pérdida de datos

# Ejemplo: proceso colgado que no responde
kill -9 $(pgrep proceso_colgado)
```

### **Escalonamiento de Señales**

```bash
# Método recomendado - escalar gradualmente:

# 1. Intentar terminación normal
kill PID
sleep 5

# 2. Si no termina, ser más insistente
kill -INT PID
sleep 3

# 3. Como último recurso, forzar
kill -9 PID

# Script para terminación escalada:
terminate_gracefully() {
    local pid=$1
    echo "Terminando proceso $pid..."
    
    # Señal TERM
    kill $pid 2>/dev/null && echo "Enviada señal TERM"
    sleep 5
    
    # Verificar si terminó
    if kill -0 $pid 2>/dev/null; then
        echo "Proceso aún activo, enviando INT..."
        kill -INT $pid
        sleep 3
        
        # Última verificación
        if kill -0 $pid 2>/dev/null; then
            echo "Forzando terminación..."
            kill -9 $pid
        fi
    fi
    
    echo "Proceso terminado"
}
```

## **Gestión de Múltiples Procesos**

### **killall - Terminar por Nombre**

```bash
# Terminar todos los procesos con un nombre
killall firefox
killall -9 chrome

# Con patrón
killall -i firefox  # Pedir confirmación
killall -v firefox  # Modo verbose

# Por usuario
killall -u usuario proceso

# Ejemplo: cerrar todas las instancias de un navegador
killall firefox
sleep 2
killall -9 firefox  # Forzar si no terminaron
```

### **pkill - Terminar con Patrones**

```bash
# Terminar por patrón de nombre
pkill firefox
pkill -f "python script.py"

# Por usuario
pkill -u usuario

# Por grupo
pkill -g grupo

# Por terminal
pkill -t pts/0

# Ejemplo: terminar todos los procesos Python de un usuario
pkill -u usuario python
```

### **Terminar Procesos Relacionados**

```bash
# Terminar proceso padre y todos sus hijos
kill_tree() {
    local pid=$1
    local children=$(pgrep -P $pid)
    
    # Terminar hijos primero
    for child in $children; do
        kill_tree $child
    done
    
    # Terminar el proceso padre
    kill $pid 2>/dev/null
}

# Uso: kill_tree 1234
```

## **Casos de Uso Prácticos**

### **Aplicación No Responde**

```bash
# Scenario: Firefox se colgó y no responde

# 1. Intentar cerrar normalmente desde la interfaz
# (Si no funciona, continuar)

# 2. Buscar el proceso
pgrep -l firefox
# Salida: 1234 firefox, 1235 firefox, 1236 firefox

# 3. Terminar proceso principal (generalmente el de menor PID)
kill 1234

# 4. Esperar unos segundos
sleep 5

# 5. Si aún hay procesos, forzar terminación
pkill -9 firefox

# 6. Verificar que terminaron
pgrep firefox || echo "Todos los procesos de Firefox terminados"
```

### **Proceso Consumiendo Mucha CPU**

```bash
# 1. Identificar proceso problemático
top -b -n 1 | head -10

# 2. O usar htop para identificar visualmente
htop

# 3. Obtener PID del proceso problemático
high_cpu_pid=$(ps aux --sort=-%cpu | awk 'NR==2 {print $2}')

# 4. Verificar qué proceso es
ps -p $high_cpu_pid -o pid,ppid,cmd

# 5. Decidir si es seguro terminarlo
# Si es seguro:
kill $high_cpu_pid

# Si no responde:
kill -9 $high_cpu_pid
```

### **Limpiar Procesos Zombie**

```bash
# Los procesos zombie no se pueden terminar directamente
# Hay que terminar el proceso padre

# 1. Encontrar procesos zombie
ps aux | awk '$8 ~ /^Z/ {print $2, $3, $11}'

# 2. Encontrar proceso padre
zombie_pid=1234
parent_pid=$(ps -o ppid= -p $zombie_pid)

# 3. Enviar señal CHLD al padre
kill -CHLD $parent_pid

# 4. Si no funciona, considerar reiniciar el padre
echo "Proceso padre: $parent_pid"
ps -p $parent_pid -o pid,cmd
# Decidir si es seguro reiniciarlo
```

## **Gestión de Trabajos en Segundo Plano**

### **Trabajos del Shell Actual**

```bash
# Ver trabajos activos
jobs

# Traer trabajo al primer plano
fg %1

# Enviar trabajo al segundo plano
bg %1

# Terminar trabajo específico
kill %1

# Ejemplo de gestión:
# 1. Ejecutar comando en segundo plano
comando_largo &

# 2. Ver trabajos
jobs
# [1]+  Running    comando_largo &

# 3. Terminarlo si es necesario
kill %1
```

### **nohup y Procesos Persistentes**

```bash
# Procesos iniciados con nohup
nohup comando_largo &

# Para terminarlos, necesitas el PID
ps aux | grep comando_largo
kill PID_encontrado

# O usar pkill
pkill -f comando_largo
```

## **Prevención y Buenas Prácticas**

### **Verificar Antes de Terminar**

```bash
# Siempre verificar qué proceso vas a terminar
ps -p PID -o pid,ppid,user,cmd

# Para procesos críticos del sistema, ser extra cuidadoso
if ps -p $pid -o user= | grep -q root; then
    echo "⚠️ Advertencia: Proceso del sistema (root)"
    echo "¿Estás seguro? (y/n)"
    read -r respuesta
    [[ $respuesta == "y" ]] || exit 1
fi
```

### **Evitar Terminar Procesos Críticos**

```bash
# NUNCA terminar estos procesos (PIDs bajos, generalmente):
# PID 1: systemd/init
# PID 2: kthreadd  
# Procesos del kernel: [nombre_entre_corchetes]

# Verificar si es proceso del kernel
check_critical_process() {
    local pid=$1
    local cmd=$(ps -p $pid -o cmd= 2>/dev/null)
    
    if [[ $cmd =~ ^\[.*\]$ ]]; then
        echo "❌ CRÍTICO: Proceso del kernel ($cmd)"
        return 1
    elif [[ $pid -eq 1 ]]; then
        echo "❌ CRÍTICO: Proceso init/systemd"
        return 1
    fi
    
    return 0
}
```

## **Troubleshooting**

### **Kill No Funciona**

```bash
# Si kill no termina el proceso:

# 1. Verificar que el proceso existe
kill -0 PID && echo "Proceso existe" || echo "Proceso no existe"

# 2. Verificar permisos
# Solo puedes terminar:
# - Tus propios procesos
# - Como root: cualquier proceso

# 3. Proceso puede estar en estado D (uninterruptible sleep)
ps aux | awk '$8 ~ /D/ {print $2, $11}'
# Estos procesos no se pueden terminar, esperar a que terminen

# 4. Proceso zombie
ps aux | awk '$8 ~ /Z/ {print $2, $11}'
# Terminar proceso padre, no el zombie
```

### **Identificar Procesos Resistentes**

```bash
# Procesos que pueden resistir terminación:

# 1. Procesos con handlers de señales
# 2. Procesos en syscalls no interrumpibles
# 3. Procesos zombie (ya están "muertos")
# 4. Procesos del kernel

# Para identificar:
check_process_state() {
    local pid=$1
    local state=$(ps -o stat= -p $pid 2>/dev/null)
    
    case $state in
        *D*) echo "Proceso en espera no interrumpible" ;;
        *Z*) echo "Proceso zombie" ;;
        *T*) echo "Proceso detenido" ;;
        *S*) echo "Proceso durmiendo (interrumpible)" ;;
        *R*) echo "Proceso ejecutándose" ;;
    esac
}
```

## **Enlaces Relacionados**

- [[ps]] - Encontrar procesos para terminar
- [[top]] - Identificar procesos problemáticos  
- [[htop]] - Interface visual para gestión de procesos
- [[jobs]] - Gestionar trabajos en segundo plano
- [[pgrep]] - Buscar procesos por nombre o atributos
- [[systemctl]] - Gestionar servicios del sistema
- [[nohup]] - Ejecutar procesos inmunes a desconexión