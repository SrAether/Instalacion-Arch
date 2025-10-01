# resolv.conf - Configuración de DNS

El archivo `/etc/resolv.conf` es el **centro de control de DNS** de tu sistema GNU/Linux. Es como la agenda telefónica de internet: convierte nombres como "archlinux.org" en direcciones IP que las computadoras pueden entender.

## **¿Qué es resolv.conf?**

Es un archivo de configuración que contiene:
- **Servidores DNS** que tu sistema consultará
- **Orden de búsqueda** para resolver nombres
- **Opciones de resolución** y timeouts
- **Dominios de búsqueda** locales

## **¿Por qué es Crucial en Arch Linux?**

Sin una configuración DNS correcta, tu sistema puede conectarse a direcciones IP directas pero no resolver nombres de dominio. Es fundamental para descargar paquetes y navegar por internet.

### **Ubicación y Estructura**

```bash
# Ver configuración actual
cat /etc/resolv.conf

# Estructura típica:
nameserver 8.8.8.8
nameserver 1.1.1.1
search localdomain
```

### **Configuración Manual Temporal**

```bash
# Editar directamente (temporal)
sudo nano /etc/resolv.conf

# Agregar servidores DNS confiables
nameserver 8.8.8.8      # Google DNS
nameserver 1.1.1.1      # Cloudflare DNS
nameserver 208.67.222.222  # OpenDNS
```

### **Configuración Permanente con systemd-resolved**

```bash
# Editar configuración systemd-resolved
sudo nano /etc/systemd/resolved.conf

# Contenido:
[Resolve]
DNS=8.8.8.8 1.1.1.1
FallbackDNS=208.67.222.222
```

## **Comandos de Diagnóstico**

```bash
# Ver configuración actual
cat /etc/resolv.conf

# Probar resolución DNS
nslookup archlinux.org

# Verificar que funciona
ping archlinux.org

# Ver estadísticas de resolv
systemd-resolve --status
```

## **Contexto en la Instalación**

### **Durante el Entorno en Vivo ([[Módulo 1 - El Taller Temporal (El entorno en vivo)]])**

```bash
# El entorno live ya tiene DNS configurado
cat /etc/resolv.conf
# nameserver 8.8.8.8
```

### **Después de la Instalación Base**

```bash
# Habilitar systemd-resolved para DNS automático
systemctl enable systemd-resolved
systemctl start systemd-resolved

# Crear enlace simbólico
sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

## **Problemas Comunes y Soluciones**

### **"No se puede resolver el nombre"**
```bash
# Verificar contenido
cat /etc/resolv.conf

# Si está vacío, agregar DNS temporal
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

### **DNS Lento**
```bash
# Cambiar a DNS más rápidos
sudo nano /etc/resolv.conf
# Usar: 1.1.1.1 (Cloudflare) o 8.8.8.8 (Google)
```

### **Configuración se Borra al Reiniciar**
```bash
# Usar systemd-resolved para persistencia
sudo systemctl enable systemd-resolved
```

## **Servidores DNS Recomendados**

| Proveedor | DNS Primario | DNS Secundario | Características |
|-----------|--------------|----------------|-----------------|
| **Cloudflare** | 1.1.1.1 | 1.0.0.1 | Rápido, privacidad |
| **Google** | 8.8.8.8 | 8.8.4.4 | Confiable, rápido |
| **OpenDNS** | 208.67.222.222 | 208.67.220.220 | Filtrado, seguridad |
| **Quad9** | 9.9.9.9 | 149.112.112.112 | Seguridad, privacidad |

## **Integración con Otras Herramientas**

### **Con systemd-networkd**
```bash
# En /etc/systemd/network/20-ethernet.network
[Network]
DHCP=yes
DNS=1.1.1.1 8.8.8.8
```

### **Con NetworkManager**
```bash
# Configurar DNS por interfaz
nmcli connection modify "Wired connection 1" ipv4.dns "1.1.1.1,8.8.8.8"
```

## **Enlaces Relacionados**

- [[ping]] - Verificar que la resolución DNS funciona
- [[systemd-networkd]] - Configuración moderna de red que gestiona DNS
- [[Módulo 1 - El Taller Temporal (El entorno en vivo)]] - DNS preconfigurado en el entorno live
- [[ip a]] - Ver interfaces que pueden necesitar configuración DNS