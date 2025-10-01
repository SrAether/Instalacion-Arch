# systemctl - Control de Servicios del Sistema

**systemctl** es tu **controlador principal** de servicios en Arch Linux. Es el comando que te permite gestionar systemd, el sistema de inicialización y gestión de servicios usado por Arch Linux.

## **¿Qué es systemctl?**

systemctl te permite:
- **Iniciar y detener servicios** del sistema
- **Habilitar servicios** para que se ejecuten al arrancar
- **Ver el estado** de servicios y procesos
- **Gestionar el sistema** (reiniciar, apagar, suspender)

## **¿Por qué es Fundamental en Arch Linux?**

En Arch Linux, systemctl es esencial porque systemd gestiona todo: desde el arranque del sistema hasta servicios como la red, SSH, bases de datos, servidores web, etc. Es el corazón de la gestión del sistema.

### **Sintaxis Básica**

```bash
# Ver estado de un servicio
systemctl status nombre_servicio

# Iniciar un servicio
sudo systemctl start nombre_servicio

# Detener un servicio
sudo systemctl stop nombre_servicio

# Reiniciar un servicio
sudo systemctl restart nombre_servicio

# Habilitar servicio al arranque
sudo systemctl enable nombre_servicio

# Deshabilitar servicio del arranque
sudo systemctl disable nombre_servicio
```

## **Gestión de Servicios**

### **Comandos Básicos de Servicios**

```bash
# Ver estado detallado del sistema
systemctl status

# Listar todos los servicios
systemctl list-units --type=service

# Listar servicios activos
systemctl list-units --type=service --state=active

# Listar servicios fallidos
systemctl list-units --type=service --state=failed

# Ver si un servicio está habilitado
systemctl is-enabled sshd

# Ver si un servicio está activo
systemctl is-active NetworkManager
```

### **Servicios Comunes en Arch Linux**

```bash
# NetworkManager (gestión de red)
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager

# SSH (acceso remoto)
sudo systemctl enable sshd
sudo systemctl start sshd

# Firewall
sudo systemctl enable ufw
sudo systemctl start ufw

# Servidor web Apache
sudo systemctl enable httpd
sudo systemctl start httpd

# Servidor web Nginx
sudo systemctl enable nginx
sudo systemctl start nginx

# Base de datos PostgreSQL
sudo systemctl enable postgresql
sudo systemctl start postgresql

# Cron (tareas programadas)
sudo systemctl enable cronie
sudo systemctl start cronie
```

## **Control del Sistema**

### **Gestión de Energía**

```bash
# Reiniciar el sistema
sudo systemctl reboot

# Apagar el sistema
sudo systemctl poweroff

# Suspender el sistema
sudo systemctl suspend

# Hibernar el sistema
sudo systemctl hibernate

# Modo híbrido (suspender + hibernar)
sudo systemctl hybrid-sleep
```

### **Cambio de Runlevels (Targets)**

```bash
# Ver target actual
systemctl get-default

# Cambiar a target gráfico
sudo systemctl set-default graphical.target

# Cambiar a target de texto
sudo systemctl set-default multi-user.target

# Cambiar temporalmente a target de rescate
sudo systemctl isolate rescue.target

# Targets disponibles
systemctl list-units --type=target
```

## **Análisis y Debugging**

### **Ver Logs de Servicios**

```bash
# Ver logs de un servicio
journalctl -u nombre_servicio

# Ver logs en tiempo real
journalctl -u nombre_servicio -f

# Ver logs desde el último arranque
journalctl -u nombre_servicio -b

# Ver logs de las últimas 24 horas
journalctl -u nombre_servicio --since "24 hours ago"

# Ver errores recientes del sistema
journalctl -p err -b
```

### **Información Detallada de Servicios**

```bash
# Ver dependencias de un servicio
systemctl list-dependencies sshd

# Ver qué servicios dependen de uno específico
systemctl list-dependencies --reverse NetworkManager

# Ver propiedades de un servicio
systemctl show sshd

# Ver archivos de configuración de un servicio
systemctl cat sshd
```

## **Durante la Instalación de Arch**

### **Servicios Esenciales Post-Instalación**

```bash
# Después de instalar Arch, estos servicios son fundamentales:

# 1. Habilitar NetworkManager para internet
sudo systemctl enable NetworkManager

# 2. Si usas DHCP
sudo systemctl enable dhcpcd

# 3. Si instalaste SSH
sudo systemctl enable sshd

# 4. Para sincronización de tiempo
sudo systemctl enable systemd-timesyncd

# 5. Si tienes impresoras
sudo systemctl enable cups
```

### **Verificar que Todo Funciona**

```bash
# Verificar servicios críticos después de la instalación
echo "=== Verificando Servicios Críticos ==="

# Red
systemctl is-active NetworkManager && echo "✅ Red OK" || echo "❌ Problema de red"

# Tiempo
systemctl is-active systemd-timesyncd && echo "✅ Sincronización de tiempo OK" || echo "⚠️ Tiempo no sincronizado"

# SSH (si está instalado)
systemctl is-enabled sshd >/dev/null 2>&1 && echo "✅ SSH habilitado" || echo "ℹ️ SSH no habilitado"

# Ver servicios fallidos
failed_services=$(systemctl list-units --type=service --state=failed --no-legend | wc -l)
if [[ $failed_services -eq 0 ]]; then
    echo "✅ No hay servicios fallidos"
else
    echo "⚠️ $failed_services servicios fallidos"
    systemctl list-units --type=service --state=failed
fi
```

## **Solución de Problemas Comunes**

### **Servicio No Arranca**

```bash
# 1. Ver el estado detallado
systemctl status nombre_servicio

# 2. Ver los logs para encontrar errores
journalctl -u nombre_servicio -n 50

# 3. Verificar el archivo de configuración
systemctl cat nombre_servicio

# 4. Recargar configuración si hiciste cambios
sudo systemctl daemon-reload

# 5. Intentar reiniciar
sudo systemctl restart nombre_servicio
```

### **Servicio Se Detiene Solo**

```bash
# Ver por qué se detuvo
journalctl -u nombre_servicio | tail -20

# Ver si hay problemas de dependencias
systemctl list-dependencies nombre_servicio

# Verificar recursos del sistema
systemctl status
```

### **Tiempo de Arranque Lento**

```bash
# Ver tiempo total de arranque
systemd-analyze

# Ver servicios que más demoran
systemd-analyze blame

# Ver cadena crítica de arranque
systemd-analyze critical-chain

# Crear gráfico de arranque (requiere graphviz)
systemd-analyze plot > boot.svg
```

## **Creación de Servicios Personalizados**

### **Servicio Básico**

```bash
# Crear archivo de servicio en /etc/systemd/system/mi-servicio.service
sudo tee /etc/systemd/system/mi-servicio.service << 'EOF'
[Unit]
Description=Mi Servicio Personalizado
After=network.target

[Service]
Type=simple
User=usuario
ExecStart=/ruta/a/mi/programa
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Recargar systemd para que reconozca el nuevo servicio
sudo systemctl daemon-reload

# Habilitar y iniciar el servicio
sudo systemctl enable mi-servicio
sudo systemctl start mi-servicio

# Verificar que funciona
systemctl status mi-servicio
```

## **Comandos Útiles para Arch Linux**

### **Mantenimiento del Sistema**

```bash
# Reiniciar servicios después de actualizar
sudo systemctl daemon-reload

# Ver servicios que necesitan reinicio después de actualización
systemctl list-units --state=failed

# Verificar integridad del sistema
systemctl --failed

# Limpiar servicios innecesarios
sudo systemctl reset-failed
```

### **Información del Sistema**

```bash
# Ver versión de systemd
systemctl --version

# Ver tiempo de actividad del sistema
systemctl status | grep "since"

# Ver usuarios logueados
systemctl list-sessions

# Ver información de arranque
systemctl list-jobs
```

## **Integración con Otros Comandos**

### **systemctl + journalctl**

```bash
# Ver logs del kernel
journalctl -k

# Ver logs desde el arranque actual
journalctl -b

# Ver logs de un usuario específico
journalctl _UID=1000

# Limpiar logs antiguos
sudo journalctl --vacuum-time=30d
```

### **systemctl + ps**

```bash
# Ver procesos de un servicio
systemctl status nombre_servicio

# Encontrar PID de un servicio
systemctl show --property MainPID nombre_servicio

# Ver árbol de procesos del servicio
ps --forest -p $(systemctl show --property MainPID --value sshd)
```

## **Enlaces Relacionados**

- [[ps]] - Ver procesos de servicios
- [[journalctl]] - Ver logs de servicios  
- [[top]] - Monitorear recursos de servicios
- [[kill]] - Terminar procesos de servicios
- [[mount]] - Gestionar filesystems que systemd puede montar
- [[firewall]] - Servicios de seguridad gestionados por systemd