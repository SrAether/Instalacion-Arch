El entorno `archiso` es un sistema Arch Linux completo y funcional que se carga y ejecuta íntegramente en la memoria RAM de tu computadora. No se instala nada en el disco duro; es un espacio de trabajo temporal y limpio, diseñado específicamente para preparar e instalar el sistema permanente.

---
### El Taller Temporal 🏗️
La mejor manera de entender `archiso` es a través de la analogía del "taller temporal" que se monta en un terreno antes de empezar una construcción. Al arrancar desde la USB de instalación de Arch, no estás modificando tu computadora todavía. En su lugar, estás desplegando este taller, que tiene todas las herramientas necesarias para el trabajo.

Este entorno vive exclusivamente en la memoria RAM, lo que implica dos cosas fundamentales:
1. **Es Temporal:** Cualquier cambio que hagas, como crear un archivo o instalar un programa dentro de `archiso`, desaparecerá en cuanto reinicies la computadora.
2. **Es un Lienzo en Blanco:** No depende de ningún sistema operativo previamente instalado en el disco duro. Te proporciona un entorno neutral y controlado desde el cual puedes trabajar sin interferencias.

Al entrar, te recibe una línea de comandos con el prompt `root@archiso ~ #`. Esto significa que eres el [[Usuario root]] (el administrador con control total) dentro del sistema temporal llamado **`archiso`**.

---
### Propósito y Herramientas 🛠️
El objetivo principal de `archiso` es proporcionar todas las herramientas necesarias para llevar a cabo la instalación. Es un entorno preconfigurado para que puedas:
- **Gestionar el Hardware:** Contiene los controladores y utilidades para reconocer el hardware de tu máquina.
- **Conectarte a Internet:** Incluye herramientas como `iwctl` para configurar redes Wi-Fi, lo cual es crucial para descargar los paquetes del sistema base.
- **Particionar y Formatear:** Proporciona utilidades esenciales como `cfdisk`, `mkfs.ext4` y `mkswap` para preparar el disco duro donde se instalará el sistema permanente.
- **Montar el Sistema de Archivos:** Te permite montar las particiones recién creadas para empezar a "construir" el nuevo sistema sobre ellas.
- **Instalar el Sistema Base:** La herramienta más importante, `pacstrap`, se ejecuta desde `archiso` para descargar e instalar el esqueleto de Arch Linux en el disco duro.

`Archiso` es el entorno de arranque en vivo que actúa como la base de operaciones para toda la instalación. Es el espacio seguro y equipado desde el cual, como un artesano, construyes y configuras tu sistema Arch Linux pieza por pieza.