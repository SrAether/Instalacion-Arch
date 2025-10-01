El comando `cd` es tu **vehículo de transporte** en el sistema de archivos de GNU/Linux. Si [[pwd]] te dice dónde estás y [[ls]] te muestra qué hay a tu alrededor, `cd` es lo que te permite **moverte** de un lugar a otro.

---
### ¿Qué Significa CD? 🚗
**CD** significa **"Change Directory"** (Cambiar Directorio). Es el comando más utilizado para navegar por el sistema de archivos, permitiéndote "entrar" y "salir" de directorios como si fuera caminar por habitaciones de una casa.

### Uso Básico
```bash
# Cambiar a un directorio específico
cd /home/usuario/Documentos

# Ir al directorio home (tu "hogar")
cd ~
cd          # Sin argumentos también te lleva home

# Subir un nivel (al directorio padre)
cd ..

# Volver al directorio anterior
cd -
```

### Navegación con Rutas Absolutas
```bash
# Desde cualquier lugar, ir directamente a ubicaciones específicas
cd /etc                              # Archivos de configuración
cd /home/usuario                     # Tu directorio personal
cd /usr/bin                          # Comandos del sistema
cd /mnt                              # Puntos de montaje (útil en instalación)
```

### Navegación con Rutas Relativas
```bash
# Si estás en /home/usuario
cd Documentos                        # Ir a /home/usuario/Documentos
cd ./Documentos                      # Lo mismo (explícito)
cd ../otro-usuario                   # Ir a /home/otro-usuario
cd ../../etc                         # Ir a /etc
```

### Patrones de Navegación Útiles

#### **Navegación Rápida al Home**
```bash
cd ~                # Tu directorio personal
cd ~/Documentos     # Directorio específico en home
cd                  # Sin argumentos = ir a home
```

#### **Navegación Hacia Atrás**
```bash
cd ..               # Subir un nivel
cd ../..            # Subir dos niveles
cd -                # Volver al directorio anterior (muy útil!)
```

#### **Navegación con Autocompletado**
```bash
# Presiona TAB para autocompletar
cd Doc[TAB]         # Se completa a "Documentos"
cd /usr/l[TAB]      # Se completa a "/usr/local"
```

### En el Contexto de la Instalación de Arch Linux

#### **Durante la Preparación ([[Módulo 1 - El Taller Temporal (El entorno en vivo)]])**
```bash
# En el entorno archiso, navegar por el sistema
cd /                 # Ir a la raíz para orientarse
cd /dev              # Ver dispositivos disponibles
cd /sys/firmware/efi # Verificar UEFI
```

#### **Durante el Particionado ([[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]])**
```bash
# Trabajar con puntos de montaje
cd /mnt              # Ir al punto de montaje principal
cd /mnt/boot         # Verificar partición boot
cd /mnt/boot/efi     # Verificar partición EFI
```

#### **Durante la Instalación ([[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]])**
```bash
# Después de pacstrap, explorar el sistema instalado
cd /mnt              # Sistema recién instalado
cd /mnt/etc          # Archivos de configuración
cd /mnt/home         # Directorios de usuarios
```

#### **En Chroot ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**
```bash
# Una vez dentro del chroot, navegar como en sistema normal
cd /etc              # Configurar el sistema
cd /boot             # Configurar arranque
cd /home             # Preparar usuarios
```

### Trucos y Consejos Avanzados

#### **Uso de Variables de Entorno**
```bash
cd $HOME             # Equivalente a cd ~
cd $OLDPWD           # Equivalente a cd -
```

#### **Navegación con Espacios en Nombres**
```bash
# Si el directorio tiene espacios
cd "Mi Directorio"           # Con comillas
cd Mi\ Directorio            # Escapando el espacio
cd 'Otro Directorio'         # Con comillas simples
```

#### **Verificar Antes de Cambiar**
```bash
# Buena práctica: verificar que el directorio existe
ls /ruta/que/quiero/verificar
cd /ruta/que/quiero/verificar
```

#### **Combinando cd con otros comandos**
```bash
# Cambiar directorio Y listar contenido
cd /etc && ls

# Cambiar directorio Y mostrar dónde estamos
cd /usr/local && pwd

# Ir a un directorio Y ejecutar un comando
cd ~/proyectos && ls -la
```

### Navegación Eficiente en Sesiones de Trabajo

#### **Ejemplo de Flujo de Trabajo Típico:**
```bash
# Inicio: estoy en ~
pwd                          # /home/usuario

# Ir a trabajar en un proyecto
cd ~/proyectos/mi-app
ls                           # ver archivos del proyecto

# Editar configuración
cd config/
nano app.conf

# Volver al directorio principal del proyecto
cd ..

# Ir a documentación
cd docs/

# Volver atrás rápidamente
cd -                         # vuelve a ~/proyectos/mi-app

# Ir a otro proyecto hermano
cd ../otro-proyecto

# Volver a home
cd
```

### Errores Comunes y Cómo Evitarlos

❌ **Error: Directorio no existe**
```bash
cd /ruta/inexistente
# Error: No such file or directory
```
✅ **Solución: Verificar primero**
```bash
ls /ruta/que/quiero/ir       # Verificar que existe
cd /ruta/que/quiero/ir       # Entonces cambiar
```

❌ **Error: Permisos insuficientes**
```bash
cd /root
# Error: Permission denied
```
✅ **Solución: Usar sudo si es necesario**
```bash
sudo -i                      # Cambiar a root
cd /root                     # Ahora funciona
```

❌ **Error: Confundir ubicación actual**
```bash
cd proyectos                 # ¿Existe proyectos aquí?
```
✅ **Solución: Siempre saber dónde estás**
```bash
pwd                          # Ver ubicación actual
ls                           # Ver qué hay disponible
cd proyectos                 # Entonces navegar
```

---
### Enlaces Relacionados
- [[pwd]] - Saber dónde estás antes/después de cambiar directorio
- [[ls]] - Ver contenido antes de entrar a un directorio
- [[Ruta absoluta]] - Usar cd con rutas desde la raíz
- [[Ruta relativa]] - Navegar relativamente a tu ubicación
- [[mkdir]] - Crear directorios antes de entrar en ellos