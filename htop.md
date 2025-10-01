# htop - Monitor Interactivo de Procesos

**htop** es la **versión mejorada** de top. Ofrece una interfaz más colorida, intuitiva y con funciones avanzadas para monitorear procesos en tiempo real en Arch Linux.

## **¿Qué es htop?**

htop te permite:
- **Ver procesos con interfaz colorida** y fácil navegación
- **Gestionar procesos** con teclas simples
- **Buscar y filtrar** procesos rápidamente
- **Ver árbol de procesos** de forma visual
- **Monitorear recursos** del sistema en tiempo real

## **¿Por qué Usar htop en Arch Linux?**

htop mejora significativamente la experiencia de monitoreo en Arch Linux con una interfaz más amigable que top, especialmente útil para usuarios que necesitan gestionar procesos frecuentemente.

### **Instalación y Uso Básico**

```bash
# Instalar htop
sudo pacman -S htop

# Ejecutar htop
htop

# Salir de htop
q o F10
```

## **Interfaz de htop**

### **Pantalla Principal**

```
   0[||||||||||     15.2%]   Tasks: 125, 89 thr; 1 running
   1[|||           8.1%]     Load average: 0.85 0.65 0.45
Mem[||||||||||| 3.2G/8.0G]  Uptime: 2 days, 03:42:15
Swp[              0K/8.0G]

PID USER    PRI NI VIRT  RES  SHR S CPU% MEM%   TIME+ Command
1234 user     20  0 2.1G 345M  89M S 12.5  4.3  5:23.45 firefox
5678 user     20  0 1.2G 234M  45M S  8.2  2.9  2:15.32 code
```

**Ventajas sobre top:**
- **Barras visuales** para CPU, memoria y swap
- **Colores** para diferentes tipos de información
- **Navegación** con teclas de flecha
- **Información más clara** y organizada

### **Navegación con Teclas**

```bash
# Navegación básica:
Flechas    # Mover cursor por la lista
Page Up/Dn # Navegar páginas
Home/End   # Ir al inicio/final

# Gestión de procesos:
F9         # Terminar proceso (kill)
F8         # Cambiar prioridad (nice)
F4         # Filtrar procesos
F3         # Buscar proceso
F5         # Alternar vista de árbol
F6         # Cambiar criterio de ordenación

# Información:
F1         # Ayuda
F2         # Configuración
F10        # Salir
```

## **Funciones Principales**

### **Búsqueda y Filtrado**

```bash
# Buscar proceso (F3)
# 1. Presionar F3
# 2. Escribir nombre del proceso
# 3. Enter para buscar
# Ejemplo: buscar "firefox"

# Filtrar procesos (F4)
# 1. Presionar F4
# 2. Escribir filtro
# 3. Solo se mostrarán procesos que coincidan
# Ejemplo: filtrar por "user"

# Limpiar filtro
# 1. Presionar F4
# 2. Borrar texto
# 3. Enter
```

### **Gestión de Procesos**

```bash
# Terminar proceso:
# 1. Seleccionar proceso con flechas
# 2. Presionar F9
# 3. Elegir señal (TERM, KILL, etc.)
# 4. Confirmar

# Cambiar prioridad:
# 1. Seleccionar proceso
# 2. Presionar F8
# 3. Usar +/- para cambiar prioridad
# 4. Enter para confirmar

# Seguir proceso específico:
# 1. Seleccionar proceso
# 2. Presionar F4 para filtrar por ese proceso
```

### **Vista de Árbol de Procesos**

```bash
# Alternar vista de árbol (F5)
# Muestra relación padre-hijo entre procesos

Ejemplo de vista de árbol:
systemd(1)
├─ NetworkManager(456)
├─ sshd(789)
│  └─ sshd(1234)
│     └─ bash(1456)
│        └─ htop(1789)
└─ X(2345)
   └─ firefox(3456)
      ├─ firefox(3457)
      └─ firefox(3458)
```

## **Configuración y Personalización**

### **Configuración Básica (F2)**

```bash
# Dentro de htop, presionar F2 para configuración:

# Display options:
[x] Show tree view
[x] Hide userland threads
[x] Shadow other users' processes
[x] Hide kernel threads
[x] Display threads in different color

# Colors:
[x] Use color in HTOP
Color scheme: Default

# Columns:
Available: PPID, PGRP, SESSION, TTY, TPGID...
Active: PID, USER, PRIORITY, NICE, M_SIZE...
```

### **Personalizar Columnas**

```bash
# En configuración (F2) → Columns:

# Columnas útiles para agregar:
PPID     # ID del proceso padre
TPGID    # ID del grupo de procesos
TTY      # Terminal asociado
STARTTIME # Hora de inicio del proceso
PROCESSOR # CPU en la que se ejecuta

# Para agregar columna:
# 1. Seleccionar de "Available"
# 2. Presionar F6 para mover a "Active"
# 3. Usar F7/F8 para reordenar
```

## **Casos de Uso Prácticos**

### **Monitorear Aplicaciones Específicas**

```bash
# Ejemplo: Monitorear Firefox y sus procesos hijo

# 1. Ejecutar htop
htop

# 2. Presionar F5 para vista de árbol
# 3. Presionar F3 para buscar
# 4. Escribir "firefox"
# 5. Navegar entre resultados con F3

# Resultado: Ver todos los procesos de Firefox
# firefox (proceso principal)
# ├─ firefox (proceso de contenido)
# ├─ firefox (proceso de extensiones)
# └─ firefox (proceso de GPU)
```

### **Identificar Procesos Problemáticos**

```bash
# Para encontrar procesos que consumen muchos recursos:

# 1. Ordenar por CPU (clic en %CPU o F6)
# 2. Buscar procesos con alto %CPU consistente
# 3. Ordenar por memoria (clic en %MEM)
# 4. Identificar memory leaks

# Señales de problemas:
# - Proceso con CPU constantemente >50%
# - Proceso con memoria creciente continuamente
# - Muchos procesos zombie (estado Z)
```

### **Limpiar Procesos Zombie**

```bash
# Los procesos zombie aparecen con estado 'Z'

# Para limpiarlos:
# 1. Buscar proceso padre (PPID)
# 2. Enviar señal CHLD al padre, o
# 3. Reiniciar proceso padre si es seguro

# En htop:
# 1. Identificar proceso zombie
# 2. Ver su PPID (agregar columna si no está)
# 3. Buscar el proceso padre
# 4. Considerar reiniciar el padre
```

## **Comparación con top**

### **Ventajas de htop**

```bash
# Interfaz:
# htop: Colorida, barras gráficas, navegación con mouse
# top:  Texto plano, solo teclado

# Usabilidad:
# htop: F-keys para funciones, búsqueda fácil
# top:  Comandos de una letra, menos intuitivo

# Información:
# htop: Vista de árbol clara, filtros visuales
# top:  Información más técnica, menos visual

# Personalización:
# htop: Configuración gráfica fácil
# top:  Configuración mediante archivos
```

### **Cuándo Usar Cada Uno**

```bash
# Usar htop cuando:
# - Necesitas gestionar procesos interactivamente
# - Quieres una interfaz amigable
# - Buscas procesos específicos frecuentemente
# - Trabajas en escritorio/laptop

# Usar top cuando:
# - Trabajas en servidor sin GUI
# - Necesitas scripts automatizados
# - Quieres menor uso de recursos
# - Sistema con limitaciones de terminal
```

## **Shortcuts Útiles**

### **Teclas Rápidas**

```bash
# Gestión rápida:
k          # Terminar proceso (equivale a F9)
r          # Cambiar prioridad (equivale a F8)
s          # Trace system calls (strace)
l          # Mostrar archivos abiertos (lsof)

# Navegación:
u          # Filtrar por usuario
/          # Buscar (equivale a F3)
\          # Filtrar (equivale a F4)
t          # Vista de árbol (equivale a F5)

# Vista:
H          # Mostrar/ocultar hilos
K          # Mostrar/ocultar hilos del kernel
Space      # Marcar proceso (para múltiples acciones)
```

### **Atajos del Sistema**

```bash
# Información rápida del sistema:
m          # Ordenar por memoria
p          # Ordenar por CPU
t          # Ordenar por tiempo
```

## **Configuración Avanzada**

### **Archivo de Configuración**

```bash
# htop guarda configuración en:
~/.config/htop/htoprc

# Configuración típica:
# fields=0 48 17 18 38 39 2 46 47 49 1
# sort_key=46
# sort_direction=1
# hide_threads=0
# hide_kernel_threads=1
# hide_userland_threads=0
# shadow_other_users=0
# show_thread_names=0
# show_program_path=1
# highlight_base_name=0
# highlight_megabytes=1
# highlight_threads=1
```

### **Temas y Colores**

```bash
# Cambiar esquema de colores en F2 → Colors:

# Default: Colores estándar
# Monochrome: Sin colores (para terminales limitados)
# Black on White: Fondo blanco
# Light Terminal: Para terminales claros
# MC: Estilo Midnight Commander
# Black Night: Tema oscuro
```

## **Troubleshooting**

### **htop No Se Instala**

```bash
# Si htop no está disponible:
sudo pacman -Sy
sudo pacman -S htop

# Si hay conflictos:
sudo pacman -Syu
sudo pacman -S htop

# Verificar instalación:
which htop
htop --version
```

### **Problemas de Permisos**

```bash
# Si no puedes terminar procesos de otros usuarios:
# 1. htop muestra todos los procesos pero solo puedes gestionar los tuyos
# 2. Para gestionar procesos del sistema:
sudo htop

# Si htop se ve mal en terminal:
# 1. Verificar variable TERM:
echo $TERM

# 2. Actualizar terminal o usar:
TERM=xterm htop
```

### **Rendimiento de htop**

```bash
# Si htop es lento:
# 1. Aumentar intervalo de actualización en F2 → Display options
# 2. Ocultar hilos del kernel: F2 → Display options
# 3. Usar modo monocromo si hay problemas gráficos

# Para sistemas de bajos recursos:
# Considerar usar top en lugar de htop
```

## **Enlaces Relacionados**

- [[top]] - Monitor básico de procesos
- [[ps]] - Información estática de procesos
- [[kill]] - Terminar procesos desde línea de comandos
- [[systemctl]] - Gestionar servicios del sistema
- [[free]] - Información de memoria
- [[jobs]] - Trabajos en segundo plano
- [[pgrep]] - Buscar procesos por nombre