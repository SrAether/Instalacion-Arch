# systemd-networkd - Configuración Permanente de Red

`systemd-networkd` es el **administrador de red integrado** en systemd, diseñado para configurar y gestionar conexiones de red de forma permanente y automática. Es como tener un mayordomo de red que mantiene tus conexiones organizadas y funcionando correctamente.

## **¿Qué es systemd-networkd?**

Es un servicio del sistema que:
- **Gestiona interfaces de red** automáticamente
- **Aplica configuraciones persistentes** que sobreviven a reinicios
- **Maneja DHCP y direcciones estáticas** de forma inteligente
- **Se integra perfectamente** con el ecosistema systemd

## **¿Por qué es Importante en Arch Linux?**

Durante la instalación de Arch Linux, necesitas que la red funcione de forma permanente después del reinicio. `systemd-networkd` es la solución moderna y robusta para esto.

### **Contexto en la Instalación**

```bash
# Durante la configuración del sistema
systemctl enable systemd-networkd
systemctl enable systemd-resolved
```

### **Configuración de Red Cableada**

```bash
# Crear configuración para ethernet
sudo nano /etc/systemd/network/20-ethernet.network

# Contenido típico:
[Match]
Name=enp*

[Network]
DHCP=yes
```

### **Configuración de Red Estática**

```bash
# Para IP fija
[Match]
Name=enp0s3

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
DNS=8.8.8.8
```

## **Comandos Relacionados**

```bash
# Ver estado del servicio
systemctl status systemd-networkd

# Reiniciar configuración de red
sudo systemctl restart systemd-networkd

# Ver configuraciones activas
networkctl status

# Listar interfaces gestionadas
networkctl list
```

## **Contexto en el Curso**

### **Durante la Configuración ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Habilitar servicios de red para el primer arranque
systemctl enable systemd-networkd
systemctl enable systemd-resolved
```

## **Diferencias con Otras Herramientas**

| Herramienta | Propósito | Momento de Uso |
|-------------|-----------|----------------|
| **[[iwctl]]** | Configuración Wi-Fi temporal | Durante instalación |
| **[[ping]]** | Verificar conectividad | Diagnóstico |
| **[[ip a]]** | Ver estado actual | Diagnóstico |
| **systemd-networkd** | Configuración permanente | Post-instalación |

## **Troubleshooting Común**

### **Red No Funciona Después del Reinicio**
```bash
# Verificar que el servicio esté activo
systemctl status systemd-networkd

# Si no está habilitado
sudo systemctl enable systemd-networkd
sudo systemctl start systemd-networkd
```

### **Conflictos con NetworkManager**
```bash
# Deshabilitar NetworkManager si usas systemd-networkd
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
```

## **Enlaces Relacionados**

- [[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]] - Configuración de red permanente
- [[ping]] - Verificar conectividad después de configurar
- [[ip a]] - Ver interfaces configuradas por systemd-networkd
- [[iwctl]] - Configuración temporal durante instalación