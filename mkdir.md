El comando `mkdir` es tu **constructor de espacios**. Si necesitas crear nuevas "habitaciones" (directorios) en el sistema de archivos, `mkdir` es la herramienta que convierte ideas en estructura organizada.

---
### ¿Qué Significa MKDIR? 🏗️
**MKDIR** significa **"Make Directory"** (Crear Directorio). Es el comando fundamental para crear la estructura organizacional de tu sistema de archivos, permitiéndote construir la jerarquía de carpetas que necesitas.

### Uso Básico
```bash
# Crear un directorio simple
mkdir mi-proyecto

# Crear múltiples directorios
mkdir docs config src

# Crear directorio con espacios (usar comillas)
mkdir "Mi Directorio Personal"
```

### Creación de Estructura Jerárquica
#### **Opción `-p` (Parents) - La Más Útil**
```bash
# Crear toda una estructura de directorios de una vez
mkdir -p proyectos/mi-app/src/components
mkdir -p documentos/2025/septiembre/notas

# Sin -p fallaría si los directorios padre no existen
# Con -p crea automáticamente toda la ruta
```

#### **Ejemplo Visual:**
```bash
# Esto crea automáticamente:
mkdir -p home/usuario/proyectos/arch-setup/configs/desktop

# Estructura resultante:
home/
└── usuario/
    └── proyectos/
        └── arch-setup/
            └── configs/
                └── desktop/
```

### En el Contexto de la Instalación de Arch Linux

#### **Durante el Particionado ([[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]])**
```bash
# Crear puntos de montaje necesarios
mkdir -p /mnt/boot/efi       # Para partición EFI
mkdir -p /mnt/home           # Para partición home (si existe)
mkdir -p /mnt/var            # Para partición var (si existe)
```

#### **Preparando la Estructura del Sistema**
```bash
# Después de montar particiones, verificar estructura
mkdir -p /mnt/etc/pacman.d   # Asegurar directorios de configuración
mkdir -p /mnt/var/log        # Directorios para logs
```

#### **Durante la Configuración ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**
```bash
# Crear directorios de usuario
mkdir -p /home/usuario/.config
mkdir -p /home/usuario/.local/share
mkdir -p /home/usuario/Documentos
mkdir -p /home/usuario/Proyectos
```

### Opciones Importantes

#### **`-p` (Parents) - Crear Directorios Padre**
```bash
# Sin -p: error si algún directorio padre no existe
mkdir docs/projects/2025     # ❌ Error si docs/ no existe

# Con -p: crea toda la estructura automáticamente
mkdir -p docs/projects/2025  # ✅ Crea docs/, projects/, y 2025/
```

#### **`-v` (Verbose) - Mostrar Lo Que Se Crea**
```bash
mkdir -pv proyectos/mi-app/{src,docs,config}
# Salida:
# mkdir: created directory 'proyectos'
# mkdir: created directory 'proyectos/mi-app'
# mkdir: created directory 'proyectos/mi-app/src'
# mkdir: created directory 'proyectos/mi-app/docs'
# mkdir: created directory 'proyectos/mi-app/config'
```

#### **`-m` (Mode) - Establecer Permisos**
```bash
# Crear directorio con permisos específicos
mkdir -m 755 directorio-publico
mkdir -m 700 directorio-privado   # Solo para el propietario
```

### Patrones de Uso Avanzados

#### **Creación de Estructura de Proyecto**
```bash
# Crear estructura completa de un proyecto de desarrollo
mkdir -p mi-proyecto/{src/{components,utils,styles},docs,tests,config}

# Resultado:
mi-proyecto/
├── src/
│   ├── components/
│   ├── utils/
│   └── styles/
├── docs/
├── tests/
└── config/
```

#### **Organización por Fechas**
```bash
# Estructura de backup o logs por fecha
mkdir -p backups/$(date +%Y/%m/%d)
mkdir -p logs/$(date +%Y-%m)

# Ejemplo: crea backups/2025/09/30/
```

#### **Directorios Temporales de Trabajo**
```bash
# Para instalación de Arch Linux
mkdir -p /tmp/arch-install/{downloads,configs,scripts}
```

### Buenas Prácticas

#### **1. Usar Nombres Descriptivos**
```bash
# ✅ Descriptivo y claro
mkdir -p proyectos/instalacion-arch/configuraciones

# ❌ Ambiguo
mkdir -p stuff/things/files
```

#### **2. Seguir Convenciones**
```bash
# Para archivos de configuración personal
mkdir -p ~/.config/mi-aplicacion
mkdir -p ~/.local/share/mi-aplicacion

# Para proyectos de desarrollo
mkdir -p ~/proyectos/nombre-del-proyecto/{src,docs,tests}
```

#### **3. Verificar Antes de Crear**
```bash
# Verificar si ya existe antes de crear
if [ ! -d "mi-directorio" ]; then
    mkdir -p mi-directorio
    echo "Directorio creado"
else
    echo "El directorio ya existe"
fi
```

#### **4. Combinar con Otros Comandos**
```bash
# Crear directorio y entrar inmediatamente
mkdir -p nuevo-proyecto/src && cd nuevo-proyecto/src

# Crear directorio y listar para confirmar
mkdir -p docs/manuales && ls -la docs/
```

### Casos de Uso en Administración de Sistemas

#### **Preparación de Entorno de Desarrollo**
```bash
mkdir -p ~/desarrollo/{python,javascript,rust,go}
mkdir -p ~/desarrollo/tools/{scripts,configs,docs}
```

#### **Organización de Datos del Sistema**
```bash
# Como administrador
mkdir -p /var/backup/{daily,weekly,monthly}
mkdir -p /etc/mi-servicio/{config,templates,scripts}
```

#### **Estructura para Dotfiles**
```bash
mkdir -p ~/dotfiles/{bash,zsh,vim,git,desktop}
mkdir -p ~/dotfiles/.config/{i3,polybar,rofi}
```

### Errores Comunes y Soluciones

❌ **Error: Permisos insuficientes**
```bash
mkdir /etc/mi-config
# Error: Permission denied
```
✅ **Solución:**
```bash
sudo mkdir /etc/mi-config
# O crear en tu directorio personal
mkdir ~/mi-config
```

❌ **Error: Directorio padre no existe**
```bash
mkdir documentos/importantes/secretos
# Error: No such file or directory
```
✅ **Solución:**
```bash
mkdir -p documentos/importantes/secretos
```

❌ **Error: Caracteres especiales problemáticos**
```bash
mkdir mi*directorio
# Puede causar problemas con expansión de shell
```
✅ **Solución:**
```bash
mkdir "mi-directorio" # Usar guiones en lugar de asteriscos
mkdir 'mi_directorio' # O guiones bajos
```

---
### Enlaces Relacionados
- [[cd]] - Entrar a los directorios que has creado
- [[ls]] - Verificar que los directorios se crearon correctamente
- [[pwd]] - Confirmar dónde estás antes de crear directorios
- [[touch]] - Crear archivos dentro de los directorios
- [[Ruta absoluta]] - Crear directorios con rutas absolutas
- [[Ruta relativa]] - Crear directorios relativos a tu ubicación