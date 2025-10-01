Una **ruta relativa** es como dar direcciones usando puntos de referencia: "dos cuadras a la izquierda", "subir un piso", "la casa de al lado". En GNU/Linux, es una ruta que **depende de tu ubicación actual** y se calcula a partir del directorio donde te encuentras.

---
### ¿Qué Define una Ruta Relativa? 🧭
Una ruta relativa tiene estas características:
1. **NO comienza con `/`** (no parte desde la raíz)
2. **Depende de tu directorio actual** (donde muestra [[pwd]])
3. **Es más corta y práctica** para trabajo local
4. **Usa símbolos especiales** para navegación

### Símbolos Especiales en Rutas Relativas
#### **`.` (Punto) = Directorio actual**
```bash
# Estos son equivalentes
ls .
ls
./script.sh
```

#### **`..` (Dos puntos) = Directorio padre**
```bash
# Subir un nivel
cd ..
ls ../
cat ../archivo.txt
```

#### **`~` (Tilde) = Tu directorio home**
```bash
# Siempre te lleva a /home/usuario
cd ~
ls ~/Documentos
cat ~/.bashrc
```

### Ejemplos Prácticos
Supongamos que estás en `/home/usuario/Documentos/proyectos`:

```bash
# RUTAS RELATIVAS desde /home/usuario/Documentos/proyectos
archivo.txt                    # /home/usuario/Documentos/proyectos/archivo.txt
./archivo.txt                  # Lo mismo que arriba
carpeta/archivo.txt            # /home/usuario/Documentos/proyectos/carpeta/archivo.txt
../notas.txt                   # /home/usuario/Documentos/notas.txt
../../Escritorio/imagen.jpg    # /home/usuario/Escritorio/imagen.jpg
~/Videos/pelicula.mp4          # /home/usuario/Videos/pelicula.mp4
```

### Navegación Relativa Paso a Paso
```bash
# Empezamos en /home/usuario
pwd                    # /home/usuario

# Entramos a Documentos (ruta relativa)
cd Documentos
pwd                    # /home/usuario/Documentos

# Entramos a proyectos (ruta relativa)
cd proyectos
pwd                    # /home/usuario/Documentos/proyectos

# Subimos un nivel (al directorio padre)
cd ..
pwd                    # /home/usuario/Documentos

# Subimos dos niveles
cd ../..
pwd                    # /home
```

### En el Contexto de la Instalación de Arch Linux

#### **Trabajando en tu Directorio Personal**
```bash
# Si estás en /home/usuario y tienes la ISO de Arch Linux
ls Descargas/archlinux-*.iso
cd Descargas
ls -la *.iso
```

#### **Durante la Configuración Post-Instalación**
```bash
# Si estás en tu directorio home configurando archivos
nano .bashrc           # Editar ~/.bashrc
ls .config/            # Ver ~/.config/
cd .local/share/       # Ir a ~/.local/share/
```

#### **Navegando en Proyectos**
```bash
# Si tienes un proyecto de dotfiles
cd ~/dotfiles
ls ./config/           # Ver ./config/
cp .vimrc ../backup/   # Copiar a ../backup/
```

### Ventajas de las Rutas Relativas
✅ **Portabilidad**: Funcionan en diferentes usuarios y sistemas
✅ **Brevedad**: Menos escritura para archivos cercanos
✅ **Intuitividad**: Navegación natural como caminar
✅ **Flexibilidad**: Fácil reorganización de directorios

### Cuándo Usar Rutas Relativas vs Absolutas

#### **Usa Rutas Relativas cuando:**
- Trabajas en tu propio directorio de usuario
- Los archivos están cerca de tu ubicación actual
- Quieres código portable entre usuarios
- Navegas de forma interactiva

#### **Ejemplo de Trabajo Típico:**
```bash
# En tu directorio de proyecto
cd ~/proyectos/mi-app
ls                     # Ver archivos del proyecto
cd src/                # Entrar a código fuente
nano main.py           # Editar archivo
cd ../docs/            # Ir a documentación
cat README.md          # Leer documentación
cd ..                  # Volver al directorio principal
```

### Combinando Rutas Relativas y Absolutas
```bash
# Desde cualquier lugar, ir a tu proyecto específico
cd ~/proyectos/arch-setup    # ~ es relativo a tu home

# Una vez ahí, usar rutas relativas para navegar
ls configs/                  # Ver configuraciones
cp templates/base.conf .     # Copiar archivo local
cd ../other-project          # Cambiar a proyecto hermano
```

### Errores Comunes
❌ **Error típico:**
```bash
# Si estás en /home/usuario/Documentos
cd proyectos/importante      # ✅ Correcto
cd /proyectos/importante     # ❌ Error: busca en la raíz /
```

❌ **Confundir ubicación:**
```bash
# Si estás en /usr/local pero crees que estás en ~
ls Documentos/               # ❌ No existe aquí
pwd                          # Siempre verifica dónde estás
cd ~/Documentos/             # ✅ Usar ~ para ir a home
```

---
### Enlaces Relacionados
- [[Ruta absoluta]] - Rutas que comienzan desde la raíz del sistema
- [[pwd]] - Verificar tu ubicación actual para rutas relativas
- [[cd]] - Navegar usando rutas relativas
- [[ls]] - Listar contenido con rutas relativas