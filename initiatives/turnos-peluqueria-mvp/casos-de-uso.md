# Reserva de turnos para peluquería — MVP

- **Etapa:** Discovery, etapa 1 (problema y casos de uso)
- **Dueño de producto:** Ezequiel Avila
- **Estado:** borrador para que lo validen Ezequiel (producto), CDO (diseño) y Architect (factibilidad)
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

Las metas numéricas (por ejemplo, % online a los 30 días) se fijan con Ezequiel al confirmar el objetivo. Antes de lanzar conviene registrar una línea de base de ausencias (aunque sea estimada por el local).

## 4. Alcance del MVP

- **Una peluquería**, **una sola sede**, **varios peluqueros**, cada uno con su agenda.
- **Una sola zona horaria:** la del local. Todos los horarios se muestran y se guardan en esa zona.
- **Cliente** (web pensada para celular y accesible):
  - ve los servicios con duración y precio de referencia (no se cobra);
  - reserva: servicio → peluquero o "cualquiera disponible" → horario → datos → confirmar;
  - no crea cuenta; datos: nombre (obligatorio), teléfono y email (opcionales);
  - al confirmar ve el resumen, un **link privado** para cambiar o cancelar (con la indicación de guardarlo) y un botón **"Agregar al calendario"**;
  - cambia o cancela su turno desde el link privado, respetando la anticipación mínima.
- **Admin del local** (con login; el mecanismo lo define el Architect):
  - carga servicios, peluqueros, el horario semanal de cada peluquero y bloqueos (feriados, ausencias);
  - ve la agenda del día y de la semana;
  - carga turnos a mano, cancela y mueve turnos;
  - marca cada turno pasado como "asistió" o "no asistió".
- **El MVP no envía ninguna notificación** (ver D-1).

## 5. Casos de uso

Prioridad: **Hito 1** = reserva + agenda (entrega intermedia propuesta, P-4); **Hito 2** = resto del MVP.

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

### CU-01 — Ver servicios

**Como** cliente, **quiero** ver los servicios con su duración y precio de referencia **para** elegir qué reservar.

- **Given** el local tiene servicios activos, **When** el cliente entra a la web de reservas, **Then** ve la lista de servicios activos con nombre, duración y precio de referencia, y una aclaración de que el precio es orientativo y se paga en el local.
- **Given** un servicio está inactivo, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** el local no tiene servicios activos, **When** el cliente entra, **Then** ve un mensaje que indica que por ahora no hay reservas online y que puede contactar al local.

### CU-02 — Ver horarios libres

**Como** cliente, **quiero** ver solo los horarios realmente libres **para** no elegir uno que no se puede.

- **Given** el cliente eligió un servicio de 45 min y un peluquero, **When** ve los horarios de un día, **Then** solo ve horarios de inicio en los que ese peluquero trabaja, no tiene bloqueos y tiene 45 min libres seguidos sin superponerse con otro turno.
- **Given** el cliente eligió "cualquiera disponible", **When** ve los horarios de un día, **Then** ve cada horario en el que al menos un peluquero que hace ese servicio está libre, sin horarios repetidos.
- **Given** un horario ya empezó o está en el pasado, **When** el cliente ve los horarios, **Then** ese horario no aparece.
- **Given** un día no tiene horarios libres, **When** el cliente lo consulta, **Then** ve "sin horarios disponibles" y puede pasar a otro día.
- **Given** el cliente consulta horarios, **When** se muestran, **Then** están en la zona horaria del local.

### CU-03 — Reservar un turno

**Como** cliente, **quiero** reservar en pocos pasos y sin crear cuenta **para** asegurar mi turno sin llamar.

- **Given** el cliente eligió servicio, peluquero (o "cualquiera disponible") y un horario libre, **When** ingresa su nombre y confirma, **Then** el turno queda confirmado en la agenda del peluquero con la duración del servicio y el cliente ve la confirmación (CU-05).
- **Given** el cliente eligió "cualquiera disponible", **When** confirma, **Then** el sistema asigna un peluquero libre para ese horario y la confirmación muestra cuál es.
- **Given** el cliente deja el nombre vacío, **When** intenta confirmar, **Then** ve un mensaje junto al campo que indica que el nombre es obligatorio y no se crea el turno.
- **Given** el cliente no ingresa teléfono ni email, **When** confirma con nombre válido, **Then** el turno se crea igual.
- **Given** el cliente ingresa un email o teléfono con formato inválido, **When** intenta confirmar, **Then** ve un mensaje que indica qué corregir y no se crea el turno.
- **Given** el cliente está en cualquier paso, **When** vuelve atrás, **Then** conserva lo que ya eligió en los pasos anteriores.

### CU-04 — Resolver una reserva simultánea del mismo horario

**Como** cliente, **quiero** saber claramente si mi horario se ocupó mientras reservaba **para** elegir otro sin confusión.

- **Given** dos clientes confirman al mismo tiempo el mismo horario con el mismo peluquero, **When** se procesan ambas reservas, **Then** se confirma una sola y en la agenda no queda ningún turno superpuesto.
- **Given** la reserva de un cliente no se pudo confirmar porque el horario se ocupó, **When** recibe la respuesta, **Then** ve un mensaje claro ("Ese horario se acaba de ocupar, elegí otro"), los horarios libres actualizados y conserva el servicio y los datos que ya cargó.
- **Given** el cliente eligió "cualquiera disponible" y el peluquero que se le iba a asignar se ocupó, **When** queda otro peluquero libre en ese horario, **Then** se le asigna ese y la reserva se confirma; **When** no queda ninguno, **Then** ve el mensaje del criterio anterior.

### CU-05 — Ver la confirmación y agregar al calendario

**Como** cliente, **quiero** ver el resumen, guardar el link y agendar el turno **para** recordarlo y gestionarlo sin que me envíen nada.

- **Given** el turno se confirmó, **When** el cliente ve la pantalla de confirmación, **Then** ve servicio, peluquero, fecha, hora, duración, precio de referencia y dirección del local.
- **Given** el turno se confirmó, **When** el cliente ve la confirmación, **Then** ve su link privado para cambiar o cancelar, una indicación visible de que lo guarde (porque no se enviará por ningún medio) y una forma de copiarlo.
- **Given** el cliente toca "Agregar al calendario", **When** se completa la acción, **Then** puede guardar el evento en el calendario de su celular con fecha, hora, duración, servicio, local y el link privado en la descripción.
- **Given** el turno se confirmó, **When** pasa cualquier tiempo, **Then** el sistema no envía email, SMS ni WhatsApp al cliente.

### CU-06 — Cambiar el turno desde el link privado

**Como** cliente, **quiero** cambiar mi turno desde el link **para** no tener que llamar.

- **Given** el cliente abre su link privado de un turno futuro, **When** la página carga, **Then** ve el resumen del turno y las opciones "Cambiar" y "Cancelar".
- **Given** faltan más horas que la anticipación mínima, **When** el cliente elige un horario nuevo libre y confirma, **Then** el turno pasa al horario nuevo, el horario original queda libre y ve la confirmación actualizada con "Agregar al calendario".
- **Given** el cliente eligió un horario nuevo que se ocupó antes de confirmar, **When** confirma, **Then** ve un mensaje claro, **conserva su turno original sin cambios** y puede elegir otro horario.
- **Given** faltan menos horas que la anticipación mínima, **When** el cliente abre el link, **Then** no puede cambiar el turno y ve un mensaje que le indica que contacte al local.
- **Given** el turno ya pasó o fue cancelado, **When** el cliente abre el link, **Then** ve el estado del turno y no puede cambiarlo.
- **Given** alguien abre un link inválido o inexistente, **When** la página carga, **Then** ve "Turno no encontrado" sin revelar datos de ningún turno.

### CU-07 — Cancelar el turno desde el link privado

**Como** cliente, **quiero** cancelar mi turno desde el link **para** liberar el horario si no puedo ir.

- **Given** faltan más horas que la anticipación mínima, **When** el cliente toca "Cancelar" y lo confirma en un segundo paso, **Then** el turno queda cancelado, el horario vuelve a estar libre y el cliente ve que la cancelación se realizó.
- **Given** el cliente toca "Cancelar", **When** no confirma el segundo paso, **Then** el turno sigue vigente.
- **Given** faltan menos horas que la anticipación mínima, **When** el cliente abre el link, **Then** no puede cancelar y ve un mensaje que le indica que contacte al local.
- **Given** el cliente perdió su link, **When** llama al local, **Then** el admin puede cambiar o cancelar el turno desde el panel (CU-13).

### CU-08 — Ingresar al panel del local

**Como** admin, **quiero** un acceso protegido **para** que solo el personal autorizado gestione la agenda.

- **Given** un usuario no autenticado, **When** intenta abrir cualquier pantalla del panel, **Then** se le pide iniciar sesión y no ve datos de la agenda ni de clientes.
- **Given** un admin con credenciales válidas, **When** inicia sesión, **Then** accede al panel.
- **Given** credenciales inválidas, **When** intenta iniciar sesión, **Then** ve un mensaje de error genérico y no accede.
- El mecanismo de autenticación lo define el Architect.

### CU-09 — Gestionar servicios

**Como** admin, **quiero** cargar los servicios **para** que el cliente vea qué reservar y cuánto dura.

- **Given** el admin está en el panel, **When** crea un servicio con nombre, duración (en minutos) y precio de referencia, **Then** el servicio aparece en la web de reservas.
- **Given** el admin deja el nombre vacío o la duración en cero, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia la duración de un servicio, **When** guarda, **Then** aplica a las reservas nuevas y los turnos ya confirmados conservan su duración.
- **Given** el admin desactiva un servicio, **When** el cliente ve la lista, **Then** ya no aparece y los turnos ya confirmados con ese servicio se mantienen.
- **Given** el admin indica qué peluqueros hacen un servicio, **When** el cliente lo reserva, **Then** solo ve esos peluqueros.

### CU-10 — Gestionar peluqueros y su horario semanal

**Como** admin, **quiero** cargar a cada peluquero con su horario **para** que la agenda refleje cuándo trabaja cada uno.

- **Given** el admin está en el panel, **When** crea un peluquero con nombre y horario semanal (por día: uno o más rangos, por ejemplo 9–13 y 15–19), **Then** el peluquero aparece en la reserva con horarios libres solo dentro de esos rangos.
- **Given** el admin carga un rango con hora de fin anterior o igual a la de inicio, o rangos superpuestos en el mismo día, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia el horario semanal y hay turnos futuros que quedan fuera del nuevo horario, **When** guarda, **Then** los turnos no se borran y el admin ve cuáles quedaron afectados para resolverlos.
- **Given** el admin desactiva un peluquero, **When** el cliente reserva, **Then** ese peluquero no aparece; si tiene turnos futuros, el admin ve cuáles son para moverlos o cancelarlos.

### CU-11 — Gestionar bloqueos (feriados y ausencias)

**Como** admin, **quiero** bloquear días u horarios **para** que nadie reserve cuando el local está cerrado o un peluquero falta.

- **Given** el admin crea un bloqueo para todo el local (por ejemplo, un feriado) en una fecha o rango, **When** el cliente consulta horarios, **Then** no ve horarios libres en ese período para ningún peluquero.
- **Given** el admin crea un bloqueo para un peluquero (por ejemplo, una ausencia), **When** el cliente consulta horarios, **Then** no ve horarios de ese peluquero en ese período.
- **Given** hay turnos confirmados dentro del período a bloquear, **When** el admin crea el bloqueo, **Then** se le avisa cuáles son; los turnos no se cancelan automáticamente y el admin decide moverlos o cancelarlos.
- **Given** el admin elimina un bloqueo, **When** el cliente consulta horarios, **Then** esos horarios vuelven a estar libres.

### CU-12 — Ver la agenda del día y de la semana

**Como** admin, **quiero** ver la agenda **para** saber quién viene, con quién y cuándo.

- **Given** el admin está en el panel, **When** abre la agenda del día, **Then** ve los turnos de ese día por peluquero, con hora, duración, servicio, nombre del cliente, contacto (si lo dejó), origen (web o panel) y estado.
- **Given** el admin cambia a la vista semanal, **When** se muestra, **Then** ve los turnos de los 7 días por peluquero y puede navegar a semanas anteriores y siguientes.
- **Given** el admin filtra por peluquero, **When** aplica el filtro, **Then** solo ve los turnos de ese peluquero.
- **Given** un cliente reservó, cambió o canceló desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el cambio reflejado.
- **Given** un turno fue cancelado, **When** el admin ve la agenda, **Then** puede distinguirlo de los vigentes y su horario figura como libre.

### CU-13 — Cargar, mover y cancelar turnos desde el panel

**Como** admin, **quiero** gestionar turnos a mano **para** registrar los que llegan por teléfono y atender a quien perdió su link.

- **Given** un cliente llama, **When** el admin carga un turno con servicio, peluquero, horario libre y nombre, **Then** el turno queda en la agenda con origen "panel" y respeta las mismas reglas de no superposición.
- **Given** el admin intenta cargar o mover un turno a un horario que se superpone con otro del mismo peluquero, **When** guarda, **Then** ve el error y el turno no se crea ni se mueve.
- **Given** el admin mueve un turno a un horario libre (puede ser con otro peluquero), **When** guarda, **Then** el turno queda en el nuevo horario y el anterior se libera.
- **Given** el admin cancela un turno, **When** confirma, **Then** el turno queda cancelado y el horario se libera.
- **Given** el admin carga, mueve o cancela un turno, **When** lo hace, **Then** no aplica la anticipación mínima (solo rige para el cliente) y no se envía ninguna notificación.
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
- **Given** el admin configura la anticipación en N horas, **When** un cliente intenta cambiar o cancelar con menos de N horas antes del inicio, **Then** no puede y ve el mensaje de contactar al local.
- **Given** el admin ingresa un valor negativo o no numérico, **When** intenta guardar, **Then** ve el error y no se guarda.

## 6. Reglas de negocio

| ID | Regla |
|---|---|
| RN-01 | Un peluquero **nunca** tiene dos turnos vigentes superpuestos, sin importar el origen (web o panel). |
| RN-02 | Si dos reservas o cambios simultáneos piden el mismo horario del mismo peluquero, se confirma **una sola**; la otra recibe un mensaje claro para elegir otro horario. |
| RN-03 | La duración del turno la define el servicio, con el valor vigente al momento de reservar. |
| RN-04 | Un horario está libre si el peluquero hace el servicio, el horario cae completo dentro de su horario semanal, no hay bloqueos del local ni del peluquero y no se superpone con otro turno vigente. |
| RN-05 | Todos los horarios se expresan en la zona horaria del local. |
| RN-06 | El cliente no crea cuenta. Nombre obligatorio; teléfono y email opcionales. |
| RN-07 | Cada turno reservado tiene un link privado, difícil de adivinar, que permite ver, cambiar o cancelar solo ese turno. |
| RN-08 | El cliente solo puede cambiar o cancelar desde el link si faltan más horas que la anticipación mínima configurada (2 h por defecto, P-2). El admin no tiene esa restricción. |
| RN-09 | Si al cambiar el turno el horario nuevo ya no está libre, el cliente conserva el turno original sin cambios. |
| RN-10 | Los turnos cancelados liberan el horario y no cuentan en la tasa de ausencias. |
| RN-11 | Solo se puede marcar asistencia en turnos vigentes cuya hora de inicio ya pasó. |
| RN-12 | El sistema no envía notificaciones de ningún tipo (email, SMS, WhatsApp, push). |
| RN-13 | El precio es solo de referencia: no se cobra ni se pide seña. |
| RN-14 | No se puede reservar en horarios pasados. Cuánto hacia adelante se puede reservar (ventana de reserva) es una pregunta abierta (P-5). |

## 7. Requisitos no funcionales

| ID | Requisito |
|---|---|
| RNF-01 | **Celular primero:** el flujo de reserva completo se puede hacer en un celular (pantalla de 360 px de ancho) sin zoom ni scroll horizontal. |
| RNF-02 | **Accesibilidad:** la web del cliente cumple WCAG 2.1 nivel AA (contraste, navegación por teclado, etiquetas en formularios, mensajes de error asociados al campo, compatibilidad con lector de pantalla). |
| RNF-03 | **Integridad de la agenda:** cero turnos superpuestos para un mismo peluquero, incluso con reservas simultáneas (RN-01, RN-02). |
| RNF-04 | **Privacidad:** los datos de clientes (nombre, teléfono, email) solo los ve el admin autenticado. El link privado no expone datos de otros turnos. Se guardan solo los datos necesarios. |
| RNF-05 | **Seguridad del panel:** todas las pantallas y acciones del admin requieren autenticación. |
| RNF-06 | **Rapidez percibida:** el cliente ve los horarios libres sin esperas notorias en una conexión móvil común (meta a validar con el Architect). |
| RNF-07 | **Costo:** el MVP debe poder operar con costo de infraestructura mínimo para la etapa de validación (factibilidad preliminar: US$ 0, ver §10). |
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
| D-2 | **Autogestión por link privado** mostrado en pantalla + botón "Agregar al calendario" que reemplaza al recordatorio (opción A del Architect). Si el cliente pierde el link, llama al local y el admin gestiona el turno. | Permite cambiar y cancelar sin cuentas ni notificaciones; el calendario del celular cumple el rol del recordatorio sin costo. | CPO (opción A del Architect) |
| D-3 | **Sin cuenta de cliente**; solo nombre obligatorio, teléfono y email opcionales. | Menos pasos, mayor conversión, menos datos personales. | Alcance acordado |
| D-4 | **Una sede, varios peluqueros, una zona horaria.** | Cubre el caso real de una peluquería y simplifica la validación. | Alcance acordado |
| D-5 | **Precio solo de referencia**, sin cobro. | Pagos y señas suman complejidad y costo; se evalúan después de medir ausencias. | Alcance acordado |
| D-6 | Al cambiar un turno, si el horario nuevo se ocupó, el cliente **conserva el original**. | El cliente nunca se queda sin turno por intentar cambiarlo. | Alcance acordado |
| D-7 | La anticipación mínima rige solo para el cliente; el admin puede gestionar turnos en cualquier momento. | El local necesita resolver casos de último momento (por ejemplo, un cliente que llama). | Propuesta PO (validar con Ezequiel junto con P-2) |

## 10. Factibilidad (referencia)

Factibilidad preliminar del Architect (no es parte de este documento decidirla):

- Entra en **Vercel Hobby a US$ 0**, sin dominio propio.
- **Estimación de referencia: 3 a 4 semanas**, con confianza media-baja hasta ver el borrador de diseño.
- **Hobby es solo para uso no comercial** (ver P-3).

Los detalles técnicos (arquitectura, autenticación del admin, manejo de concurrencia, link privado, archivo de calendario) los define el Architect en su documentación.

## 11. Preguntas abiertas

| ID | Pregunta | Propuesta | Dueño | Impacta |
|---|---|---|---|---|
| P-1 | ¿Se confirman el objetivo y las métricas de §3? ¿Qué metas numéricas? | Objetivo y métricas de §3. | Ezequiel Avila | §3, RNF-08 |
| P-2 | ¿Cuál es la anticipación mínima por defecto para cambiar o cancelar? | 2 h, configurable por el local. | Ezequiel Avila | CU-06, CU-07, CU-15, RN-08 |
| P-3 | ¿Vercel Hobby o Pro (US$ 20 por mes y por miembro)? | Hobby para validar; Pro antes del primer piloto con una peluquería real (Hobby es solo uso no comercial). | Ezequiel Avila (con el Architect) | Costo, §10 |
| P-4 | ¿Se hace una entrega intermedia con reserva + agenda como primer hito? | Sí: Hito 1 = CU-01 a CU-05, CU-08 a CU-10 y CU-12. | Ezequiel Avila | Plan de entrega, §5 |
| P-5 | ¿Con cuánta anticipación máxima se puede reservar (por ejemplo, 30 días)? | 30 días. | Ezequiel Avila | CU-02, RN-14 |
