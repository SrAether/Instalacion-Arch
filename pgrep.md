# pgrep - Buscar Procesos por Nombre

**pgrep** es tu **buscador inteligente** de procesos en Arch Linux. Te permite encontrar procesos por nombre, usuario, o diversos criterios sin tener que usar ps con grep, siendo más eficiente y preciso.

## **¿Qué es pgrep?**

pgrep te permite:
- **Buscar procesos por nombre** de forma directa
- **Filtrar por usuario, grupo** o terminal
- **Obtener PIDs** para usar con otros comandos
- **Combinar múltiples criterios** de búsqueda

## **¿Por qué es Útil en Arch Linux?**

En Arch Linux, pgrep es fundamental para encontrar procesos específicos rápidamente, especialmente útil durante troubleshooting, gestión de servicios, y automatización de tareas.

### **Uso Básico**

```bash
# Buscar proceso por nombre
pgrep firefox

# Buscar proceso por patrón
pgrep -f "python script.py"

# Mostrar nombre y PID
pgrep -l firefox

# Buscar procesos de un usuario
pgrep -u usuario
```

## **Búsqueda Básica**

### **Por Nombre de Proceso**

```bash
# Buscar por nombre exacto
pgrep ssh

# Buscar por patrón parcial
pgrep fire    # Encontrará firefox, firewall, etc.

# Mostrar nombres junto con PIDs
pgrep -l ssh
# Salida: 1234 sshd

# Contar procesos encontrados
pgrep -c firefox
# Salida: 3 (si hay 3 procesos de firefox)
```

### **Por Línea de Comando Completa**

```bash
# Buscar en toda la línea de comando
pgrep -f "python /usr/bin/programa.py"

# Buscar script específico
pgrep -f "backup.sh"

# Buscar comando con argumentos
pgrep -f "rsync.*backup"

# Ejemplo práctico:
# Proceso: python /home/user/scripts/monitor.py --verbose
# Búsqueda: pgrep -f "monitor.py"
```

## **Filtros por Atributos**

### **Por Usuario**

```bash
# Procesos de un usuario específico
pgrep -u root

# Procesos de múltiples usuarios
pgrep -u root,daemon

# Procesos que NO son de un usuario
pgrep -v -u root

# Combinar usuario y nombre
pgrep -u usuario firefox
```

### **Por Grupo**

```bash
# Procesos de un grupo
pgrep -g grupo

# Por ID de grupo
pgrep -G 1000

# Procesos del grupo actual
pgrep -g $(id -gn)
```

### **Por Terminal**

```bash
# Procesos de un terminal específico
pgrep -t pts/0

# Procesos sin terminal (daemons)
pgrep -t ?

# Ver terminal actual
tty
# Después buscar procesos en esa terminal
pgrep -t $(tty | sed 's|/dev/||')
```

## **Opciones de Selección**

### **Procesos Padre e Hijo**

```bash
# Buscar procesos hijo de un PID específico
pgrep -P 1234

# Buscar procesos de una sesión
pgrep -s session_id

# Buscar por ID de proceso padre
parent_pid=$(pgrep systemd | head -1)
pgrep -P $parent_pid
```

### **Procesos Más Nuevos/Antiguos**

```bash
# Solo el proceso más nuevo
pgrep -n firefox

# Solo el proceso más antiguo  
pgrep -o firefox

# Comparar:
echo "Firefox más antiguo: $(pgrep -o firefox)"
echo "Firefox más nuevo: $(pgrep -n firefox)"
```

### **Por Tiempo de Ejecución**

```bash
# Buscar procesos ejecutándose (no zombie)
pgrep -r firefox

# En sistemas que lo soporten, filtrar por tiempo
# (Verificar man pgrep para opciones específicas del sistema)
```

## **Casos de Uso Prácticos**

### **Gestión de Servicios**

```bash
# Verificar si un servicio está ejecutándose
if pgrep -x nginx >/dev/null; then
    echo "Nginx está ejecutándose"
else
    echo "Nginx no está ejecutándose"
fi

# Obtener PID de un servicio para otras operaciones
nginx_pid=$(pgrep -x nginx)
if [[ -n $nginx_pid ]]; then
    echo "Nginx PID: $nginx_pid"
    # Usar PID para ver información detallada
    ps -p $nginx_pid -o pid,ppid,user,cmd
fi
```

### **Monitoreo de Aplicaciones**

```bash
# Verificar cuántas instancias de una aplicación están ejecutándose
firefox_count=$(pgrep -c firefox)
echo "Instancias de Firefox: $firefox_count"

# Listar todos los procesos de una aplicación
echo "Procesos de Firefox:"
pgrep -l firefox

# Ver procesos hijo de firefox
for pid in $(pgrep firefox); do
    echo "Hijos de $pid:"
    pgrep -P $pid -l
done
```

### **Limpieza de Procesos**

```bash
# Encontrar y terminar procesos específicos
# (Usar con cuidado)

# Buscar procesos zombie
zombie_pids=$(pgrep -x "defunct" 2>/dev/null)
if [[ -n $zombie_pids ]]; then
    echo "Procesos zombie encontrados: $zombie_pids"
fi

# Buscar procesos de un script específico
script_pids=$(pgrep -f "mi_script.sh")
if [[ -n $script_pids ]]; then
    echo "Instancias del script: $script_pids"
    # kill $script_pids  # Descomentar solo si es seguro
fi
```

## **Combinación con Otros Comandos**

### **pgrep + kill**

```bash
# Terminar procesos encontrados por pgrep
# CUIDADO: Verificar antes de ejecutar

# Buscar procesos primero
pgrep -l firefox

# Si está seguro, terminar
pgrep firefox | xargs kill

# Más seguro: terminar procesos específicos
firefox_pids=$(pgrep firefox)
for pid in $firefox_pids; do
    echo "Terminando proceso $pid"
    ps -p $pid -o pid,cmd  # Verificar qué es
    # kill $pid  # Descomentar si está seguro
done
```

### **pgrep + ps**

```bash
# Obtener información detallada de procesos encontrados
pgrep firefox | xargs ps -o pid,ppid,user,cmd -p

# Función útil para información detallada
show_process_info() {
    local pattern="$1"
    local pids=$(pgrep "$pattern")
    
    if [[ -n $pids ]]; then
        echo "Procesos que coinciden con '$pattern':"
        ps -o pid,ppid,user,%cpu,%mem,cmd -p $pids
    else
        echo "No se encontraron procesos para '$pattern'"
    fi
}

# Uso: show_process_info firefox
```

### **pgrep + systemctl**

```bash
# Verificar si un servicio systemd está realmente ejecutándose
check_service() {
    local service="$1"
    
    # Verificar estado según systemd
    if systemctl is-active "$service" >/dev/null 2>&1; then
        echo "$service está activo según systemd"
        
        # Verificar si realmente hay procesos
        if pgrep -x "$service" >/dev/null; then
            echo "✅ $service tiene procesos ejecutándose"
        else
            echo "⚠️ $service activo en systemd pero sin procesos"
        fi
    else
        echo "$service no está activo según systemd"
    fi
}

# Ejemplos:
check_service sshd
check_service nginx
```

## **Filtros Avanzados**

### **Expresiones Regulares**

```bash
# Usar expresiones regulares en el nombre
pgrep "^ssh"      # Procesos que empiezan con ssh
pgrep "d$"        # Procesos que terminan con d (daemons)
pgrep "[0-9]"     # Procesos con números en el nombre

# En línea de comando completa
pgrep -f "python.*\.py$"    # Scripts de Python
pgrep -f "\.sh.*--daemon"   # Scripts con opción daemon
```

### **Combinaciones Complejas**

```bash
# Procesos de usuario específico con patrón
pgrep -u www-data -f "apache"

# Procesos NO de root con terminal específico
pgrep -v -u root -t pts/0

# Procesos hijo de systemd (PID 1) que contengan "network"
pgrep -P 1 -f "network"
```

## **Comparación con Alternativas**

### **pgrep vs ps + grep**

```bash
# Método tradicional (menos eficiente)
ps aux | grep firefox | grep -v grep | awk '{print $2}'

# Con pgrep (más directo)
pgrep firefox

# pgrep es:
# - Más rápido
# - Más legible
# - Menos propenso a errores
# - No incluye el proceso grep
```

### **pgrep vs pidof**

```bash
# pidof: Solo por nombre exacto de programa
pidof firefox

# pgrep: Más flexible
pgrep firefox           # Por nombre
pgrep -f "firefox.*"    # Por patrón
pgrep -u user firefox   # Por usuario y nombre

# Usar pidof para programas específicos conocidos
# Usar pgrep para búsquedas más complejas
```

## **Troubleshooting**

### **No Encuentra Procesos Esperados**

```bash
# Si pgrep no encuentra un proceso que sabes que existe:

# 1. Verificar nombre exacto
ps aux | grep proceso_buscado

# 2. Usar búsqueda en línea de comando
pgrep -f "nombre_completo_o_ruta"

# 3. Verificar si es de otro usuario
pgrep -a proceso  # Muestra línea de comando completa

# 4. Buscar sin restricciones de usuario
sudo pgrep proceso
```

### **Demasiados Resultados**

```bash
# Si pgrep devuelve muchos resultados:

# 1. Ser más específico
pgrep firefox         # Puede incluir firefox, firefox-esr, etc.
pgrep -x firefox      # Solo exactamente "firefox"

# 2. Combinar filtros
pgrep -u usuario -x programa

# 3. Usar el más específico
pgrep -n programa     # Solo el más nuevo
pgrep -o programa     # Solo el más antiguo
```

### **Verificar Resultados**

```bash
# Siempre verificar antes de actuar sobre PIDs
verify_pids() {
    local pattern="$1"
    local pids=$(pgrep "$pattern")
    
    if [[ -n $pids ]]; then
        echo "PIDs encontrados para '$pattern':"
        for pid in $pids; do
            ps -p $pid -o pid,user,cmd
        done
    else
        echo "No se encontraron procesos para '$pattern'"
    fi
}

verify_pids firefox
```

## **Automatización y Scripts**

### **Monitoreo Simple**

```bash
# Verificar si un proceso crítico está ejecutándose
monitor_process() {
    local process="$1"
    local max_attempts=3
    
    for ((i=1; i<=max_attempts; i++)); do
        if pgrep -x "$process" >/dev/null; then
            echo "✅ $process está ejecutándose"
            return 0
        else
            echo "⚠️ Intento $i: $process no encontrado"
            sleep 5
        fi
    done
    
    echo "❌ $process no se encuentra después de $max_attempts intentos"
    return 1
}

# Uso:
monitor_process sshd
```

### **Estadísticas de Procesos**

```bash
# Contar procesos por usuario
count_user_processes() {
    echo "=== Procesos por Usuario ==="
    for user in $(cut -d: -f1 /etc/passwd | sort); do
        count=$(pgrep -u "$user" -c "" 2>/dev/null || echo 0)
        if [[ $count -gt 0 ]]; then
            printf "%-15s %3d procesos\n" "$user" "$count"
        fi
    done
}

count_user_processes
```

## **Enlaces Relacionados**

- [[ps]] - Ver información detallada de procesos
- [[kill]] - Terminar procesos encontrados con pgrep
- [[top]] - Monitorear procesos en tiempo real
- [[jobs]] - Gestionar procesos en segundo plano
- [[systemctl]] - Gestionar servicios del sistema
- [[pidof]] - Alternativa simple para buscar PIDs por nombre