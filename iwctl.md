Es la herramienta interactiva que se utiliza dentro del entorno de instalación de Arch Linux para conectarse a una red Wi-Fi. Piénsalo como el walkie-talkie con el que contactas a tus proveedores para abrir la línea de suministros (internet) y poder descargar todos los materiales (paquetes) necesarios para tu construcción.

---
### Conectando a la Red Paso a Paso 📡
El proceso es una conversación directa con tu hardware de red. Aquí te guiaré a través de cada comando que necesitas.
1. **Iniciar la Herramienta** Primero, debes entrar en el modo interactivo de `iwctl`. Al hacerlo, tu _prompt_ (la línea donde escribes) cambiará para indicar que estás dentro de la herramienta.
    ```
    iwctl
    ```
2. **Identificar tu Tarjeta Wi-Fi** Necesitas saber el nombre que el sistema le ha dado a tu dispositivo Wi-Fi. Generalmente es algo como `wlan0`.
    ```
    device list
    ```
3. **Escanear en Busca de Redes** Ahora, le pides a tu tarjeta que busque todas las redes Wi-Fi a su alcance. Recuerda reemplazar `wlan0` con el nombre de tu dispositivo si es diferente.
	```
    station wlan0 scan
    ```
4. **Listar las Redes Encontradas** Este comando te mostrará una lista de todas las redes que el escaneo anterior detectó.
    ```
    station wlan0 get-networks
    ```
5. **Conectarse a tu Red** El paso final es darle la orden de conexión, especificando el nombre exacto de tu red entre comillas. El sistema te pedirá la contraseña; escríbela y presiona `Enter`. No verás los caracteres por seguridad.
    ```
    station wlan0 connect "NombreDeTuRed"
    ```
6. **Salir de la Herramienta** Una vez conectado, puedes salir del modo interactivo para volver a la terminal principal.
    ```
    exit
    ```
    Finalmente, es crucial verificar que la conexión fue exitosa. La mejor forma es hacer `ping` a un servidor confiable. Si recibes respuesta, ¡la línea de suministros está abierta!.
    ```
    ping archlinux.org
    ```
    Presiona `Ctrl + C` para detener el ping.
---

### Ejemplo Práctico: Conectando a la Red "MiWifi"

Imaginemos que tu tarjeta es `wlan0` y tu red se llama **MiWifi**. La secuencia completa se vería así:

```
# Entramos a la herramienta
root@archiso ~ # iwctl

# El prompt cambia. Listamos los dispositivos y vemos 'wlan0'
[iwd]# device list

# Le pedimos a 'wlan0' que busque redes
[iwd]# station wlan0 scan

# Vemos la lista y encontramos "MiWifi"
[iwd]# station wlan0 get-networks

# Nos conectamos a ella
[iwd]# station wlan0 connect "MiWifi"
Type the passphrase for "MiWifi": *********

# Salimos de la herramienta
[iwd]# exit

# Volvemos al prompt normal y verificamos la conexión
root@archiso ~ # ping archlinux.org
PING archlinux.org (95.217.163.246) 56(84) bytes of data.
64 bytes from archlinux.org (95.217.163.246): icmp_seq=1 ttl=55 time=23.5 ms
...
```