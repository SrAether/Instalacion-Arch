El comando `ls` es tu **detector de contenido**. Si [[pwd]] te dice dónde estás, `ls` te muestra **qué hay** en ese lugar. Es como encender una linterna en una habitación oscura para ver todos los objetos que te rodean.

---
### ¿Qué Significa LS? 👀
**LS** significa **"List"** (Listar). Su función es mostrar el contenido de un directorio: archivos, subdirectorios y toda la información asociada que solicites.

### Uso Básico
```bash
# Listar contenido del directorio actual
ls

# Listar contenido de un directorio específico
ls /home/usuario/Documentos
```

### Opciones Más Útiles
#### **`ls -l` (Long format)**
Muestra información detallada: permisos, propietario, tamaño, fecha de modificación.
```bash
$ ls -l
drwxr-xr-x 2 usuario usuario 4096 Sep 30 10:30 Documentos
-rw-r--r-- 1 usuario usuario  156 Sep 30 09:15 archivo.txt
```

#### **`ls -a` (All files)**
Muestra **todos** los archivos, incluyendo los ocultos (que comienzan con punto).
```bash
$ ls -a
.  ..  .bashrc  .profile  Documentos  archivo.txt
```

#### **`ls -la` (Combinación más usada)**
Combina formato largo con archivos ocultos. Es probablemente la forma más común de usar `ls`.
```bash
$ ls -la
total 24
drwxr-xr-x 4 usuario usuario 4096 Sep 30 10:30 .
drwxr-xr-x 3 root    root    4096 Sep 29 15:20 ..
-rw-r--r-- 1 usuario usuario  220 Sep 29 15:20 .bash_logout
-rw-r--r-- 1 usuario usuario 3526 Sep 29 15:20 .bashrc
drwxr-xr-x 2 usuario usuario 4096 Sep 30 10:30 Documentos
```

#### **`ls -h` (Human readable)**
Muestra tamaños de archivo en formato legible para humanos (KB, MB, GB).
```bash
$ ls -lh
-rw-r--r-- 1 usuario usuario 1.5M Sep 30 10:30 video.mp4
-rw-r--r-- 1 usuario usuario  15K Sep 30 09:15 documento.pdf
```

### En el Contexto de la Instalación de Arch Linux

#### **Durante la Preparación ([[Módulo 1 - El Taller Temporal (El entorno en vivo)]])**
```bash
# Verificar el contenido del directorio de instalación
ls /mnt

# Comprobar que las particiones se montaron correctamente
ls -la /mnt
```

#### **Al Trabajar con Particiones ([[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]])**
```bash
# Listar dispositivos de bloque disponibles
ls /dev/sd*
ls /dev/nvme*

# Verificar puntos de montaje
ls -la /mnt/boot/efi
```

#### **Durante la Instalación del Sistema Base ([[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]])**
```bash
# Verificar que pacstrap instaló el sistema correctamente
ls -la /mnt
ls /mnt/bin
ls /mnt/etc
```

### Patrones Útiles
```bash
# Listar solo directorios
ls -d */

# Listar archivos por extensión
ls *.txt
ls *.md

# Ordenar por tamaño (más grandes primero)
ls -lS

# Ordenar por fecha de modificación
ls -lt
```

### Entendiendo la Salida de `ls -l`
```
drwxr-xr-x 2 usuario usuario 4096 Sep 30 10:30 Documentos
│││││││││ │ │       │       │    │             │
│││││││││ │ │       │       │    │             └─ Nombre
│││││││││ │ │       │       │    └─ Fecha de modificación
│││││││││ │ │       │       └─ Tamaño
│││││││││ │ │       └─ Grupo propietario
│││││││││ │ └─ Usuario propietario
│││││││││ └─ Número de enlaces
└─────────── Permisos (d=directorio, -=archivo, rwx=lectura/escritura/ejecución)
```

---
### Enlaces Relacionados
- [[pwd]] - Saber dónde estás antes de listar contenido
- [[cd]] - Cambiar a un directorio antes de listarlo
- [[Ruta absoluta]] - Usar ls con rutas desde la raíz
- [[Ruta relativa]] - Listar contenido relativo a tu ubicación
- [[cat]] - Ver contenido de archivos encontrados con ls