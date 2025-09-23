
Bienvenido a la primera fase de nuestra construcción. Antes de siquiera pensar en cavar los cimientos, cualquier buen equipo de construcción monta un taller temporal en el terreno. Este taller es un espacio controlado desde donde se planifica, se preparan las herramientas y se organiza todo el proyecto.

Nuestro **Entorno en Vivo** (o LiveCD) es exactamente eso. Al arrancar desde la USB de Arch Linux, no estamos instalando nada todavía. Estamos iniciando un sistema operativo completo que vive temporalmente en la memoria RAM de tu computadora. Es nuestro taller limpio y ordenado.

**Nuestro objetivo en esta fase es simple pero crucial:** preparar este taller para que el trabajo de construcción real se desarrolle sin problemas. Vamos a calibrar nuestras herramientas, asegurarnos de que la línea de suministro de materiales esté abierta y sincronizar los relojes de todo el equipo.

**Requisitos previos:**
- Haber creado una USB de arranque con la ISO oficial de Arch Linux.
- Haber configurado tu computadora (en la BIOS/UEFI) para que arranque desde esa USB.
---
### **Paso 1: Arrancar en el Entorno en Vivo**
Al iniciar tu computadora desde la USB, verás un menú de Arch Linux. Simplemente presiona Enter en la primera opción (Arch Linux install medium). Tras unos segundos de carga, te encontrarás frente a una pantalla negra con texto blanco que termina en:

```
root@archiso ~ #
```

¡Felicidades! Has entrado al taller. Eres el [[Usuario root]] (el administrador total) dentro de un sistema temporal [[Archiso]]. Desde aquí, daremos todas las órdenes.

---
### **Paso 2: Calibrar Nuestra Herramienta Principal (El Teclado)**

**El Porqué:** La herramienta que más usaremos es el teclado. Por defecto, el sistema arranca con una distribución en inglés (US). Si tu teclado físico es diferente, o si necesitas escribir caracteres en español, debemos configurarlo correctamente para evitar errores frustrantes.

**El Cómo: Identifica tu teclado y elige el comando correcto**
Primero, observa tu teclado físico y elige una de las siguientes opciones.

#### **Opción A: Tienes un teclado Físico en Español o Latinoamericano**

- **Identificación:** Tu teclado tiene una tecla dedicada para la Ñ.
- **Comando para teclado de España:**
  ```
    loadkeys es
    ```
- **Comando para teclado Latinoamericano** (usualmente con @ en AltGr + Q):
   ```
    loadkeys la-latin1
    ```
#### **Opción B: Tienes un teclado Físico en Inglés (US)**
- **Identificación:** Tu teclado NO tiene la tecla Ñ. La tecla Enter es un rectángulo horizontal.
- **Tu objetivo:** Escribir en español (acentos y ñ) de forma cómoda.
- **El Método Recomendado: "AltGr Directo"**  
    Esta es la mejor opción para la mayoría, especialmente si programas o usas mucho los símbolos ' y ". Tus teclas funcionarán de forma normal, y usarás la tecla AltGr (el Alt a la derecha de la barra espaciadora) para crear los caracteres especiales.
    - **Comportamiento:**
        - AltGr + n → ñ
        - AltGr + a → á
        - AltGr + e → é (y así con todas las vocales)
    - **Comando:**        
        ```
        loadkeys us-altgr-intl
        ```
    > **Nota:** Existe otro método (loadkeys us-intl) que usa "teclas muertas" (presionas ' y luego a para obtener á), pero puede ser incómodo para el uso diario de los símbolos. Nos quedaremos con el método AltGr por su comodidad y precisión.
**Verificación:**  
Este paso es fundamental. Escribe en la terminal y comprueba que todo funciona como esperas:
- Confirma que los símbolos básicos están en su lugar: /, _, :, -.
- **Si elegiste es o la-latin1:** Presiona la tecla Ñ y asegúrate de que aparece.
- **Si elegiste us-altgr-intl:**
    1. Prueba las combinaciones: AltGr + n (debe salir ñ), AltGr + a (debe salir á).
    2. **Muy importante:** Presiona la tecla del apóstrofo (') una sola vez. Debe aparecer un apóstrofo inmediatamente. Si no lo hace, cargaste una distribución incorrecta.
---

### **Paso 3: Verificar el Tipo de Permiso de Construcción (Modo de Arranque [[UEFI]])**

**El Porqué:** Las computadoras modernas usan un sistema de arranque llamado [[UEFI]]. Es fundamental asegurarnos de que nuestro entorno en vivo haya arrancado en este modo. Si arrancamos en el modo antiguo (Legacy/BIOS), el gestor de arranque que instalaremos en la Fase 5 no funcionará. Es como presentarse con los planos para un rascacielos cuando el permiso es para una cabaña.

**El Cómo:** Ejecuta el siguiente comando para listar el contenido de un directorio específico.
```
ls /sys/firmware/efi/efivars
```
**Verificación:**
- **Si el comando muestra una larga lista de archivos:** ¡Perfecto! Estás en modo UEFI. Puedes continuar.
- **Si el comando responde No such file or directory:** ¡Atención! No has arrancado en modo UEFI. Debes reiniciar, entrar en la configuración de la BIOS/UEFI de tu máquina y asegurarte de que esté configurada para arrancar en modo "UEFI" y no "Legacy" o "CSM".

---

### **Paso 4: Abrir la Línea de Suministros (Conexión a Internet)**

**El Porqué:** Nuestra construcción requiere materiales (paquetes de software). Necesitamos una conexión a internet estable para descargarlos desde los repositorios de Arch. Sin internet, nuestro equipo de construcción ([[Pacstrap]]) no podrá trabajar.
**Elige el método que se ajuste a tu situación:**
#### **Opción A: Conexión por Cable Ethernet (Hardware Físico)**
Si tu computadora está conectada directamente al router con un cable, lo más probable es que la conexión ya esté funcionando. Pasa directamente a la sección de **Verificación Universal**.

#### **Opción B: Conexión por Wi-Fi (Hardware Físico)**
Si usas una red inalámbrica, sigue estos pasos con la herramienta [[iwctl]]:
1. **Inicia la herramienta:** `iwctl` (Tu prompt cambiará a [iwd]#).
2. **Encuentra tu dispositivo:** `device lis`t (Busca el nombre, ej. wlan0).
3. **Escanea redes:** `station wlan0 scan` (reemplaza wlan0 si es necesario).
4. **Lista las redes:** `station wlan0 get-networks`.
5. **Conéctate:** `station wlan0 connect "NombreDeTuRed"` (Te pedirá la contraseña).
6. **Sal de la herramienta:** `exit`.

#### **Opción C: Si estás usando un Virtualizador (Hyper-V, VirtualBox, QEMU/KVM)**
Esta es la magia de la virtualización. Tu [[Virtualizadores Computadoras dentro de tu computadora|hipervisor]] actúa como un router para tu máquina virtual, compartiéndole la conexión a internet de tu computadora anfitriona.
- **El "Cómo":** ¡No necesitas hacer nada!
- **La Razón:** Durante la creación de la máquina virtual en las guías de [[Virtualización en windows con Hyper-V|Hyper-V]], [[Virtualización en windows con VirtualBox|VirtualBox]] o [[Virtualización en Linux con QEMU|QEMU/KVM]], ya configuramos la red (seleccionando opciones como **"Default Switch"** o **"NAT"**). Esto hace que la máquina virtual vea la conexión como si fuera un simple cable Ethernet conectado. Ya está todo listo.

---
#### **Verificación Universal (para cualquier método)**
No importa cómo te conectes, la prueba final es la misma. Usa el comando ping para confirmar que tienes salida al mundo:
```
ping archlinux.org
```

Si ves líneas de respuesta que dicen 64 bytes from..., ¡felicidades! Tu línea de suministros está abierta. Presiona Ctrl + C para detener el ping y continuar.

---

### **Paso 5: Sincronizar los Relojes del Equipo (Actualizar la Hora del Sistema)**
**El Porqué:** Es vital que la hora de nuestro sistema sea correcta. Muchas conexiones seguras (HTTPS), que usaremos para descargar paquetes, fallan si el reloj del sistema está desfasado. Sincronizar los relojes asegura que toda la comunicación sea fluida y segura.
**El Cómo:** Usaremos un comando para activar la sincronización automática de la hora a través de la red (NTP).
```
timedatectl set-ntp true
```
**Verificación:**  
Puedes revisar el estado del servicio para confirmar que está activo.
```
timedatectl status
```
Busca la línea NTP service: active. Si dice "active", los relojes están sincronizados.

---

### **Checkpoint de la Fase 1: Taller Listo para la Acción**
¡Excelente trabajo! Has completado la fase de preparación.
- ✅ **Arrancamos** en nuestro taller temporal.
- ✅ **Calibramos** el teclado.
- ✅ **Confirmamos** que tenemos el permiso de construcción correcto (UEFI).
- ✅ **Abrimos** la línea de suministros (internet).
- ✅ **Sincronizamos** los relojes.

Nuestro taller está impecable, las herramientas están listas y tenemos acceso a todos los materiales. Ahora que la preparación está completa, estamos listos para salir del taller y empezar a trabajar en el terreno real.

### Es hora de pasar al [[Módulo 2 - El Terreno y los Planos (Particionado y Formateo)]] 