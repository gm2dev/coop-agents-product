# Reserva de turnos para peluquería — MVP

- **Etapa:** Discovery, etapa 1 (problema y casos de uso)
- **Dueño de producto:** Ezequiel Avila
- **Estado:** borrador, ronda 2 (aplica las observaciones del PO Reviewer y D-8), para que lo validen Ezequiel (producto), CDO (diseño) y Architect (factibilidad)
- **Lectores:** CDO (flujos y pantallas) y Architect (factibilidad y estimación). Este documento define **qué** y **por qué**; no decide tecnología ni componentes.

## 1. Problema

Hoy los turnos se toman por teléfono o WhatsApp. Esto genera tres problemas:

1. **El personal pierde tiempo** atendiendo llamadas y mensajes para reservar, cambiar o cancelar, muchas veces mientras atiende a un cliente.
2. **Hay reservas duplicadas**: dos clientes quedan en el mismo horario con el mismo peluquero porque la agenda no se actualiza a tiempo.
3. **Hay clientes que faltan sin avisar** y no se registra quién falta, así que el local no puede medir el problema ni actuar sobre él.

## 2. Usuarios

| Usuario | Descripción | Necesidad principal |
|---|---|---|
| **Cliente** | Persona que quiere cortarse el pelo u otro servicio. Usa sobre todo el celular. No quiere crear una cuenta. | Ver horarios libres y reservar solo, en cualquier momento, sin llamar. Poder cambiar o cancelar fácilmente. |
| **Admin del local** | Dueño o encargado de la peluquería (puede ser también peluquero). | Mantener la agenda al día sin duplicados, cargar turnos que llegan por teléfono y registrar quién asistió. |
| **Peluquero** | Trabaja con su propia agenda. En el MVP **no usa el sistema directamente**: su agenda la gestiona el admin. | Saber qué turnos tiene y no tener turnos superpuestos. |

## 3. Objetivo y métricas propuestas

> **Pendiente de confirmación de Ezequiel (P-1).**

**Objetivo propuesto:** que el cliente reserve solo y sin reservas duplicadas, y medir las ausencias.

| Métrica | Definición | Fuente |
|---|---|---|
| **% de turnos online** | Turnos reservados por el cliente desde la web ÷ total de turnos (web + cargados a mano por el admin), por semana. | Origen del turno (cliente o admin). |
| **Tasa de ausencias** | Turnos marcados "no asistió" ÷ turnos pasados marcados (asistió + no asistió), por semana. | Marca de asistencia (CU-14). |
| **Conversión de reserva** | Reservas confirmadas ÷ reservas iniciadas (cliente que eligió un servicio), por semana. | Inicio y confirmación del flujo de reserva. |
| **Reservas duplicadas** (guardarraíl) | Cantidad de turnos superpuestos para un mismo peluquero. Meta: **0**. | Agenda. |

| **Tiempo perdido en reservas** (estimada) | Llamadas y mensajes por semana para reservar, cambiar o cancelar. Se releva con el local (conteo o estimación del personal) **antes** del piloto y **durante** el piloto, y se comparan. | Relevamiento con el local (no lo mide el sistema). |

Las metas numéricas (por ejemplo, % online a los 30 días) se fijan con Ezequiel al confirmar el objetivo. Antes del piloto (H2) conviene registrar una línea de base de ausencias y de llamadas y mensajes por semana (aunque sea estimada por el local). En H1 (demo interna con datos de prueba, D-8) no se miden métricas de uso real.

**Supuesto sobre los admins:** el panel lo usan uno o pocos admins (dueño o encargado). Si comparten una misma cuenta o tienen cuentas individuales es una pregunta abierta (P-8). El MVP **no tiene roles distintos**: todos los admins pueden hacer lo mismo.

## 4. Alcance del MVP

- **Una peluquería**, **una sola sede**, **varios peluqueros**, cada uno con su agenda.
- **Una sola zona horaria:** la del local. Todos los horarios se muestran y se guardan en esa zona.
- **Cliente** (web pensada para celular y accesible):
  - ve los servicios con duración y precio de referencia (no se cobra);
  - reserva: servicio → peluquero o "cualquiera disponible" → horario → datos → confirmar;
  - no crea cuenta; datos: nombre (obligatorio), teléfono y email (opcionales; el teléfono se recomienda, propuesta P-6);
  - al confirmar ve el resumen, un **link privado** (con la indicación de guardarlo) y un botón **"Agregar al calendario"**;
  - desde el link privado cambia el **día y el horario** (mismo servicio y mismo peluquero o "cualquiera") o cancela, si faltan N horas o más (anticipación mínima). Para cambiar el servicio, cancela y vuelve a reservar.
- **Admin del local** (con login; el mecanismo lo define el Architect):
  - configura los datos del local (nombre, dirección, teléfono de contacto);
  - carga servicios, peluqueros, el horario semanal de cada peluquero y bloqueos (feriados, ausencias);
  - ve la agenda del día y de la semana;
  - carga turnos a mano, cancela y mueve turnos;
  - marca cada turno pasado como "asistió" o "no asistió".
- **El MVP no envía ninguna notificación** (ver D-1).

## 5. Casos de uso

Hitos (D-8, a confirmar por Ezequiel en P-4):

- **Hito 1 (H1) — demo interna:** reserva + agenda con **datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. H1 no depende de nada de H2: la confirmación muestra el resumen y el link privado abre ese resumen en **solo lectura** (los botones de cambiar y cancelar llegan en H2).
- **Hito 2 (H2) — MVP operable:** completa lo necesario para operar con clientes reales en un piloto (cambiar y cancelar desde el link, bloqueos, gestión manual de turnos, asistencia y anticipación mínima).

Los criterios marcados **(H2)** dentro de un CU de H1 se implementan en H2.

| ID | Título | Actor | Hito |
|---|---|---|---|
| CU-01 | Ver servicios | Cliente | 1 |
| CU-02 | Ver horarios libres | Cliente | 1 |
| CU-03 | Reservar un turno | Cliente | 1 |
| CU-04 | Resolver una reserva simultánea del mismo horario | Cliente / sistema | 1 |
| CU-05 | Ver la confirmación y agregar al calendario | Cliente | 1 |
| CU-06 | Cambiar el turno desde el link privado | Cliente | 2 |
| CU-07 | Cancelar el turno desde el link privado | Cliente | 2 |
| CU-08 | Ingresar al panel del local | Admin | 1 |
| CU-09 | Gestionar servicios | Admin | 1 |
| CU-10 | Gestionar peluqueros y su horario semanal | Admin | 1 |
| CU-11 | Gestionar bloqueos (feriados y ausencias) | Admin | 2 |
| CU-12 | Ver la agenda del día y de la semana | Admin | 1 |
| CU-13 | Cargar, mover y cancelar turnos desde el panel | Admin | 2 |
| CU-14 | Marcar asistencia | Admin | 2 |
| CU-15 | Configurar la anticipación mínima | Admin | 2 |
| CU-16 | Configurar datos del local | Admin | 1 |

### CU-01 — Ver servicios

**Como** cliente, **quiero** ver los servicios con su duración y precio de referencia **para** elegir qué reservar.

- **Given** el local tiene servicios activos, **When** el cliente entra a la web de reservas, **Then** ve la lista de servicios activos con nombre, duración y precio de referencia, y una aclaración de que el precio es orientativo y se paga en el local.
- **Given** un servicio está inactivo, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** el local no tiene servicios activos, **When** el cliente entra, **Then** ve un mensaje que indica que por ahora no hay reservas online y que puede contactar al local, con el teléfono de contacto del local (CU-16).
- **Given** un servicio activo no tiene ningún peluquero activo asignado, **When** el cliente ve la lista, **Then** ese servicio no aparece.

### CU-02 — Ver horarios libres

**Como** cliente, **quiero** ver solo los horarios realmente libres **para** no elegir uno que no se puede.

- **Given** el cliente eligió un servicio de 45 min y un peluquero, **When** ve los horarios de un día, **Then** solo ve horarios de inicio en los que ese peluquero trabaja, no tiene bloqueos y tiene 45 min libres seguidos sin superponerse con otro turno.
- **Given** el cliente eligió "cualquiera disponible", **When** ve los horarios de un día, **Then** ve cada horario en el que al menos un peluquero que hace ese servicio está libre, sin horarios repetidos.
- **Given** un horario ya empezó o está en el pasado, **When** el cliente ve los horarios, **Then** ese horario no aparece.
- **Given** un día no tiene horarios libres, **When** el cliente lo consulta, **Then** ve "sin horarios disponibles" y puede pasar a otro día.
- **Given** el cliente consulta horarios, **When** se muestran, **Then** están en la zona horaria del local.
- *(Tentativo, P-5)* **Given** el cliente navega los días, **When** intenta ver un día que está a más de 30 días de hoy, **Then** no puede elegirlo y ve que se puede reservar hasta 30 días hacia adelante.

### CU-03 — Reservar un turno

**Como** cliente, **quiero** reservar en pocos pasos y sin crear cuenta **para** asegurar mi turno sin llamar.

- **Given** el cliente eligió servicio, peluquero (o "cualquiera disponible") y un horario libre, **When** ingresa su nombre y confirma, **Then** el turno queda confirmado en la agenda del peluquero con la duración del servicio y el cliente ve la confirmación (CU-05).
- **Given** el cliente eligió "cualquiera disponible", **When** confirma, **Then** el sistema asigna un peluquero que hace ese servicio y está libre en ese horario, y la confirmación muestra cuál es. El criterio para elegir entre varios peluqueros libres se define más adelante (P-7).
- **Given** el cliente deja el nombre vacío, **When** intenta confirmar, **Then** ve un mensaje junto al campo que indica que el nombre es obligatorio y no se crea el turno.
- **Given** el cliente no ingresa teléfono ni email, **When** confirma con nombre válido, **Then** el turno se crea igual.
- *(Propuesta P-6)* **Given** el cliente está en el paso de datos, **When** ve el campo teléfono, **Then** figura como opcional con un texto que recomienda dejarlo (por ejemplo, "para avisarte si hay un cambio").
- **Given** el cliente ingresa un email o teléfono con formato inválido, **When** intenta confirmar, **Then** ve un mensaje que indica qué corregir y no se crea el turno.
- **Given** el cliente está en cualquier paso, **When** vuelve atrás, **Then** conserva lo que ya eligió en los pasos anteriores.

### CU-04 — Resolver una reserva simultánea del mismo horario

**Como** cliente, **quiero** saber claramente si mi horario se ocupó mientras reservaba **para** elegir otro sin confusión.

- **Given** dos clientes confirman al mismo tiempo el mismo horario con el mismo peluquero, **When** se procesan ambas reservas, **Then** se confirma una sola y en la agenda no queda ningún turno superpuesto.
- **Given** la reserva de un cliente no se pudo confirmar porque el horario se ocupó, **When** recibe la respuesta, **Then** ve un mensaje claro ("Ese horario se acaba de ocupar, elegí otro"), los horarios libres actualizados y conserva el servicio y los datos que ya cargó.
- **Given** el cliente eligió "cualquiera disponible" y el peluquero que se le iba a asignar se ocupó, **When** queda otro peluquero que hace ese servicio y está libre en ese horario, **Then** se le asigna ese (criterio de elección: P-7) y la reserva se confirma; **When** no queda ninguno, **Then** ve el mensaje del criterio anterior.

### CU-05 — Ver la confirmación y agregar al calendario

**Como** cliente, **quiero** ver el resumen, guardar el link y agendar el turno **para** recordarlo y gestionarlo sin que me envíen nada.

- **Given** el turno se confirmó, **When** el cliente ve la pantalla de confirmación, **Then** ve servicio, peluquero, fecha, hora, duración, precio de referencia y nombre, dirección y teléfono de contacto del local (CU-16).
- **Given** el turno se confirmó, **When** el cliente ve la confirmación, **Then** ve su link privado, una indicación visible de que lo guarde (porque no se enviará por ningún medio) y una forma de copiarlo.
- **Given** el cliente abre su link privado, **When** la página carga, **Then** ve el resumen de su turno en solo lectura, con el estado vigente (si el admin lo movió, ve el turno actualizado). En H1 no hay botones de cambiar ni cancelar; llegan en H2 (CU-06, CU-07).
- **Given** alguien abre un link inválido o inexistente, **When** la página carga, **Then** ve "Turno no encontrado" sin revelar datos de ningún turno.
- **Given** el cliente toca "Agregar al calendario", **When** se completa la acción, **Then** puede guardar el evento en el calendario de su celular con fecha, hora, duración, servicio, local y el link privado en la descripción.
- **Given** el turno se confirmó, **When** pasa cualquier tiempo, **Then** el sistema no envía email, SMS ni WhatsApp al cliente.

### CU-06 — Cambiar el turno desde el link privado

**Como** cliente, **quiero** cambiar el día y el horario de mi turno desde el link **para** no tener que llamar.

Alcance del cambio en el MVP: solo **día y horario**, con el **mismo servicio** y el **mismo peluquero** (o "cualquiera disponible", si así reservó). Para cambiar el servicio, el cliente cancela y vuelve a reservar. Es lo más simple para el MVP.

- **Given** el cliente abre su link privado de un turno futuro, **When** la página carga, **Then** ve el resumen del turno (CU-05) y las opciones "Cambiar" y "Cancelar".
- **Given** faltan N horas o más para el inicio (N = anticipación mínima, CU-15), **When** el cliente elige un día y horario nuevos libres para el mismo servicio y peluquero (o "cualquiera") y confirma, **Then** el turno pasa al horario nuevo, el horario original queda libre y ve la confirmación actualizada.
- **Given** el cambio se confirmó, **When** el cliente ve la confirmación, **Then** el link privado sigue siendo el mismo, ve "Agregar al calendario" para el turno nuevo y un aviso que le sugiere borrar de su calendario el evento anterior.
- **Given** el cliente quiere otro servicio, **When** busca cómo cambiarlo, **Then** no puede hacerlo desde "Cambiar" y ve que debe cancelar y reservar de nuevo.
- **Given** el cliente eligió un horario nuevo que se ocupó antes de confirmar, **When** confirma, **Then** ve un mensaje claro, **conserva su turno original sin cambios** y puede elegir otro horario.
- **Given** faltan menos de N horas para el inicio, **When** el cliente abre el link, **Then** no puede cambiar el turno y ve un mensaje que le indica que contacte al local, con el teléfono de contacto del local (CU-16).
- **Given** el turno ya pasó o fue cancelado, **When** el cliente abre el link, **Then** ve el estado del turno y no puede cambiarlo.

### CU-07 — Cancelar el turno desde el link privado

**Como** cliente, **quiero** cancelar mi turno desde el link **para** liberar el horario si no puedo ir.

- **Given** faltan N horas o más para el inicio (N = anticipación mínima, CU-15), **When** el cliente toca "Cancelar" y lo confirma en un segundo paso, **Then** el turno queda cancelado, el horario vuelve a estar libre y el cliente ve que la cancelación se realizó, con un aviso que le sugiere borrar el evento de su calendario.
- **Given** el cliente toca "Cancelar", **When** no confirma el segundo paso, **Then** el turno sigue vigente.
- **Given** faltan menos de N horas para el inicio, **When** el cliente abre el link, **Then** no puede cancelar y ve un mensaje que le indica que contacte al local, con el teléfono de contacto del local (CU-16).
- **Given** el cliente perdió su link, **When** llama al teléfono del local, **Then** el admin puede cambiar o cancelar el turno desde el panel (CU-13).

### CU-08 — Ingresar al panel del local

**Como** admin, **quiero** un acceso protegido **para** que solo el personal autorizado gestione la agenda.

- **Given** un usuario no autenticado, **When** intenta abrir cualquier pantalla del panel, **Then** se le pide iniciar sesión y no ve datos de la agenda ni de clientes.
- **Given** un admin con credenciales válidas, **When** inicia sesión, **Then** accede al panel.
- **Given** credenciales inválidas, **When** intenta iniciar sesión, **Then** ve un mensaje de error genérico y no accede.
- El mecanismo de autenticación lo define el Architect. Referencia (§10): usuario y contraseña, sin recuperación por email; el equipo resetea la contraseña a mano.

### CU-09 — Gestionar servicios

**Como** admin, **quiero** cargar los servicios **para** que el cliente vea qué reservar y cuánto dura.

- **Given** el admin está en el panel, **When** crea un servicio con nombre, duración (en minutos) y precio de referencia, **Then** el servicio aparece en la web de reservas.
- **Given** el admin deja el nombre vacío o la duración en cero, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia la duración de un servicio, **When** guarda, **Then** aplica a las reservas nuevas y los turnos ya confirmados conservan su duración.
- **Given** el admin desactiva un servicio, **When** el cliente ve la lista, **Then** ya no aparece y los turnos ya confirmados con ese servicio se mantienen.
- **Given** el admin indica qué peluqueros hacen un servicio, **When** el cliente lo reserva, **Then** solo ve esos peluqueros.
- **Given** un servicio no tiene ningún peluquero activo asignado, **When** el cliente ve la lista de servicios, **Then** ese servicio no se ofrece; **And** el panel le indica al admin que el servicio no tiene peluqueros asignados.

### CU-10 — Gestionar peluqueros y su horario semanal

**Como** admin, **quiero** cargar a cada peluquero con su horario **para** que la agenda refleje cuándo trabaja cada uno.

- **Given** el admin está en el panel, **When** crea un peluquero con nombre, los servicios que hace y su horario semanal (por día: uno o más rangos, por ejemplo 9–13 y 15–19), **Then** el peluquero aparece en la reserva de esos servicios con horarios libres solo dentro de esos rangos.
- **Given** el admin crea o edita un peluquero sin asignarle servicios, **When** guarda, **Then** el peluquero no aparece en la reserva hasta que tenga al menos un servicio, y el panel se lo indica.
- **Given** el admin carga un rango con hora de fin anterior o igual a la de inicio, o rangos superpuestos en el mismo día, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia el horario semanal y hay turnos futuros que quedan fuera del nuevo horario, **When** guarda, **Then** los turnos no se borran ni se modifican.
- **(H2)** **Given** el cambio de horario deja turnos futuros fuera del nuevo horario, **When** guarda, **Then** el admin ve en el panel cuáles quedaron afectados para moverlos o cancelarlos (CU-13).
- **Given** el admin desactiva un peluquero, **When** el cliente reserva, **Then** ese peluquero no aparece y sus turnos futuros se mantienen.
- **(H2)** **Given** el peluquero desactivado tiene turnos futuros, **When** el admin lo desactiva, **Then** ve en el panel cuáles son para moverlos o cancelarlos (CU-13).

### CU-11 — Gestionar bloqueos (feriados y ausencias)

**Como** admin, **quiero** bloquear días u horarios **para** que nadie reserve cuando el local está cerrado o un peluquero falta.

- **Given** el admin crea un bloqueo para todo el local (por ejemplo, un feriado) en una fecha o rango, **When** el cliente consulta horarios, **Then** no ve horarios libres en ese período para ningún peluquero.
- **Given** el admin crea un bloqueo para un peluquero (por ejemplo, una ausencia), **When** el cliente consulta horarios, **Then** no ve horarios de ese peluquero en ese período.
- **Given** hay turnos confirmados dentro del período a bloquear, **When** el admin crea el bloqueo, **Then** ve en el panel cuáles son; los turnos no se cancelan automáticamente y el admin decide moverlos o cancelarlos.
- **Given** el admin elimina un bloqueo, **When** el cliente consulta horarios, **Then** esos horarios vuelven a estar libres.

### CU-12 — Ver la agenda del día y de la semana

**Como** admin, **quiero** ver la agenda **para** saber quién viene, con quién y cuándo.

- **Given** el admin está en el panel, **When** abre la agenda del día, **Then** ve los turnos de ese día por peluquero, con hora, duración, servicio, nombre del cliente, contacto (si lo dejó), origen (web o panel) y estado.
- **Given** el admin cambia a la vista semanal, **When** se muestra, **Then** ve los turnos de los 7 días por peluquero y puede navegar a semanas anteriores y siguientes.
- **Given** el admin filtra por peluquero, **When** aplica el filtro, **Then** solo ve los turnos de ese peluquero.
- **Given** un cliente reservó desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el turno nuevo.
- **(H2)** **Given** un cliente cambió o canceló desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el cambio reflejado.
- **(H2)** **Given** un turno fue cancelado, **When** el admin ve la agenda, **Then** puede distinguirlo de los vigentes y su horario figura como libre.

### CU-13 — Cargar, mover y cancelar turnos desde el panel

**Como** admin, **quiero** gestionar turnos a mano **para** registrar los que llegan por teléfono y atender a quien perdió su link.

- **Given** un cliente llama o viene al local, **When** el admin carga un turno con servicio, peluquero, horario, nombre y teléfono, **Then** el turno queda en la agenda con origen "panel" y respeta las mismas reglas de no superposición.
- *(Propuesta P-6)* **Given** el admin carga un turno a mano, **When** deja el teléfono vacío, **Then** ve que el teléfono es obligatorio y no se crea el turno (el cliente llamó o vino en persona, así que el dato está disponible).
- **Given** el admin intenta cargar o mover un turno a un horario que se superpone con otro del mismo peluquero, **When** guarda, **Then** ve el error y el turno no se crea ni se mueve. Esto no se puede forzar (RN-01).
- **Given** el admin carga o mueve un turno a un horario fuera del horario semanal del peluquero o dentro de un bloqueo, **When** guarda, **Then** el sistema le advierte el motivo y le pide confirmación; si confirma, el turno se guarda; si no, no se guarda.
- **Given** el admin mueve un turno a otro horario (puede ser con otro peluquero), **When** guarda, **Then** el turno queda en el nuevo horario, el anterior se libera y el link privado del cliente sigue funcionando y muestra el turno actualizado.
- **Given** el admin cancela un turno, **When** confirma, **Then** el turno queda cancelado, el horario se libera y el link privado del cliente muestra el turno como cancelado.
- *(Propuesta D-7)* **Given** el admin carga, mueve o cancela un turno, **When** lo hace, **Then** no aplica la anticipación mínima (solo rige para el cliente).
- **Given** el admin mueve o cancela un turno, **When** lo hace, **Then** no se envía ninguna notificación; el panel muestra el teléfono del cliente (si lo tiene) para que el local lo contacte por su cuenta (ver P-6).
- **Given** el admin carga un turno a mano, **When** se guarda, **Then** el panel muestra el link privado de ese turno para que el admin pueda dictárselo al cliente si lo pide.

### CU-14 — Marcar asistencia

**Como** admin, **quiero** marcar si el cliente vino **para** medir las ausencias.

- **Given** un turno vigente cuya hora de inicio ya pasó, **When** el admin lo marca "asistió" o "no asistió", **Then** el turno guarda esa marca y se cuenta en la tasa de ausencias.
- **Given** un turno futuro, **When** el admin intenta marcar asistencia, **Then** no puede hacerlo.
- **Given** el admin se equivocó al marcar, **When** cambia la marca, **Then** queda la última marca.
- **Given** hay turnos pasados sin marcar, **When** el admin abre la agenda, **Then** los ve identificados como "sin marcar" para completarlos.
- **Given** un turno cancelado, **When** el admin ve la agenda, **Then** no se puede marcar asistencia y no cuenta en la tasa de ausencias.

### CU-15 — Configurar la anticipación mínima

**Como** admin, **quiero** definir con cuánta anticipación el cliente puede cambiar o cancelar **para** proteger la agenda de cambios de último momento.

- **Given** el local no configuró nada, **When** un cliente intenta cambiar o cancelar, **Then** rige la anticipación mínima por defecto (propuesta: 2 h, P-2).
- **Given** el admin configura la anticipación en N horas, **When** un cliente intenta cambiar o cancelar y faltan N horas o más para el inicio, **Then** puede hacerlo.
- **Given** el admin configura la anticipación en N horas, **When** un cliente intenta cambiar o cancelar y faltan menos de N horas para el inicio, **Then** no puede y ve el mensaje de contactar al local con su teléfono de contacto (CU-16).
- **Given** el admin ingresa un valor negativo o no numérico, **When** intenta guardar, **Then** ve el error y no se guarda.

### CU-16 — Configurar datos del local

**Como** admin, **quiero** cargar el nombre, la dirección y el teléfono de contacto del local **para** que el cliente los vea en la confirmación y sepa a dónde ir y a quién llamar.

- **Given** el admin está en el panel, **When** carga nombre, dirección y teléfono de contacto del local y guarda, **Then** esos datos se muestran en la web de reservas y en la confirmación del turno (CU-05).
- **Given** el admin deja vacío el nombre, la dirección o el teléfono de contacto, **When** intenta guardar, **Then** ve el error junto al campo y no se guarda.
- **Given** el admin cambia el teléfono de contacto, **When** un cliente ve un mensaje de "contactá al local", **Then** el mensaje muestra el teléfono nuevo.
- **Given** cualquier texto al cliente que le indique contactar al local, **When** se muestra, **Then** incluye el teléfono de contacto cargado en este CU.
- **Given** el admin cambia la dirección, **When** el cliente usa "Agregar al calendario", **Then** el evento lleva la dirección vigente.

## 6. Reglas de negocio

| ID | Regla |
|---|---|
| RN-01 | Un peluquero **nunca** tiene dos turnos vigentes superpuestos, sin importar el origen (web o panel). |
| RN-02 | Si dos reservas o cambios simultáneos piden el mismo horario del mismo peluquero, se confirma **una sola**; la otra recibe un mensaje claro para elegir otro horario. |
| RN-03 | La duración del turno la define el servicio, con el valor vigente al momento de reservar. |
| RN-04 | Un horario está libre si el peluquero hace el servicio, el horario cae completo dentro de su horario semanal, no hay bloqueos del local ni del peluquero y no se superpone con otro turno vigente. |
| RN-05 | Todos los horarios se expresan en la zona horaria del local. |
| RN-06 | El cliente no crea cuenta. Nombre obligatorio; teléfono y email opcionales (el teléfono se recomienda en la web). *(Propuesta P-6)* En los turnos que carga el admin, el teléfono es obligatorio. |
| RN-07 | Cada turno tiene un link privado, difícil de adivinar, que permite ver solo ese turno (H1) y cambiarlo o cancelarlo (H2). El link no cambia aunque el cliente o el admin muevan el turno, y siempre muestra el estado vigente. |
| RN-08 | El cliente puede cambiar o cancelar desde el link si faltan **N horas o más** para el inicio (N = anticipación mínima configurada, 2 h por defecto, P-2); con **menos de N horas**, no. *(Propuesta D-7)* El admin no tiene esa restricción. |
| RN-08b | Desde el link, el cliente solo cambia día y horario, con el mismo servicio y el mismo peluquero (o "cualquiera"). Para cambiar el servicio, cancela y reserva de nuevo. |
| RN-09 | Si al cambiar el turno el horario nuevo ya no está libre, el cliente conserva el turno original sin cambios. |
| RN-10 | Los turnos cancelados liberan el horario y no cuentan en la tasa de ausencias. |
| RN-11 | Solo se puede marcar asistencia en turnos vigentes cuya hora de inicio ya pasó. |
| RN-12 | El sistema no envía notificaciones de ningún tipo (email, SMS, WhatsApp, push). |
| RN-13 | El precio es solo de referencia: no se cobra ni se pide seña. |
| RN-14 | No se puede reservar en horarios pasados. Cuánto hacia adelante se puede reservar (ventana de reserva) es una pregunta abierta (P-5; tentativo: 30 días). |
| RN-15 | Un servicio sin peluqueros activos asignados no se ofrece al cliente. Un peluquero sin servicios asignados no aparece en la reserva. |
| RN-16 | El admin puede cargar o mover turnos fuera del horario semanal o sobre un bloqueo solo después de una advertencia y su confirmación. Nunca puede superponer turnos (RN-01). |
| RN-17 | Todo texto al cliente que indique contactar al local muestra el teléfono de contacto configurado (CU-16). |

## 7. Requisitos no funcionales

| ID | Requisito |
|---|---|
| RNF-01 | **Celular primero:** el flujo de reserva completo se puede hacer en un celular (pantalla de 360 px de ancho) sin zoom ni scroll horizontal. |
| RNF-02 | **Accesibilidad:** la web del cliente cumple WCAG 2.1 nivel AA (contraste, navegación por teclado, etiquetas en formularios, mensajes de error asociados al campo, compatibilidad con lector de pantalla). |
| RNF-03 | **Integridad de la agenda:** cero turnos superpuestos para un mismo peluquero, incluso con reservas simultáneas (RN-01, RN-02). |
| RNF-04 | **Privacidad:** los datos de clientes (nombre, teléfono, email) solo los ve el admin autenticado. El link privado no expone datos de otros turnos. Se guardan solo los datos necesarios. |
| RNF-05 | **Seguridad del panel:** todas las pantallas y acciones del admin requieren autenticación. |
| RNF-06 | **Rapidez percibida** *(propuesta, a validar por el Architect)*: los horarios libres se muestran en **menos de 3 s** en un celular con conexión 4G, medido desde que el cliente elige el día. Se exceptúa el primer acceso después de un rato de inactividad (arranque en frío señalado por el Architect). |
| RNF-07 | **Costo:** el MVP opera con costo de infraestructura US$ 0 en la etapa de validación (Vercel Hobby, D-9, §10). Antes del uso comercial se revisa la licencia. |
| RNF-08 | **Medición:** se registran los datos necesarios para calcular las métricas de §3 (origen del turno, inicio y confirmación de reserva, asistencia). |
| RNF-09 | **Idioma:** interfaz en español. |

## 8. Fuera de alcance del MVP

- **Notificaciones** de cualquier tipo: email de confirmación, recordatorio, avisos por email, WhatsApp o SMS (iteración futura).
- Pagos y señas.
- Varias sedes y varias zonas horarias.
- Fidelización, puntos o promociones.
- Reseñas y calificaciones.
- App nativa (iOS/Android).
- Lista de espera.
- Cuentas de cliente e historial del cliente.
- Integración con Google Calendar (u otros calendarios) más allá del botón "Agregar al calendario".
- Acceso propio de los peluqueros al sistema (lo gestiona el admin).

## 9. Decisiones

| ID | Decisión | Por qué | Quién |
|---|---|---|---|
| D-1 | El MVP **no envía ninguna notificación** (ni confirmación, ni recordatorio, ni links por email o WhatsApp). | Cero fricción y cero costo para validar rápido; evita proveedores, plantillas y costos de mensajería. | Ezequiel Avila |
| D-2 | **Autogestión por link privado** mostrado en pantalla + botón "Agregar al calendario" que reemplaza al recordatorio (opción A del Architect). Si el cliente pierde el link, llama al local y el admin gestiona el turno. | Permite cambiar y cancelar sin cuentas ni notificaciones; el calendario del celular cumple el rol del recordatorio sin costo. | CPO (sobre la opción A del Architect) |
| D-3 | **Sin cuenta de cliente**; solo nombre obligatorio, teléfono y email opcionales (ver P-6 para los turnos que carga el admin). | Menos pasos, mayor conversión, menos datos personales. | CPO |
| D-4 | **Una sede, varios peluqueros, una zona horaria.** | Cubre el caso real de una peluquería y simplifica la validación. | CPO |
| D-5 | **Precio solo de referencia**, sin cobro. | Pagos y señas suman complejidad y costo; se evalúan después de medir ausencias. | CPO |
| D-6 | Al cambiar un turno, si el horario nuevo se ocupó, el cliente **conserva el original**. | El cliente nunca se queda sin turno por intentar cambiarlo. | CPO |
| D-7 | *(Propuesta)* La anticipación mínima rige solo para el cliente; el admin puede gestionar turnos en cualquier momento. | El local necesita resolver casos de último momento (por ejemplo, un cliente que llama). | Propuesta PO; la valida Ezequiel en P-2 |
| D-8 | **H1 es una demo interna con datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. **H2 completa el MVP operable**, que es el que se usa en un piloto real. En H1 el link privado abre el resumen en solo lectura; cambiar y cancelar llegan en H2. | Es la opción más barata y coherente con Vercel Hobby, que es solo para uso no comercial. Además, operar con clientes reales sin que puedan cancelar y sin que el local pueda cargar turnos a mano empeoraría el problema que se quiere resolver. | CPO (resuelve B1; Ezequiel la confirma en P-4) |
| D-9 | **Infraestructura: Vercel Hobby, US$ 0** (factibilidad del Architect). **Condición:** antes de que una peluquería real lo use comercialmente (piloto de H2) hay que volver a decidir la licencia, porque Hobby es solo para uso no comercial. | Costo cero para la demo interna y la validación; coherente con D-8. | Ezequiel Avila (aprobación escrita: "Apruebo la factibilidad Hobby") |
| D-10 | Desde el link, el cliente cambia solo **día y horario** (mismo servicio y peluquero o "cualquiera"); para cambiar el servicio cancela y reserva de nuevo. El link no cambia. | Es lo más simple para el MVP. | CPO (pedido del PO Reviewer) |
| D-11 | El admin puede cargar o mover turnos fuera del horario semanal o sobre un bloqueo con advertencia y confirmación, pero nunca superponerlos. | El local resuelve excepciones reales (un cliente que viene fuera de hora) sin romper la regla de no superposición. | CPO (pedido del PO Reviewer) |

## 10. Factibilidad (referencia)

Factibilidad del Architect, **aprobada por Ezequiel Avila** con el plan Hobby (D-9). Este documento no la decide; la registra como referencia.

- Entra en **Vercel Hobby a US$ 0**, sin dominio propio.
- **Estimación actualizada del Architect:** H1 de **1,5 a 2 semanas**; MVP completo (H1 + H2) de **3 a 4 semanas**. Confianza **media**.
- **Riesgo / condición:** Hobby es solo para uso no comercial. Por eso H1 es una demo interna sin clientes reales (D-8), y antes de que una peluquería real lo use comercialmente hay que volver a decidir la licencia (D-9).
- **Nota del Architect sobre el admin:** entra con usuario y contraseña. Como el MVP no envía emails, no hay recuperación de contraseña: si el admin la olvida, el equipo la resetea a mano (ver CU-08).
- Puede haber un **arranque en frío** en el primer acceso después de un rato de inactividad (ver RNF-06).

Los detalles técnicos (arquitectura, autenticación del admin, manejo de concurrencia, link privado, archivo de calendario) los define el Architect en su documentación.

## 11. Preguntas abiertas

| ID | Pregunta | Propuesta | Dueño | Impacta |
|---|---|---|---|---|
| P-1 | ¿Se confirman el objetivo y las métricas de §3? ¿Qué metas numéricas? | Objetivo y métricas de §3. | Ezequiel Avila | §3, RNF-08 |
| P-2 | ¿Cuál es la anticipación mínima por defecto para cambiar o cancelar? ¿Se confirma D-7 (la anticipación no rige para el admin)? | 2 h, configurable por el local; se puede si faltan N horas o más. Sí a D-7. | Ezequiel Avila | CU-06, CU-07, CU-13, CU-15, RN-08, D-7 |
| P-4 | ¿Se confirma la definición de hitos de D-8? | Sí: **H1 = demo interna con datos de prueba** (CU-01 a CU-05, CU-08 a CU-10, CU-12 y CU-16; link en solo lectura). **H2 = MVP operable para el piloto real** (CU-06, CU-07, CU-11, CU-13, CU-14, CU-15 y los criterios marcados (H2)). | Ezequiel Avila | Plan de entrega, §5, D-8 |
| P-5 | ¿Con cuánta anticipación máxima se puede reservar (por ejemplo, 30 días)? | 30 días. | Ezequiel Avila | CU-02, RN-14 |
| P-6 | Cuando el local mueve o cancela un turno, un cliente sin teléfono no se entera (el MVP no notifica). ¿Cómo se maneja? | En la web el teléfono sigue opcional pero se recomienda ("para avisarte si hay un cambio"); en los turnos que carga el admin el teléfono es obligatorio. Alternativa: aceptar el riesgo. Los criterios marcados "(propuesta P-6)" asumen la propuesta. | Ezequiel Avila | CU-03, CU-13, RN-06 |
| P-7 | Con "cualquiera disponible", si hay varios peluqueros libres, ¿con qué criterio se asigna (por ejemplo, el de menos turnos en el día, rotación u orden fijo)? | Por ahora basta con que sea un peluquero que hace ese servicio y está libre; el criterio se define más adelante. | Ezequiel Avila con el CDO (diseño) | CU-03, CU-04 |
| P-8 | ¿Cuántos admins usan el panel y si comparten una cuenta o tienen cuentas individuales? | Uno o pocos admins; el MVP no tiene roles distintos. | Architect y Ezequiel Avila | CU-08, §3 |
