Al iniciar, un disco duro es como un gran terreno diáfano: un espacio amplio pero sin estructura. Para poder construir y organizar nuestro sistema, primero debemos dividir ese terreno en parcelas funcionales. Este proceso se llama **particionar**, y cada una de estas parcelas es una **partición**.

Las particiones son divisiones lógicas que le dicen al sistema operativo cómo está distribuido el espacio de almacenamiento. En nuestra instalación, definimos un plano con propósitos claros para cada una:

- **Partición del Sistema EFI:** Es el **vestíbulo de entrada** de nuestro hogar digital. Se trata de una partición pequeña y obligatoria para los sistemas modernos que usan [[UEFI]]. No guarda nuestros archivos, sino las instrucciones que el ordenador necesita para saber cómo arrancar el sistema operativo.
    
- **Partición SWAP:** Funciona como una **bodega de emergencias**. Cuando la memoria principal (RAM) se llena, el sistema utiliza este espacio en el disco como una extensión temporal para evitar colapsar.
    
- **Partición Raíz (`/`):** Son los **cimientos y la estructura** de la casa. Aquí reside el sistema operativo, los programas y las configuraciones. Es el corazón funcional del sistema.
    
- **Partición Home (`/home`):** Son nuestras **habitaciones personales**. En ella se guardan todos nuestros documentos, descargas y configuraciones de usuario. Separarla de la raíz permite reinstalar el sistema sin perder nuestros archivos personales.