#Virtualizadores #Hipervisores 
VirtualBox es una solución de virtualización de código abierto desarrollada por Oracle. A diferencia de Hyper-V, es una herramienta multiplataforma, lo que significa que puedes usarla en Windows, macOS y Linux, convirtiéndola en una de las opciones más populares y versátiles para usuarios de todo el mundo.
**Ventajas principales**
- **Totalmente Gratuito y de Código Abierto:** No tiene ningún costo y su código puede ser revisado por la comunidad, lo que aporta transparencia.
- **Multiplataforma:** Funciona en los principales sistemas operativos, permitiéndote crear las mismas máquinas virtuales sin importar si usas Windows, macOS o Linux.
- **Facilidad de Uso:** Su interfaz gráfica es muy intuitiva, lo que la hace ideal para principiantes que se inician en el mundo de la virtualización.
- **Amplia Compatibilidad:** Soporta una enorme cantidad de sistemas operativos "invitados", desde las últimas versiones de Windows y Linux hasta sistemas más antiguos.
- **Funciones Adicionales Potentes:** Herramientas como las **"Guest Additions"** mejoran enormemente la experiencia, permitiendo un portapapeles compartido, arrastrar y soltar archivos, y un ajuste de resolución de pantalla automático.

---
### **Parte 1: Prerrequisitos**
Antes de instalar VirtualBox, es posible que necesites un componente clave de Windows en el que se apoya.
#### Instalar Microsoft Visual C++ 2019

VirtualBox requiere este paquete de librerías para funcionar. Si no lo tienes, la instalación fallará.

1. **Descarga el instalador:** Ve a la [página oficial de descargas de Microsoft Visual C++](https://learn.microsoft.com/es-es/cpp/windows/latest-supported-vc-redist) y descarga la versión que corresponda a tu sistema (casi siempre será **X64**). 
	![[Pasted image 20250830200422.png]]
    
2. **Ejecuta el instalador:** Abre el archivo descargado, acepta los términos de la licencia y haz clic en **"Instalar"**. Concede los permisos de administrador cuando te lo pida.
	![[Pasted image 20250830200820.png]]
	![[Captura de pantalla_20250830_202725.png]]
    
3. **Finaliza:** Una vez completada la instalación, cierra la ventana. ¡Ya estás listo para el siguiente paso! ![[Pasted image 20250830202859.png]]
### **Parte 2: Instalación de VirtualBox**

1. **Descargar VirtualBox:** Ve a la [página web oficial de VirtualBox](https://www.virtualbox.org/) y haz clic en el botón de descargas. Selecciona el paquete para tu sistema operativo (ej. "Windows Hosts"). 
	![[Pasted image 20250829130223.png]]
	![[Pasted image 20250829133233.png]]
    
2. **Iniciar la instalación:** Ejecuta el archivo descargado y concede los permisos de administrador para comenzar.
	![[Pasted image 20250829134531.png]]
	![[Pasted image 20250830195807.png]]
    
3. **Asistente de instalación:** Haz clic en **"Next"** para avanzar por las primeras ventanas del asistente. Te recomiendo dejar las opciones de configuración por defecto. 
	![[Pasted image 20250830203308.png]]
	Lee detenidamente la licencia de uso y si te parece adecuada acepta y continua con la instalación.
	![[Pasted image 20250830203418.png]]
	![[Pasted image 20250830203552.png]]
    
4. **Advertencia de Red:** El instalador te avisará que desconectará temporalmente tu red. Asegúrate de no tener descargas o procesos importantes en línea y haz clic en **"Yes"**. 
	![[Pasted image 20250830203822.png]]
    
5. **Advertencia de Dependencias:** Es posible que te avise sobre dependencias de Python.
    
    > 📝 **Nota:** Estas dependencias son para desarrolladores que quieran controlar VirtualBox con scripts. Para un uso normal, **no son necesarias**. Puedes hacer clic en **"Yes"** sin problemas. 
    ![[Pasted image 20250830204515.png]]
    
6. **Instalar:** Finalmente, haz clic en **"Install"** para comenzar la copia de archivos. 
	![[Pasted image 20250830204728.png]]
    
7. **Finalizar:** Una vez completado, deja marcada la casilla para iniciar VirtualBox y haz clic en **"Finish"**. 
	![[Pasted image 20250830204916.png]]



### **Parte 3: Creación de la máquina virtual (Arch Linux)**

1. **Descargar la ISO:** Para instalar un sistema operativo, necesitas su imagen de instalación. Descarga la #ISO de #Arch Linux desde su [página oficial](https://archlinux.org/download/).
2. **Crear nueva VM:** En la interfaz principal de VirtualBox, haz clic en el icono azul de **"Nueva"**. 
	![[Pasted image 20250830205316.png]]
    
3. **Configuración inicial:**
    - **Nombre:** Asígnale un nombre, por ejemplo, "Mi Arch Linux".
    - **Imagen ISO:** Haz clic en el icono de la carpeta para seleccionar el archivo ISO que acabas de descargar.
    
    > 📝 **Nota:** VirtualBox es inteligente. Al seleccionar la ISO o escribir "Arch" en el nombre, detecta el sistema operativo y aplica una **plantilla de configuraciones recomendadas**. No es que tenga una imagen preinstalada, sino que ajusta las opciones por ti. ![[Pasted image 20250830223503.png]]
    - Haz clic en **"Siguiente"**.
 4. **Asignación de Hardware:**
    
    - **Memoria RAM:** Se recomienda asignar la mitad de la RAM de tu equipo. Arch Linux funciona bien con **2 GB (2048 MB)**, pero si tienes recursos muy limitados, el mínimo absoluto sería 512 MB (no recomendado).
    - **Procesadores (CPUs):** Igualmente, la mitad de los núcleos de tu CPU es una buena regla. Arch puede funcionar con 1, pero **2 es lo ideal** para una mejor experiencia.
    - **Habilitar EFI:** Marca la casilla **"Use EFI"**.
    - **Disco Duro Virtual:** Define el tamaño del almacenamiento. Para este ejemplo, **30 GB** es suficiente.
    
    > 📝 **¿Qué es EFI?** Es el sistema moderno que reemplaza a la antigua BIOS para arrancar el ordenador. Es estándar en equipos actuales y necesario para muchos sistemas operativos modernos. ![[Pasted image 20250830224117.png]]
    - Haz clic en **"Siguiente"**        
5. **Resumen y Finalización:** Revisa el resumen de la configuración. Si todo está correcto, haz clic en **"Terminar"**. 
	![[Pasted image 20250830224154.png]]
    

### Parte 4: ¡A iniciar!
Tu máquina virtual ya está creada y aparece en la lista principal.
1. Selecciónala y haz clic en el botón verde **"Iniciar"**. ![[Pasted image 20250830224240.png]]
    
2. Se abrirá una nueva ventana que te mostrará la salida de video de tu VM, arrancando directamente desde la ISO de Arch Linux. 
	![[Pasted image 20250830224342.png]]
    ![[Pasted image 20250830224727.png]]

¡Listo! Has instalado VirtualBox y creado tu primera máquina virtual con éxito.****

