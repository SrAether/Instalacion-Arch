Si las [[Particiones y sus Tipos|particiones]] son las habitaciones de nuestra casa digital, la **tabla de particiones** es el **plano maestro o el registro de la propiedad** del terreno. Es un pequeño y vital sector al inicio del disco duro que contiene la información fundamental sobre cómo está dividido el resto del espacio.

Sin este plano maestro, el firmware de tu computadora ([[UEFI]]) sería como un constructor llegando a un terreno sin indicaciones: no sabría dónde empieza el vestíbulo, dónde están los cimientos o dónde terminan las habitaciones. La tabla de particiones le dice exactamente cuántas particiones existen, de qué tamaño son y dónde comienza y termina cada una.

**GPT: El Estándar Arquitectónico Moderno**

En la guía, cuando usamos `cfdisk`, seleccionamos explícitamente `gpt`. **GPT (GUID Partition Table)** es el estándar moderno para estos planos maestros. Es el equivalente a un plano digital en CAD, superando al antiguo sistema MBR, que sería como un boceto hecho a mano.

Las ventajas de usar GPT son cruciales para los sistemas actuales:

- **Soporte para discos grandes:** Puede manejar discos de tamaños inmensos, mucho más allá de los límites del antiguo MBR.
    
- **Mayor número de particiones:** Te permite crear una gran cantidad de particiones, sin la vieja limitación de solo cuatro particiones primarias.
    
- **Mayor seguridad:** GPT guarda una copia de seguridad de sí mismo al final del disco. Si el plano maestro principal se daña, el sistema puede usar la copia para recuperar la estructura del disco.
    

Cuando `cfdisk` te pide que elijas una "etiqueta" (`label`), te está preguntando qué estándar de arquitectura quieres usar para tus planos. Al elegir `gpt`, te aseguras de que tu construcción sea moderna, robusta y compatible con [[UEFI]].