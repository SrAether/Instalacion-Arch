Hemos completado la preparación en nuestro taller temporal. Ahora, nos ponemos el casco y salimos al terreno: nuestro disco duro. En este momento, es solo una parcela de tierra vacía y sin definir.
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
### **Paso 2: Dibujar los Planos (Crear las Particiones con cfdisk)**

**El Porqué:** Ahora dividiremos nuestro disco en las "habitaciones" esenciales que nuestra casa de Arch Linux necesitará. Usaremos cfdisk, una herramienta con una interfaz amigable que nos facilita el trabajo.

**Nuestras "Habitaciones" (Particiones Recomendadas):**

- **Partición EFI (/boot/efi):** La entrada principal. Un pequeño espacio requerido por los sistemas [[UEFI]] para saber cómo arrancar el sistema operativo.
    
- **Partición SWAP:** La bodega de emergencias. Es un espacio en el disco que el sistema usa como si fuera RAM extra cuando la memoria principal se llena.
    
- **Partición Raíz (/):** Los cimientos y la estructura. Aquí vivirá el sistema operativo, sus programas y configuraciones.
    
- **Partición Home (/home):** Nuestras habitaciones personales. Aquí se guardarán tus documentos, descargas, música y configuraciones de usuario. Separarla de la raíz es una excelente práctica: te permite reinstalar el sistema operativo (la estructura) sin perder tus pertenencias.
    

**El Cómo:**

1. **Inicia el arquitecto (cfdisk)** apuntando a tu disco:
    
    codeBash
    
    ```
    cfdisk /dev/sdX
    ```
    
2. **Elige la etiqueta de partición.** Se te preguntará qué tipo de tabla de particiones usar. Selecciona gpt, que es el estándar moderno para UEFI.
    
3. **Crea las particiones una por una.** Te encontrarás en la interfaz principal. Usa las flechas del teclado para navegar. El proceso es siempre el mismo:
    
    - Selecciona el Free space (espacio libre).
        
    - Elige [ New ] en el menú inferior.
        
    - Introduce el tamaño y presiona Enter. Ejemplos: 512M (para Megabytes), 8G (para Gigabytes).
        
    
    **Sigue este plano:**  
    a. **Partición EFI:**  
    * Tamaño: 512M  
    * Después de crearla, selecciónala, ve a [ Type ] en el menú y elige EFI System.
    
    b. **Partición SWAP:**  
    * Tamaño: Depende de tu RAM. Una buena regla es un tamaño igual a tu RAM (ej: 8G si tienes 8GB de RAM).  
    * Después de crearla, selecciónala, ve a [ Type ] y elige Linux swap.
    
    c. **Partición Raíz (/):**  
    * Tamaño: 30G es un tamaño inicial robusto.  
    * El tipo por defecto (Linux filesystem) es correcto, no necesitas cambiarlo.
    
    d. **Partición Home (/home):**  
    * Tamaño: Usa todo el espacio restante (simplemente presiona Enter cuando te pida el tamaño).  
    * El tipo (Linux filesystem) también es correcto.
    
4. **Guarda los Planos.** Una vez que las cuatro particiones estén definidas, selecciona [ Write ] en el menú. Te pedirá confirmación. Escribe yes y presiona Enter.
    
    > **Punto de no retorno:** Una vez que escribes los cambios, la antigua estructura del disco se borra para siempre.
    
5. **Sal del arquitecto.** Selecciona [ Quit ].
    

**Verificación:**  
Ejecuta lsblk para ver la nueva estructura. Deberías ver tu disco (sdX) con sus nuevas particiones (sdX1, sdX2, sdX3, sdX4).

codeBash

```
lsblk
```

---

### **Paso 3: Preparar la Tierra (Formatear las Particiones)**

**El Porqué:** Los planos están dibujados, pero cada habitación necesita un suelo adecuado. Formatear es darle a cada partición un "sistema de archivos" para que pueda almacenar información.

**El Cómo:** Ejecutaremos un comando específico para cada partición que creamos.

1. **Formatear la partición EFI (El suelo de la entrada):** Debe ser FAT32, el estándar que UEFI entiende.
    
    codeBash
    
    ```
    mkfs.fat -F32 /dev/sdX1
    ```
    
2. **Preparar y activar la SWAP (La bodega):**
    
    codeBash
    
    ```
    mkswap /dev/sdX2
    swapon /dev/sdX2
    ```
    
3. **Formatear la partición Raíz (Los cimientos):** Usaremos ext4, el sistema de archivos estándar y más confiable para Linux.
    
    codeBash
    
    ```
    mkfs.ext4 /dev/sdX3
    ```
    
4. **Formatear la partición Home (Nuestras habitaciones):** También usaremos ext4.
    
    codeBash
    
    ```
    mkfs.ext4 /dev/sdX4
    ```
    

---

### **Checkpoint de la Fase 2: Terreno Listo para Construir**

¡Misión cumplida! Ha sido un trabajo de precisión, pero el resultado es una base sólida para nuestra nueva casa.

- ✅ **Inspeccionamos** el terreno y elegimos la parcela correcta.
    
- ✅ **Dibujamos** los planos, creando una partición para cada necesidad.
    
- ✅ **Preparamos** la tierra, dándole a cada partición el formato adecuado.
    

Nuestro terreno ya no es una parcela vacía. Es un proyecto de construcción bien definido, con los cimientos listos para recibir la estructura principal.

**Ahora, es el momento de llamar al equipo de construcción en la [Fase 3: Los Cimientos (Instalación del Sistema Base)](https://www.google.com/url?sa=E&q=link%20a%20tu%20siguiente%20nota%20de%20obsidian).**