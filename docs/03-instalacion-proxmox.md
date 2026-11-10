 
# Fase 3 — Instalación de Proxmox VE

## 1. Objetivo

Instalar y configurar Proxmox VE como plataforma de virtualización para el desarrollo del proyecto de infraestructura, utilizando VirtualBox como entorno de instalación.

## 2. Preparación de la máquina virtual

Se creó una máquina virtual en VirtualBox para instalar Proxmox VE.

La máquina virtual se configuró con los recursos necesarios para ejecutar el hipervisor y permitir posteriormente la creación y gestión de máquinas virtuales y contenedores.

Para la instalación se utilizó la imagen ISO de Proxmox VE, configurada como medio de arranque.

## 3. Instalación de Proxmox VE

Se inició la máquina virtual desde la imagen ISO de Proxmox VE y se seleccionó la opción de instalación gráfica.

Durante el proceso de instalación se realizaron los siguientes pasos:

1. Aceptación de las condiciones de licencia.
2. Selección del disco de instalación.
3. Configuración de la ubicación, zona horaria y distribución del teclado.
4. Establecimiento de la contraseña del usuario `root` y del correo electrónico.
5. Configuración de los parámetros de red y del nombre del servidor.
6. Confirmación de la configuración e instalación del sistema.

Al finalizar la instalación, se reinició la máquina virtual para iniciar Proxmox VE.

## 4. Acceso a la interfaz web de Proxmox

Una vez iniciado Proxmox VE, se accedió a su interfaz de administración mediante un navegador web.

La interfaz está disponible a través del puerto `8006`, utilizando HTTPS:

```text
https://IP_DEL_SERVIDOR:8006
```

Se inició sesión utilizando el usuario `root` y la contraseña configurada durante la instalación, seleccionando el método de autenticación **Linux PAM**.

## 5. Comprobación de la instalación

Tras completar la instalación y acceder a la interfaz web, se realizaron comprobaciones desde la terminal del servidor mediante los siguientes comandos:

```bash
hostnamectl
ip -br addr
pveversion
df -h
```

## 6. Conclusión

Se completó la instalación de Proxmox VE en el entorno virtualizado y se accedió a su interfaz de administración web.

También se realizaron las comprobaciones iniciales del sistema, dejando preparado el entorno para continuar con las siguientes fases del proyecto.

