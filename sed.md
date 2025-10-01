# sed - Editor de Flujo de Texto

El comando `sed` (Stream Editor) es tu **cirujano de texto**. Es un editor no interactivo que permite realizar transformaciones complejas en archivos y flujos de texto de manera automática, perfecto para edición masiva y automatización.

## **¿Qué es sed?**

`sed` te permite:
- **Editar texto automáticamente** sin abrir editores interactivos
- **Buscar y reemplazar** patrones complejos
- **Insertar, eliminar y modificar** líneas específicas
- **Procesar flujos de datos** en tiempo real

## **¿Por qué es Fundamental en Arch Linux?**

Durante la administración del sistema, `sed` es indispensable para automatizar la edición de archivos de configuración, procesar logs, modificar scripts y realizar transformaciones masivas de texto.

### **Sintaxis Básica**

```bash
# Estructura básica
sed 'comando' archivo

# Procesar entrada estándar
comando | sed 'comando'

# Múltiples comandos
sed 'comando1; comando2' archivo

# Usar archivo de comandos
sed -f script.sed archivo
```

### **Comandos Fundamentales**

```bash
# s = substituir (buscar y reemplazar)
# d = eliminar líneas
# p = imprimir líneas
# i = insertar antes de línea
# a = agregar después de línea
# c = cambiar línea completa
```

## **Buscar y Reemplazar**

### **Sustituciones Básicas**

```bash
# Reemplazar primera ocurrencia en cada línea
sed 's/antiguo/nuevo/' archivo.txt

# Reemplazar todas las ocurrencias (global)
sed 's/antiguo/nuevo/g' archivo.txt

# Reemplazar solo en línea específica
sed '3s/antiguo/nuevo/' archivo.txt

# Reemplazar en rango de líneas
sed '1,5s/antiguo/nuevo/g' archivo.txt

# Guardar cambios en el archivo original
sed -i 's/antiguo/nuevo/g' archivo.txt
```

### **Sustituciones Avanzadas**

```bash
# Usar diferentes delimitadores
sed 's|/path/old|/path/new|g' archivo.txt
sed 's#http://old#https://new#g' archivo.txt

# Reemplazar con referencia al texto encontrado (&)
sed 's/palabra/[&]/' archivo.txt                # palabra -> [palabra]

# Usar grupos de captura
sed 's/\([0-9]*\)-\([0-9]*\)/\2-\1/' archivo.txt    # 123-456 -> 456-123

# Reemplazar solo si línea contiene patrón
sed '/patrón/s/old/new/g' archivo.txt
```

## **Casos de Uso en Arch Linux**

### **Configuración del Sistema**

```bash
# Cambiar hostname en configuración
sudo sed -i 's/localhost/mi-hostname/' /etc/hostname

# Modificar configuración de red
sudo sed -i 's/#DHCP=yes/DHCP=yes/' /etc/systemd/network/20-ethernet.network

# Activar repositorio en pacman.conf
sudo sed -i '/\[multilib\]/,/Include/s/^#//' /etc/pacman.conf

# Cambiar servidor DNS
sudo sed -i 's/nameserver .*/nameserver 8.8.8.8/' /etc/resolv.conf
```

### **Procesamiento de Logs**

```bash
# Extraer solo timestamp y mensaje de error
sed -n '/ERROR/s/.*\([0-9][0-9]:[0-9][0-9]:[0-9][0-9]\).*/\1/p' /var/log/system.log

# Limpiar logs eliminando líneas de debug
sed '/DEBUG/d' /var/log/application.log

# Convertir formato de fecha
sed 's/\([0-9][0-9]\)\/\([0-9][0-9]\)\/\([0-9][0-9][0-9][0-9]\)/\3-\1-\2/' logs.txt

# Enmascarar IPs en logs para privacidad
sed 's/\([0-9]\{1,3\}\.\)\{3\}[0-9]\{1,3\}/XXX.XXX.XXX.XXX/g' access.log
```

### **Automatización de Configuración**

```bash
# Backup y modificación segura
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/#Port 22/Port 2222/' /etc/ssh/sshd_config

# Configurar múltiples archivos
for config in /etc/systemd/system/*.service; do
    sudo sed -i 's/Type=simple/Type=forking/' "$config"
done

# Actualizar todas las referencias de path
find /etc -name "*.conf" -exec sudo sed -i 's|/old/path|/new/path|g' {} \;
```

## **Operaciones de Líneas**

### **Eliminar Líneas**

```bash
# Eliminar línea específica
sed '3d' archivo.txt

# Eliminar rango de líneas
sed '1,3d' archivo.txt

# Eliminar líneas que contienen patrón
sed '/patrón/d' archivo.txt

# Eliminar líneas vacías
sed '/^$/d' archivo.txt

# Eliminar comentarios
sed '/^#/d' archivo.conf
```

### **Insertar y Agregar Líneas**

```bash
# Insertar línea antes de la línea 3
sed '3i\Nueva línea insertada' archivo.txt

# Agregar línea después de la línea 3
sed '3a\Nueva línea agregada' archivo.txt

# Insertar línea antes de patrón
sed '/patrón/i\Nueva línea' archivo.txt

# Agregar múltiples líneas
sed '3a\Línea 1\nLínea 2\nLínea 3' archivo.txt
```

### **Modificar Líneas**

```bash
# Cambiar línea completa
sed '3c\Nueva línea de reemplazo' archivo.txt

# Cambiar líneas que contienen patrón
sed '/patrón/c\Línea de reemplazo' archivo.txt

# Cambiar múltiples líneas
sed '2,4c\Una sola línea reemplaza las tres' archivo.txt
```

## **Patrones y Direcciones**

### **Tipos de Direcciones**

```bash
# Número de línea
sed '5s/old/new/' archivo.txt

# Rango de líneas
sed '1,10s/old/new/' archivo.txt

# Desde línea hasta final
sed '5,$s/old/new/' archivo.txt

# Patrón regex
sed '/^START/,/^END/s/old/new/' archivo.txt

# Cada n líneas
sed '1~2s/old/new/' archivo.txt                # Líneas impares
```

### **Patrones Complejos**

```bash
# Líneas que NO contienen patrón
sed '/patrón/!d' archivo.txt

# Entre dos patrones específicos
sed '/BEGIN/,/END/p' archivo.txt

# Solo la primera coincidencia
sed '0,/patrón/s/old/new/' archivo.txt

# Última ocurrencia
sed '$s/old/new/' archivo.txt
```

## **Expresiones Regulares en sed**

### **Metacaracteres Básicos**

```bash
# ^ = inicio de línea
sed '/^root/d' /etc/passwd

# $ = final de línea
sed '/bash$/p' /etc/passwd

# . = cualquier carácter
sed 's/r.ot/ROOT/' archivo.txt

# * = cero o más del carácter anterior
sed 's/lo*g/LOG/' archivo.txt

# [abc] = cualquiera de los caracteres
sed 's/[0-9]/X/g' archivo.txt
```

### **Expresiones Regulares Extendidas**

```bash
# Usar -E para expresiones regulares extendidas
sed -E 's/[0-9]+/NUM/g' archivo.txt

# + = uno o más
sed -E 's/lo+g/LOG/' archivo.txt

# ? = cero o uno
sed -E 's/colou?r/COLOR/' archivo.txt

# | = alternativa (OR)
sed -E 's/(cat|dog)/animal/g' archivo.txt

# () = grupos
sed -E 's/([0-9]+)-([0-9]+)/\2-\1/' archivo.txt
```

## **Scripts sed Complejos**

### **Archivo de Comandos sed**

```bash
# Crear archivo script.sed
cat > script.sed << 'EOF'
# Limpiar y formatear log
/^$/d                           # Eliminar líneas vacías
/^#/d                           # Eliminar comentarios
s/ERROR/[ERROR]/g               # Formatear errores
s/\([0-9][0-9]:[0-9][0-9]\)/[\1]/g  # Formatear tiempo
EOF

# Usar el script
sed -f script.sed /var/log/application.log
```

### **Script de Configuración Automática**

```bash
#!/bin/bash
# Script para configurar SSH automáticamente

CONFIG_FILE="/etc/ssh/sshd_config"
BACKUP_FILE="${CONFIG_FILE}.backup.$(date +%Y%m%d)"

# Crear backup
sudo cp "$CONFIG_FILE" "$BACKUP_FILE"

# Configurar SSH con sed
sudo sed -i '
    s/#Port 22/Port 2222/
    s/#PermitRootLogin yes/PermitRootLogin no/
    s/#PasswordAuthentication yes/PasswordAuthentication no/
    s/#PubkeyAuthentication yes/PubkeyAuthentication yes/
    /^#ClientAliveInterval/a\ClientAliveInterval 300
    /^#ClientAliveCountMax/a\ClientAliveCountMax 2
' "$CONFIG_FILE"

echo "SSH configurado. Backup guardado en: $BACKUP_FILE"
```

## **Técnicas Avanzadas**

### **Hold Space y Pattern Space**

```bash
# Intercambiar líneas adyacentes
sed 'N;s/\(.*\)\n\(.*\)/\2\n\1/' archivo.txt

# Eliminar línea actual y siguiente
sed 'N;d' archivo.txt

# Mantener línea en hold space
sed 'h;d' archivo.txt                # h = copiar a hold space, d = eliminar

# Recuperar de hold space
sed 'g' archivo.txt                  # g = copiar de hold space a pattern space
```

### **Loops y Branching**

```bash
# Loop simple para procesar hasta encontrar patrón
sed ':a;N;/fin/!ba;s/\n/ /g' archivo.txt

# Branch condicional
sed '/patrón/b skip; s/old/new/; :skip' archivo.txt

# Test y branch
sed 's/old/new/;t;s/another/replacement/' archivo.txt
```

## **Integración con Otros Comandos**

### **Con [[grep]] y [[awk]]**

```bash
# Pipeline de procesamiento
grep "ERROR" /var/log/syslog | sed 's/.*ERROR: //' | awk '{ print "Error: " $0 }'

# Filtrar y editar
find /etc -name "*.conf" | xargs sed -n '/^[^#]/p' | grep "localhost"
```

### **Con [[find]] para edición masiva**

```bash
# Cambiar en todos los archivos de configuración
find /etc -name "*.conf" -exec sed -i 's/old_value/new_value/g' {} \;

# Backup antes de cambios masivos
find /home -name "*.txt" -exec sh -c 'cp "$1" "$1.bak" && sed -i "s/old/new/g" "$1"' _ {} \;
```

### **Con variables de shell**

```bash
# Usar variables en sed
USER="juan"
sed "s/usuario_placeholder/$USER/g" template.conf > user.conf

# Escapar variables complejas
PATH_VAR="/path/with/slashes"
sed "s|PATH_PLACEHOLDER|$PATH_VAR|g" archivo.txt
```

## **Casos de Uso Específicos**

### **Administración de Configuraciones**

```bash
# Activar módulos en configuración
sudo sed -i '/^#LoadModule rewrite_module/s/^#//' /etc/httpd/conf/httpd.conf

# Cambiar configuración de base de datos
sed -i "s/localhost/$DB_HOST/" /etc/myapp/database.conf

# Actualizar configuración de red
sudo sed -i '/^BOOTPROTO=/c\BOOTPROTO=static' /etc/sysconfig/network-scripts/ifcfg-eth0
```

### **Procesamiento de Datos**

```bash
# Convertir CSV a formato específico
sed 's/,/ | /g' datos.csv

# Limpiar datos exportados
sed 's/"//g; s/,$//' export.csv

# Formatear números
sed 's/\([0-9]\{3\}\)\([0-9]\{3\}\)/\1,\2/' numeros.txt    # 123456 -> 123,456
```

### **Generación de Código/Configuración**

```bash
# Generar configuración desde template
sed "s/{{HOSTNAME}}/$HOSTNAME/g; s/{{IP}}/$IP_ADDRESS/g" config.template > config.conf

# Crear múltiples archivos desde template
for user in juan maria pedro; do
    sed "s/USERNAME/$user/g" user.template > "/home/$user/.config"
done
```

## **Debugging y Optimización**

### **Debugging sed**

```bash
# Mostrar cada paso de procesamiento
sed -n 'l' archivo.txt                    # Mostrar caracteres especiales

# Imprimir número de línea
sed '=' archivo.txt

# Mostrar pattern space en cada paso
sed -n 'p' archivo.txt
```

### **Optimización**

```bash
# Para archivos grandes, usar rangos específicos
sed '1,1000s/old/new/g' archivo_grande.txt

# Combinar múltiples sustituciones
sed 's/old1/new1/g; s/old2/new2/g' archivo.txt

# Usar sed una sola vez en lugar de múltiples pipelines
sed 's/old/new/g; /unwanted/d; /pattern/a\New line' archivo.txt
```

## **Troubleshooting Común**

### **Problemas con Caracteres Especiales**

```bash
# Escapar caracteres especiales
sed 's/\$/DOLLAR/g' archivo.txt
sed 's/\*/ASTERISK/g' archivo.txt

# Usar diferentes delimitadores
sed 's|/path/to/old|/path/to/new|g' archivo.txt
```

### **Problemas con Encodings**

```bash
# Verificar encoding del archivo
file archivo.txt

# Manejar archivos con BOM
sed '1s/^\xEF\xBB\xBF//' archivo.txt
```

## **Enlaces Relacionados**

- [[awk]] - Procesador de texto que complementa sed para casos complejos
- [[grep]] - Buscar patrones antes de editar con sed
- [[find]] - Buscar archivos para editar en masa con sed
- [[cat]] - Mostrar archivos antes/después de editar con sed
- [[cp]] - Hacer backups antes de usar sed -i
- [[chmod]] - Ajustar permisos de archivos editados con sed