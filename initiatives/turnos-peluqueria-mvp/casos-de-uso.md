# Reserva de turnos para peluquería — MVP

- **Etapa:** Discovery, etapa 1 (problema y casos de uso)
- **Dueño de producto:** Ezequiel Avila
- **Estado:** borrador, ronda 3 (registra las respuestas de Ezequiel a P-1, P-2, P-4, P-5, P-7 y P-9, y las decisiones del CPO sobre las preguntas del diseño, D-13 a D-22). Solo queda abierta P-8.
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

**Supuesto sobre los admins:** el panel (la parte web con usuario y contraseña que usa el local) lo usan uno o pocos admins (dueño o encargado). Propuesta del Architect (P-8): una cuenta por persona, creada a mano por el equipo, para saber quién cargó, movió o canceló cada turno. El MVP **no tiene roles distintos** (todos los admins pueden hacer lo mismo) ni pantalla para administrar usuarios. Queda abierto cuántas personas del local usan el panel (P-8).

## 3. Objetivo

**Objetivo del MVP (confirmado por Ezequiel, D-16):** que el cliente **reserve solo**, sin llamar, y que **no haya reservas duplicadas**.

- La ausencia de turnos superpuestos es una **regla de negocio** (RN-01, RN-02, RNF-03), no una métrica: se cumple siempre.
- **Métricas: fuera del MVP por decisión de Ezequiel (D-16); se retoman más adelante.** El MVP no define metas numéricas ni registra datos con el fin de medir.
- *Referencia futura (no se implementa en el MVP):* las métricas que se habían propuesto eran % de turnos online, tasa de ausencias, conversión de reserva y llamadas o mensajes por semana para reservar (relevados con el local). Se reconsideran cuando se retome la medición.

## 4. Alcance del MVP

- **Una peluquería**, **una sola sede**, **varios peluqueros**, cada uno con su agenda.
- **Una sola zona horaria:** la del local. Todos los horarios se muestran y se guardan en esa zona.
- **Cliente** (web pensada para celular y accesible):
  - ve los servicios con duración y precio de referencia, si el local lo cargó (no se cobra; si no hay precio, ve "Consultá el precio en el local", D-19);
  - reserva: servicio → peluquero o "cualquiera disponible" → horario (inicios cada 15 minutos, hasta 30 días hacia adelante) → datos → confirmar;
  - no crea cuenta; datos: nombre (obligatorio), teléfono y email (opcionales; el teléfono se recomienda en la web, D-12);
  - al confirmar ve el resumen, un **link privado** (con la indicación de guardarlo) y un botón **"Agregar al calendario"**;
  - desde el link privado cambia el **día y el horario** (mismo servicio y mismo peluquero o "cualquiera") o cancela, si faltan **2 horas o más** para el inicio (anticipación mínima fija, D-13). Para cambiar el servicio, cancela y vuelve a reservar.
- **Admin del local** (con login; el mecanismo lo define el Architect):
  - configura los datos del local (nombre, dirección, teléfono de contacto);
  - carga servicios, peluqueros, el horario semanal de cada peluquero y bloqueos (feriados, ausencias);
  - ve la agenda del día y de la semana;
  - carga turnos a mano, cancela y mueve turnos;
  - marca cada turno pasado como "asistió" o "no asistió".
- **El MVP no envía ninguna notificación** (ver D-1).

## 5. Casos de uso

Hitos (D-8, confirmados por Ezequiel):

- **Hito 1 (H1) — demo interna:** reserva + agenda con **datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. H1 no depende de nada de H2: la confirmación muestra el resumen y el link privado abre ese resumen en **solo lectura** (los botones de cambiar y cancelar llegan en H2).
- **Hito 2 (H2) — MVP operable:** completa lo necesario para operar con clientes reales en un piloto (cambiar y cancelar desde el link con la anticipación mínima fija de 2 horas, bloqueos, gestión manual de turnos y asistencia). **H1:** CU-01 a CU-05, CU-08 a CU-10, CU-12 y CU-16. **H2:** CU-06, CU-07, CU-11, CU-13, CU-14 y los criterios marcados (H2).

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
| CU-15 | *Eliminado (D-13): la anticipación mínima es fija, no se configura* | — | — |
| CU-16 | Configurar datos del local | Admin | 1 |

### CU-01 — Ver servicios

**Como** cliente, **quiero** ver los servicios con su duración y precio de referencia **para** elegir qué reservar.

- **Given** el local tiene servicios activos, **When** el cliente entra a la web de reservas, **Then** ve la lista de servicios activos con nombre, duración y precio de referencia, y una aclaración de que el precio es orientativo y se paga en el local.
- **Given** un servicio activo no tiene precio de referencia cargado, **When** el cliente ve la lista, **Then** en lugar del precio ve "Consultá el precio en el local" (D-19).
- **Given** un servicio está inactivo, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** el local no tiene servicios activos, **When** el cliente entra, **Then** ve un mensaje que indica que por ahora no hay reservas online y que puede contactar al local, con el teléfono de contacto del local (CU-16).
- **Given** un servicio activo no tiene ningún peluquero activo asignado, **When** el cliente ve la lista, **Then** ese servicio no aparece.

### CU-02 — Ver horarios libres

**Como** cliente, **quiero** ver solo los horarios realmente libres **para** no elegir uno que no se puede.

- **Given** el cliente eligió un servicio de 45 min y un peluquero, **When** ve los horarios de un día, **Then** solo ve horarios de inicio en los que ese peluquero trabaja, no tiene bloqueos y tiene 45 min libres seguidos sin superponerse con otro turno.
- **Given** el cliente eligió "cualquiera disponible", **When** ve los horarios de un día, **Then** ve cada horario en el que al menos un peluquero que hace ese servicio está libre, sin horarios repetidos.
- **Given** el cliente ve los horarios de un día, **When** se muestran, **Then** todos los horarios de inicio caen en múltiplos de 15 minutos de la hora (:00, :15, :30, :45) (RN-19).
- **Given** un horario ya empezó o está en el pasado, **When** el cliente ve los horarios, **Then** ese horario no aparece.
- **Given** un día no tiene horarios libres, **When** el cliente lo consulta, **Then** ve "sin horarios disponibles" y puede pasar a otro día.
- **Given** el cliente consulta horarios, **When** se muestran, **Then** están en la zona horaria del local.
- **Given** hoy es 1 de marzo, **When** el cliente navega los días, **Then** puede elegir desde hoy hasta el 31 de marzo inclusive (hoy + 30 días corridos) (RN-14, D-14).
- **Given** el cliente intenta ver un día posterior a hoy + 30 días, **When** navega, **Then** no puede elegirlo y ve que se puede reservar hasta 30 días hacia adelante.

### CU-03 — Reservar un turno

**Como** cliente, **quiero** reservar en pocos pasos y sin crear cuenta **para** asegurar mi turno sin llamar.

- **Given** el cliente eligió servicio, peluquero (o "cualquiera disponible") y un horario libre, **When** ingresa su nombre y confirma, **Then** el turno queda confirmado en la agenda del peluquero con la duración del servicio y el cliente ve la confirmación (CU-05).
- **Given** el cliente eligió "cualquiera disponible", **When** confirma, **Then** el sistema asigna un peluquero que hace ese servicio y está libre en ese horario según RN-20, y la confirmación muestra cuál es.
- **Given** el cliente eligió "cualquiera disponible" y en ese horario están libres Ana (3 turnos vigentes ese día) y Beto (1 turno vigente ese día), **When** confirma, **Then** el turno se asigna a Beto (el de menos turnos ese día) (RN-20, D-15).
- **Given** el cliente eligió "cualquiera disponible" y en ese horario están libres Ana y Beto con la misma cantidad de turnos vigentes ese día, y Ana fue cargada antes que Beto en el panel, **When** confirma, **Then** el turno se asigna a Ana (desempate por orden de carga, RN-20).
- **Given** el cliente deja el nombre vacío, **When** intenta confirmar, **Then** ve un mensaje junto al campo que indica que el nombre es obligatorio y no se crea el turno.
- **Given** el cliente no ingresa teléfono ni email, **When** confirma con nombre válido, **Then** el turno se crea igual.
- **Given** el cliente está en el paso de datos, **When** ve el campo teléfono, **Then** figura como opcional con un texto que recomienda dejarlo (por ejemplo, "para que el local pueda contactarte si hay un cambio") (D-12).
- **Given** el cliente ingresa un email o teléfono con formato inválido, **When** intenta confirmar, **Then** ve un mensaje que indica qué corregir y no se crea el turno.
- **Given** el cliente está en cualquier paso, **When** vuelve atrás, **Then** conserva lo que ya eligió en los pasos anteriores.

### CU-04 — Resolver una reserva simultánea del mismo horario

**Como** cliente, **quiero** saber claramente si mi horario se ocupó mientras reservaba **para** elegir otro sin confusión.

- **Given** dos clientes confirman al mismo tiempo el mismo horario con el mismo peluquero, **When** se procesan ambas reservas, **Then** se confirma una sola y en la agenda no queda ningún turno superpuesto.
- **Given** la reserva de un cliente no se pudo confirmar porque el horario se ocupó, **When** recibe la respuesta, **Then** ve un mensaje claro ("Ese horario se acaba de ocupar, elegí otro"), los horarios libres actualizados y conserva el servicio y los datos que ya cargó.
- **Given** el cliente eligió "cualquiera disponible" y el peluquero que se le iba a asignar se ocupó, **When** queda otro peluquero que hace ese servicio y está libre en ese horario, **Then** se le asigna ese (si queda más de uno, según RN-20) y la reserva se confirma; **When** no queda ninguno, **Then** ve el mensaje del criterio anterior.

### CU-05 — Ver la confirmación y agregar al calendario

**Como** cliente, **quiero** ver el resumen, guardar el link y agendar el turno **para** recordarlo y gestionarlo sin que me envíen nada.

- **Given** el turno se confirmó, **When** el cliente ve la pantalla de confirmación, **Then** ve servicio, peluquero, fecha, hora, duración, precio de referencia (o "Consultá el precio en el local" si el servicio no tiene precio, D-19) y nombre, dirección y teléfono de contacto del local (CU-16).
- **Given** el turno se confirmó, **When** el cliente ve la confirmación, **Then** ve su link privado, una indicación visible de que lo guarde (porque no se enviará por ningún medio) y una forma de copiarlo.
- **Given** el cliente abre su link privado en H1, **When** la página carga, **Then** ve el resumen de su turno en solo lectura con el estado "Confirmado" y sin botones de cambiar ni cancelar (llegan en H2, CU-06 y CU-07) (D-18).
- **(H2)** **Given** el admin movió el turno desde el panel (CU-13), **When** el cliente abre su link privado, **Then** ve el turno con los datos nuevos y un aviso del tipo "El local cambió tu turno: ahora es el <día> a las <hora> con <peluquero>" (D-18).
- **(H2)** **Given** el admin canceló el turno desde el panel (CU-13), **When** el cliente abre su link privado, **Then** ve que el turno está cancelado por el local, con el teléfono de contacto del local (CU-16), y no ve botones de cambiar ni cancelar (D-18).
- **Given** alguien abre un link inválido o inexistente, **When** la página carga, **Then** ve "Turno no encontrado" sin revelar datos de ningún turno.
- **Given** el cliente toca "Agregar al calendario", **When** se completa la acción, **Then** puede guardar el evento en el calendario de su celular con fecha, hora, duración, servicio, local y el link privado en la descripción.
- **Given** el turno se confirmó, **When** pasa cualquier tiempo, **Then** el sistema no envía email, SMS ni WhatsApp al cliente.

### CU-06 — Cambiar el turno desde el link privado

**Como** cliente, **quiero** cambiar el día y el horario de mi turno desde el link **para** no tener que llamar.

Alcance del cambio en el MVP: solo **día y horario**, con el **mismo servicio** y el **mismo peluquero** (o "cualquiera disponible", si así reservó). Para cambiar el servicio, el cliente cancela y vuelve a reservar. Es lo más simple para el MVP.

- **Given** el cliente abre su link privado de un turno futuro, **When** la página carga, **Then** ve el resumen del turno (CU-05) y las opciones "Cambiar" y "Cancelar".
- **Given** faltan 2 horas o más para el inicio (anticipación mínima fija, RN-08), **When** el cliente elige un día y horario nuevos libres para el mismo servicio y peluquero (o "cualquiera") y confirma, **Then** el turno pasa al horario nuevo, el horario original queda libre y ve la confirmación actualizada. Si reservó con "cualquiera disponible", el peluquero del horario nuevo se asigna según RN-20.
- **Given** el turno empieza a las 18:00, **When** el cliente intenta cambiarlo a las 16:00 del mismo día, **Then** puede hacerlo (faltan exactamente 2 horas); **When** lo intenta a las 16:01, **Then** no puede.
- **Given** el cliente está eligiendo el horario nuevo, **When** navega los días, **Then** rigen la misma ventana de 30 días (RN-14) y los mismos inicios cada 15 minutos (RN-19) que al reservar.
- **Given** el cambio se confirmó, **When** el cliente ve la confirmación, **Then** el link privado sigue siendo el mismo, ve "Agregar al calendario" para el turno nuevo y un aviso que le sugiere borrar de su calendario el evento anterior.
- **Given** el cliente quiere otro servicio, **When** busca cómo cambiarlo, **Then** no puede hacerlo desde "Cambiar" y ve que debe cancelar y reservar de nuevo.
- **Given** el cliente eligió un horario nuevo que se ocupó antes de confirmar, **When** confirma, **Then** ve un mensaje claro, **conserva su turno original sin cambios** y puede elegir otro horario.
- **Given** faltan menos de 2 horas para el inicio, **When** el cliente abre el link, **Then** no puede cambiar el turno y ve un mensaje que le indica que contacte al local, con el teléfono de contacto del local (CU-16).
- **Given** el turno ya pasó o fue cancelado, **When** el cliente abre el link, **Then** ve el estado del turno y no puede cambiarlo.

### CU-07 — Cancelar el turno desde el link privado

**Como** cliente, **quiero** cancelar mi turno desde el link **para** liberar el horario si no puedo ir.

- **Given** faltan 2 horas o más para el inicio (anticipación mínima fija, RN-08), **When** el cliente toca "Cancelar" y lo confirma en un segundo paso, **Then** el turno queda cancelado, el horario vuelve a estar libre y el cliente ve que la cancelación se realizó, con un aviso que le sugiere borrar el evento de su calendario.
- **Given** el cliente toca "Cancelar", **When** no confirma el segundo paso, **Then** el turno sigue vigente.
- **Given** faltan menos de 2 horas para el inicio, **When** el cliente abre el link, **Then** no puede cancelar y ve un mensaje que le indica que contacte al local, con el teléfono de contacto del local (CU-16).
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
- **Given** el admin crea un servicio con nombre y duración válida pero sin precio de referencia, **When** guarda, **Then** el servicio se guarda y la web muestra "Consultá el precio en el local" en lugar del precio (D-19).
- **Given** el admin deja el nombre vacío, **When** intenta guardar, **Then** ve el error junto al campo y no se guarda.
- **Given** el admin ingresa una duración entre 5 y 480 minutos que es múltiplo de 5 (por ejemplo, 5, 45 o 480), **When** guarda, **Then** el servicio se guarda (D-20).
- **Given** el admin ingresa una duración vacía, menor que 5, mayor que 480 o que no es múltiplo de 5 (por ejemplo, 0, 3, 47 o 485), **When** intenta guardar, **Then** ve un error junto al campo que indica el rango y el múltiplo válidos, y no se guarda.
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

- **Given** el admin está en el panel, **When** abre la agenda del día, **Then** ve los turnos de ese día por peluquero, con hora, duración, servicio, nombre del cliente, contacto (si lo dejó), origen (web o, **(H2)**, panel; en H1 no hay carga manual, CU-13) y estado.
- **Given** el admin cambia a la vista semanal, **When** se muestra, **Then** ve los turnos de los 7 días por peluquero, como una lista agrupada por día, y puede navegar a semanas anteriores y siguientes.

> **Nota (D-22):** en H1 la agenda semanal es una **lista por día**, no una grilla horaria. En la demo de H1 se valida si hace falta una grilla; no es una pregunta bloqueante.

- **Given** el admin filtra por peluquero, **When** aplica el filtro, **Then** solo ve los turnos de ese peluquero.
- **Given** un cliente reservó desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el turno nuevo.
- **(H2)** **Given** un cliente cambió o canceló desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el cambio reflejado.
- **(H2)** **Given** un turno fue cancelado, **When** el admin ve la agenda, **Then** puede distinguirlo de los vigentes y su horario figura como libre.

### CU-13 — Cargar, mover y cancelar turnos desde el panel

**Como** admin, **quiero** gestionar turnos a mano **para** registrar los que llegan por teléfono y atender a quien perdió su link.

- **Given** un cliente llama o viene al local, **When** el admin carga un turno con servicio, peluquero, horario, nombre (y, si lo tiene, teléfono y email), **Then** el turno queda en la agenda con origen "panel" y respeta las mismas reglas de no superposición y de inicio cada 15 minutos (RN-19).
- **Given** el admin carga un turno a mano, **When** deja vacíos el teléfono y el email, **Then** el turno se crea igual (solo el nombre es obligatorio) (D-12).
- **Given** el admin intenta cargar o mover un turno a un horario que se superpone con otro del mismo peluquero, **When** guarda, **Then** ve el error y el turno no se crea ni se mueve. Esto no se puede forzar (RN-01).
- **Given** el admin carga o mueve un turno a un horario fuera del horario semanal del peluquero o dentro de un bloqueo, **When** guarda, **Then** el sistema le advierte el motivo y le pide confirmación; si confirma, el turno se guarda; si no, no se guarda.
- **Given** el admin mueve un turno a otro horario (puede ser con otro peluquero), **When** guarda, **Then** el turno queda en el nuevo horario, el anterior se libera y el link privado del cliente sigue funcionando y muestra el turno actualizado con el aviso "El local cambió tu turno: ahora es…" (CU-05, D-18).
- **Given** el admin cancela un turno, **When** confirma, **Then** el turno queda cancelado, el horario se libera y el link privado del cliente muestra que el local canceló el turno (CU-05, D-18).
- **Given** faltan menos de 2 horas para un turno, **When** el admin lo mueve o lo cancela, **Then** puede hacerlo: la anticipación mínima rige solo para el cliente (D-7).
- *(Propuesta PO, D-14)* **Given** el admin carga o mueve un turno a un día posterior a hoy + 30 días, **When** guarda, **Then** puede hacerlo: la ventana de 30 días rige solo para el cliente.
- **Given** el admin mueve o cancela un turno, **When** lo hace, **Then** no se envía ninguna notificación; el panel muestra el teléfono del cliente (si lo tiene) para que el local lo contacte por su cuenta (ver D-12).
- **Given** el admin carga un turno a mano, **When** se guarda, **Then** el panel muestra el link privado de ese turno para que el admin pueda dictárselo al cliente si lo pide.

### CU-14 — Marcar asistencia

**Como** admin, **quiero** marcar si el cliente vino **para** registrar quién falta sin avisar.

- **Given** un turno vigente cuya hora de inicio ya pasó, **When** el admin lo marca "asistió" o "no asistió", **Then** el turno guarda esa marca y se ve en la agenda.
- **Given** un turno futuro, **When** el admin intenta marcar asistencia, **Then** no puede hacerlo.
- **Given** el admin se equivocó al marcar, **When** cambia la marca, **Then** queda la última marca.
- **Given** hay turnos pasados sin marcar, **When** el admin abre la agenda, **Then** los ve identificados como "sin marcar" para completarlos.
- **Given** un turno cancelado, **When** el admin ve la agenda, **Then** no se puede marcar asistencia.

### CU-15 — Eliminado (D-13)

Este caso de uso se eliminó: la anticipación mínima para que el cliente cambie o cancele es **fija en 2 horas** para todos los locales y no se configura (D-13, RN-08). El ID se conserva para no romper referencias externas (por ejemplo, del diseño).

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
| RN-06 | El cliente no crea cuenta. Nombre obligatorio; teléfono y email opcionales, tanto en la web como en los turnos que carga el admin desde el panel; el teléfono se recomienda en la web (D-12). |
| RN-07 | Cada turno tiene un link privado, difícil de adivinar, que permite ver solo ese turno (H1) y cambiarlo o cancelarlo (H2). El link no cambia aunque el cliente o el admin muevan el turno, y siempre muestra el estado vigente: en H1, "Confirmado"; en H2, además, el aviso "El local cambió tu turno: ahora es…" si el admin lo movió, o que está cancelado si el admin lo canceló (D-18). |
| RN-08 | El cliente puede cambiar o cancelar desde el link si faltan **2 horas o más** para el inicio; con **menos de 2 horas**, no. El valor es **fijo** para todos los locales y no se configura (D-13). El admin no tiene esa restricción (D-7). |
| RN-09 | Si al cambiar el turno el horario nuevo ya no está libre, el cliente conserva el turno original sin cambios. |
| RN-10 | Los turnos cancelados liberan el horario y no se les puede marcar asistencia. |
| RN-11 | Solo se puede marcar asistencia en turnos vigentes cuya hora de inicio ya pasó. |
| RN-12 | El sistema no envía notificaciones de ningún tipo (email, SMS, WhatsApp, push). |
| RN-13 | El precio es solo de referencia: no se cobra ni se pide seña. Es **opcional** al crear el servicio; si falta, la web muestra "Consultá el precio en el local" (D-19). |
| RN-14 | No se puede reservar en horarios pasados. El cliente puede reservar, o cambiar su turno, desde hoy hasta **30 días corridos hacia adelante** (hoy + 30 días, inclusive) (D-14). *(Propuesta PO)* La ventana no limita al admin. |
| RN-15 | Un servicio sin peluqueros activos asignados no se ofrece al cliente. Un peluquero sin servicios asignados no aparece en la reserva. |
| RN-16 | El admin puede cargar o mover turnos fuera del horario semanal o sobre un bloqueo solo después de una advertencia y su confirmación. Nunca puede superponer turnos (RN-01). |
| RN-17 | Todo texto al cliente que indique contactar al local muestra el teléfono de contacto configurado (CU-16). |
| RN-18 | Desde el link, el cliente solo cambia día y horario, con el mismo servicio y el mismo peluquero (o "cualquiera"). Para cambiar el servicio, cancela y reserva de nuevo. |
| RN-19 | Los turnos empiezan en **intervalos de 15 minutos** (:00, :15, :30, :45), tanto los que reserva el cliente como los que carga o mueve el admin (D-21). |
| RN-20 | Con "cualquiera disponible", el turno se asigna, entre los peluqueros activos que hacen ese servicio y están libres en ese horario, al que tenga **menos turnos vigentes ese día** (sin contar cancelados). *(Propuesta PO)* **Desempate:** si empatan, se asigna al que fue cargado primero en el panel (orden de carga) (D-15). |
| RN-21 | La duración de un servicio va de **5 a 480 minutos**, en múltiplos de 5 (D-20). |

## 7. Requisitos no funcionales

| ID | Requisito |
|---|---|
| RNF-01 | **Celular primero:** el flujo de reserva completo se puede hacer en un celular (pantalla de 360 px de ancho) sin zoom ni scroll horizontal. |
| RNF-02 | **Accesibilidad:** la web del cliente cumple WCAG 2.1 nivel AA (contraste, navegación por teclado, etiquetas en formularios, mensajes de error asociados al campo, compatibilidad con lector de pantalla). |
| RNF-03 | **Integridad de la agenda:** cero turnos superpuestos para un mismo peluquero, incluso con reservas simultáneas (RN-01, RN-02). |
| RNF-04 | **Privacidad:** los datos de clientes (nombre, teléfono, email) solo los ve el admin autenticado. El link privado no expone datos de otros turnos. Se guardan solo los datos necesarios. |
| RNF-05 | **Seguridad del panel:** todas las pantallas y acciones del admin requieren autenticación. |
| RNF-06 | **Rapidez percibida** *(validada por el Architect)*: los horarios libres se muestran en **menos de 3 s** en un celular con conexión 4G, medido desde que el cliente elige el día. Se exceptúa el primer acceso después de un rato de inactividad (arranque en frío señalado por el Architect). Según el Architect es factible: lo esperable es menos de 1 s, con la app y la base de datos en la misma región. |
| RNF-07 | **Costo:** el MVP opera con costo de infraestructura US$ 0 en Vercel Hobby (D-9, §10), incluido el piloto de H2, que se considera uso no comercial por decisión de Ezequiel (D-17). **Condición:** si el uso pasa a ser comercial, se revisa la licencia. |
| RNF-08 | *Eliminado (D-16): el MVP no tiene métricas; la medición queda fuera de alcance.* |
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
- **Métricas y medición** (% online, ausencias, conversión, llamadas): fuera del MVP por decisión de Ezequiel; se retoman más adelante (D-16).
- Configurar la anticipación mínima por local: es fija en 2 horas (D-13).

## 9. Decisiones

| ID | Decisión | Por qué | Quién |
|---|---|---|---|
| D-1 | El MVP **no envía ninguna notificación** (ni confirmación, ni recordatorio, ni links por email o WhatsApp). | Cero fricción y cero costo para validar rápido; evita proveedores, plantillas y costos de mensajería. | Ezequiel Avila |
| D-2 | **Autogestión por link privado** mostrado en pantalla + botón "Agregar al calendario" que reemplaza al recordatorio (opción A del Architect). Si el cliente pierde el link, llama al local y el admin gestiona el turno. | Permite cambiar y cancelar sin cuentas ni notificaciones; el calendario del celular cumple el rol del recordatorio sin costo. | CPO (sobre la opción A del Architect) |
| D-3 | **Sin cuenta de cliente**; solo nombre obligatorio, teléfono y email opcionales (ver D-12 para los turnos que carga el admin). | Menos pasos, mayor conversión, menos datos personales. | CPO |
| D-4 | **Una sede, varios peluqueros, una zona horaria.** | Cubre el caso real de una peluquería y simplifica la validación. | CPO |
| D-5 | **Precio solo de referencia**, sin cobro. | Pagos y señas suman complejidad y costo; se evalúan después del MVP. | CPO |
| D-6 | Al cambiar un turno, si el horario nuevo se ocupó, el cliente **conserva el original**. | El cliente nunca se queda sin turno por intentar cambiarlo. | CPO |
| D-7 | La anticipación mínima rige solo para el cliente; el admin puede gestionar turnos en cualquier momento. | El local necesita resolver casos de último momento (por ejemplo, un cliente que llama). | CPO (Ezequiel no la objetó al responder P-2) |
| D-8 | **H1 es una demo interna con datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. **H2 completa el MVP operable**, que es el que se usa en un piloto real. En H1 el link privado abre el resumen en solo lectura; cambiar y cancelar llegan en H2. | Es la opción más barata y coherente con Vercel Hobby (D-9, D-17). Además, operar con clientes reales sin que puedan cancelar y sin que el local pueda cargar turnos a mano empeoraría el problema que se quiere resolver. | CPO (resuelve B1); **confirmada por Ezequiel Avila** (P-4) |
| D-9 | **Infraestructura: Vercel Hobby, US$ 0** (factibilidad del Architect). **Condición:** Hobby es solo para uso no comercial. El uso actual, incluido el piloto de H2, se considera no comercial (D-17); si pasa a uso comercial, hay que volver a decidir la licencia. | Costo cero para la demo interna, la validación y el piloto; coherente con D-8. | Ezequiel Avila (aprobación escrita: "Apruebo la factibilidad Hobby") |
| D-10 | Desde el link, el cliente cambia solo **día y horario** (mismo servicio y peluquero o "cualquiera"); para cambiar el servicio cancela y reserva de nuevo. El link no cambia. | Es lo más simple para el MVP. | CPO (pedido del PO Reviewer) |
| D-11 | El admin puede cargar o mover turnos fuera del horario semanal o sobre un bloqueo con advertencia y confirmación, pero nunca superponerlos. | El local resuelve excepciones reales (un cliente que viene fuera de hora) sin romper la regla de no superposición. | CPO (pedido del PO Reviewer) |
| D-12 | En los turnos que **carga el admin**, el teléfono también es **opcional**: el local carga el turno con los mismos datos que la web (nombre obligatorio, teléfono y email opcionales). **Riesgo aceptado para el MVP:** si el local mueve o cancela el turno de un cliente sin teléfono, el cliente no se entera hasta que abra su link privado, si lo tiene (el MVP no notifica, D-1). | Menos fricción para el local y la misma lógica que la web. | Ezequiel Avila |
| D-13 | La **anticipación mínima** para que el cliente cambie o cancele es **2 horas, fija**, para siempre y sin configuración por local. Se elimina CU-15 (el ID se conserva marcado como eliminado). | Simplifica el MVP: no hace falta pantalla ni validaciones de configuración. | Ezequiel Avila (P-2) |
| D-14 | **Ventana de reserva: 30 días hacia adelante** (hoy + 30 días corridos, inclusive) para reservar y para cambiar desde el link. *(Propuesta PO: no limita al admin.)* | Evita reservas muy lejanas que el local no puede garantizar. | Ezequiel Avila (P-5); alcance al admin: propuesta PO |
| D-15 | Con **"cualquiera disponible"**, se asigna al peluquero que hace el servicio, está libre en ese horario y tiene **menos turnos vigentes ese día**. *(Propuesta PO)* **Desempate:** el que fue cargado primero en el panel. | Reparte la carga del día; el desempate por orden de carga es simple, determinístico y no requiere configuración. | Ezequiel Avila (P-7); desempate: propuesta PO |
| D-16 | **Por ahora sin métricas.** El objetivo del MVP es cualitativo: que el cliente reserve solo y sin duplicados. Las métricas propuestas quedan fuera del MVP y se retoman más adelante; se elimina RNF-08. La regla de cero superposiciones se mantiene como regla de negocio. | Prioriza validar el flujo antes de medir. | Ezequiel Avila (P-1) |
| D-17 | El uso actual, **incluido el piloto de H2, se considera no comercial**; se mantiene **Vercel Hobby**. La licencia se revisa solo si pasa a uso comercial. | "No es uso comercial esto por ahora." | Ezequiel Avila (P-9) |
| D-18 | Estado en el **link privado**: en H1 solo muestra "Confirmado". En H2, si el admin movió el turno, muestra un aviso del tipo "El local cambió tu turno: ahora es…"; si lo canceló, muestra que está cancelado. | Sin notificaciones (D-1), el link es la única vía para que el cliente se entere de cambios del local. | CPO (P-D1 del diseño) |
| D-19 | El **precio de referencia es opcional** al crear un servicio; si falta, la web muestra "Consultá el precio en el local". | Hay servicios con precio variable; no bloquear la carga del local. | CPO (P-D2 del diseño) |
| D-20 | La **duración de un servicio** va de **5 a 480 minutos**, en múltiplos de 5. | Cubre desde un retoque hasta un servicio de jornada completa y evita valores sin sentido. | CPO (P-D3 del diseño) |
| D-21 | Los turnos **empiezan cada 15 minutos** (intervalo de inicio). | Agenda prolija y fácil de leer, con suficiente flexibilidad. | CPO (pregunta del diseño) |
| D-22 | La **agenda semanal en H1 es una lista por día**; en la demo de H1 se valida si hace falta una grilla horaria. | Es lo más simple para H1; la grilla se decide con evidencia de la demo. | CPO (P-D6 del diseño) |

## 10. Factibilidad (referencia)

Factibilidad del Architect, **aprobada por Ezequiel Avila** con el plan Hobby (D-9). Este documento no la decide; la registra como referencia.

- Entra en **Vercel Hobby a US$ 0**, sin dominio propio.
- **Estimación actualizada del Architect:** H1 de **1,5 a 2 semanas**; MVP completo (H1 + H2) de **3 a 4 semanas**. Confianza **media**.
- **Riesgo / condición:** Hobby es solo para uso no comercial. Por decisión de Ezequiel, el uso actual, incluido el piloto de H2, se considera no comercial (D-17), así que se mantiene Hobby. Si pasa a uso comercial, hay que volver a decidir la licencia (D-9).
- **Nota del Architect sobre el admin:** entra con usuario y contraseña. Como el MVP no envía emails, no hay recuperación de contraseña: si el admin la olvida, el equipo la resetea a mano (ver CU-08).
- Puede haber un **arranque en frío** en el primer acceso después de un rato de inactividad (ver RNF-06).

Los detalles técnicos (arquitectura, autenticación del admin, manejo de concurrencia, link privado, archivo de calendario) los define el Architect en su documentación.

## 11. Preguntas abiertas

| ID | Pregunta | Propuesta | Dueño | Impacta |
|---|---|---|---|---|
| P-8 | ¿Cuántas personas del local van a usar el panel? **Qué es "el panel":** la parte web, con usuario y contraseña, que usa el local para gestionar todo lo que no ve el cliente: datos del local (CU-16), servicios (CU-09), peluqueros y su horario semanal (CU-10), bloqueos (CU-11), agenda del día y de la semana (CU-12), carga, movimiento y cancelación de turnos (CU-13) y asistencia (CU-14). Se entra con usuario y contraseña (CU-08). | Propuesta del Architect: **una cuenta por persona**, sin roles distintos; las cuentas las crea el equipo a mano y el MVP no tiene pantalla para administrar usuarios. Así se sabe quién cargó, movió o canceló cada turno. Queda abierto solo cuántas personas lo usan. | Ezequiel Avila | CU-08, CU-13, §2 |

### Preguntas resueltas

| ID | Resolución |
|---|---|
| P-1 | Sin métricas por ahora; objetivo cualitativo (D-16). |
| P-2 | Anticipación mínima fija de 2 horas, sin configuración; CU-15 eliminado (D-13). D-7 confirmada. |
| P-4 | Hitos de D-8 confirmados por Ezequiel. |
| P-5 | Ventana de reserva de 30 días (D-14). |
| P-7 | Peluquero con menos turnos ese día; desempate por orden de carga (D-15). |
| P-9 | Uso no comercial, incluido el piloto; se mantiene Vercel Hobby (D-17). |
