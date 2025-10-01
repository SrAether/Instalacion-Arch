Hemos dejado atrás el trabajo de planificación y topografía. Ahora, nos ponemos el casco de construcción, porque en esta fase llegan los materiales y la maquinaria pesada. Nuestro terreno, con sus divisiones bien marcadas, está a punto de recibir la estructura que lo convertirá en un hogar.

Este módulo se centra en tres acciones cruciales:

1. **Montar el Andamiaje (Montaje de Particiones):** Antes de verter el hormigón, un equipo de construcción monta andamios y establece un orden. Nosotros haremos lo mismo: conectaremos nuestras particiones preparadas a un directorio temporal (`/mnt`), creando el esqueleto sobre el cual se construirá el sistema.
    
2. **Llega la Grúa ([[pacstrap]]):** La maquinaria pesada entra en acción. El comando `pacstrap` es nuestra grúa de construcción, que se conectará a los almacenes de Arch Linux por internet, descargará los paquetes esenciales (el acero, el hormigón, las vigas maestras) y los instalará de forma ordenada en nuestro terreno.
    
3. **Registrar la Propiedad ([[genfstab]]):** Una vez que los cimientos están puestos, debemos hacerlo oficial. Crearemos el "registro de la propiedad" de nuestro sistema, un archivo que le indicará permanentemente dónde se encuentra cada habitación, para que nunca se pierda al arrancar.
    

---

### **Paso 1: Montar el Andamiaje (Montaje de las Particiones y Subvolúmenes)**

**El Porqué:** En este momento, nuestras particiones son como parcelas preparadas pero desconectadas. El proceso de "montaje" consiste en asignarlas a un punto dentro de una estructura de directorios temporal (`/mnt`) en nuestro sistema en vivo. Esto le dice al instalador: "Aquí, en `/mnt`, es donde quiero que construyas la raíz de mi nuevo sistema. El vestíbulo (`/boot/efi`) irá en esta subcarpeta, y las habitaciones (`/home`) irán en esta otra".

**El Cómo:** Los comandos que ejecutaste al final del Módulo 2 ya realizaron este paso crucial. No obstante, es fundamental entender qué hicimos y cómo verificarlo. Montamos los **subvolúmenes Btrfs** y la partición EFI en la estructura correcta.

1. **Verificar el Montaje:** Antes de continuar, ejecuta este comando para asegurarte de que tu andamio está bien montado.
    
    ```
    lsblk
    ```
    
    La salida debería mostrar claramente que `/dev/sdX3` (o tu partición Btrfs) está montada en `/mnt` y `/mnt/home`, y que `/dev/sdX1` (tu partición EFI) está montada en `/mnt/boot/efi`.
    

---

### **Paso 2: La Grúa en Acción (Instalar el Sistema Base con `pacstrap`)**

**El Porqué:** Con el andamio en su sitio, es hora de traer los materiales. `pacstrap` es el comando mágico que automatiza este proceso. Se conecta a los repositorios de Arch, descarga los paquetes fundamentales y los instala dentro de `/mnt`, colocando cada archivo en su lugar correcto como si siguiera un plano de ingeniería.

**El Cómo:** Este único comando instalará el corazón y el esqueleto de nuestro nuevo sistema.

```
pacstrap /mnt base linux linux-firmware base-devel nano networkmanager
```

Analicemos los "materiales" que estamos pidiendo:

- `base`: El esqueleto mínimo de un sistema GNU/Linux funcional.
    
- `linux`: El corazón del sistema operativo, el **kernel de Linux**.
    
- `linux-firmware`: Los "drivers" o controladores que permiten al kernel comunicarse con casi cualquier pieza de hardware.
    
- `base-devel`: Un conjunto de herramientas esenciales para construir software, algo fundamental en el ecosistema Arch.
    
- `nano`: Un editor de texto sencillo para la terminal. Nuestra primera herramienta manual para ajustes finos.
    
- `networkmanager`: El servicio que gestionará nuestras conexiones de red (Wi-Fi y cable) una vez que nos mudemos a nuestra nueva casa.
    

> 🏗️ **Paciencia, la grúa está trabajando:** Este proceso tardará unos minutos, ya que descargará varios cientos de megabytes de internet. Es el momento perfecto para tomar un café y admirar cómo los cimientos de tu sistema se construyen solos.

---

### **Paso 3: Registrar la Propiedad (Generar el `fstab`)**

**El Porqué:** Hemos montado las particiones de forma temporal para la instalación. Pero, ¿cómo sabrá el nuevo sistema dónde encontrar su vestíbulo, sus habitaciones o su bodega cada vez que arranque? Necesitamos crear un registro permanente. El archivo `fstab` (File System Table) es ese registro oficial.

El comando `genfstab` escanea la forma en que hemos montado nuestras particiones en `/mnt` y genera automáticamente este archivo de configuración.

**El Cómo:**

```
genfstab -U /mnt >> /mnt/etc/fstab
```

- `genfstab -U /mnt`: Le decimos que escanee `/mnt` y use identificadores únicos (UUIDs) para cada partición, lo cual es más robusto.
    
- `>> /mnt/etc/fstab`: El operador `>>` toma la salida del comando y la **añade** al final del archivo `fstab` dentro de nuestro nuevo sistema.
    

**Verificación Opcional:** Puedes echar un vistazo al registro de la propiedad recién creado con este comando:

```
cat /mnt/etc/fstab
```

Verás las líneas que le dicen a tu sistema cómo montar los subvolúmenes `@` y `@home`, la partición EFI y la SWAP en cada arranque.

---

### **Checkpoint de la Fase 3: ¡Tenemos Cimientos!**

¡Excelente trabajo! La parte más pesada de la construcción ha terminado.

- ✅ **Montamos** el andamiaje, creando la estructura para la instalación.
    
- ✅ **Instalamos** los cimientos con `pacstrap`, trayendo el corazón de Arch Linux a nuestro disco.
    
- ✅ **Registramos** la estructura con `genfstab`, asegurando que el sistema nunca olvide su propia distribución.
    

Nuestro proyecto ya no es solo un plano en un terreno vacío. Ahora es una estructura sólida, lista para que entremos a instalar el "cableado interno" en la siguiente fase.