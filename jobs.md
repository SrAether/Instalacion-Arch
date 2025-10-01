# jobs - Gestión de Trabajos en Segundo Plano

**jobs** es tu **controlador de tareas** del shell. Te permite gestionar procesos que ejecutas en segundo plano, pausarlos, reanudarlos y traerlos al primer plano en tu sesión actual de Arch Linux.

## **¿Qué es jobs?**

jobs te permite:
- **Ver trabajos activos** en tu sesión de shell actual
- **Gestionar procesos** en segundo y primer plano
- **Pausar y reanudar** comandos
- **Controlar múltiples tareas** simultáneamente

## **¿Por qué es Útil en Arch Linux?**

En Arch Linux, jobs es fundamental para multitarea en terminal, especialmente útil durante la instalación, compilación de software, o cuando trabajas con múltiples comandos largos.

### **Conceptos Básicos**

```bash
# Ejecutar comando en segundo plano
comando &

# Ver trabajos activos
jobs

# Traer trabajo al primer plano
fg %numero_trabajo

# Enviar trabajo al segundo plano
bg %numero_trabajo

# Pausar trabajo actual
Ctrl+Z
```

## **Trabajos en Segundo Plano**

### **Crear Trabajos en Segundo Plano**

```bash
# Ejecutar comando en segundo plano
sleep 300 &

# Múltiples comandos en segundo plano
find / -name "*.txt" 2>/dev/null > archivos.txt &
tar -czf backup.tar.gz /home/usuario &
wget https://archivo-grande.com/archivo.iso &

# Ver trabajos activos
jobs
# Salida típica:
# [1]   Running    sleep 300 &
# [2]-  Running    find / -name "*.txt" 2>/dev/null > archivos.txt &
# [3]+  Running    tar -czf backup.tar.gz /home/usuario &
```

### **Información de Trabajos**

```bash
# Ver trabajos con PID
jobs -l

# Ver solo trabajos ejecutándose
jobs -r

# Ver solo trabajos detenidos
jobs -s

# Ver trabajo específico
jobs %1

# Ejemplo de salida:
# [1]  1234 Running    sleep 300 &
# [2]- 1235 Running    find / -name "*.txt" 2>/dev/null > archivos.txt &
# [3]+ 1236 Stopped    vim archivo.txt
```

## **Control de Trabajos**

### **Pausar y Reanudar Trabajos**

```bash
# Pausar proceso actual (en primer plano)
# Ctrl+Z

# Ejemplo práctico:
vim archivo.txt
# Presionar Ctrl+Z para pausar vim
# [1]+  Stopped    vim archivo.txt

# Continuar en segundo plano
bg %1
# [1]+ vim archivo.txt &

# Traer de vuelta al primer plano
fg %1
```

### **Cambiar Entre Primer y Segundo Plano**

```bash
# Comando ejecutándose en primer plano
find /usr -name "*.so"

# Pausarlo: Ctrl+Z
# [1]+  Stopped    find /usr -name "*.so"

# Continuarlo en segundo plano
bg %1
# [1]+ find /usr -name "*.so" &

# Verificar estado
jobs
# [1]+  Running    find /usr -name "*.so" &

# Traerlo al primer plano cuando termine de buscar
fg %1
```

### **Identificación de Trabajos**

```bash
# Formas de referenciar trabajos:
%1      # Trabajo número 1
%+      # Trabajo actual (último manipulado)
%-      # Trabajo anterior
%vi     # Trabajo que contiene "vi" en el comando
%?vim   # Trabajo que contiene "vim" en cualquier parte
%%      # Trabajo actual (igual que %+)
```

## **Casos de Uso Prácticos**

### **Instalación y Compilación en Arch**

```bash
# Durante instalación de Arch, ejecutar múltiples tareas:

# 1. Descargar ISO en segundo plano
wget https://mirror.archlinux.org/iso/latest/archlinux.iso &

# 2. Preparar particionado mientras descarga
cfdisk /dev/sda

# 3. Ver progreso de descarga
jobs
fg %1  # Ver descarga
Ctrl+Z # Pausar para hacer otra cosa
bg %1  # Continuar descarga en segundo plano

# 4. Mientras tanto, hacer otra tarea
pacman -Sy
```

### **Compilación de Software**

```bash
# Compilar múltiples paquetes AUR simultáneamente

# 1. Clonar repositorios
git clone https://aur.archlinux.org/yay.git &
git clone https://aur.archlinux.org/visual-studio-code-bin.git &

# 2. Verificar clonado
jobs

# 3. Compilar primer paquete
cd yay
makepkg -si &

# 4. Mientras compila, preparar segundo paquete
cd ../visual-studio-code-bin
makepkg -si &

# 5. Monitorear compilaciones
jobs -l
```

### **Gestión de Descargas**

```bash
# Descargar múltiples archivos grandes

# 1. Iniciar descargas
wget https://servidor1.com/archivo1.iso &
wget https://servidor2.com/archivo2.tar.gz &
curl -O https://servidor3.com/archivo3.zip &

# 2. Verificar progreso
jobs
# [1]   Running    wget https://servidor1.com/archivo1.iso &
# [2]-  Running    wget https://servidor2.com/archivo2.tar.gz &
# [3]+  Running    curl -O https://servidor3.com/archivo3.zip &

# 3. Traer descarga específica al primer plano para ver progreso
fg %1

# 4. Pausar y volver a segundo plano
Ctrl+Z
bg %1
```

## **Gestión Avanzada**

### **Terminar Trabajos**

```bash
# Ver trabajos con PID
jobs -l
# [1]  1234 Running    sleep 1000 &
# [2]+ 1235 Running    find / -name "*.log" 2>/dev/null &

# Terminar trabajo específico
kill %1

# Terminar trabajo por PID
kill 1235

# Forzar terminación si no responde
kill -9 %2

# Verificar que terminó
jobs
```

### **Esperar a que Terminen Trabajos**

```bash
# Esperar a que termine trabajo específico
wait %1

# Esperar a que terminen todos los trabajos
wait

# Ejemplo práctico:
echo "Iniciando tareas..."
sleep 10 &
sleep 15 &
sleep 20 &

echo "Esperando a que terminen todas las tareas..."
wait
echo "Todas las tareas completadas"
```

### **Trabajos con Redirección**

```bash
# Trabajos guardando salida en archivos
find /usr -name "*.conf" > configuraciones.txt 2>&1 &
du -sh /* > tamaños_directorios.txt 2>&1 &

# Ver progreso indirectamente
jobs
tail -f configuraciones.txt &  # Otro trabajo para ver progreso
```

## **Diferencias con nohup y screen**

### **jobs vs nohup**

```bash
# jobs: Solo para sesión actual
comando &
jobs  # Visible

# Si cierras terminal, el trabajo se pierde

# nohup: Supervive al cierre de terminal
nohup comando &
# Trabajo continúa aunque cierres terminal
# jobs no lo muestra si reconectas
```

### **jobs vs screen/tmux**

```bash
# jobs: Gestión simple en sesión actual
sleep 100 &
jobs

# screen/tmux: Sesiones persistentes
screen -S mi_sesion
sleep 100
Ctrl+A, D  # Detach
screen -r mi_sesion  # Reattach
```

## **Comandos Relacionados**

### **disown - Desligar Trabajos**

```bash
# Ejecutar trabajo
sleep 1000 &

# Ver trabajos
jobs
# [1]+  Running    sleep 1000 &

# Desligar del shell (no aparecerá más en jobs)
disown %1

# Verificar
jobs  # Ya no aparece
```

### **nohup - Procesos Inmunes**

```bash
# Ejecutar comando inmune a desconexión
nohup comando_largo > salida.log 2>&1 &

# El proceso continuará aunque cierres terminal
# No aparecerá en jobs si reconectas
```

## **Solución de Problemas**

### **Trabajo No Responde**

```bash
# Si un trabajo no responde a fg o bg:

# 1. Verificar estado
jobs -l
ps aux | grep PID_del_trabajo

# 2. Intentar diferentes señales
kill -CONT %1  # Continuar si está pausado
kill -TERM %1  # Terminar normalmente
kill -KILL %1  # Forzar terminación
```

### **Trabajo Desaparecido**

```bash
# Si un trabajo desaparece de jobs pero sigue ejecutándose:

# 1. Buscar por nombre de comando
pgrep -f nombre_comando

# 2. Ver todos los procesos de tu usuario
ps -u $(whoami)

# 3. Si lo encuentras, puedes terminarlo con kill
kill PID_encontrado
```

### **Terminal Se Cuelga**

```bash
# Si hay trabajo colgado que impide cerrar terminal:

# 1. Ver trabajos activos
jobs

# 2. Terminar todos los trabajos
kill $(jobs -p)

# 3. O forzar terminación
kill -9 $(jobs -p)

# 4. Si nada funciona:
exit  # Intentar salir
# Si no funciona, cerrar terminal desde fuera
```

## **Mejores Prácticas**

### **Organización de Trabajos**

```bash
# Dar nombres descriptivos usando comentarios
long_backup_job() {
    tar -czf backup_$(date +%Y%m%d).tar.gz /home
}

long_backup_job &  # [1] Backup job
find / -name "core" -delete 2>/dev/null &  # [2] Cleanup job

# Verificar trabajos periódicamente
jobs
```

### **Monitoreo de Trabajos**

```bash
# Función para verificar trabajos automáticamente
check_jobs() {
    local job_count=$(jobs | wc -l)
    if [[ $job_count -gt 0 ]]; then
        echo "Trabajos activos: $job_count"
        jobs
    else
        echo "No hay trabajos activos"
    fi
}

# Ejecutar cada cierto tiempo
watch -n 30 'check_jobs'
```

## **Enlaces Relacionados**

- [[kill]] - Terminar trabajos específicos
- [[ps]] - Ver todos los procesos (no solo trabajos)
- [[top]] - Monitorear recursos de trabajos
- [[nohup]] - Ejecutar comandos inmunes a desconexión
- [[screen]] - Sesiones de terminal persistentes
- [[systemctl]] - Gestionar servicios del sistema