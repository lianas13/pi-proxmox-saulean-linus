# Inventario de hardware y viabilidad

- **Origen de los datos:** equipo de aula, sistema Linux instalado. `systemd-detect-virt` devuelve `none`, por lo que es un equipo fisico y no una VM ([evidencia](../evidencias/capturas-o-salidas/s02-detect-virt.jpeg)).
- **Fecha de recogida:** 2026-10-13.
- **Modalidad:** datos de equipo fisico; la modalidad definitiva se confirma con el profesor.
- **Nota:** las salidas son fotografias de la pantalla, no ficheros de texto.

## Inventario inicial

| Componente | Dato observado | Comando | Evidencia |
|---|---|---|---|
| CPU | Intel Pentium G4560 @ 3,50 GHz, 2 nucleos, 2 hilos por nucleo (4 CPU logicas), 1 socket, VT-x, flags `vmx`, `ept` y `vpid` | `lscpu` | [1](../evidencias/capturas-o-salidas/s02-lscpu-1.jpeg), [2](../evidencias/capturas-o-salidas/s02-lscpu-2.jpeg) |
| RAM | 7,6 GiB totales, 1,0 GiB usados, 5,4 GiB libres; swap 511 MiB | `free -h` | [captura](../evidencias/capturas-o-salidas/s02-free-lsblk.jpeg) |
| Almacenamiento | 1 disco TOSHIBA DT01ACA050, 465,8 GiB, SATA, rotacional (`ROTA=1`); una particion `sda1` montada en `/` | `lsblk -o NAME,TYPE,SIZE,MODEL,ROTA,TRAN` | [captura](../evidencias/capturas-o-salidas/s02-lsblk-modelo.jpeg) |
| Espacio | `/dev/sda1`: 458 GiB, 67 GiB usados, 368 GiB disponibles (16 %) | `df -h` | [captura](../evidencias/capturas-o-salidas/s02-df.jpeg) |
| Unidad optica | ATAPI iHAS124 F, SATA (`sr0`) | `lsblk` | [captura](../evidencias/capturas-o-salidas/s02-lsblk-modelo.jpeg) |
| Red | `eth0` en estado DOWN (Realtek RTL8111/8168/8411 Gigabit, driver `r8169`); ademas `virbr0` 192.168.122.1/24 y `docker0` 172.17.0.1/16, ambas DOWN | `ip -br addr`, `lspci -nnk` | [ip](../evidencias/capturas-o-salidas/s02-ip.jpeg), [lspci](../evidencias/capturas-o-salidas/s02-lspci-2.jpeg) |
| Plataforma | Chipset Intel 200 Series (LPC B250), SATA en modo AHCI, graficos integrados HD 610 | `lspci -nnk` | [1](../evidencias/capturas-o-salidas/s02-lspci-1.jpeg), [2](../evidencias/capturas-o-salidas/s02-lspci-2.jpeg) |
| Firmware (BIOS/UEFI) | No comprobado | pendiente | - |

Observacion: el `lspci` muestra el subsistema de la placa de red como "PRIME B450M-A", mientras que el chipset es de la serie 200 de Intel. No uso ese modelo de placa como dato; queda por confirmar con la etiqueta de la placa o `dmidecode`.

El primer intento de `egrep -c '(vmx|svm)' /proc/cpui` fallo por un error al escribir el fichero (`/proc/cpuinfo`). El soporte de virtualizacion se comprueba con los flags de `lscpu`.

## Pregunta 1: ¿Puedo instalar Proxmox en este equipo?

Requisitos de [Proxmox VE](https://pve.proxmox.com/wiki/System_Requirements) frente al equipo:

| Requisito | Dato del equipo | Fuente | Veredicto |
|---|---|---|---|
| CPU x86 de 64 bits con Intel VT | Pentium G4560, arquitectura `x86_64`, VT-x y EPT | `lscpu`; [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html) | Cumple. Falta comprobar que VT-x esta activo en la BIOS |
| RAM minima de evaluacion: 1 GB mas los invitados | 7,6 GiB | `free -h` | Cumple |
| RAM recomendada: 2 GB para Proxmox mas los invitados | 7,6 GiB | `free -h` | Cumple, con margen limitado |
| Almacenamiento: un disco duro | 1 HDD SATA de 465,8 GiB | `lsblk` | Cumple. No hay SSD ni RAID |
| Red: una NIC | 1 NIC Gigabit Realtek, con `eth0` DOWN | `ip -br addr`, `lspci` | Cumple con la tarjeta; pendiente confirmar el enlace (cable, switch) |
| Plataforma / firmware | Chipset Intel serie 200, AHCI; modo BIOS/UEFI sin comprobar | `lspci` | Pendiente |

Sobre VT-x: `lscpu` indica `Virtualizacion: VT-x` y los flags incluyen `vmx` y `ept`, pero la linea de vulnerabilidades muestra `KVM: Mitigation: VMX disabled`. Eso significa que KVM no esta usando VMX en este momento. No prueba que este desactivado en la BIOS. Hay que comprobar `lsmod | grep kvm`, `ls /dev/kvm` y la opcion de virtualizacion en la BIOS.

**¿Puedo instalar y arrancar Proxmox?** Si. CPU de 64 bits con VT-x, 7,6 GiB de RAM, un disco SATA y una NIC superan los requisitos minimos. Condiciones: VT-x activo en BIOS y que el disco pueda borrarse, porque el instalador usa el disco entero y el sistema actual ocupa 67 GiB.

**¿Puedo ejecutar el proyecto completo (srv-aso y cli-linux)?** Si, con un reparto de recursos ajustado, ver la pregunta 2. El cuello de botella es la RAM y el disco rotacional, no la CPU.

## Pregunta 2: Limitaciones con hardware reducido

### Reparto inicial de recursos

Supuestos: la RAM total es 7,6 GiB y el disco 465,8 GiB. La swap no cuenta como RAM adicional, el disco dinamico no cuenta como espacio garantizado y las vCPU no equivalen a nucleos exclusivos (hay 4 hilos para todo).

| Elemento | CPU / vCPU | RAM | Disco | Justificacion |
|---|---|---|---|---|
| Proxmox (host) | compartida | 2,0 GiB | 30 GiB | Proxmox recomienda al menos 2 GB para el sistema y sus servicios |
| srv-aso | 2 vCPU | 2,0 GiB | 40 GiB | Servicio principal del laboratorio |
| cli-linux | 1 vCPU | 1,5 GiB | 20 GiB | Cliente de prueba; puede ser LXC para consumir menos |
| Plantilla, ISOs y copias | 0 (apagada) | 0 | 85 GiB (15 plantilla, 10 ISOs, 60 copias) | Solo ocupa disco |
| **Total reservado** | | **5,5 GiB** | **175 GiB** | |

### Saldo

| Recurso | Total | Reservado | Saldo |
|---|---|---|---|
| RAM | 7,6 GiB | 5,5 GiB | 2,1 GiB de margen |
| Disco | 465,8 GiB | 175 GiB | 290,8 GiB libres |
| vCPU | 4 hilos | 3 vCPU en invitados | Se pueden asignar mas vCPU que hilos, con riesgo de lentitud si coinciden las cargas |

### Limitaciones y medidas

- **RAM:** con 2,1 GiB de margen, una tercera VM o un servicio pesado agotaria la memoria. Medida: usar LXC en lugar de VM cuando sea posible, porque comparten el kernel y consumen menos. No resuelve los servicios que necesiten un kernel propio.
- **Disco rotacional:** clonar, restaurar copias y arrancar varias VM a la vez seran lentos porque el HDD es el limite de operaciones de E/S. Medida: no ejecutar varias tareas pesadas a la vez. No elimina la lentitud; solo la evita.
- **Copias en el mismo disco:** las copias de seguridad (60 GiB) estan en el mismo disco que las VM. Protegen de errores, pero no de un fallo del disco. Medida: copiar los backups a un medio externo.
- **CPU:** 2 nucleos con 4 hilos bastan para un servidor y un cliente con carga baja. Con varios invitados activos habra contencion.
- **Red:** una sola NIC; el laboratorio usara `vmbr0` para el acceso externo. Una red interna `vmbr1` entre servidor y cliente no necesita NIC fisica.

Si no se cumplieran las condiciones de la pregunta 1 (por ejemplo, VT-x desactivado y no modificable en BIOS), se pasaria a Proxmox virtualizado en VirtualBox, que ademas exigiria virtualizacion anidada.

## Pregunta 3: Componentes criticos

| Prioridad | Componente | Funcion | Si es insuficiente | Si falla | Evidencia |
|---|---|---|---|---|---|
| 1 | RAM (7,6 GiB) | Memoria de Proxmox y de todos los invitados | Memoria agotada, uso de swap (511 MiB) y lentitud o cierre de procesos | El equipo se cuelga o no arranca | [free -h](../evidencias/capturas-o-salidas/s02-free-lsblk.jpeg) |
| 2 | Almacenamiento (1 HDD de 465,8 GiB) | Sistema, VM, ISOs y copias | Operaciones de disco muy lentas (clonar, restaurar) por ser rotacional | Se pierde todo el laboratorio y las copias, porque estan en el mismo disco | [lsblk](../evidencias/capturas-o-salidas/s02-lsblk-modelo.jpeg), [df](../evidencias/capturas-o-salidas/s02-df.jpeg) |
| 3 | CPU (2 nucleos, 4 hilos) | Ejecuta los invitados con virtualizacion VT-x | Contencion de CPU con varias VM activas | No hay equipo | [lscpu](../evidencias/capturas-o-salidas/s02-lscpu-1.jpeg) |
| 4 | Red (1 NIC Gigabit) | Acceso web al nodo y salida a Internet | Poco ancho de banda para las copias en red | Sin acceso remoto al nodo | [ip](../evidencias/capturas-o-salidas/s02-ip.jpeg), [lspci](../evidencias/capturas-o-salidas/s02-lspci-2.jpeg) |

- **Limita el rendimiento:** la RAM, por el numero de invitados, y el HDD, por la velocidad de las operaciones de E/S.
- **Mayor impacto si falla:** el disco, porque es un unico punto de fallo y las copias estan en el mismo.
- **Primera mejora:** un SSD. Acelera clonados, arranques y restauraciones, y permite separar las copias del sistema. La RAM seria la segunda mejora, ya que el reparto actual deja 2,1 GiB de margen. No hace falta comprar nada para el laboratorio inicial.

## Fuentes

| Fuente | URL | Version o modelo | Consulta | Afirmacion que respalda |
|---|---|---|---|---|
| Proxmox VE, requisitos del sistema | https://pve.proxmox.com/wiki/System_Requirements | Revision de la wiki del 2024-11-28 (la pagina no indica version de PVE) | 2026-10-13 | CPU 64 bits con Intel VT; minimo 1 GB de RAM mas invitados; 2 GB recomendados para el sistema; un disco y una NIC |
| Intel ARK, Pentium G4560 | https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html | Pentium G4560 | 2026-10-13 | 2 nucleos y 4 hilos, 3,50 GHz, VT-x, VT-d y EPT, memoria maxima de 64 GB DDR4-2133/2400 |
| Toshiba, serie DT01ACA | https://toshiba.semicon-storage.com/eu/product/storage-products/client-hdd/dt01acaxxx.html | DT01ACA050 (500 GB) | 2026-10-13 | Disco de 3,5", SATA 6 Gbit/s, 7200 rpm |

## Dictamen de viabilidad

**Apto con condiciones** para la propuesta actual.

Condiciones antes de instalar:
1. Confirmar que VT-x esta activo en la BIOS (`/dev/kvm` presente tras activarlo).
2. Comprobar si el equipo arranca en BIOS o UEFI.
3. Confirmar que el sistema Linux actual (67 GiB usados) puede borrarse, porque el instalador usa el disco entero.
4. Verificar el enlace de red de `eth0` (aparece DOWN en la captura).

Motivo: se cumplen los requisitos de Proxmox con holgura en CPU y RAM, pero el proyecto completo cabe con un margen de RAM de 2,1 GiB, y el disco unico y rotacional limita el rendimiento y la seguridad de las copias.

Volver al [README](../README.md).
