No todo el mobiliario es igual. Mientras que [[ext4]] es como una estantería de madera clásica (robusta y fiable), `Btrfs` (_B-Tree File System_) es un sistema de almacenamiento modular e inteligente, diseñado para la flexibilidad y la seguridad.

Sus características clave son:
- **Copy-on-Write (CoW):** `Btrfs` nunca modifica los datos "en su sitio". Cuando se cambia un archivo, primero hace una copia en un nuevo lugar, realiza la modificación y, solo si tiene éxito, actualiza los punteros. Esto lo hace increíblemente resistente a la corrupción de datos por fallos de energía.
- **Instantáneas (Snapshots):** Permite tomar una "fotografía" del estado de tus archivos en un instante, casi sin ocupar espacio adicional. Si una actualización rompe el sistema, puedes volver al estado exacto de antes de la actualización en segundos. Es una red de seguridad potentísima.
- **Subvolúmenes:** Esta es su característica más flexible. Imagina que tienes una única y gran partición Btrfs (una gran nave industrial). En lugar de construir muros físicos (particiones rígidas), puedes usar cintas y señales para crear "zonas de trabajo" (subvolúmenes) para la raíz (`/`) y para el home (`/home`). Parecen separadas, pero comparten el mismo espacio flexible, permitiendo que una zona crezca si la otra no usa todo su espacio.
- **Integridad de Datos:** `Btrfs` utiliza sumas de verificación (_checksums_) para los datos y los metadatos. Esto significa que puede detectar si un archivo se ha corrompido silenciosamente (por ejemplo, por un fallo del disco) y, en configuraciones de múltiples discos, incluso repararlo.

---
### Enlaces Relacionados
- [[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]] - Uso de Btrfs en la instalación
- [[Sistema de archivos]] - Conceptos generales sobre sistemas de archivos