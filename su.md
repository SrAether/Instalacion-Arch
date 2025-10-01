# su - Cambiar de Usuario

El comando `su` (Switch User) es tu **cambiaformas digital**. Te permite transformarte temporalmente en otro usuario sin cerrar tu sesión actual, como ponerte el disfraz de otro usuario para realizar tareas específicas.

## **¿Qué es su?**

Es un comando que te permite:
- **Cambiar a otro usuario** temporalmente
- **Ejecutar comandos** como otro usuario
- **Acceder a privilegios** de otros usuarios
- **Mantener o cambiar** el entorno de trabajo

## **¿Por qué es Útil en Arch Linux?**

Durante la administración del sistema, a veces necesitas ejecutar comandos como otro usuario específico o cambiar a root temporalmente para tareas que [[Sudo]] no cubre adecuadamente.

### **Uso Básico**

```bash
# Cambiar a root (requiere contraseña de root)
su

# Cambiar a root con entorno completo
su -

# Cambiar a usuario específico
su nombreusuario

# Cambiar a usuario con su entorno
su - nombreusuario
```

### **Diferencia entre `su` y `su -`**

```bash
# su (sin guión) - mantiene entorno actual
su root
pwd          # Sigues en tu directorio actual
echo $HOME   # Sigue siendo tu HOME

# su - (con guión) - entorno completo del usuario destino
su - root
pwd          # Estás en /root
echo $HOME   # Ahora es /root
```

## **Comparación con sudo**

| Aspecto | su | [[Sudo]] |
|---------|----|----|
| **Contraseña** | Del usuario destino | Tu contraseña |
| **Duración** | Hasta que hagas `exit` | Solo para el comando |
| **Configuración** | No requiere setup | Requiere configurar /etc/sudoers |
| **Auditoría** | Limitada | Registra todos los comandos |
| **Seguridad** | Conocer contraseña de root | Permisos granulares |

## **Casos de Uso Prácticos**

### **Administración de Sistema**

```bash
# Cambiar a root para múltiples tareas administrativas
su -
# Ahora eres root hasta que hagas exit

# Realizar varias operaciones
pacman -Syu
systemctl restart httpd
nano /etc/hosts

# Volver a tu usuario
exit
```

### **Ejecutar Comando Específico**

```bash
# Ejecutar un solo comando como otro usuario
su -c "comando" usuario

# Ejemplos:
su -c "systemctl restart apache2" root
su -c "whoami" otroUsuario
```

### **Cambiar a Usuario de Servicio**

```bash
# Cambiar a usuario de aplicación (ej: nginx, apache)
su - nginx
su - mysql
su - postgres

# Útil para debugging de servicios
```

## **Contexto en el Curso**

### **Durante Instalación ([[Módulo 4 - Amueblando desde Dentro (Configuración en Chroot)]])**

```bash
# Si necesitas cambiar a root frecuentemente
su -

# Realizar configuraciones múltiples sin sudo repetido
useradd -m usuario
passwd usuario
usermod -a -G wheel usuario
```

### **Post-Instalación**

```bash
# Para tareas administrativas extensas
su -
# Configurar servicios, instalar software, etc.
```

## **Opciones Avanzadas**

### **Preservar Entorno Específico**

```bash
# Mantener variables de entorno específicas
su -p usuario        # Preservar PATH
su --preserve-environment usuario
```

### **Especificar Shell**

```bash
# Usar shell específico
su -s /bin/bash usuario
su --shell=/bin/zsh usuario
```

### **Comando con Argumentos**

```bash
# Ejecutar comando complejo
su -c "find /var -name '*.log' -size +100M" root
```

## **Seguridad y Consideraciones**

### **Buenas Prácticas**

```bash
# ✅ Usar su - para entorno limpio
su - root

# ✅ Limitar tiempo como root
su -
# hacer tareas
exit  # Volver inmediatamente

# ❌ No dejar sesiones de root abiertas
# ❌ No usar su sin guión para tareas administrativas
```

### **Logging y Auditoría**

```bash
# Ver intentos de su en logs
sudo journalctl | grep su:

# Ver sesiones de su activas
w
who
```

## **Troubleshooting Común**

### **"Authentication failure"**
```bash
# Contraseña incorrecta del usuario destino
# Verificar que el usuario existe:
getent passwd nombreusuario

# Verificar que tiene contraseña configurada:
sudo passwd -S nombreusuario
```

### **"This account is currently not available"**
```bash
# Usuario con shell no válido
# Verificar shell del usuario:
getent passwd nombreusuario | cut -d: -f7

# Cambiar shell si es necesario:
sudo chsh -s /bin/bash nombreusuario
```

### **Problemas de Entorno**

```bash
# Si el entorno no se carga correctamente
su - usuario  # Usar el guión

# Si PATH está mal configurado
su -c "echo \$PATH" usuario
```

## **Integración con Otros Comandos**

### **Con whoami e id**

```bash
# Verificar cambio de usuario
whoami      # Antes: tu_usuario
su - root
whoami      # Después: root
id          # Verificar grupos y permisos completos
```

### **Con passwd**

```bash
# Cambiar contraseñas desde root
su -
passwd usuario1
passwd usuario2
exit
```

### **En Scripts**

```bash
# Script que necesita ejecutar como otro usuario
#!/bin/bash

# Verificar que no somos ya root
if [[ $(whoami) == "root" ]]; then
    echo "Ya eres root"
else
    echo "Cambiando a root..."
    su - root -c "bash $0"
    exit $?
fi

# Aquí el script continúa como root
echo "Ejecutando como: $(whoami)"
```

## **Alternativas Modernas**

### **sudo vs su**

```bash
# Moderno (recomendado): sudo
sudo comando

# Clásico: su
su -c "comando" root

# Para múltiples comandos: sudo con shell
sudo -i    # Similar a su -
sudo -s    # Similar a su
```

### **systemd-run**

```bash
# Ejecutar como otro usuario con systemd
sudo systemd-run --uid=usuario --gid=grupo comando
```

## **Configuración del Sistema**

### **Archivo /etc/login.defs**

```bash
# Configuraciones que afectan su
grep SU_ /etc/login.defs
# SU_WHEEL_ONLY yes    # Solo grupo wheel puede usar su
```

### **PAM Configuration**

```bash
# Configuración avanzada en /etc/pam.d/su
cat /etc/pam.d/su
```

## **Casos de Uso Específicos**

### **Desarrollo y Testing**

```bash
# Probar aplicación como usuario específico
su - testuser -c "cd /app && ./mi_aplicacion"
```

### **Mantenimiento de Base de Datos**

```bash
# Cambiar a usuario de base de datos
su - postgres
psql
```

### **Servicios Web**

```bash
# Debug como usuario del servidor web
su - nginx -s /bin/bash
```

## **Enlaces Relacionados**

- [[Sudo]] - Alternativa moderna y más segura para privilegios elevados
- [[whoami]] - Verificar usuario actual antes y después de su
- [[id]] - Ver información completa de permisos después de cambiar usuario
- [[passwd]] - Gestionar contraseñas necesarias para su
- [[groups]] - Verificar grupos del usuario al que cambias