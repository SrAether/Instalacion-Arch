Dentro del **[[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]]**, una vez que estamos sobre el terreno (nuestro disco duro), necesitamos una herramienta para trazar los planos directamente sobre él. Esa herramienta es `cfdisk`.

Piénsalo como el acto de usar estacas y cuerdas para dibujar el contorno exacto de cada habitación antes de empezar a cavar. `cfdisk` es un editor de particiones interactivo que nos permite hacer precisamente eso: dividir el espacio total del disco en las parcelas funcionales que necesita nuestro sistema. Con él, definimos los linderos para la zona de acceso (la partición del sistema EFI para el `[[UEFI]]`), la reserva de recursos (la partición `swap`), la parcela para los cimientos del sistema (la raíz `/`) y el área para nuestras pertenencias (el `/home`).

---
### Enlaces Relacionados
- [[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]] - Guía completa de particionado
- [[UEFI]] - Sistema de arranque moderno que requiere partición EFI
- [[Tabla de particones]] - Conceptos sobre tablas de particiones GPT

Su gran ventaja es que, aunque funciona en la terminal, nos presenta un mapa visual de cómo está quedando el terreno. Es una herramienta segura, ya que no realiza ningún cambio permanente hasta que revisamos el plano y le damos la orden explícita de "Escribir" (_Write_). Esto nos permite verificar que las divisiones son correctas antes de que sean definitivas.

Una vez que `cfdisk` ha terminado su labor y las divisiones están marcadas, el terreno queda listo para el siguiente equipo: las herramientas `mkfs` y `mkswap`, que se encargarán de preparar y acondicionar cada una de estas parcelas (formatearlas) para que la grúa de `[[pacstrap]]` pueda, finalmente, comenzar a levantar la estructura.