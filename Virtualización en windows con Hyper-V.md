#Virtualizadores #Hipervisores
Hyper-V es la solución de virtualización nativa de Microsoft.  Viene integrada de forma gratuita en las ediciones pro, enterprise y education de windows 11 y windows 10, lo que la convierte en una opción muy conveniente si ya dispones de una de estas versiones.
**Ventajas Principales**
* **Integración perfecta con Windows**: Al ser parte del sistema operativo, ofrece una integración profunda y un rendimiento optimizado, especialmente al virtualizar otros sistemas Windows.
* **Sin costo adicional**: Si tienes una versión compatible de Windows, no necesitas descargar ni pagar por software adicional.
* **Seguridad**: Se beneficia de la arquitectura de seguridad de Windows y permite funciones avanzadas como la seguridad  basada en virtualización (VBS).
* **Buen rendimiento**: ofrece un rendimiento muy competitivo, cercano al nativo cuando se configura correctamente.

---

### **Parte 1: Activación de Hyper-V**
Antes de poder usarlo, primero debes activar Hyper-V en tu sistema.
1. Presiona la tecla **Windows**, busca **"hyper"** y accede a la opción **"Activar o desactivar las características de Windows"**.
	![[Pasted image 20250827001243.png]]
2. En la ventana que aparece, busca la casilla de **Hyper-V** y márcala.
	![[Pasted image 20250827001412.png]]
3. Una vez marcada, haz clic en **Aceptar**.
	![[Pasted image 20250827001518.png]]
4. Espera a que Windows aplique los cambios y, cuando te lo pida, **reinicia el equipo**.
	![[Pasted image 20250827001626.png]]
	
	

---
### **Parte 2:  Creación de la Máquina Virtual**
Con Hyper-V activado, ya podemos crear nuestra primera máquina virtual.
5. Abre el menú de inicio, busca **"hyper"** de nuevo y ejecuta el **Administrador de Hyper-V**.
	![[Pasted image 20250827001740.png]]
6. En el panel izquierdo, verás el nombre de tu equipo (en este ejemplo, es "APATE"). Haz clic sobre él.
	![[Pasted image 20250827002112.png]]
	![[Pasted image 20250827002208.png]]
7. En el panel derecho de "Acciones", haz clic en **Nuevo** y luego en **Máquina virtual...**
	![[Pasted image 20250827002338.png]]
	![[Pasted image 20250827002824.png]]
8. Se abrirá el asistente de creación. Haz clic en **Siguiente**.
	![[Pasted image 20250827002924.png]]
9. **Asigna un nombre** a tu nueva máquina virtual (por ejemplo, "Soy una maquina virtual") y haz clic en **Siguiente**.
	![[Pasted image 20250827003220.png]]
10. **Especificar generación:** Deja seleccionada la **Generación 2**.
		**Nota:** La **Generación 2** es más moderna, usa firmware UEFI y es la recomendada para sistemas operativos de 64 bits actuales como Arch Linux. La Generación 1 se usa para compatibilidad con sistemas más antiguos de 32 bits. 
	![[Pasted image 20250827003342.png]]
11. **Asignar memoria:** Este es un punto crítico. Asigna la memoria RAM que usará tu VM (se mide en MB).
	* Una buena regla es **asignar la mitad de la RAM total** de tu equipo.
	* Para este ejemplo con Arch Linux, el mínimo es de 512 MB, pero **se recomienda asignar al menos 2 GB (2048 MB)** para un funcionamiento fluido.
	![[Pasted image 20250827003515.png]]
12. **Configurar funciones de red:** Es muy importante cambiar la conexión. Despliega el menú y selecciona **"Default Switch"**. Esto creará una red virtual que comparte la conexión a internet de tu computadora de forma sencilla.
	![[Pasted image 20250827004123.png]]
	![[Pasted image 20250827004259.png]]
13. **Conectar disco duro virtual:** Aquí defines el tamaño del almacenamiento. **Recomiendo dar al menos 30 GB**.
	![[Pasted image 20250827004614.png]]
14. **Opciones de instalación:** En este paso, le diremos a la VM desde dónde instalar el sistema operativo.
		**¿Qué es un archivo ISO?** Es una copia digital exacta de un disco (como un CD o DVD). En este caso, contiene el sistema operativo completo que vamos a instalar. Para este ejemplo, usaremos una #ISO de #Arch Linux, que se puede descargar desde su [página oficial](https://archlinux.org/download/).
	 - Selecciona la opción **"Instalar un sistema operativo desde un CD/DVD-ROM de arranque"**.
	 - Luego, elige **"Archivo de imagen (.iso)"** y haz clic en **"Examinar..."** para buscar y seleccionar el archivo ISO que descargaste.*
	![[Pasted image 20250827004724.png]]
	![[Pasted image 20250827004905.png]]
	![[Pasted image 20250827005005.png]]
	![[Pasted image 20250827005056.png]]
15. **Resumen:** Revisa que toda la configuración sea correcta y haz clic en **"Finalizar"**.
	![[Pasted image 20250827005137.png]]
	Se crea la maquina virtual
	![[Pasted image 20250827005218.png]]
	¡Listo! La máquina virtual ha sido creada y aparecerá en el panel central del Administrador de Hyper-V.
	![[Pasted image 20250827005247.png]]

---
### **Parte 3: Configuración Final e Inicio**
Antes de encenderla, necesitamos hacer un ajuste final importante para sistemas operativos como Linux.
#DESACTIVAR_ARRANQUE_SEGURO
16. En el listado de máquinas virtuales, haz clic derecho sobre la que acabas de crear y selecciona "Configuración...".
	![[Pasted image 20250827234733.png]]
17. En la nueva ventana, ve a la sección de **"Seguridad"**.
	![[Pasted image 20250827011300.png]]
18. **Desmarca** la casilla que dice **"Habilitar Arranque seguro"**.
		**¿Por qué hacemos esto?** El Arranque seguro (Secure Boot) verifica que solo se inicie software de confianza. Muchas distribuciones de Linux no son compatibles por defecto, por lo que debemos desactivarlo para que puedan arrancar desde la ISO. 
	![[Pasted image 20250827011454.png]]
	Haz clic en **Aceptar** para guardar los cambios.
	**Iniciar la maquina virtual**
19. Ahora sí, haz doble clic sobre tu VM para abrir la ventana de conexión.
	![[Pasted image 20250827235217.png]]
20. Haz clic en el botón **"Iniciar"**.
	![[Pasted image 20250827005457.png]]
	¡Felicidades! Tu máquina virtual con Arch Linux está funcionando dentro de Hyper-V.
	![[Pasted image 20250827005545.png]]
	![[Pasted image 20250827010647.png]]
	![[Pasted image 20250827010730.png]]![[Pasted image 20250827010946.png]]