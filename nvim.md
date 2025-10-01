El comando `nvim` (Neovim) es tu **editor de texto potente y moderno**. Es una versión mejorada de Vim con mejor extensibilidad, interfaz más moderna y configuración más flexible. Aunque tiene una curva de aprendizaje empinada, es extremadamente poderoso para edición de código y archivos de configuración.

---
### ¿Qué Hace NVIM? 🚀
**NVIM** (Neovim) es un editor modal que ofrece:
1. **Edición modal** (Normal, Insert, Visual, Command)
2. **Configuración extensible** con Lua
3. **Plugin ecosystem** masivo
4. **Rendimiento superior** para archivos grandes
5. **Integración moderna** con LSP, Git, etc.

### Instalación
```bash
# Instalar Neovim en Arch Linux
sudo pacman -S neovim

# Verificar instalación
nvim --version

# Crear alias común (opcional)
echo "alias vim='nvim'" >> ~/.bashrc
echo "alias vi='nvim'" >> ~/.bashrc
```

### Sintaxis Básica
```bash
nvim archivo.txt           # Abrir/crear archivo
nvim                        # Abrir editor vacío
nvim +número archivo.txt    # Ir a línea específica
nvim -R archivo.txt         # Solo lectura
```

### En el Contexto de la Instalación de Arch Linux

#### **Editar Configuraciones del Sistema**
```bash
# Configurar fstab
sudo nvim /etc/fstab

# Configurar pacman
sudo nvim /etc/pacman.conf

# Configurar grub
sudo nvim /etc/default/grub

# Configurar systemd
sudo nvim /etc/systemd/system/mi-servicio.service
```

#### **Configurar Shell y Dotfiles**
```bash
# Configurar bash
nvim ~/.bashrc

# Configurar zsh
nvim ~/.zshrc

# Configurar git
nvim ~/.gitconfig

# Configurar el propio nvim
nvim ~/.config/nvim/init.lua
```

#### **Desarrollo y Scripts**
```bash
# Crear scripts de instalación
nvim install_arch.sh

# Editar configuraciones personalizadas
nvim ~/.config/i3/config
nvim ~/.config/polybar/config
nvim ~/.xinitrc
```

### Modos de Neovim

#### **Modo Normal (Navegación y Comandos)**
```
Esc         - Entrar en modo normal
h j k l     - Mover izquierda/abajo/arriba/derecha
w           - Próxima palabra
b           - Palabra anterior
0           - Inicio de línea
$           - Final de línea
gg          - Inicio del archivo
G           - Final del archivo
```

#### **Modo Insert (Escribir Texto)**
```
i           - Insertar antes del cursor
a           - Insertar después del cursor
I           - Insertar al inicio de la línea
A           - Insertar al final de la línea
o           - Nueva línea abajo
O           - Nueva línea arriba
```

#### **Modo Visual (Seleccionar Texto)**
```
v           - Modo visual (selección por caracteres)
V           - Modo visual línea (selección por líneas)
Ctrl+v      - Modo visual bloque (selección rectangular)
```

#### **Modo Command (Comandos)**
```
:           - Entrar en modo comando
:w          - Guardar archivo
:q          - Salir
:wq         - Guardar y salir
:q!         - Salir sin guardar
```

### Comandos Esenciales de Navegación

#### **Navegación Básica**
```
h j k l     - Izquierda, abajo, arriba, derecha
w           - Siguiente palabra
b           - Palabra anterior
e           - Final de palabra actual
0           - Inicio de línea
^           - Primer carácter no vacío
$           - Final de línea
```

#### **Navegación por Pantalla**
```
Ctrl+f      - Página siguiente
Ctrl+b      - Página anterior
Ctrl+d      - Media página abajo
Ctrl+u      - Media página arriba
H           - Top de pantalla
M           - Medio de pantalla
L           - Bottom de pantalla
```

#### **Navegación por Archivo**
```
gg          - Inicio del archivo
G           - Final del archivo
:numero     - Ir a línea número
%           - Ir al paréntesis/bracket correspondiente
```

### Comandos de Edición

#### **Eliminar y Cambiar**
```
x           - Eliminar carácter bajo cursor
dd          - Eliminar línea completa
dw          - Eliminar palabra
d$          - Eliminar hasta final de línea
c           - Cambiar (eliminar y entrar en modo insert)
cw          - Cambiar palabra
cc          - Cambiar línea completa
```

#### **Copiar y Pegar**
```
yy          - Copiar línea
yw          - Copiar palabra
y$          - Copiar hasta final de línea
p           - Pegar después del cursor
P           - Pegar antes del cursor
```

#### **Deshacer y Rehacer**
```
u           - Deshacer
Ctrl+r      - Rehacer
.           - Repetir último comando
```

### Búsqueda y Reemplazo

#### **Búsqueda**
```
/texto      - Buscar "texto" hacia adelante
?texto      - Buscar "texto" hacia atrás
n           - Siguiente ocurrencia
N           - Anterior ocurrencia
*           - Buscar palabra bajo cursor
#           - Buscar palabra bajo cursor (hacia atrás)
```

#### **Reemplazo**
```
:s/viejo/nuevo/         - Reemplazar primera ocurrencia en línea
:s/viejo/nuevo/g        - Reemplazar todas en línea
:%s/viejo/nuevo/g       - Reemplazar todas en archivo
:%s/viejo/nuevo/gc      - Reemplazar con confirmación
```

### Casos de Uso Específicos en Arch Linux

#### **1. Configurar Fstab**
```bash
sudo nvim /etc/fstab

# Dentro de nvim:
# 1. Usar 'o' para nueva línea
# 2. Escribir entrada de montaje:
#    UUID=xxxxx /mount/point ext4 defaults 0 2
# 3. Esc para salir de insert
# 4. :wq para guardar y salir
```

#### **2. Editar Scripts de Instalación**
```bash
nvim install_packages.sh

# Configurar con syntax highlighting automático
# Nvim detecta .sh y aplica colores

# Ejemplo de contenido:
#!/bin/bash
packages=(
    "git"
    "base-devel"
    "linux-headers"
)

for pkg in "${packages[@]}"; do
    sudo pacman -S --noconfirm "$pkg"
done
```

#### **3. Configurar Neovim**
```bash
# Crear configuración básica
mkdir -p ~/.config/nvim
nvim ~/.config/nvim/init.lua

# Configuración inicial:
-- Configuración básica
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.tabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true
vim.opt.autoindent = true

-- Mapeos de teclas
vim.keymap.set('n', '<leader>w', ':w<CR>')
vim.keymap.set('n', '<leader>q', ':q<CR>')
```

#### **4. Editar Configuraciones de Sistema**
```bash
# Configurar servicios systemd
sudo nvim /etc/systemd/system/mi-servicio.service

# Dentro del archivo:
# - Usar Ctrl+v para selección de bloque
# - Usar >> y << para indentar
# - Usar :syntax on para highlighting
```

### Configuración Básica de Neovim

#### **Archivo de Configuración Principal**
```bash
# Crear directorio de configuración
mkdir -p ~/.config/nvim

# Configuración en Lua (moderna)
nvim ~/.config/nvim/init.lua
```

#### **Configuración Básica Recomendada**
```lua
-- ~/.config/nvim/init.lua

-- Opciones básicas
vim.opt.number = true                -- Números de línea
vim.opt.relativenumber = true        -- Números relativos
vim.opt.mouse = 'a'                  -- Habilitar ratón
vim.opt.ignorecase = true            -- Búsqueda insensible a mayúsculas
vim.opt.smartcase = true             -- Sensible si hay mayúsculas
vim.opt.hlsearch = false             -- No destacar búsquedas
vim.opt.wrap = false                 -- No quebrar líneas
vim.opt.tabstop = 4                  -- Tamaño de tab
vim.opt.shiftwidth = 4               -- Tamaño de indentación
vim.opt.expandtab = true             -- Usar espacios en lugar de tabs
vim.opt.autoindent = true            -- Indentación automática

-- Mapeos útiles
vim.g.mapleader = ' '                -- Tecla líder = espacio
vim.keymap.set('n', '<leader>w', ':w<CR>')        -- Líder+w = guardar
vim.keymap.set('n', '<leader>q', ':q<CR>')        -- Líder+q = salir
vim.keymap.set('n', '<leader>x', ':wq<CR>')       -- Líder+x = guardar y salir

-- Colorscheme
vim.cmd('colorscheme habamax')       -- Tema por defecto
```

### Plugins Esenciales

#### **Gestor de Plugins (Packer)**
```bash
# Instalar packer
git clone --depth 1 https://github.com/wbthomason/packer.nvim \
    ~/.local/share/nvim/site/pack/packer/start/packer.nvim
```

```lua
-- ~/.config/nvim/lua/plugins.lua
return require('packer').startup(function(use)
    use 'wbthomason/packer.nvim'     -- Packer se gestiona a sí mismo
    
    -- Explorador de archivos
    use 'nvim-tree/nvim-tree.lua'
    use 'nvim-tree/nvim-web-devicons'
    
    -- Syntax highlighting
    use 'nvim-treesitter/nvim-treesitter'
    
    -- LSP (Language Server Protocol)
    use 'neovim/nvim-lspconfig'
    
    -- Autocompletado
    use 'hrsh7th/nvim-cmp'
    use 'hrsh7th/cmp-nvim-lsp'
    
    -- Fuzzy finder
    use 'nvim-telescope/telescope.nvim'
    use 'nvim-lua/plenary.nvim'
    
    -- Git integration
    use 'lewis6991/gitsigns.nvim'
    
    -- Status line
    use 'nvim-lualine/lualine.nvim'
end)
```

### Flujo de Trabajo Típico

#### **Editar Archivo de Configuración**
```bash
# 1. Abrir archivo
sudo nvim /etc/pacman.conf

# 2. Navegar al lugar correcto
/Color              # Buscar "Color"
n                   # Siguiente ocurrencia si es necesario

# 3. Descomentar línea
^                   # Ir al inicio de línea
x                   # Eliminar # si está comentado

# 4. Guardar y salir
:wq
```

#### **Crear Script desde Cero**
```bash
# 1. Crear nuevo archivo
nvim ~/backup_script.sh

# 2. Entrar en modo insert y escribir shebang
i
#!/bin/bash

# 3. Continuar escribiendo script
# 4. Esc para salir de insert
# 5. :wq para guardar

# 6. Hacer ejecutable
chmod +x ~/backup_script.sh
```

### Trucos y Atajos Avanzados

#### **Edición Múltiple**
```
Ctrl+v      - Modo visual bloque
j/k         - Seleccionar líneas
I           - Insertar al inicio de selección
texto       - Escribir texto
Esc         - Aplicar a todas las líneas
```

#### **Ventanas y Pestañas**
```
:split      - División horizontal
:vsplit     - División vertical
Ctrl+w h/j/k/l  - Navegar entre ventanas
:tabnew     - Nueva pestaña
gt          - Siguiente pestaña
gT          - Pestaña anterior
```

#### **Marcadores**
```
ma          - Marcar posición como 'a'
'a          - Ir a marca 'a'
``          - Ir a última posición
```

### Comparación con Otros Editores

#### **NVIM vs NANO**
```bash
# NANO: fácil pero limitado
nano archivo.txt
# - Atajos simples (Ctrl+X)
# - Perfecto para principiantes
# - Funcionalidad básica

# NVIM: potente pero complejo
nvim archivo.txt
# - Altamente personalizable
# - Muy eficiente una vez aprendido
# - Curva de aprendizaje empinada
```

#### **NVIM vs VIM Clásico**
```bash
# VIM clásico
vim archivo.txt
# - Vimscript para configuración
# - Ecosistema maduro
# - Menos features modernas

# NEOVIM
nvim archivo.txt
# - Configuración en Lua
# - LSP integrado
# - Mejor performance
# - Desarrollo más activo
```

### Solución de Problemas

#### **Problema: Quiero Salir Pero No Puedo**
```
# Si estás atascado:
Esc         - Asegurar que estás en modo normal
:q!         - Salir sin guardar
# O
ZQ          - Atajo para salir sin guardar
```

#### **Problema: Cambios No Guardados**
```
# Ver estado del archivo
:w          - Guardar cambios
:wq         - Guardar y salir
:q!         - Salir sin guardar (perder cambios)
```

#### **Problema: No Funciona Highlighting**
```
# Verificar syntax
:syntax on
:set filetype=sh        # Para scripts bash
:set filetype=conf      # Para archivos de configuración
```

#### **Problema: Configuración Perdida**
```bash
# Verificar configuración
nvim --version
nvim -c "echo \$MYVIMRC" -c "quit"

# Reinstalar configuración
rm -rf ~/.config/nvim
# Recrear configuración básica
```

### Scripts Útiles con Neovim

#### **Configurador Rápido**
```bash
#!/bin/bash
# nvim_setup.sh

echo "Configurando Neovim..."

# Crear directorios
mkdir -p ~/.config/nvim/lua

# Configuración básica
cat > ~/.config/nvim/init.lua << 'EOF'
-- Configuración básica de Neovim
vim.opt.number = true
vim.opt.mouse = 'a'
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.tabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true

-- Mapeos básicos
vim.g.mapleader = ' '
vim.keymap.set('n', '<leader>w', ':w<CR>')
vim.keymap.set('n', '<leader>q', ':q<CR>')
EOF

echo "Configuración básica de Neovim instalada en ~/.config/nvim/init.lua"
```

#### **Editor Seguro con Backup**
```bash
#!/bin/bash
# safe_nvim.sh

file="$1"

if [[ -z "$file" ]]; then
    echo "Uso: $0 archivo"
    exit 1
fi

# Backup si existe
if [[ -f "$file" ]]; then
    cp "$file" "${file}.bak.$(date +%Y%m%d_%H%M%S)"
    echo "Backup creado"
fi

# Editar con nvim
nvim "$file"
```

### Recursos de Aprendizaje

#### **Tutorial Integrado**
```bash
# Tutorial interactivo de Vim (también aplica a nvim)
vimtutor

# O específico de Neovim
nvim -c ":Tutor"
```

#### **Comandos de Ayuda**
```
:help           - Ayuda general
:help i         - Ayuda sobre modo insert
:help :w        - Ayuda sobre comando :w
:help nvim      - Ayuda específica de Neovim
```

### Buenas Prácticas

#### **1. Empezar con Configuración Mínima**
```lua
-- Solo lo esencial al principio
vim.opt.number = true
vim.opt.mouse = 'a'
vim.g.mapleader = ' '
vim.keymap.set('n', '<leader>w', ':w<CR>')
```

#### **2. Aprender Gradualmente**
```bash
# Día 1: Movimiento básico (h,j,k,l)
# Día 2: Modos (i,v,esc,:)
# Día 3: Edición básica (dd,yy,p)
# Día 4: Búsqueda (/,n,N)
# Semana 2: Comandos avanzados
```

#### **3. Usar para Casos Apropiados**
```bash
# Nvim es excelente para:
# - Código fuente
# - Configuraciones complejas
# - Edición masiva
# - Trabajo remoto (SSH)

# Considera nano para:
# - Edición rápida y simple
# - Usuarios principiantes
# - Archivos pequeños
```

#### **4. Configurar Alias Útiles**
```bash
# En ~/.bashrc
alias vi='nvim'
alias vim='nvim'
alias e='nvim'              # Editor rápido
alias se='sudo nvim'        # Sudo editor
```

---
### Enlaces Relacionados
- [[nano]] - Editor simple y amigable para principiantes
- [[cat]] - Ver contenido de archivos sin editar
- [[less]] - Navegar archivos sin modificar
- [[Sudo]] - Ejecutar nvim con permisos elevados
- [[find]] - Buscar archivos para abrir con nvim
