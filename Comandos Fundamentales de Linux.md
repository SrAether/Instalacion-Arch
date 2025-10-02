# Comandos Fundamentales de Linux para Arch Linux

Este es tu **guía de referencia rápida** de comandos esenciales de Linux que necesitarás durante y después de la instalación de Arch Linux. Cada comando incluye su función principal y un enlace a su documentación completa.

---

## 📂 Navegación y Sistema de Archivos

### [[pwd]] - Mostrar Directorio Actual
```bash
pwd
```
**¿Qué hace?** Te muestra exactamente dónde estás en el sistema de archivos. Esencial para orientarte durante la instalación.

**Cuándo usarlo:**
- Verificar ubicación antes de ejecutar comandos críticos
- Confirmar que estás en el directorio correcto para [[Pacstrap]]
- Orientarte después de usar [[cd]]

---

### [[ls]] - Listar Contenido
```bash
ls -la
```
**¿Qué hace?** Muestra archivos y directorios. Con `-la` ves archivos ocultos, permisos y detalles completos.

**Cuándo usarlo:**
- Explorar el contenido de directorios
- Verificar que los archivos se crearon correctamente
- Ver permisos antes de modificar archivos de configuración

---

### [[cd]] - Cambiar Directorio
```bash
cd /directorio
cd ..
cd ~
```
**¿Qué hace?** Te mueve entre directorios del sistema de archivos.

**Cuándo usarlo:**
- Navegar al directorio de montaje `/mnt` durante instalación
- Acceder a directorios de configuración como `/etc`
- Volver al directorio home con `cd ~`

---

## 📍 Conceptos de Rutas

### [[Ruta absoluta]] - Rutas Completas
```bash
/home/usuario/archivo.txt
/etc/pacman.conf
/boot/grub/grub.cfg
```
**¿Qué es?** Rutas que empiezan desde la raíz (`/`) del sistema. Siempre funcionan sin importar dónde estés.

**Cuándo usarlas:**
- Scripts de instalación que deben funcionar desde cualquier ubicación
- Configuración de servicios systemd
- Referencias en archivos de configuración

---

### [[Ruta relativa]] - Rutas Desde Ubicación Actual
```bash
./archivo.txt
../directorio/
docs/manual.pdf
```
**¿Qué es?** Rutas que dependen de tu ubicación actual. Más cortas pero dependientes del contexto.

**Cuándo usarlas:**
- Navegación rápida en directorios cercanos
- Scripts que trabajan en el directorio actual
- Operaciones dentro del mismo proyecto

---

## 📁 Gestión de Archivos y Directorios

### [[mkdir]] - Crear Directorios
```bash
mkdir directorio
mkdir -p ruta/completa/nueva
```
**¿Qué hace?** Crea directorios nuevos. Con `-p` crea toda la estructura de directorios.

**Cuándo usarlo:**
- Crear puntos de montaje durante instalación
- Organizar estructura de directorios personalizados
- Preparar directorios para configuraciones

---

### [[touch]] - Crear Archivos Vacíos
```bash
touch archivo.txt
touch /etc/hostname
```
**¿Qué hace?** Crea archivos vacíos o actualiza la fecha de modificación de archivos existentes.

**Cuándo usarlo:**
- Crear archivos de configuración desde cero
- Preparar archivos antes de editarlos
- Actualizar timestamps de archivos

---

### [[cp]] - Copiar Archivos
```bash
cp archivo.txt copia.txt
cp -r directorio/ copia_directorio/
```
**¿Qué hace?** Copia archivos y directorios. Con `-r` copia directorios recursivamente.

**Cuándo usarlo:**
- Hacer backup de configuraciones antes de modificarlas
- Copiar archivos de ejemplo para personalizar
- Duplicar configuraciones entre usuarios

---

### [[mv]] - Mover y Renombrar
```bash
mv archivo.txt nuevo_nombre.txt
mv archivo.txt /otra/ubicacion/
```
**¿Qué hace?** Mueve archivos a nueva ubicación o les cambia el nombre. No crea copias.

**Cuándo usarlo:**
- Renombrar archivos de configuración
- Reorganizar estructura de archivos
- Mover archivos temporales a ubicación definitiva

---

### [[rm]] - Eliminar Archivos ⚠️
```bash
rm archivo.txt
rm -r directorio/
rm -i archivo_importante.txt
```
**¿Qué hace?** Elimina archivos permanentemente. ⚠️ **¡NO HAY PAPELERA!**

**Cuándo usarlo:**
- Limpiar archivos temporales después de instalación
- Eliminar configuraciones de prueba
- Liberar espacio en disco
- **¡SIEMPRE con precaución!**

---

## 📖 Visualización de Contenido

### [[cat]] - Mostrar Contenido Completo
```bash
cat archivo.txt
cat /etc/hostname
```
**¿Qué hace?** Muestra todo el contenido de archivos de texto de una vez.

**Cuándo usarlo:**
- Ver archivos de configuración pequeños
- Verificar contenido de archivos de sistema
- Combinar múltiples archivos

---

### [[less]] - Navegación Paginada
```bash
less archivo_largo.txt
less /var/log/pacman.log
```
**¿Qué hace?** Permite navegar por archivos grandes con búsqueda y paginación.

**Cuándo usarlo:**
- Explorar logs del sistema
- Leer documentación extensa
- Navegar archivos de configuración grandes

---

## 🌐 Red y Conectividad

### [[ping]] - Verificar Conectividad
```bash
ping google.com
ping -c 4 8.8.8.8
```
**¿Qué hace?** Prueba la conectividad de red y mide latencia.

**Cuándo usarlo:**
- Verificar internet durante instalación con [[iwctl]]
- Diagnosticar problemas de red
- Probar conectividad después de configurar red

---

### [[ip a]] - Ver Interfaces de Red
```bash
ip a
ip a show eth0
```
**¿Qué hace?** Muestra todas las interfaces de red, sus direcciones IP y estado.

**Cuándo usarlo:**
- Verificar que la red funciona después de configurar con [[iwctl]]
- Diagnosticar problemas de conectividad
- Ver direcciones IP asignadas

---

## 👤 Información de Usuario

### [[whoami]] - Identificar Usuario
```bash
whoami
sudo whoami
```
**¿Qué hace?** Muestra el nombre del usuario actual.

**Cuándo usarlo:**
- Verificar que estás ejecutando como usuario correcto
- Confirmar que [[Sudo]] funciona correctamente
- Debug de problemas de permisos

---

## ✏️ Editores de Texto

### [[nano]] - Editor Simple
```bash
nano archivo.txt
sudo nano /etc/fstab
```
**¿Qué hace?** Editor de texto fácil de usar con atajos familiares (Ctrl+X para salir).

**Cuándo usarlo:**
- Editar archivos de configuración como principiante
- Modificaciones rápidas de archivos de texto
- Cuando necesitas una interfaz simple y directa

---

### [[nvim]] - Editor Avanzado
```bash
nvim archivo.txt
sudo nvim /etc/pacman.conf
```
**¿Qué hace?** Editor modal potente y extensible, versión moderna de Vim.

**Cuándo usarlo:**
- Edición de código y configuraciones complejas
- Cuando necesitas funcionalidades avanzadas
- Trabajo con múltiples archivos simultáneamente

---

## 📤 Salida y Texto

### [[echo]] - Mostrar Texto
```bash
echo "Hola mundo"
echo $HOME
echo "texto" > archivo.txt
```
**¿Qué hace?** Muestra texto en pantalla o lo redirige a archivos.

**Cuándo usarlo:**
- Crear contenido básico en archivos
- Mostrar valores de variables
- Generar output para scripts

---

## 🔄 Flujo de Trabajo Típico en Arch Linux

### Durante la Instalación:
1. **[[pwd]]** - Verificar ubicación actual
2. **[[ls]]** - Ver dispositivos disponibles para particionar
3. **[[ping]]** - Verificar conectividad a internet
4. **[[ip a]]** - Confirmar configuración de red
5. **[[mkdir]]** - Crear puntos de montaje
6. **[[nano]]** - Editar configuraciones del sistema
7. **[[cat]]** - Verificar contenido de archivos creados

### Post-Instalación:
1. **[[whoami]]** - Verificar usuario actual
2. **[[cd]]** - Navegar a directorios de configuración
3. **[[cp]]** - Hacer backup de configuraciones
4. **[[nvim]]** - Editar configuraciones personalizadas
5. **[[less]]** - Revisar logs del sistema
6. **[[rm]]** - Limpiar archivos temporales

---

## 💡 Consejos de Uso

### Combinaciones Útiles:
```bash
# Verificar ubicación y contenido
pwd && ls -la

# Hacer backup antes de editar
cp archivo.conf archivo.conf.backup && nano archivo.conf

# Verificar red completa
ip a && ping -c 3 google.com

# Navegación segura
pwd && ls && cd directorio && pwd
```

### Atajos de Productividad:
```bash
# Historial de comandos
history | grep comando

# Autocompletado con Tab
cd /et<Tab>  # Completa a /etc/

# Repetir último comando
!!

# Editar último comando
fc
```

---

## 🚨 Comandos de Emergencia

### Si te pierdes:
```bash
pwd                  # ¿Dónde estoy?
ls                   # ¿Qué hay aquí?
cd ~                 # Ir a home
cd /                 # Ir a raíz
```

### Si algo sale mal:
```bash
whoami               # ¿Qué usuario soy?
sudo whoami          # ¿Funciona sudo?
ip a                 # ¿Tengo red?
ping google.com      # ¿Llego a internet?
```

### Si necesitas ayuda:
```bash
man comando          # Manual del comando
comando --help       # Ayuda rápida
info comando         # Información detallada
```

---

## 📚 Secuencia de Aprendizaje Recomendada

### Nivel Principiante:
1. [[pwd]], [[ls]], [[cd]] - Navegación básica
2. [[whoami]], [[echo]] - Orientación en el sistema
3. [[cat]], [[nano]] - Ver y editar archivos simples

### Nivel Intermedio:
4. [[mkdir]], [[touch]], [[cp]] - Gestión de archivos
5. [[mv]], [[rm]] - Operaciones avanzadas (¡con cuidado!)
6. [[less]], [[ping]] - Exploración y diagnóstico

### Nivel Avanzado:
7. [[Ruta absoluta]], [[Ruta relativa]] - Conceptos avanzados
8. [[ip a]], [[nvim]] - Herramientas profesionales
9. Combinación de comandos y scripts

---

## 🔗 Enlaces del Proyecto

- **[[README]]** - Información general del curso
- **[[Módulo 0]]** - Introducción al curso
- **[[Módulo 1 - El Taller Temporal (El entorno en vivo)]]** - Preparación inicial
- **[[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]]** - Preparación del disco
- **[[iwctl]]** - Configuración de Wi-Fi
- **[[Pacstrap]]** - Instalación del sistema base
- **[[Usuario root]]** - Configuración de permisos
- **[[Sudo]]** - Configuración de privilegios

---

> **💡 Tip Final:** No intentes memorizar todos los comandos de una vez. Usa esta guía como referencia y practica los comandos básicos primero. La fluidez viene con la práctica constante durante tu instalación y uso diario de Arch Linux.