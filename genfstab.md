Una vez que hemos montado el andamiaje y la grúa de `pacstrap` ha instalado los cimientos en nuestro terreno `/mnt`, nos enfrentamos a una pregunta crucial: la estructura que hemos montado es **temporal**. Si reiniciáramos el ordenador ahora, el nuevo sistema se despertaría completamente desorientado, sin saber dónde está su vestíbulo de entrada (EFI), sus habitaciones (`/home`) o incluso sus propios cimientos (`/`).

Aquí es donde entra en juego `genfstab`. Si `pacstrap` es la grúa que construye, **`genfstab` es el notario que oficializa la construcción**. Su única y vital misión es crear el **"registro de la propiedad"** de nuestro sistema: un archivo de configuración llamado `/etc/fstab` (`fstab` significa _File System Table_ o Tabla del Sistema de Archivos).

Este archivo es una de las primeras cosas que el kernel de Linux lee al arrancar. Actúa como un mapa o una dirección postal permanente que le dice al sistema, sin lugar a dudas, cómo debe ensamblarse a sí mismo. Cada línea de este registro contiene una instrucción precisa, como:

> "La partición con la identificación única `XXXX` debe conectarse al directorio `/home`, usando el sistema de archivos `Btrfs` y con estas opciones específicas."

`genfstab` es una herramienta inteligente que inspecciona la forma en que hemos montado temporalmente nuestras particiones en `/mnt` y genera este archivo de forma automática y sin errores. Hacerlo a mano sería tedioso y arriesgado.

Al ejecutar el comando:

```
genfstab -U /mnt >> /mnt/etc/fstab
```

Le estamos ordenando al notario (`genfstab`):

- **Inspecciona** (`/mnt`): Revisa toda la estructura que hemos montado en nuestro sitio de construcción.
    
- **Sé preciso** (`-U`): Usa los identificadores únicos y permanentes (UUIDs) de cada partición, no sus nombres temporales como `/dev/sda1`, que podrían cambiar.
    
- **Inscríbelo en el registro** (`>> /mnt/etc/fstab`): Escribe tus hallazgos de forma permanente en el archivo `fstab` de nuestro nuevo sistema.