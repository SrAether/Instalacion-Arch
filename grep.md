# grep - Buscar Texto en Archivos

El comando `grep` es tu **buscador de texto láser**. Es una herramienta extraordinariamente poderosa para encontrar líneas específicas de texto dentro de archivos, filtrar salidas de comandos y realizar búsquedas con patrones complejos usando expresiones regulares.

## **¿Qué es grep?**

`grep` (Global Regular Expression Print) te permite:
- **Buscar texto específico** dentro de archivos
- **Filtrar salidas** de otros comandos
- **Usar patrones complejos** con expresiones regulares
- **Procesar múltiples archivos** simultáneamente

## **¿Por qué es Esencial en Arch Linux?**

Durante la instalación y administración del sistema, `grep` es fundamental para buscar en archivos de configuración, filtrar logs, encontrar información específica y procesar salidas de comandos.

### **Sintaxis Básica**

```bash
# Estructura básica
grep "patrón" archivo

# Buscar en múltiples archivos
grep "patrón" archivo1 archivo2

# Buscar en todos los archivos de un directorio
grep "patrón" /etc/*
```

### **Búsquedas Básicas**

```bash
# Buscar palabra específica
grep "localhost" /etc/hosts

# Buscar sin distinguir mayúsculas/minúsculas
grep -i "arch" /etc/os-release

# Mostrar números de línea
grep -n "root" /etc/passwd

# Buscar palabra completa (no como parte de otra)
grep -w "root" /etc/passwd
```

### **Operadores de Búsqueda**

```bash
# Mostrar líneas que NO contienen el patrón
grep -v "comentario" archivo.conf

# Contar líneas que coinciden
grep -c "error" /var/log/messages

# Mostrar solo nombres de archivos que contienen el patrón
grep -l "systemd" /etc/systemd/*

# Mostrar solo el texto que coincide
grep -o "192\.168\.[0-9]*\.[0-9]*" /etc/hosts
```

## **Uso con Expresiones Regulares**

### **Patrones Básicos**

```bash
# Buscar líneas que empiecen con texto
grep "^root" /etc/passwd

# Buscar líneas que terminen con texto
grep "bash$" /etc/passwd

# Buscar cualquier carácter (.)
grep "r..t" /etc/passwd

# Buscar uno o más caracteres (+)
grep -E "lo+" /etc/hosts

# Buscar cero o más caracteres (*)
grep "http.*" /etc/services
```

### **Clases de Caracteres**

```bash
# Buscar números
grep "[0-9]" archivo.txt

# Buscar letras mayúsculas
grep "[A-Z]" archivo.txt

# Buscar direcciones IP
grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" /etc/hosts

# Buscar emails
grep -E "[a-zA-Z0-9]+@[a-zA-Z0-9]+\.[a-zA-Z]{2,}" archivo.txt
```

## **Contexto en Arch Linux**

### **Durante la Instalación**

```bash
# Verificar dispositivos de disco
lsblk | grep "disk"

# Buscar configuración UEFI
ls /sys/firmware/efi | grep efi

# Verificar conexión de red
ip a | grep "inet"
```

### **Configuración del Sistema**

```bash
# Buscar configuraciones específicas
grep -r "hostname" /etc/

# Verificar servicios habilitados
systemctl list-units | grep "enabled"

# Buscar en archivos de configuración
grep -n "Server" /etc/pacman.d/mirrorlist
```

### **Diagnóstico y Logs**

```bash
# Buscar errores en logs
grep "error" /var/log/Xorg.0.log

# Buscar en journal del sistema
journalctl | grep "failed"

# Buscar procesos específicos
ps aux | grep "firefox"
```

## **Opciones Avanzadas**

### **Búsqueda Recursiva**

```bash
# Buscar en todos los subdirectorios
grep -r "pattern" /etc/

# Buscar recursivamente con números de línea
grep -rn "systemd" /etc/systemd/

# Excluir tipos de archivo específicos
grep -r --exclude="*.log" "error" /var/

# Incluir solo tipos específicos
grep -r --include="*.conf" "server" /etc/
```

### **Contexto de Líneas**

```bash
# Mostrar 3 líneas antes y después del match
grep -C 3 "error" /var/log/messages

# Mostrar 5 líneas antes del match
grep -B 5 "Failed" /var/log/auth.log

# Mostrar 2 líneas después del match
grep -A 2 "Warning" /var/log/system.log
```

### **Múltiples Patrones**

```bash
# Buscar múltiples patrones (OR)
grep -E "error|warning|failed" /var/log/messages

# Usar archivo con patrones
echo -e "error\nwarning\nfailed" > patterns.txt
grep -f patterns.txt /var/log/messages

# Buscar múltiples patrones (AND) - usar pipes
grep "error" /var/log/messages | grep "kernel"
```

## **Integración con Otros Comandos**

### **Con [[find]]**

```bash
# Buscar archivos y luego contenido
find /etc -name "*.conf" -exec grep -l "localhost" {} \;

# Buscar y mostrar contexto
find /var/log -name "*.log" | xargs grep -n "error"
```

### **Con [[cat]] y [[less]]**

```bash
# Filtrar contenido antes de mostrar
cat /etc/services | grep -i "http"

# Usar con less para navegación
grep -r "config" /etc/ | less
```

### **Con comandos de sistema**

```bash
# Filtrar salida de ps
ps aux | grep -v "grep" | grep "python"

# Filtrar salida de netstat
netstat -tulpn | grep ":80"

# Filtrar interfaces de red
ip a | grep -E "inet.*192"
```

## **Casos de Uso Prácticos**

### **Administración del Sistema**

```bash
# Verificar configuración de red
grep -r "nameserver" /etc/

# Buscar usuarios del sistema
grep "/bin/bash" /etc/passwd

# Verificar servicios en funcionamiento
systemctl status | grep "active (running)"

# Buscar configuraciones modificadas
find /etc -name "*.conf" -exec grep -l "custom" {} \;
```

### **Debugging y Troubleshooting**

```bash
# Buscar errores recientes en logs
grep "$(date '+%b %d')" /var/log/messages | grep -i error

# Verificar fallos de autenticación
grep "authentication failure" /var/log/auth.log

# Buscar problemas de hardware
dmesg | grep -i "error\|fail\|warning"

# Verificar uso de memoria
free -h | grep "Mem:"
```

### **Desarrollo y Configuración**

```bash
# Buscar TODO y FIXME en código
grep -rn "TODO\|FIXME" /home/proyecto/

# Verificar configuraciones de servidor
grep -r "Listen\|ServerName" /etc/httpd/

# Buscar passwords hardcodeados (mala práctica)
grep -ri "password.*=" /home/proyecto/ --exclude-dir=.git
```

## **Optimización y Rendimiento**

### **grep vs otras herramientas**

```bash
# grep estándar (más lento con archivos grandes)
grep "pattern" archivo_grande.log

# ripgrep (rg) - alternativa moderna más rápida
rg "pattern" archivo_grande.log

# ag (the silver searcher) - alternativa rápida
ag "pattern" directorio/
```

### **Optimizar búsquedas**

```bash
# Usar grep con fixed strings (más rápido para texto literal)
grep -F "texto_exacto" archivo

# Limitar resultados
grep "pattern" archivo | head -10

# Usar con parallel para múltiples archivos
find . -name "*.log" | parallel grep "error" {}
```

## **Troubleshooting Común**

### **No encuentra texto que sí existe**

```bash
# Verificar encoding del archivo
file archivo.txt

# Usar grep con diferentes encodings
grep -a "pattern" archivo_binario

# Verificar caracteres invisibles
cat -A archivo.txt | grep "pattern"
```

### **Patrones con caracteres especiales**

```bash
# Escapar caracteres especiales
grep "\$HOME" archivo.sh

# Usar comillas para proteger patrones
grep '$HOME' archivo.sh

# Usar grep -F para texto literal
grep -F "$HOME" archivo.sh
```

### **Archivos muy grandes**

```bash
# Usar con limitaciones
grep -m 100 "pattern" archivo_enorme.log  # Solo primeros 100 matches

# Combinar con tail/head
tail -n 1000 archivo_enorme.log | grep "pattern"
```

## **Expresiones Regulares Avanzadas**

### **Patrones Comunes**

```bash
# Direcciones IP
grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" archivo

# URLs
grep -E "https?://[^\s]+" archivo

# Fechas (formato YYYY-MM-DD)
grep -E "[0-9]{4}-[0-9]{2}-[0-9]{2}" archivo

# Números de teléfono
grep -E "\([0-9]{3}\) [0-9]{3}-[0-9]{4}" archivo
```

### **Validaciones**

```bash
# Verificar formato de archivo de configuración
grep -E "^[a-zA-Z_][a-zA-Z0-9_]*\s*=" archivo.conf

# Buscar líneas que no son comentarios
grep -v "^#\|^$" archivo.conf

# Validar formato de email básico
grep -E "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$" emails.txt
```

## **Enlaces Relacionados**

- [[find]] - Buscar archivos que luego procesar con grep
- [[cat]] - Mostrar contenido que se puede filtrar con grep
- [[less]] - Navegar por resultados largos de grep
- [[sed]] - Edición de texto que complementa las búsquedas de grep
- [[awk]] - Procesamiento avanzado de texto junto con grep