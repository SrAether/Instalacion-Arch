# netstat - Ver Conexiones de Red

`netstat` es tu **monitor de tráfico de red**. Es como tener una ventana al centro de comunicaciones de tu sistema, mostrándote todas las conexiones activas, puertos abiertos y estadísticas de red en tiempo real.

## **¿Qué es netstat?**

Es una herramienta de diagnóstico que te permite:
- **Ver conexiones activas** TCP y UDP
- **Listar puertos abiertos** y servicios escuchando
- **Mostrar estadísticas** de interfaces de red
- **Identificar procesos** que usan conexiones específicas

## **¿Por qué es Importante en Arch Linux?**

Durante y después de la instalación, `netstat` te ayuda a verificar que los servicios de red estén funcionando correctamente y diagnosticar problemas de conectividad.

### **Instalación**

```bash
# netstat viene incluido en net-tools
sudo pacman -S net-tools

# Alternativa moderna (recomendada)
# ss viene preinstalado y es más rápido
```

### **Comandos Básicos**

```bash
# Ver todas las conexiones
netstat -a

# Ver solo conexiones TCP
netstat -t

# Ver solo conexiones UDP  
netstat -u

# Mostrar números de puerto (no resolver nombres)
netstat -n

# Mostrar procesos asociados
netstat -p
```

### **Combinaciones Útiles**

```bash
# Ver conexiones activas con procesos
netstat -tulpn

# Explicación de flags:
# -t: TCP
# -u: UDP  
# -l: Solo servicios escuchando
# -p: Mostrar PID/nombre del proceso
# -n: Mostrar números de puerto
```

## **Interpretando la Salida**

```bash
netstat -tulpn
Proto Recv-Q Send-Q Local Address    Foreign Address   State    PID/Program
tcp   0      0      0.0.0.0:22       0.0.0.0:*         LISTEN   1234/sshd
tcp   0      0      127.0.0.1:631    0.0.0.0:*         LISTEN   5678/cupsd
udp   0      0      0.0.0.0:68       0.0.0.0:*                  910/dhcpcd
```

**Explicación:**
- **Proto**: Protocolo (tcp/udp)
- **Local Address**: IP:Puerto local
- **Foreign Address**: IP:Puerto remoto
- **State**: Estado de la conexión
- **PID/Program**: Proceso responsable

## **Casos de Uso Durante Instalación**

### **Verificar Servicios de Red**

```bash
# Ver si DHCP está funcionando
netstat -tulpn | grep :68

# Verificar SSH (si lo instalaste)
netstat -tulpn | grep :22

# Ver conexiones de pacman durante descarga
netstat -tn | grep :80
netstat -tn | grep :443
```

### **Diagnóstico de Conectividad**

```bash
# Ver conexiones establecidas
netstat -tn | grep ESTABLISHED

# Verificar si hay servicios escuchando
netstat -tln
```

## **Estados de Conexión TCP**

| Estado | Significado |
|--------|-------------|
| **LISTEN** | Servicio esperando conexiones |
| **ESTABLISHED** | Conexión activa |
| **TIME_WAIT** | Conexión terminando (normal) |
| **SYN_SENT** | Intentando conectar |
| **CLOSE_WAIT** | Esperando cierre de aplicación |

## **Comparación con Herramientas Modernas**

### **netstat vs ss (recomendado)**

```bash
# netstat tradicional
netstat -tulpn

# ss moderno (más rápido)
ss -tulpn

# Ventajas de ss:
# - Más rápido con muchas conexiones
# - Mejor filtrado
# - Información más detallada
```

### **Ejemplos con ss**

```bash
# Ver solo conexiones HTTP/HTTPS
ss -tn dst :80 or dst :443

# Ver conexiones por proceso específico
ss -tlpn | grep sshd

# Estadísticas resumidas
ss -s
```

## **Diagnóstico de Problemas Comunes**

### **Puerto Ya en Uso**
```bash
# Encontrar qué está usando el puerto 80
netstat -tlpn | grep :80
# o
ss -tlpn | grep :80
```

### **Conexiones Colgadas**
```bash
# Ver conexiones en TIME_WAIT (normal después de cerrar)
netstat -tn | grep TIME_WAIT

# Si hay demasiadas, puede indicar problema
```

### **Verificar Conectividad a Internet**
```bash
# Ver conexiones salientes activas
netstat -tn | grep :80   # HTTP
netstat -tn | grep :443  # HTTPS
```

## **Contexto en el Curso**

### **Durante la Instalación ([[Módulo 1 - El Taller Temporal (El entorno en vivo)]])**

```bash
# Verificar que DHCP obtuvo IP correctamente
netstat -rn  # Ver tabla de enrutamiento
```

### **Post-Instalación**

```bash
# Verificar servicios habilitados
netstat -tlpn | grep LISTEN
```

## **Opciones Adicionales**

```bash
# Ver estadísticas de interfaces
netstat -i

# Ver tabla de enrutamiento
netstat -rn

# Estadísticas de protocolos
netstat -s

# Solo IPv6
netstat -6
```

## **Seguridad y Monitoreo**

### **Detectar Conexiones Sospechosas**
```bash
# Ver todas las conexiones establecidas
netstat -tn | grep ESTABLISHED

# Buscar conexiones a puertos inusuales
netstat -tn | grep -v ':80\|:443\|:22'
```

### **Monitoreo Continuo**
```bash
# Actualizar cada 2 segundos
watch -n 2 'netstat -tn'

# O usar ss con watch
watch -n 2 'ss -tn'
```

## **Enlaces Relacionados**

- [[ping]] - Verificar conectividad básica antes de analizar conexiones
- [[ip a]] - Ver interfaces que aparecen en netstat
- [[traceroute]] - Rastrear problemas de ruta que netstat puede revelar
- [[systemd-networkd]] - Servicios de red que netstat puede mostrar