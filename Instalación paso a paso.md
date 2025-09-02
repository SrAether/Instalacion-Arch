# Guía Detallada de Instalación de Arch Linux (UEFI)

Este tutorial te guiará a través de la instalación de Arch Linux en un sistema con arranque UEFI. Cada fase contiene los comandos necesarios para completar el proceso.

### **Fase 1: Preparación del Entorno en Vivo (LiveCD)**

Configuraciones iniciales antes de modificar el disco duro.

1. **Configuración del Teclado:**
    
    - Para inglés internacional (recomendado para programar, con acceso a `ñ` y acentos vía `AltGr`):
        
        ```
        loadkeys us-intl
        ```
        
    - Para español:
        
        ```
        loadkeys es
        ```
        
    - Para Latinoamérica:
        
        ```
        loadkeys la-latin1
        ```
        
2. **Verificar Conexión a Internet:**
    
    ```
    ping -c 3 archlinux.org
    ```
    
    Si usas Wi-Fi:
    
    ```
    iwctl
    # Dentro de iwctl:
    # device list (para ver tu dispositivo, ej. wlan0)
    # station [dispositivo] scan
    # station [dispositivo] get-networks
    # station [dispositivo] connect [nombre-de-la-red]
    # exit
    ```
    
3. **Actualizar el Reloj del Sistema:**
    
    ```
    timedatectl set-ntp true
    ```
    
4. **Verificar Modo de Arranque UEFI:** (Si este directorio existe, estás en modo UEFI).
    
    ```
    ls /sys/firmware/efi/efivars
    ```
    

### **Fase 2: Particionado y Formateo del Disco**

Se prepara el disco duro para la instalación. (Ejemplo con `/dev/sda`).

1. **Identificar el Disco:**
    
    ```
    fdisk -l
    ```
    
2. **Crear Particiones con `cfdisk`:**
    
    ```
    cfdisk /dev/sda
    ```
    
    - **Recomendación de particionado (GPT):**
        
        - `/dev/sda1`: 512M - Tipo: `EFI System`
            
        - `/dev/sda2`: (Tamaño deseado) - Tipo: `Linux swap`
            
        - `/dev/sda3`: (Tamaño deseado, ej. 30G) - Tipo: `Linux filesystem` (para la raíz `/`)
            
        - `/dev/sda4`: (Resto del disco) - Tipo: `Linux filesystem` (para el home `/home`)
            
3. **Formatear las Particiones:**
    
    - **EFI:**
        
        ```
        mkfs.fat -F32 /dev/sda1
        ```
        
    - **SWAP:**
        
        ```
        mkswap /dev/sda2
        swapon /dev/sda2
        ```
        
    - **Raíz (Root):**
        
        ```
        mkfs.ext4 /dev/sda3
        ```
        
    - **Home:**
        
        ```
        mkfs.ext4 /dev/sda4
        ```
        

### **Fase 3: Instalación del Sistema Base**

Montaje de particiones e instalación de los paquetes esenciales.

1. **Montar las Particiones:**
    
    ```
    mount /dev/sda3 /mnt
    mkdir -p /mnt/boot/efi
    mount /dev/sda1 /mnt/boot/efi
    mkdir /mnt/home
    mount /dev/sda4 /mnt/home
    ```
    
2. **Instalar el Sistema Base:**
    
    ```
    pacstrap /mnt base linux linux-firmware base-devel nano networkmanager
    ```
    
3. **Generar Fstab:** (Define cómo se montarán las particiones al arrancar).
    
    ```
    genfstab -U /mnt >> /mnt/etc/fstab
    ```
    

### **Fase 4: Configuración del Sistema Instalado (Chroot)**

Se ingresa al nuevo sistema para configurarlo.

1. **Entrar en Chroot:**
    
    ```
    arch-chroot /mnt
    ```
    
2. **Zona Horaria:** (Cambia `America/Mexico_City` por tu zona).
    
    ```
    ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime
    hwclock --systohc
    ```
    
3. **Idioma y Localización:**
    
    - Edita `/etc/locale.gen` y descomenta tu localización (ej. `en_US.UTF-8 UTF-8` y `es_MX.UTF-8 UTF-8`).
        
        ```
        nano /etc/locale.gen
        ```
        
    - Genera los locales:
        
        ```
        locale-gen
        ```
        
    - Crea el archivo `locale.conf`:
        
        ```
        echo "LANG=en_US.UTF-8" > /etc/locale.conf
        ```
        
4. **Configuración de Teclado (Consola/TTY):**
    
    ```
    echo "KEYMAP=us-intl" > /etc/vconsole.conf
    ```
    
5. **Configuración de Red:**
    
    - Nombre del equipo:
        
        ```
        echo "arch-pc" > /etc/hostname
        ```
        
    - Archivo hosts:
        
        ```
        nano /etc/hosts
        ```
        
        Añade estas líneas:
        
        ```
        127.0.0.1   localhost
        ::1         localhost
        127.0.1.1   arch-pc.localdomain arch-pc
        ```
        
6. **Usuarios:**
    
    - Contraseña para `root`:
        
        ```
        passwd
        ```
        
    - Crear un nuevo usuario (reemplaza `usuario`):
        
        ```
        useradd -m -G wheel usuario
        passwd usuario
        ```
        
    - Habilitar `sudo` para el grupo `wheel`:
        
        ```
        pacman -S sudo
        EDITOR=nano visudo
        ```
        
        Dentro del editor, descomenta la línea: `%wheel ALL=(ALL:ALL) ALL`
        

### **Fase 5: Instalación del Gestor de Arranque (GRUB)**

1. **Instalar Paquetes Necesarios:**
    
    ```
    pacman -S grub efibootmgr
    ```
    
2. **Instalar GRUB:**
    
    ```
    grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
    ```
    
3. **Crear Configuración de GRUB:**
    
    ```
    grub-mkconfig -o /boot/grub/grub.cfg
    ```
    

### **Fase 6: Finalización y Reinicio**

1. **Habilitar Servicio de Red:**
    
    ```
    systemctl enable NetworkManager
    ```
    
2. **Salir y Reiniciar:**
    
    ```
    exit
    umount -R /mnt
    reboot
    ```
    
    **¡Recuerda retirar el medio de instalación!**
    

### **Fase 7: Configuración Post-Instalación**

Primeros pasos en tu nuevo sistema.

1. **Configurar Teclado (Entorno Gráfico X.Org):** Este es el paso clave para que el teclado funcione como deseas en tu escritorio.
    
    ```
    localectl set-x11-keymap us pc105 intl
    ```
    
2. **Instalar Entorno Gráfico y Drivers:**
    
    - **Servidor Gráfico:**
        
        ```
        sudo pacman -S xorg
        ```
        
    - **Driver de Video** (ejemplo para Intel):
        
        ```
        sudo pacman -S xf86-video-intel
        ```
        
    - **Entorno de Escritorio** (ejemplo para GNOME):
        
        ```
        sudo pacman -S gnome
        sudo systemctl enable gdm
        ```
        
3. **Reiniciar para Iniciar en Modo Gráfico:**
    
    ```
    reboot
    ```
    

¡Felicidades! Has instalado y configurado Arch Linux.