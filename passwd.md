# passwd - Cambiar Contraseña

El comando `passwd` es tu **gestor de credenciales de acceso**. Te permite cambiar contraseñas de usuarios, gestionar la seguridad de cuentas y configurar políticas de autenticación en tu sistema Arch Linux.

## **¿Qué es passwd?**

Es un comando fundamental que:
- **Cambia contraseñas** de usuarios
- **Configura políticas** de contraseñas
- **Bloquea/desbloquea** cuentas de usuario
- **Gestiona expiración** de contraseñas

## **¿Por qué es Crucial en Arch Linux?**

Durante la instalación y configuración post-instalación, `passwd` es esencial para establecer contraseñas seguras para root y usuarios normales.

### **Uso Básico**

```bash
# Cambiar tu propia contraseña
passwd

# Cambiar contraseña de otro usuario (como root)
sudo passwd nombreusuario

# Cambiar contraseña de root
sudo passwd root
```

### **Proceso Interactivo**

```bash
passwd
# Current password: [ingresa contraseña actual]
# New password: [ingresa nueva contraseña]
# Retype new password: [confirma nueva contraseña]
# passwd: password updated successfully
```

## **Contexto en la Instalación de Arch**

### **Durante la Instalación ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Establecer contraseña de root
passwd

# Crear usuario y establecer su contraseña
useradd -m -G wheel nombreusuario
passwd nombreusuario
```

### **Primera Configuración del Sistema**

```bash
# Al entrar al sistema por primera vez
# 1. Cambiar contraseña de root (si es necesario)
sudo passwd root

# 2. Establecer/cambiar tu contraseña
passwd
```

## **Opciones Avanzadas**

### **Gestión de Cuentas**

```bash
# Bloquear cuenta de usuario
sudo passwd -l nombreusuario

# Desbloquear cuenta
sudo passwd -u nombreusuario

# Eliminar contraseña (sin contraseña)
sudo passwd -d nombreusuario

# Forzar cambio en próximo login
sudo passwd -e nombreusuario
```

### **Información de Estado**

```bash
# Ver estado de contraseña
sudo passwd -S nombreusuario

# Salida típica:
# nombreusuario PS 2023-12-01 0 99999 7 -1
# Usuario Estado Último_cambio Min_días Max_días Aviso_días Inactividad
```

## **Políticas de Contraseñas**

### **Configuración en /etc/login.defs**

```bash
# Ver configuración actual
grep -E '^PASS_' /etc/login.defs

# Configuraciones típicas:
# PASS_MAX_DAYS   99999    # Días hasta expiración
# PASS_MIN_DAYS   0        # Días mínimos entre cambios
# PASS_WARN_AGE   7        # Días de aviso antes de expirar
# PASS_MIN_LEN    5        # Longitud mínima
```

### **Cambiar Políticas**

```bash
# Establecer expiración de contraseña
sudo passwd -x 90 nombreusuario  # Expira en 90 días

# Establecer días mínimos entre cambios
sudo passwd -n 1 nombreusuario   # Mínimo 1 día

# Establecer aviso de expiración
sudo passwd -w 7 nombreusuario   # Avisar 7 días antes
```

## **Buenas Prácticas de Seguridad**

### **Contraseñas Seguras**

```bash
# Criterios para contraseñas seguras:
# - Mínimo 12 caracteres
# - Incluir mayúsculas, minúsculas, números, símbolos
# - No usar información personal
# - No reutilizar contraseñas

# Generar contraseña aleatoria
openssl rand -base64 32
```

### **Verificación de Fortaleza**

```bash
# passwd rechazará contraseñas débiles automáticamente
passwd
# New password: 123456
# BAD PASSWORD: The password is too short
# BAD PASSWORD: The password is a palindrome
```

## **Troubleshooting Común**

### **"Authentication token manipulation error"**
```bash
# Filesystem de solo lectura
mount -o remount,rw /

# Permisos incorrectos en /etc/shadow
sudo chmod 640 /etc/shadow
sudo chown root:shadow /etc/shadow
```

### **Contraseña No Acepta Cambios**
```bash
# Verificar que no esté bloqueada
sudo passwd -S usuario

# Si muestra 'L' está bloqueada, desbloquear:
sudo passwd -u usuario
```

### **"Password unchanged"**
```bash
# Contraseña nueva muy similar a la actual
# Usar contraseña completamente diferente
```

## **Integración con Otros Sistemas**

### **Con sudo**
```bash
# Cambiar configuración para no pedir contraseña
sudo visudo
# Agregar: usuario ALL=(ALL) NOPASSWD: ALL
```

### **Con SSH**
```bash
# Después de cambiar contraseña, actualizar claves SSH si es necesario
ssh-keygen -t ed25519 -C "tu@email.com"
```

## **Automatización y Scripts**

### **Cambio de Contraseña en Scripts**

```bash
# NUNCA hagas esto (inseguro):
# echo "nueva_contraseña" | passwd --stdin usuario

# Mejor: usar chpasswd para automatización
echo "usuario:nueva_contraseña" | sudo chpasswd

# O generar contraseña aleatoria
nueva_pass=$(openssl rand -base64 32)
echo "usuario:$nueva_pass" | sudo chpasswd
echo "Nueva contraseña para usuario: $nueva_pass"
```

### **Verificación en Scripts**

```bash
# Verificar si usuario tiene contraseña configurada
if sudo passwd -S usuario | grep -q " PS "; then
    echo "Usuario tiene contraseña configurada"
else
    echo "Usuario necesita contraseña"
fi
```

## **Archivos Relacionados**

### **Archivos del Sistema**

```bash
# /etc/passwd - Información de usuarios
grep usuario /etc/passwd

# /etc/shadow - Contraseñas encriptadas (solo root)
sudo grep usuario /etc/shadow

# /etc/login.defs - Configuración de políticas
grep PASS /etc/login.defs
```

## **Diferencias con Herramientas Relacionadas**

| Comando | Función | Cuándo Usar |
|---------|---------|-------------|
| **passwd** | Cambiar contraseñas | Gestión de credenciales |
| **[[su]]** | Cambiar usuario | Acceso temporal a otra cuenta |
| **[[Sudo]]** | Ejecutar como otro usuario | Comandos con privilegios elevados |
| **chage** | Gestión avanzada de expiración | Políticas corporativas |

## **Casos de Uso Específicos**

### **Recuperación de Acceso**

```bash
# Si olvidaste tu contraseña, desde root:
sudo passwd tu_usuario

# Si olvidaste contraseña de root, arrancar en modo rescue
# y cambiar desde allí
```

### **Configuración Multi-Usuario**

```bash
# Configurar múltiples usuarios con contraseñas
for user in juan maria pedro; do
    sudo useradd -m -G wheel "$user"
    sudo passwd "$user"
done
```

### **Auditoría de Seguridad**

```bash
# Ver usuarios sin contraseña
sudo awk -F: '($2 == "" || $2 == "!") {print $1}' /etc/shadow
```

## **Enlaces Relacionados**

- [[whoami]] - Verificar usuario actual antes de cambiar contraseña
- [[su]] - Cambiar de usuario después de establecer contraseñas
- [[Sudo]] - Configurar privilegios que requieren autenticación con contraseña
- [[groups]] - Gestionar grupos de usuarios cuyas contraseñas gestionas
- [[id]] - Verificar identidad después de cambios de contraseña