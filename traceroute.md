# traceroute - Rastrear Ruta de Red

`traceroute` es tu **detective de red**. Mientras que [[ping]] te dice si puedes llegar a un destino, `traceroute` te muestra exactamente **el camino** que toman tus paquetes de datos para llegar allí, revelando cada salto intermedio.

## **¿Qué es traceroute?**

Es una herramienta de diagnóstico que:
- **Mapea la ruta** que siguen los paquetes hasta un destino
- **Muestra cada router intermedio** (hop) en el camino
- **Mide el tiempo** que toma cada salto
- **Identifica dónde ocurren** problemas de red

## **¿Por qué es Útil en Arch Linux?**

Durante y después de la instalación, `traceroute` te ayuda a diagnosticar problemas de conectividad más complejos que un simple ping no puede revelar.

### **Instalación**

```bash
# En el sistema base, instalar traceroute
sudo pacman -S traceroute

# En algunos sistemas viene como tracepath
which tracepath
```

### **Uso Básico**

```bash
# Rastrear ruta a un sitio web
traceroute archlinux.org

# Rastrear ruta con IPv6
traceroute6 archlinux.org

# Mostrar direcciones IP numéricas (más rápido)
traceroute -n 8.8.8.8
```

### **Interpretando la Salida**

```bash
traceroute archlinux.org
 1  192.168.1.1 (192.168.1.1)  1.234 ms  1.123 ms  1.456 ms
 2  10.0.0.1 (10.0.0.1)       15.234 ms  14.123 ms  16.456 ms
 3  * * *
 4  172.16.1.1 (172.16.1.1)   25.234 ms  24.123 ms  26.456 ms
```

**Explicación:**
- **Columna 1**: Número de salto
- **Columna 2**: IP y nombre del router
- **Columnas 3-5**: Tiempo de respuesta (3 intentos)
- **\* \* \***: Router que no responde (normal en algunos casos)

## **Opciones Útiles**

```bash
# Usar UDP en lugar de ICMP (útil si ICMP está bloqueado)
traceroute -U archlinux.org

# Especificar puerto de destino
traceroute -p 80 archlinux.org

# Usar TCP (útil para diagnóstico web)
traceroute -T archlinux.org

# Limitar número máximo de saltos
traceroute -m 15 archlinux.org
```

## **Casos de Uso en Instalación de Arch**

### **Diagnóstico de Conectividad**

```bash
# Si ping funciona pero la descarga es lenta
ping -c 3 archlinux.org      # ✅ Responde
traceroute archlinux.org     # Muestra dónde está el cuello de botella
```

### **Verificar Ruta a Repositorios**

```bash
# Verificar conectividad a mirrors de Arch
traceroute mirror.rackspace.com
traceroute archlinux.c3sl.ufpr.br
```

### **Diagnóstico de DNS**

```bash
# Rastrear ruta a servidores DNS
traceroute 8.8.8.8          # Google DNS
traceroute 1.1.1.1          # Cloudflare DNS
```

## **Interpretación de Resultados**

### **Escenarios Comunes**

**1. Conectividad Local Problemática:**
```bash
traceroute 8.8.8.8
 1  * * *                    # Problema con gateway local
```

**2. Problema en ISP:**
```bash
traceroute archlinux.org
 1  192.168.1.1  1ms
 2  10.0.0.1     15ms
 3  * * *                    # ISP tiene problemas
 4  * * *
```

**3. Filtrado de Red:**
```bash
traceroute archlinux.org
 1-10  [normal]
11  * * *                    # Firewall bloqueando traceroute
```

## **Diferencias con Otros Comandos**

| Comando | Propósito | Información Que Proporciona |
|---------|-----------|------------------------------|
| **[[ping]]** | Verificar conectividad | ¿Puedo llegar al destino? |
| **traceroute** | Mapear ruta | ¿Qué camino toman mis paquetes? |
| **[[ip a]]** | Ver interfaces | ¿Qué interfaces tengo configuradas? |
| **[[netstat]]** | Ver conexiones | ¿Qué conexiones están activas? |

## **Troubleshooting con traceroute**

### **Timeouts Excesivos**
```bash
# Si hay muchos timeouts, usar TCP
traceroute -T -p 80 archlinux.org
```

### **Rutas Asimétricas**
```bash
# Comparar ruta de ida y vuelta
traceroute archlinux.org
# vs
traceroute archlinux.org -s [tu_ip_publica]
```

### **Problemas de MTU**
```bash
# Verificar con paquetes grandes
traceroute -F archlinux.org
```

## **Contexto en el Curso**

### **Durante Diagnóstico de Red ([[Módulo 1 - El Taller Temporal (El entorno en vivo)]])**

Útil cuando [[ping]] funciona pero hay problemas de conectividad intermitentes durante la descarga de paquetes.

### **Post-Instalación**

Excelente para diagnosticar problemas de red en el sistema instalado.

## **Enlaces Relacionados**

- [[ping]] - Verificación básica de conectividad antes de usar traceroute
- [[ip a]] - Ver interfaces de red que traceroute utilizará
- [[netstat]] - Ver conexiones activas para diagnóstico complementario
- [[resolv.conf]] - Configuración DNS que afecta la resolución de nombres en traceroute