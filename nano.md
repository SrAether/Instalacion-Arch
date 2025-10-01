El comando `nano` es tu **editor de texto simple e intuitivo**. Es perfecto para principiantes porque usa atajos familiares (Ctrl+X para salir, Ctrl+S para guardar) y muestra ayuda en pantalla. Es ideal para editar archivos de configuración durante la instalación de Arch Linux.

---
### ¿Qué Hace NANO? ✏️
**NANO** es un editor de texto en terminal que ofrece:
1. **Interfaz amigable** con atajos visibles
2. **Edición simple** sin modos complicados
3. **Sintaxis highlighting** para código
4. **Búsqueda y reemplazo** integrados
5. **Perfecto para principiantes** en Linux

### Sintaxis Básica
```bash
nano archivo.txt           # Abrir/crear archivo
nano                        # Abrir editor vacío
nano +número archivo.txt    # Ir a línea específica
```

### Uso Básico
```bash
# Abrir archivo existente
nano ~/.bashrc

# Crear archivo nuevo
nano mi_archivo.txt

# Abrir con número de línea
nano +25 archivo_largo.txt

# Abrir solo lectura
nano -v archivo.txt
```

### En el Contexto de la Instalación de Arch Linux

#### **Editar Configuraciones Durante Instalación**
```bash
# Configurar mirrors de pacman
nano /etc/pacman.d/mirrorlist

# Configurar fstab
nano /etc/fstab

# Configurar hostname
nano /etc/hostname

# Configurar hosts
nano /etc/hosts

# Configurar locale
nano /etc/locale.gen
nano /etc/locale.conf
```

#### **Configurar Red**
```bash
# Configurar systemd-networkd
nano /etc/systemd/network/20-ethernet.network

# Configurar DNS
nano /etc/resolv.conf

# Configurar netctl
nano /etc/netctl/mi-perfil
```

#### **Configurar Usuario y Seguridad**
```bash
# Configurar sudo
nano /etc/sudoers         # ⚠️ Usar visudo es más seguro

# Configurar SSH
nano /etc/ssh/sshd_config

# Configurar shell del usuario
nano ~/.bashrc
nano ~/.zshrc
```

### Atajos de Teclado Esenciales

#### **Navegación Básica**
```
Ctrl + A    - Ir al inicio de la línea
Ctrl + E    - Ir al final de la línea
Ctrl + Y    - Página anterior
Ctrl + V    - Página siguiente
Alt + A     - Seleccionar texto (marcar)
```

#### **Edición**
```
Ctrl + K    - Cortar línea completa
Ctrl + U    - Pegar línea cortada
Ctrl + 6    - Marcar/seleccionar texto
Alt + 6     - Copiar texto marcado
Ctrl + Z    - Deshacer
Alt + U     - Rehacer
```

#### **Archivo**
```
Ctrl + S    - Guardar archivo (Write Out)
Ctrl + O    - Guardar como (Write Out to file)
Ctrl + X    - Salir (te pregunta si guardar)
Ctrl + R    - Leer/insertar otro archivo
```

#### **Búsqueda y Reemplazo**
```
Ctrl + W    - Buscar texto
Ctrl + \    - Buscar y reemplazar
Alt + W     - Buscar siguiente
F6          - Buscar hacia atrás (en algunas versiones)
```

#### **Ayuda**
```
Ctrl + G    - Mostrar ayuda completa
F1          - Ayuda (en algunas versiones)
```

### Opciones de Línea de Comandos

#### **`-n` (Line Numbers) - Mostrar Números**
```bash
# Mostrar números de línea
nano -n archivo.txt

# Útil para debugging y referencias
nano -n /var/log/pacman.log
```

#### **`-w` (No Wrap) - No Quebrar Líneas**
```bash
# No quebrar líneas largas
nano -w /etc/pacman.conf

# Útil para archivos de configuración con líneas largas
```

#### **`-T` (Tab Size) - Tamaño de Tab**
```bash
# Establecer tamaño de tab a 4 espacios
nano -T 4 archivo.py

# Para código con indentación específica
nano -T 2 archivo.yaml
```

#### **`-B` (Backup) - Crear Backup**
```bash
# Crear backup automático
nano -B archivo_importante.conf

# Crea archivo.conf~ como respaldo
```

#### **`-m` (Mouse) - Habilitar Ratón**
```bash
# Permitir usar ratón para navegar
nano -m archivo.txt

# Útil en terminales que soportan ratón
```

### Casos de Uso Específicos en Arch Linux

#### **1. Configurar Mirrorlist de Pacman**
```bash
# Editar lista de espejos
nano /etc/pacman.d/mirrorlist

# Dentro del archivo:
# - Descomenta mirrors de tu país
# - Comenta mirrors lentos
# - Guarda con Ctrl+S
# - Sal con Ctrl+X
```

#### **2. Configurar Fstab**
```bash
# Editar tabla de montaje
nano /etc/fstab

# Formato típico:
# UUID=xxxxx /     ext4 defaults 0 1
# UUID=yyyyy /boot vfat defaults 0 2
# UUID=zzzzz swap  swap defaults 0 0

# ⚠️ Error aquí puede impedir arranque
```

#### **3. Crear Scripts de Usuario**
```bash
# Crear script de utilidad
nano ~/mi_script.sh

# Contenido típico:
#!/bin/bash
echo "Mi script personalizado"
# ... más comandos

# Hacer ejecutable después:
chmod +x ~/mi_script.sh
```

#### **4. Configurar Servicios systemd**
```bash
# Crear archivo de servicio personalizado
nano ~/.config/systemd/user/mi-servicio.service

# Contenido típico:
[Unit]
Description=Mi servicio personalizado

[Service]
ExecStart=/home/usuario/mi_script.sh
Restart=always

[Install]
WantedBy=default.target
```

### Configuración de Nano

#### **Archivo de Configuración Global**
```bash
# Configuración para todos los usuarios
nano /etc/nanorc

# Opciones útiles para descomentar:
# set linenumbers      # Mostrar números de línea
# set autoindent       # Indentación automática
# set tabstospaces     # Convertir tabs a espacios
# set tabsize 4        # Tamaño de tab
# set mouse            # Habilitar ratón
```

#### **Configuración Personal**
```bash
# Crear configuración personal
nano ~/.nanorc

# Configuración recomendada:
set linenumbers
set autoindent
set tabstospaces
set tabsize 4
set mouse
set softwrap
set constantshow
set titlecolor brightwhite,red
set statuscolor brightwhite,green
set keycolor brightwhite,blue
```

#### **Syntax Highlighting**
```bash
# Habilitar colores de sintaxis
echo 'include "/usr/share/nano/*.nanorc"' >> ~/.nanorc

# Verificar archivos de sintaxis disponibles
ls /usr/share/nano/*.nanorc

# Algunos ejemplos:
# - sh.nanorc (bash scripts)
# - python.nanorc
# - html.nanorc
# - conf.nanorc (archivos de configuración)
```

### Flujo de Trabajo Típico

#### **Editar Archivo de Configuración**
```bash
# 1. Hacer backup (buena práctica)
sudo cp /etc/archivo.conf /etc/archivo.conf.backup

# 2. Editar archivo
sudo nano /etc/archivo.conf

# 3. Dentro de nano:
#    - Ctrl+W para buscar configuración específica
#    - Editar valores necesarios
#    - Ctrl+S para guardar
#    - Ctrl+X para salir

# 4. Verificar sintaxis si es posible
sudo archivo_comando -t  # Para algunos comandos
```

#### **Crear Script desde Cero**
```bash
# 1. Crear nuevo archivo
nano ~/nuevo_script.sh

# 2. Escribir shebang
#!/bin/bash

# 3. Agregar contenido
# 4. Guardar con Ctrl+S
# 5. Salir con Ctrl+X

# 6. Hacer ejecutable
chmod +x ~/nuevo_script.sh

# 7. Probar
./nuevo_script.sh
```

### Búsqueda y Reemplazo Avanzado

#### **Búsqueda Simple**
```bash
# Dentro de nano:
# Ctrl+W
# Escribir texto a buscar
# Enter para buscar
# Alt+W para buscar siguiente
```

#### **Búsqueda y Reemplazo**
```bash
# Dentro de nano:
# Ctrl+\
# Escribir texto a buscar
# Enter
# Escribir texto de reemplazo
# Enter
# Opciones:
#   Y - reemplazar esta ocurrencia
#   N - saltar esta ocurrencia
#   A - reemplazar todas
```

#### **Búsqueda con Expresiones Regulares**
```bash
# Habilitar regex en búsqueda:
# Alt+R durante la búsqueda

# Ejemplos útiles:
# ^#.*     - Líneas que empiezan con #
# .*error  - Líneas que terminan con error
# [0-9]+   - Números
```

### Comparación con Otros Editores

#### **NANO vs VIM**
```bash
# NANO: fácil para principiantes
nano archivo.txt
# - Atajos visibles en pantalla
# - Ctrl+X para salir
# - Funciona como editor normal

# VIM: más potente pero difícil
vim archivo.txt
# - Modos (normal, insertar, visual)
# - :wq para guardar y salir
# - Curva de aprendizaje empinada
```

#### **NANO vs EMACS**
```bash
# NANO: simple y directo
nano archivo.txt
# - Atajos simples
# - Funcionalidad básica
# - Fácil de aprender

# EMACS: muy extensible
emacs archivo.txt
# - Altamente personalizable
# - Muchas características
# - Complejo para principiantes
```

### Solución de Problemas

#### **Problema: No Puede Guardar Archivo**
```bash
# Error: "Permission denied"
# Solución: abrir con sudo
sudo nano /etc/archivo_protegido.conf

# O cambiar permisos temporalmente
sudo chmod 666 archivo.txt
nano archivo.txt
sudo chmod 644 archivo.txt
```

#### **Problema: Archivo Muy Largo**
```bash
# Nano puede ser lento con archivos grandes
# Alternativas:
less archivo_grande.txt     # Solo lectura
head -100 archivo_grande.txt | nano  # Primeras 100 líneas
tail -100 archivo_grande.txt | nano  # Últimas 100 líneas
```

#### **Problema: Caracteres Extraños**
```bash
# Verificar encoding del archivo
file archivo.txt

# Abrir con encoding específico
nano archivo.txt
# Dentro de nano: Alt+F para configurar formato
```

#### **Problema: Perdiste Cambios**
```bash
# Nano crea archivos temporales
ls -la ~/.nano/

# Recuperar desde backup automático
ls -la archivo.txt~  # Backup automático si se configuró
```

### Scripts Útiles con Nano

#### **Editor Seguro con Backup**
```bash
#!/bin/bash
# safe_edit.sh

file="$1"

if [[ -z "$file" ]]; then
    echo "Uso: $0 archivo"
    exit 1
fi

# Crear backup si el archivo existe
if [[ -f "$file" ]]; then
    cp "$file" "${file}.backup.$(date +%Y%m%d_%H%M%S)"
    echo "Backup creado: ${file}.backup.$(date +%Y%m%d_%H%M%S)"
fi

# Editar archivo
nano "$file"

echo "Edición completada"
```

#### **Configurador de Sistema**
```bash
#!/bin/bash
# system_config.sh

echo "=== Configurador de Sistema ==="
echo "1. Editar hostname"
echo "2. Editar hosts" 
echo "3. Editar fstab"
echo "4. Editar sudoers (¡CUIDADO!)"
echo "5. Salir"

read -p "Opción: " choice

case $choice in
    1) sudo nano /etc/hostname ;;
    2) sudo nano /etc/hosts ;;
    3) sudo nano /etc/fstab ;;
    4) echo "⚠️ Usar visudo es más seguro"
       read -p "¿Continuar con nano? (y/N): " confirm
       [[ $confirm == "y" ]] && sudo nano /etc/sudoers ;;
    5) exit 0 ;;
    *) echo "Opción inválida" ;;
esac
```

### Personalización Avanzada

#### **Combinaciones de Colores**
```bash
# En ~/.nanorc
set titlecolor brightwhite,blue
set statuscolor brightwhite,green  
set errorcolor brightwhite,red
set selectedcolor brightwhite,magenta
set stripecolor yellow
set numbercolor cyan
set keycolor brightcyan
set functioncolor green
```

#### **Configuración para Programación**
```bash
# En ~/.nanorc para programadores
set linenumbers
set autoindent
set tabstospaces
set tabsize 4
set trimblanks
set constantshow
set softwrap
set atblanks

# Habilitar sintaxis
include "/usr/share/nano/*.nanorc"
```

### Buenas Prácticas

#### **1. Hacer Backup Antes de Editar Archivos Críticos**
```bash
# Siempre backup de configuraciones importantes
sudo cp /etc/fstab /etc/fstab.backup
sudo nano /etc/fstab
```

#### **2. Usar Nano para Archivos Simples**
```bash
# Nano es perfecto para:
# - Archivos de configuración
# - Scripts simples
# - Notas rápidas
# - Edición de principiantes

# No es ideal para:
# - Archivos enormes (>10MB)
# - Programación compleja
# - Edición masiva de código
```

#### **3. Configurar Entorno Cómodo**
```bash
# Configuración mínima recomendada
echo "set linenumbers" >> ~/.nanorc
echo "set autoindent" >> ~/.nanorc
echo "set mouse" >> ~/.nanorc
```

#### **4. Verificar Cambios Después de Editar**
```bash
# Después de editar configuraciones
sudo systemctl daemon-reload  # Para archivos systemd
sudo systemctl restart servicio  # Para servicios
# O el comando apropiado para verificar sintaxis
```

---
### Enlaces Relacionadas
- [[nvim]] - Editor vim moderno y potente (alternativa avanzada)
- [[cat]] - Ver contenido de archivos sin editar
- [[less]] - Navegar archivos grandes sin editar
- [[Usuario root]] - Configurar sudo para editar archivos del sistema
- [[Sudo]] - Ejecutar nano con permisos elevados
- [[cp]] - Hacer backup antes de editar archivos importantes