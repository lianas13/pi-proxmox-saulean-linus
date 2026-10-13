# Inventario de hardware y viabilidad

Modalidad: PENDIENTE (virtualizada o fisica).
Origen de los datos: PENDIENTE (sistema actual, Linux de inspeccion o VM).

## Inventario inicial

| Componente | Dato observado | Comando o fuente |
|---|---|---|
| CPU | PENDIENTE | `lscpu` |
| RAM | PENDIENTE | `free -h` |
| Almacenamiento | PENDIENTE | `lsblk`, `df -h` |
| Red | PENDIENTE | `ip -br addr` |
| Plataforma / firmware | PENDIENTE | `lspci -nnk`, `systemd-detect-virt` |

## Pregunta 1: ¿Puedo instalar Proxmox en este equipo?

| Requisito | Dato del equipo | Fuente | Veredicto |
|---|---|---|---|
| CPU | PENDIENTE | PENDIENTE | PENDIENTE |
| RAM | PENDIENTE | PENDIENTE | PENDIENTE |
| Disco | PENDIENTE | PENDIENTE | PENDIENTE |
| Red | PENDIENTE | PENDIENTE | PENDIENTE |
| Plataforma / firmware | PENDIENTE | PENDIENTE | PENDIENTE |

- ¿Puedo instalar y arrancar Proxmox? PENDIENTE
- ¿Puedo ejecutar el proyecto completo (srv-aso y cli-linux)? PENDIENTE

## Pregunta 2: Limitaciones con hardware reducido

### Reparto inicial de recursos

| Elemento | CPU / vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Proxmox | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |
| srv-aso | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |
| cli-linux | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |
| Plantilla, ISOs y copias | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |

### Saldo y medidas

- Saldo de RAM: PENDIENTE
- Saldo de disco: PENDIENTE
- Medidas propuestas: PENDIENTE

## Pregunta 3: Componentes criticos

| Prioridad | Componente | Funcion | Si es insuficiente | Si falla | Evidencia |
|---|---|---|---|---|---|
| 1 | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |
| 2 | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |
| 3 | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |

Primera mejora y motivo: PENDIENTE

## Fuentes

| Fuente | URL | Version o modelo | Fecha de consulta | Afirmacion que respalda |
|---|---|---|---|---|
| PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE | PENDIENTE |

## Dictamen de viabilidad

PENDIENTE (apto, apto con condiciones o no apto para la propuesta actual).

Volver al [README](../README.md).
