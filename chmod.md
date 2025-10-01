# chmod - Cambiar Permisos de Archivos

El comando `chmod` (Change Mode) es tu **gestor de permisos**. Te permite controlar quién puede leer, escribir o ejecutar archivos y directorios, estableciendo las reglas de acceso que protegen tu sistema y organizan el flujo de trabajo.

## **¿Qué es chmod?**

`chmod` te permite modificar:
- **Permisos de lectura** (read) - r
- **Permisos de escritura** (write) - w  
- **Permisos de ejecución** (execute) - x
- **Para tres categorías**: propietario, grupo, otros

## **¿Por qué es Fundamental en Arch Linux?**

Los permisos correctos son esenciales para la seguridad del sistema. Durante la configuración post-instalación, necesitarás ajustar permisos de scripts, archivos de configuración y directorios de usuario.

### **Entendiendo los Permisos**

```bash
# Ver permisos actuales
ls -la archivo.txt
# -rw-r--r-- 1 usuario grupo 1234 Oct 30 12:00 archivo.txt

# Desglose: -rw-r--r--
# - = tipo de archivo (- archivo, d directorio, l enlace)
# rw- = permisos del propietario (lectura, escritura, sin ejecución)
# r-- = permisos del grupo (solo lectura)  
# r-- = permisos de otros (solo lectura)
```

### **Sistema Numérico (Octal)**

```bash
# Valores numéricos de permisos:
# 4 = lectura (r)
# 2 = escritura (w)
# 1 = ejecución (x)

# Combinaciones comunes:
# 7 = 4+2+1 = rwx (todos los permisos)
# 6 = 4+2   = rw- (lectura y escritura)
# 5 = 4+1   = r-x (lectura y ejecución)
# 4 = 4     = r-- (solo lectura)
# 0 = 0     = --- (sin permisos)
```

### **Cambios Básicos con Números**

```bash
# Dar todos los permisos al propietario, lectura al grupo y otros
chmod 744 archivo.txt

# Permisos típicos para archivos de configuración
chmod 644 ~/.bashrc

# Hacer ejecutable un script
chmod 755 script.sh

# Permisos muy restrictivos (solo propietario)
chmod 600 archivo_secreto.txt
```

## **Sistema Simbólico**

### **Sintaxis Simbólica**

```bash
# Estructura: chmod [ugoa][+-=][rwx] archivo
# u = usuario (propietario)
# g = grupo  
# o = otros
# a = todos (all)

# + = agregar permiso
# - = quitar permiso
# = = establecer permiso exacto
```

### **Ejemplos Simbólicos**

```bash
# Agregar ejecución para el propietario
chmod u+x script.sh

# Quitar escritura para grupo y otros
chmod go-w archivo.txt

# Dar lectura y ejecución a todos
chmod a+rx directorio/

# Establecer exactamente lectura y escritura para propietario
chmod u=rw archivo.txt

# Combinaciones múltiples
chmod u+x,g+r,o-w archivo.txt
```

## **Casos de Uso en Arch Linux**

### **Scripts y Ejecutables**

```bash
# Hacer ejecutable un script recién creado
chmod +x mi_script.sh

# Permisos típicos para binarios del sistema
chmod 755 /usr/local/bin/mi_programa

# Script solo para el propietario
chmod 700 script_personal.sh
```

### **Archivos de Configuración**

```bash
# Configuraciones del usuario (lectura/escritura propietario, lectura grupo)
chmod 644 ~/.bashrc
chmod 644 ~/.vimrc

# Archivos sensibles (solo propietario)
chmod 600 ~/.ssh/id_rsa
chmod 600 ~/.netrc

# Configuraciones del sistema
chmod 644 /etc/hosts
chmod 755 /etc/init.d/mi_servicio
```

### **Directorios**

```bash
# Directorio personal estándar
chmod 755 /home/usuario

# Directorio privado
chmod 700 /home/usuario/privado

# Directorio compartido de grupo
chmod 775 /shared/grupo

# Directorio público de lectura
chmod 755 /var/www/html
```

## **Permisos Especiales**

### **Sticky Bit, SUID, SGID**

```bash
# Sticky bit en directorio (solo propietario puede eliminar)
chmod 1755 /tmp
chmod +t directorio_compartido/

# SUID - ejecutar con permisos del propietario del archivo
chmod 4755 programa
chmod u+s programa

# SGID - ejecutar con permisos del grupo del archivo  
chmod 2755 programa
chmod g+s programa

# Combinación de permisos especiales
chmod 6755 programa  # SUID + SGID
```

### **Verificar Permisos Especiales**

```bash
# Ver permisos especiales
ls -la /usr/bin/sudo
# -rwsr-xr-x (la 's' indica SUID)

ls -la /tmp/
# drwxrwxrwt (la 't' indica sticky bit)
```

## **Operaciones Recursivas**

### **Cambiar Permisos en Subdirectorios**

```bash
# Aplicar permisos recursivamente
chmod -R 755 directorio/

# Permisos diferentes para archivos y directorios
find directorio/ -type f -exec chmod 644 {} \;
find directorio/ -type d -exec chmod 755 {} \;

# Usando chmod con find para mayor control
find /var/www -name "*.php" -exec chmod 644 {} \;
find /var/www -name "*.sh" -exec chmod 755 {} \;
```

## **Contexto en el Curso**

### **Durante la Configuración ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Configurar permisos de usuario después de la instalación
chmod 755 /home/usuario
chmod 644 /home/usuario/.bashrc

# Scripts de arranque personalizado
chmod 755 /etc/init.d/mi_servicio
chmod 644 /etc/systemd/system/mi_servicio.service
```

### **Post-Instalación**

```bash
# Configurar SSH
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys

# Scripts personales
chmod +x ~/bin/mis_scripts.sh
```

## **Permisos Comunes y Sus Usos**

### **Para Archivos**

```bash
# 644 - Archivos de configuración, documentos
chmod 644 ~/.bashrc

# 600 - Archivos privados, claves
chmod 600 ~/.ssh/id_rsa

# 755 - Scripts ejecutables, binarios
chmod 755 ~/bin/script.sh

# 666 - Archivos de datos compartidos (raro)
chmod 666 /tmp/archivo_compartido
```

### **Para Directorios**

```bash
# 755 - Directorios estándar
chmod 755 ~/Documentos

# 700 - Directorios privados  
chmod 700 ~/privado

# 775 - Directorios de grupo de trabajo
chmod 775 /shared/proyecto

# 777 - Acceso total (¡usar con precaución!)
chmod 777 /tmp/temporal
```

## **Troubleshooting de Permisos**

### **Problemas Comunes**

```bash
# "Permission denied" al ejecutar script
ls -la script.sh  # Verificar permisos
chmod +x script.sh  # Agregar ejecución

# No puedes editar archivo
ls -la archivo.txt  # Ver propietario y permisos
chmod u+w archivo.txt  # Agregar escritura

# Directorio no accesible
chmod +x directorio/  # Necesitas 'x' para entrar

# Problemas con SSH
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
```

### **Verificar y Diagnosticar**

```bash
# Ver permisos detallados
ls -la archivo

# Ver permisos de directorio padre
ls -lad directorio/

# Verificar propietario
stat archivo.txt

# Ver permisos en formato octal
stat -c "%a %n" archivo.txt
```

## **Buenas Prácticas de Seguridad**

### **Principio de Menor Privilegio**

```bash
# ✅ Dar solo los permisos necesarios
chmod 644 archivo_lectura.txt  # Solo lectura para grupo/otros
chmod 700 directorio_privado/  # Solo propietario

# ❌ Evitar permisos excesivos
chmod 777 archivo  # ¡Muy peligroso!
```

### **Archivos Sensibles**

```bash
# Archivos de contraseñas y claves
chmod 600 /etc/shadow
chmod 600 ~/.ssh/id_rsa
chmod 600 ~/.netrc

# Archivos de configuración del sistema
chmod 644 /etc/passwd
chmod 644 /etc/hosts
chmod 755 /etc/init.d/*
```

## **Automatización con Scripts**

### **Scripts de Configuración**

```bash
#!/bin/bash
# Script para configurar permisos estándar

# Directorio home del usuario
chmod 755 "$HOME"
chmod 644 "$HOME"/.bashrc
chmod 644 "$HOME"/.profile

# SSH si existe
if [ -d "$HOME/.ssh" ]; then
    chmod 700 "$HOME/.ssh"
    chmod 600 "$HOME/.ssh"/*
    chmod 644 "$HOME/.ssh"/*.pub 2>/dev/null
fi

# Scripts personales
if [ -d "$HOME/bin" ]; then
    chmod 755 "$HOME/bin"
    chmod +x "$HOME/bin"/*.sh 2>/dev/null
fi
```

### **Verificación de Permisos**

```bash
# Script para auditar permisos
find /home -type f -perm 777 -ls  # Archivos con permisos 777
find /etc -type f -perm -002 -ls  # Archivos escribibles por otros
find / -type f -perm -4000 -ls 2>/dev/null  # Archivos SUID
```

## **Integración con Otros Comandos**

### **Con [[chown]]**

```bash
# Cambiar propietario y permisos juntos
sudo chown usuario:grupo archivo.txt
chmod 644 archivo.txt

# En una línea con &&
sudo chown usuario:grupo archivo.txt && chmod 644 archivo.txt
```

### **Con [[find]]**

```bash
# Buscar y cambiar permisos
find /var/www -name "*.php" -exec chmod 644 {} \;
find /home -type d -exec chmod 755 {} \;
```

### **Con [[ls]]**

```bash
# Verificar permisos después de cambios
chmod 755 script.sh && ls -la script.sh
```

## **Enlaces Relacionados**

- [[chown]] - Cambiar propietario de archivos (complementa chmod)
- [[ls]] - Ver permisos actuales antes de cambiarlos
- [[find]] - Buscar archivos para cambiar permisos en lote
- [[stat]] - Ver información detallada de permisos
- [[umask]] - Configurar permisos por defecto para nuevos archivos