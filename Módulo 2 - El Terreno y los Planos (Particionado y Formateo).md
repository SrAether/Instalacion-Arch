Hemos completado la preparación en nuestro taller temporal. Ahora, nos ponemos el casco y salimos al terreno: nuestr**Ahora, es el momento de llamar al equipo de construcción en el [[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]].**

---
### Enlaces Relacionados
- [[Módulo 1 - El Taller Temporal (El entorno en vivo)]] - Módulo anterior: preparación del entorno
- [[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]] - Siguiente paso: instalación del sistema base
- [[cfdisk]] - Herramienta principal de particionado utilizada
- [[UEFI]] - Sistema de arranque que requiere partición EFI
- [[Btrfs]] - Sistema de archivos moderno utilizado
- [[Tabla de particones]] - Conceptos sobre esquemas de particionado disco duro. En este momento, es solo una parcela de tierra vacía y sin definir.
Nuestro trabajo en esta fase se divide en dos grandes tareas de arquitectura y cimentación:
1. **Dibujar los Planos (Particionado):** Usaremos nuestra herramienta de arquitecto (cfdisk) para trazar líneas en el terreno, dividiéndolo en "habitaciones" funcionales. Cada habitación será una **partición** con un propósito específico: la entrada principal, los cimientos, las áreas comunes, etc.
2. **Preparar la Tierra (Formateo):** Una vez que los planos están dibujados, no podemos construir directamente sobre la tierra. Debemos prepararla: verter el concreto para el piso, preparar el suelo para el jardín. Esto es el **formateo**. Le damos a cada habitación (partición) un "sistema de archivos" para que pueda almacenar datos de forma organizada.

Este es un paso deliberado y metódico. Un error aquí es como construir una pared en el lugar equivocado. Pero no te preocupes, seguiremos el plano al pie de la letra.

---
### **Paso 1: Inspeccionar el Terreno (Identificar el Disco Duro)**
**El Porqué:** Antes de dibujar, debemos asegurarnos de que estamos trabajando en la parcela correcta. Si tu computadora tiene varios discos (un SSD y un HDD, por ejemplo), elegir el incorrecto podría borrar datos importantes.
**El Cómo:** Usaremos el comando fdisk para listar todos los dispositivos de almacenamiento conectados.
```
fdisk -l
```
Verás una lista de discos. Presta atención a los nombres y tamaños para identificar tu objetivo.
- En una PC física, los nombres suelen ser /dev/sda, /dev/sdb, etc., para discos SATA/USB, o /dev/nvme0n1 para discos NVMe modernos.
- En una máquina virtual, el nombre a menudo es /dev/vda.

> **¡ADVERTENCIA!** Asegúrate al 100% del nombre de tu disco. Las acciones en los siguientes pasos son **destructivas** y borrarán todo el contenido del disco seleccionado. Para esta guía, usaremos /dev/sdX como ejemplo. **Debes reemplazar sdX con el nombre real de tu disco.**
---
### **Paso 2: Dibujar los Planos (Crear las [[Tabla de particones |particiones]] con [[cfdisk]])**

**El Porqué:** Ahora dividiremos nuestro disco en las "habitaciones" esenciales que nuestra casa de Arch Linux necesitará. Usaremos [[cfdisk]], una herramienta con una interfaz amigable que nos facilita el trabajo.

**Nuestras "Habitaciones" (Particiones Recomendadas):**

- **Partición EFI (/boot/efi):** La entrada principal. Un pequeño espacio requerido por los sistemas [[UEFI]] para saber cómo arrancar el sistema operativo.
- **Partición SWAP:** La bodega de emergencias. Es un espacio en el disco que el sistema usa como si fuera RAM extra cuando la memoria principal se llena.
- **Partición Raíz (/):** Los cimientos y la estructura. Aquí vivirá el sistema operativo, sus programas y configuraciones.
- **Partición [[Btrfs]]:** **La Gran Parcela Principal.** Aquí está la verdadera innovación. En lugar de crear muros de hormigón para la raíz y el home, reservamos una única y enorme parcela de tierra de primera calidad. Más adelante, sobre esta parcela, usaremos Btrfs para dibujar "zonas flexibles" (subvolúmenes) para los cimientos (`/`) y nuestras habitaciones (`/home`).

**El Cómo:**
Con nuestro plano de tres particiones en mente, nos arremangamos y comenzamos el trazado.
1. **Desplegar la Mesa de Dibujo (`cfdisk`):** Apuntamos la herramienta a nuestro terreno.
    ```
    cfdisk /dev/sdX
    ```
2. **Elegir el Estilo Arquitectónico (`gpt`):** Se nos pedirá elegir el tipo de tabla de particiones. `gpt` (GUID Partition Table) es el estándar moderno, un requisito para `[[UEFI]]` que nos permite construir estructuras más grandes y complejas. Selecciónalo y presiona Enter.
3. **Trazar las Divisiones (Crear cada Partición):** Ahora estás frente a la interfaz principal. El proceso es metódico: seleccionas el espacio libre (`Free space`), eliges `[ New ]`, defines el tamaño y ajustas el tipo si es necesario.
    **Sigue este plano con precisión:**
    a. **Partición EFI:**
    - Tamaño: `512M`
    - Tras crearla, selecciónala, navega a `[ Type ]` y elige `EFI System`.
    b. **Partición SWAP:**
    - Tamaño: Una buena regla es un tamaño igual a tu RAM (ej: `8G` si tienes 8GB de RAM).
    - Tras crearla, ve a `[ Type ]` y elige `Linux swap`.
    c. **Partición Btrfs:**
    - Tamaño: Asígnale **todo el espacio restante** (simplemente presiona Enter cuando te pida el tamaño).
    - El tipo por defecto (`Linux filesystem`) es el correcto.
4. **Firmar los Planos (`Write`):** Con las tres divisiones estructurales listas en nuestro borrador, es momento de grabarlas en piedra. Este es el momento de la firma final, donde confirmamos nuestro diseño. Selecciona `[ Write ]`, te pedirá una última confirmación. Escribe `yes` y presiona Enter.
    > **Punto de no retorno:** Una vez que escribes los cambios, el antiguo mapa del disco se borra para siempre para dar paso a tu nuevo y eficiente diseño.
5. **Recoger las Herramientas (`Quit`):** Con los planos firmados y entregados, nuestro trabajo de diseño ha concluido. Selecciona `[ Quit ]` para salir.

**Paso Final: Supervisar el Trazado (`lsblk`)**
Un buen arquitecto siempre supervisa la obra. El comando `lsblk` nos mostrará el mapa del disco con las nuevas divisiones que acabamos de crear.
```
lsblk
```
Deberías ver claramente tu disco (`sdX`) con sus **tres particiones estructurales** (`sdX1`, `sdX2`, `sdX3`), listas para ser preparadas en el siguiente paso, donde desataremos el poder de Btrfs.

---

### **Paso 3: Preparar la Tierra (Formatear las Particiones)**

**El Cómo:**

1. **Formatear la partición EFI y SWAP:**
    ```
    mkfs.fat -F32 /dev/sdX1
    mkswap /dev/sdX2
    swapon /dev/sdX2
    ```
2. **Formatear la partición principal con Btrfs:**
    ```
    mkfs.btrfs /dev/sdX3
    ```
3. **Crear y Montar los Subvolúmenes (El Paso Clave):**
    - Primero, montamos temporalmente toda la partición Btrfs para poder trabajar en ella.
        ```
        mount /dev/sdX3 /mnt
        ```
    - Dentro, creamos nuestros subvolúmenes. El `@` es una convención común.
        ```
        btrfs subvolume create /mnt/@
        btrfs subvolume create /mnt/@home
        ```
    - Ahora desmontamos la partición principal para volver a montar los subvolúmenes de la forma correcta para la instalación.
        ```
        umount /mnt
        ```
    - Finalmente, montamos los subvolúmenes en sus destinos finales. Esta es la estructura que Arch Linux necesita.
        ```
        mount -o noatime,compress=zstd,subvol=@ /dev/sdX3 /mnt
        mkdir -p /mnt/home
        mount -o noatime,compress=zstd,subvol=@home /dev/sdX3 /mnt/home
        ```
    - No olvides montar la partición EFI también:
        ```
        mkdir -p /mnt/boot/efi
        mount /dev/sdX1 /mnt/boot/efi
        ```
---

### **Checkpoint de la Fase 2: Terreno Listo para Construir**

¡Misión cumplida! Ha sido un trabajo de precisión, pero el resultado es una base sólida para nuestra nueva casa.

- ✅ **Inspeccionamos** el terreno y elegimos la parcela correcta.
    
- ✅ **Dibujamos** los planos, creando una partición para cada necesidad.
    
- ✅ **Preparamos** la tierra, dándole a cada partición el formato adecuado.
    

Nuestro terreno ya no es una parcela vacía. Es un proyecto de construcción bien definido, con los cimientos listos para recibir la estructura principal.

**Ahora, es el momento de llamar al equipo de construcción en el [[Módulo 3 - Los Cimientos (Instalación del Sistema Base)]].**