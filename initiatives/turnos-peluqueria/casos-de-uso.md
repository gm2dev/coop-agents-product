# Reserva de turnos para peluquería — Casos de uso del MVP

- **Etapa:** Discovery, etapa 1 (problema y casos de uso).
- **Dueño de producto:** Ezequiel Avila.
- **Estado:** borrador para validar con Ezequiel (producto), el CDO (experiencia y diseño) y el Architect (factibilidad).
- **Alcance del documento:** define **qué** hace el MVP y **por qué**. No diseña pantallas ni define tecnología.
- **Convención:** las decisiones marcadas **[Ezequiel]** ya las tomó el dueño de producto. Las marcadas **[Supuesto]** son propuestas del CPO/PO a confirmar.

## 1. Contexto y problema

Una peluquería de una sola sucursal, con varios profesionales, toma hoy los turnos por **teléfono, WhatsApp o en el mostrador**. Esto genera:

1. **Tiempo perdido del personal:** atienden llamadas y mensajes para reservar, a veces mientras atienden a un cliente.
2. **Dobles reservas:** la agenda no se actualiza a tiempo y dos clientes quedan en el mismo horario con el mismo profesional.
3. **El cliente depende del horario del local:** solo puede reservar cuando alguien le responde.

**Evidencia:** relato del dueño de producto. Aún no hay datos cuantitativos (cuántos turnos por semana, cuántas dobles reservas); ver P-01.

## 2. Objetivo y métricas

**Objetivo del MVP:** que el cliente pueda **reservar solo, sin intervención del local, y sin dobles reservas**.

| ID | Métrica | Definición | Meta propuesta [Supuesto] |
|---|---|---|---|
| M-1 | **Adopción de la reserva online** | Turnos creados por el cliente desde la web ÷ total de turnos (web + cargados por el admin), por semana. | ≥ 40 % a las 8 semanas del lanzamiento. |
| M-2 | **Dobles reservas** (guardarraíl) | Cantidad de pares de turnos vigentes superpuestos para un mismo profesional. | **0** siempre. |
| M-3 | **Reserva completada sin ayuda** | Reservas confirmadas ÷ reservas iniciadas (el cliente eligió un servicio), por semana. | ≥ 60 %. |

- Las metas numéricas las confirma Ezequiel (P-02).
- **Línea de base para el futuro (no es objetivo del MVP) [Ezequiel]:** el admin marca asistencia/ausencia (CU-12). Con eso se obtiene la tasa de ausencias como línea de base para una fase futura (por ejemplo, recordatorios). El MVP **no** persigue reducir ausencias.

## 3. Actores

| Actor | Descripción | En el MVP |
|---|---|---|
| **Cliente** | Persona que quiere un servicio. Usa sobre todo el celular. No quiere crear una cuenta. | Consulta servicios y disponibilidad y reserva desde la web (mobile-first). |
| **Administrador / Dueño del local** | Dueño o encargado. Puede ser también profesional. | Ingresa con acceso protegido; configura local, servicios, profesionales, horarios y bloqueos; gestiona la agenda; carga turnos tomados por WhatsApp, teléfono o mostrador; cancela y mueve turnos; marca asistencia. |
| **Profesional** | Peluquero/a con agenda propia. | **Fuera del MVP como usuario** (D-07). Existe como dato (su agenda), pero no ingresa al sistema. |

## 4. Alcance

**Dentro del MVP**

- Una peluquería, una sucursal, varios profesionales con agenda propia, una zona horaria (la del local).
- Cliente: ver servicios, ver disponibilidad, reservar (servicio → profesional o "cualquiera" → fecha y hora → nombre y teléfono → confirmar) y ver la confirmación **en pantalla** con el WhatsApp del local.
- Información visible de que **para cancelar o cambiar un turno hay que escribir por WhatsApp** al local, con link de contacto.
- Admin: acceso protegido; datos del local (incluido el WhatsApp); servicios; profesionales y horarios; bloqueos; agenda diaria y semanal; crear, cancelar y mover turnos; marcar asistencia.

**Fuera del MVP**

- Emails, recordatorios y **cualquier notificación** (email, SMS, WhatsApp, push) [Ezequiel].
- Cancelación o reprogramación **por el cliente desde la app** [Ezequiel].
- Integración con la API de WhatsApp (solo número y link de contacto).
- Pagos y señas.
- Varias sucursales.
- Cuentas de cliente, historial del cliente.
- Programas de fidelidad, reseñas y calificaciones.
- App nativa (iOS/Android).
- Lista de espera.
- Reportes avanzados (solo se registran los datos para calcular M-1 a M-3 y la línea de base de ausencias).
- Acceso propio del profesional (D-07).

## 5. Decisiones

| ID | Decisión | Por qué | Origen |
|---|---|---|---|
| D-01 | **Sin emails, recordatorios ni notificaciones** de ningún tipo. | Simplifica el MVP y evita proveedores y costos de mensajería; se valida primero que el cliente reserve solo. | [Ezequiel] |
| D-02 | La confirmación se muestra **solo en pantalla** al terminar la reserva: servicio, profesional, fecha y hora, y el WhatsApp del local. | Consecuencia de D-01: es el único momento en que el cliente recibe la información. | [Ezequiel] |
| D-03 | El cliente **no cancela ni reprograma desde la app**. La app indica, en la confirmación y en un lugar visible de la web de reservas, que para cancelar o cambiar hay que escribir por WhatsApp al local, con su número como link de contacto. El admin resuelve esos pedidos cancelando o moviendo el turno a mano. | Reduce el alcance; el volumen de cambios se puede absorber por WhatsApp mientras se valida la reserva autónoma. | [Ezequiel] |
| D-04 | El cliente se identifica con **nombre y teléfono, sin cuenta ni contraseña**. Ambos obligatorios en la web. | Menos pasos = más reservas completadas (M-3). Sin notificaciones, el teléfono es el **único canal** para que el local contacte al cliente (por ejemplo, si debe mover o cancelar su turno). Se piden solo los datos necesarios (privacidad). | [Supuesto] |
| D-05 | **Una peluquería, una sucursal, varios profesionales** con agenda propia, una zona horaria. | Cubre el caso real y simplifica la validación. | [Supuesto] |
| D-06 | La **duración del turno la define el servicio**. | El cliente no debe calcular tiempos; la agenda se arma sola y sin superposiciones. | [Supuesto] |
| D-07 | El **profesional no es usuario** del MVP; su agenda la gestiona el admin. | Cada usuario extra suma acceso, permisos y diseño. El admin ya ve la agenda de todos y el problema a resolver (reserva autónoma y sin dobles reservas) no requiere que el profesional ingrese. Es candidato natural a la siguiente iteración (vista de solo lectura de su agenda), ver P-05. | [Supuesto] |
| D-08 | El cliente puede elegir **"cualquiera"** como profesional. | Muchos clientes no tienen preferencia; mostrar todos los horarios libres aumenta la chance de reservar. | [Supuesto] |
| D-09 | El **precio es informativo**: no se cobra ni se pide seña. | Pagos están fuera del MVP. | [Supuesto] |
| D-10 | El admin entra con un **acceso protegido**; el MVP no tiene roles distintos ni pantalla de gestión de usuarios. | Protege los datos personales de los clientes con el mínimo alcance. El mecanismo lo define el Architect. | [Supuesto] |
| D-11 | Los turnos que carga el admin piden **nombre obligatorio y teléfono recomendado** (opcional). | Quien reserva en el mostrador puede no querer dar su teléfono; no debe bloquear la carga. Riesgo: si el local debe mover ese turno, no puede avisar. | [Supuesto] (P-04) |

## 6. Casos de uso

Prioridad: **Must** = sin esto no se cumple el objetivo del MVP; **Should** = aporta valor, puede salir en una segunda entrega si el Architect indica que el costo lo justifica.

| ID | Caso de uso | Actor | Prioridad |
|---|---|---|---|
| CU-01 | Consultar servicios | Cliente | Must |
| CU-02 | Consultar disponibilidad | Cliente | Must |
| CU-03 | Reservar un turno | Cliente | Must |
| CU-04 | Ver la confirmación y cómo cambiar o cancelar | Cliente | Must |
| CU-05 | Ingresar al panel del local | Admin | Must |
| CU-06 | Configurar datos del local (incluido WhatsApp) | Admin | Must |
| CU-07 | Gestionar servicios | Admin | Must |
| CU-08 | Gestionar profesionales y horarios de atención | Admin | Must |
| CU-09 | Gestionar bloqueos (feriados, vacaciones) | Admin | Must |
| CU-10 | Ver la agenda diaria y semanal | Admin | Must |
| CU-11 | Crear, cancelar y mover turnos manualmente | Admin | Must |
| CU-12 | Marcar asistencia o ausencia | Admin | Should |

### CU-01 — Consultar servicios

- **Actor:** Cliente. **Prioridad:** Must.
- **Precondiciones:** el local tiene al menos un servicio activo con un profesional activo que lo realiza.
- **Flujo principal:** 1) el cliente entra a la web de reservas; 2) ve los servicios activos con nombre, duración y precio informativo; 3) elige uno y pasa a CU-02.
- **Alternativos / errores:** A1) no hay servicios disponibles: ve un mensaje de que por ahora no hay reservas online y el WhatsApp del local.

**Criterios de aceptación**

- **Given** hay servicios activos con al menos un profesional activo, **When** el cliente entra a la web de reservas, **Then** ve cada servicio con nombre, duración en minutos y precio, y una aclaración de que el precio es informativo y se paga en el local.
- **Given** un servicio está inactivo o no tiene ningún profesional activo asignado, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** no hay ningún servicio disponible, **When** el cliente entra, **Then** ve el mensaje "Por ahora no hay reservas online" y el WhatsApp del local como link de contacto.
- **Given** cualquier pantalla de la web de reservas, **When** el cliente la ve, **Then** tiene a la vista el aviso "Para cancelar o cambiar un turno, escribinos por WhatsApp" con el número del local como link de contacto (RN-09).

### CU-02 — Consultar disponibilidad

- **Actor:** Cliente. **Prioridad:** Must.
- **Precondiciones:** el cliente eligió un servicio (CU-01).
- **Flujo principal:** 1) el cliente elige un profesional que hace ese servicio o "cualquiera"; 2) elige un día; 3) ve los horarios de inicio libres de ese día; 4) elige uno y pasa a CU-03.
- **Alternativos / errores:** A1) el día no tiene horarios libres: ve "Sin horarios disponibles" y puede elegir otro día. A2) el cliente vuelve atrás y cambia servicio o profesional: los horarios se recalculan.

**Criterios de aceptación**

- **Given** el cliente eligió un servicio de 45 min y un profesional, **When** consulta un día, **Then** solo ve horarios de inicio en los que ese profesional tiene 45 min seguidos libres dentro de su horario de atención, sin bloqueos y sin superponerse con otro turno vigente (RN-04).
- **Given** el cliente eligió "cualquiera", **When** consulta un día, **Then** ve cada horario en el que al menos un profesional que hace ese servicio está libre, sin horarios repetidos.
- **Given** la hora actual del local es 15:10, **When** el cliente consulta el día de hoy, **Then** no ve horarios de inicio anteriores o iguales a 15:10 (RN-06).
- **Given** el día consultado tiene un bloqueo de todo el local, **When** el cliente lo consulta, **Then** ve "Sin horarios disponibles".
- **Given** el cliente intenta consultar un día posterior a la ventana de reserva (propuesta: 30 días, P-03), **When** navega el calendario, **Then** no puede elegirlo.
- **Given** el cliente está en otra zona horaria, **When** ve los horarios, **Then** se muestran en la hora del local (RN-05).

### CU-03 — Reservar un turno

- **Actor:** Cliente. **Prioridad:** Must.
- **Precondiciones:** el cliente eligió servicio, profesional (o "cualquiera") y un horario libre (CU-02).
- **Flujo principal:** 1) el cliente ingresa nombre y teléfono; 2) ve el resumen (servicio, profesional, fecha, hora, duración, precio informativo) y el aviso de que para cambiar o cancelar debe escribir por WhatsApp; 3) confirma; 4) el sistema vuelve a verificar que el horario sigue libre y crea el turno con la duración del servicio; 5) pasa a CU-04.
- **Alternativos / errores:**
  - A1) "cualquiera": el sistema asigna un profesional que hace el servicio y está libre en ese horario (criterio entre varios libres: P-06).
  - E1) datos faltantes o inválidos: se indica qué corregir junto al campo y no se crea el turno.
  - E2) **el horario se ocupó mientras reservaba** (otro cliente o el admin lo tomó antes): no se crea el turno; el cliente ve un mensaje claro, los horarios actualizados y conserva servicio, profesional y datos cargados.
  - E3) el horario dejó de ser válido (pasó la hora, se cargó un bloqueo): mismo tratamiento que E2.

**Criterios de aceptación**

- **Given** el cliente eligió un horario libre e ingresó nombre y teléfono válidos, **When** confirma, **Then** se crea un turno vigente para ese profesional, con la duración del servicio, origen "web", y el cliente ve la confirmación (CU-04).
- **Given** el cliente deja vacío el nombre o el teléfono, **When** intenta confirmar, **Then** ve junto al campo qué falta y no se crea el turno.
- **Given** el teléfono tiene un formato inválido (formato aceptado: P-07), **When** intenta confirmar, **Then** ve qué corregir y no se crea el turno.
- **Given** dos clientes confirman al mismo tiempo el mismo horario con el mismo profesional, **When** se procesan ambas confirmaciones, **Then** se crea un solo turno y el otro cliente ve "Ese horario se acaba de ocupar, elegí otro" con los horarios actualizados (RN-02).
- **Given** el cliente recibió el mensaje de horario ocupado, **When** vuelve a elegir horario, **Then** conserva servicio, profesional, nombre y teléfono ya cargados.
- **Given** el cliente eligió "cualquiera" y hay al menos un profesional libre que hace el servicio en ese horario, **When** confirma, **Then** el turno se asigna a uno de ellos y la confirmación muestra su nombre.
- **Given** el cliente eligió "cualquiera" y el profesional que iba a asignarse se ocupó, **When** queda otro profesional libre que hace el servicio en ese horario, **Then** se asigna ese; **When** no queda ninguno, **Then** aplica el mensaje de horario ocupado.
- **Given** el cliente está en cualquier paso, **When** vuelve atrás, **Then** conserva lo elegido en los pasos anteriores.
- **Given** el turno se creó, **When** pasa cualquier tiempo, **Then** el sistema no envía email, SMS, WhatsApp ni notificación al cliente ni al local (RN-08).

### CU-04 — Ver la confirmación y cómo cambiar o cancelar

- **Actor:** Cliente. **Prioridad:** Must.
- **Precondiciones:** el turno se creó (CU-03).
- **Flujo principal:** 1) el cliente ve la pantalla de confirmación con el resumen del turno; 2) ve el aviso de que para cancelar o cambiar debe escribir por WhatsApp al local, con el número como link de contacto; 3) opcionalmente toca el link y se abre una conversación de WhatsApp con el local.
- **Alternativos / errores:** A1) el cliente cierra la pantalla: la información no se vuelve a mostrar ni se envía (D-01); si necesita algo, escribe por WhatsApp. A2) el cliente quiere reservar otro turno: puede volver al inicio.

**Criterios de aceptación**

- **Given** el turno se creó, **When** el cliente ve la confirmación, **Then** ve servicio, profesional, fecha, hora de inicio, duración, precio informativo, nombre del local y dirección.
- **Given** el cliente ve la confirmación, **When** la lee, **Then** ve el texto "Para cancelar o cambiar tu turno, escribinos por WhatsApp" con el número configurado del local (CU-06) visible y como link de contacto.
- **Given** el cliente toca el link de WhatsApp en un celular con WhatsApp, **When** se abre, **Then** se inicia una conversación con el número del local (texto prellenado con los datos del turno: P-08).
- **Given** el cliente ve la confirmación, **When** la lee, **Then** ve una indicación de que no recibirá confirmación por ningún medio y que conviene anotar o capturar los datos del turno.
- **Given** la confirmación se muestra, **When** el cliente busca opciones de cancelar o cambiar dentro de la app, **Then** no existen (D-03).

### CU-05 — Ingresar al panel del local

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** el admin tiene credenciales (creadas en la puesta en marcha; cómo se crean y recuperan: Architect, P-09).
- **Flujo principal:** 1) el admin abre el panel; 2) se identifica; 3) accede a la agenda (CU-10).
- **Alternativos / errores:** E1) credenciales inválidas: mensaje genérico, sin acceso. E2) sesión vencida: se pide identificarse de nuevo sin perder datos personales en pantalla.

**Criterios de aceptación**

- **Given** un usuario no identificado, **When** intenta abrir cualquier pantalla o acción del panel, **Then** se le pide identificarse y no ve datos de turnos ni de clientes.
- **Given** credenciales válidas, **When** el admin se identifica, **Then** accede al panel.
- **Given** credenciales inválidas, **When** intenta identificarse, **Then** ve un mensaje genérico (sin indicar qué dato falló) y no accede.
- **Given** el admin cierra sesión, **When** otra persona usa el mismo dispositivo, **Then** no puede ver el panel sin identificarse.

### CU-06 — Configurar datos del local (incluido WhatsApp)

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado (CU-05).
- **Flujo principal:** 1) el admin carga nombre del local, dirección y número de WhatsApp; 2) guarda; 3) los datos se ven en la web de reservas y en la confirmación.
- **Alternativos / errores:** E1) campo obligatorio vacío o número inválido: error junto al campo, no se guarda.

**Criterios de aceptación**

- **Given** el admin carga nombre, dirección y WhatsApp válidos, **When** guarda, **Then** se muestran en la web de reservas y en la confirmación (CU-04).
- **Given** el admin deja vacío el nombre, la dirección o el WhatsApp, **When** intenta guardar, **Then** ve el error junto al campo y no se guarda.
- **Given** el admin cambia el número de WhatsApp, **When** un cliente abre la web o ve una confirmación nueva, **Then** ve el número nuevo en todos los avisos de contacto.
- **Given** el WhatsApp del local no está configurado, **When** se intenta habilitar la web de reservas, **Then** no se habilita hasta que esté cargado (sin él, el cliente no tendría cómo cancelar o cambiar).

### CU-07 — Gestionar servicios

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado.
- **Flujo principal:** 1) el admin crea un servicio con nombre, duración en minutos y precio informativo; 2) indica qué profesionales lo hacen; 3) guarda; 4) el servicio aparece en la web de reservas.
- **Alternativos / errores:** A1) editar un servicio. A2) desactivar un servicio. E1) nombre vacío, duración ≤ 0 o precio negativo: error, no se guarda.

**Criterios de aceptación**

- **Given** el admin crea un servicio válido con al menos un profesional activo, **When** guarda, **Then** el servicio aparece en la web de reservas.
- **Given** nombre vacío, duración ≤ 0 o precio negativo, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia la duración de un servicio, **When** guarda, **Then** aplica solo a turnos nuevos; los turnos existentes conservan su duración.
- **Given** el admin desactiva un servicio, **When** el cliente ve la lista, **Then** ya no aparece, y los turnos existentes de ese servicio se mantienen.
- **Given** un servicio sin profesionales activos asignados, **When** el admin ve la lista de servicios, **Then** el panel le indica que no se ofrece al cliente por ese motivo.

### CU-08 — Gestionar profesionales y horarios de atención

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado.
- **Flujo principal:** 1) el admin crea un profesional con nombre; 2) le asigna los servicios que hace; 3) carga su horario semanal (por día, uno o más rangos, por ejemplo 9–13 y 15–19); 4) guarda.
- **Alternativos / errores:** A1) editar horario. A2) desactivar profesional. E1) rango con fin ≤ inicio o rangos superpuestos en un día: error, no se guarda.

**Criterios de aceptación**

- **Given** el admin crea un profesional con servicios y horario semanal válidos, **When** guarda, **Then** aparece en la reserva de esos servicios con horarios libres solo dentro de sus rangos.
- **Given** un rango con hora de fin anterior o igual a la de inicio, o dos rangos superpuestos el mismo día, **When** intenta guardar, **Then** ve el error y no se guarda.
- **Given** el admin cambia el horario y quedan turnos futuros fuera del nuevo horario, **When** guarda, **Then** esos turnos no se borran ni se modifican y el panel le muestra cuáles quedaron afectados para resolverlos (CU-11).
- **Given** el admin desactiva un profesional, **When** el cliente reserva, **Then** ese profesional no aparece; sus turnos futuros se mantienen y el panel los lista para resolverlos.

### CU-09 — Gestionar bloqueos (feriados, vacaciones)

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado.
- **Flujo principal:** 1) el admin crea un bloqueo indicando alcance (todo el local o un profesional), fecha/hora de inicio y fin, y motivo opcional; 2) guarda; 3) ese período deja de ofrecerse.
- **Alternativos / errores:** A1) eliminar un bloqueo. A2) hay turnos dentro del período (ver criterio). E1) fin ≤ inicio: error.

**Criterios de aceptación**

- **Given** un bloqueo de todo el local (feriado) para una fecha, **When** el cliente consulta esa fecha, **Then** no ve horarios para ningún profesional.
- **Given** un bloqueo de un profesional (vacaciones) para un rango de fechas, **When** el cliente consulta esas fechas, **Then** no ve horarios de ese profesional y sí de los demás.
- **Given** hay turnos vigentes dentro del período a bloquear, **When** el admin crea el bloqueo, **Then** ve cuáles son; los turnos **no** se cancelan automáticamente y el admin decide (contactar por teléfono y mover o cancelar, CU-11).
- **Given** el admin elimina un bloqueo, **When** el cliente consulta ese período, **Then** los horarios vuelven a ofrecerse si cumplen RN-04.

### CU-10 — Ver la agenda diaria y semanal

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado.
- **Flujo principal:** 1) el admin abre la agenda del día; 2) ve los turnos por profesional; 3) puede pasar a la vista semanal, navegar días o semanas y filtrar por profesional.
- **Alternativos / errores:** A1) día sin turnos: lo indica claramente.

**Criterios de aceptación**

- **Given** hay turnos en el día, **When** el admin abre la agenda diaria, **Then** ve por profesional cada turno con hora de inicio y fin, servicio, nombre y teléfono del cliente, origen (web o panel) y estado.
- **Given** el admin cambia a vista semanal, **When** se muestra, **Then** ve los 7 días por profesional y puede ir a semanas anteriores y siguientes.
- **Given** el admin filtra por un profesional, **When** aplica el filtro, **Then** solo ve los turnos de ese profesional.
- **Given** un cliente acaba de reservar desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el turno nuevo.
- **Given** un turno cancelado, **When** el admin ve la agenda, **Then** lo distingue de los vigentes y su horario figura libre.
- **Given** períodos bloqueados, **When** el admin ve la agenda, **Then** los distingue de los horarios libres.

### CU-11 — Crear, cancelar y mover turnos manualmente

- **Actor:** Admin. **Prioridad:** Must.
- **Precondiciones:** admin identificado.
- **Flujo principal (crear):** 1) un cliente pide turno por WhatsApp, teléfono o mostrador; 2) el admin elige servicio, profesional y horario; 3) carga nombre y teléfono; 4) guarda; el turno queda con origen "panel".
- **Flujo principal (cancelar o mover a pedido por WhatsApp):** 1) el cliente escribe por WhatsApp; 2) el admin busca el turno en la agenda (por día, profesional o nombre/teléfono del cliente); 3) lo cancela o lo mueve a otro horario o profesional; 4) le responde al cliente por WhatsApp por fuera del sistema.
- **Alternativos / errores:** E1) superposición con otro turno del mismo profesional: error, no se guarda (no se puede forzar). E2) horario fuera del horario de atención o dentro de un bloqueo: advertencia y confirmación explícita (P-10). E3) horario pasado: no se permite crear ni mover a un horario pasado.

**Criterios de aceptación**

- **Given** el admin carga un turno con servicio, profesional, horario libre y nombre, **When** guarda, **Then** el turno queda vigente con la duración del servicio y origen "panel".
- **Given** el admin no carga teléfono, **When** guarda, **Then** el turno se crea igual y el panel recomienda cargarlo (D-11, P-04).
- **Given** el horario elegido se superpone con otro turno vigente del mismo profesional, **When** el admin guarda (al crear o mover), **Then** ve el error y el turno no se crea ni se mueve (RN-01).
- **Given** un cliente web y el admin toman el mismo horario al mismo tiempo, **When** se procesan, **Then** se confirma uno solo y el otro recibe el aviso de horario ocupado (RN-02).
- **Given** el horario está fuera del horario de atención o dentro de un bloqueo, **When** el admin guarda, **Then** ve el motivo y debe confirmar; si confirma, se guarda; si no, no.
- **Given** el admin mueve un turno a otro horario o profesional, **When** guarda, **Then** el turno queda en el nuevo horario y el anterior queda libre.
- **Given** el admin cancela un turno y confirma, **When** se procesa, **Then** el turno queda cancelado, el horario se libera y vuelve a ofrecerse en la web.
- **Given** el admin busca por nombre o teléfono del cliente, **When** hay coincidencias, **Then** ve los turnos futuros de ese cliente.
- **Given** el admin crea, mueve o cancela un turno, **When** se guarda, **Then** el sistema no notifica al cliente; el panel muestra su teléfono para contactarlo por fuera del sistema (RN-08).

### CU-12 — Marcar asistencia o ausencia

- **Actor:** Admin. **Prioridad:** Should (línea de base para una fase futura, no es objetivo del MVP).
- **Precondiciones:** admin identificado; turno vigente cuya hora de inicio ya pasó.
- **Flujo principal:** 1) el admin ve en la agenda un turno pasado; 2) lo marca "asistió" o "no asistió".
- **Alternativos / errores:** A1) corregir la marca. E1) turno futuro o cancelado: no se puede marcar.

**Criterios de aceptación**

- **Given** un turno vigente cuya hora de inicio ya pasó, **When** el admin lo marca "asistió" o "no asistió", **Then** se guarda la marca.
- **Given** un turno futuro o cancelado, **When** el admin intenta marcarlo, **Then** no puede.
- **Given** el admin se equivocó, **When** cambia la marca, **Then** queda la última.
- **Given** turnos pasados sin marcar, **When** el admin abre la agenda, **Then** los ve como "sin marcar".

## 7. Reglas de negocio

| ID | Regla |
|---|---|
| RN-01 | Un profesional **nunca** tiene dos turnos vigentes superpuestos, sin importar el origen (web o panel). El admin tampoco puede forzarlo. |
| RN-02 | Si dos reservas o movimientos simultáneos piden el mismo horario del mismo profesional, se confirma **uno solo**; el otro recibe un aviso claro para elegir otro horario. |
| RN-03 | La duración del turno la define el servicio, con el valor vigente al momento de crearlo. |
| RN-04 | Un horario se ofrece al cliente si: el profesional está activo y hace el servicio; el turno completo (inicio + duración) cae dentro de su horario de atención; no hay bloqueo del local ni del profesional; no se superpone con otro turno vigente; está dentro de la ventana de reserva. |
| RN-05 | Todos los horarios se muestran y se interpretan en la **zona horaria del local**. |
| RN-06 | No se puede reservar ni mover un turno a un horario que ya empezó o está en el pasado. |
| RN-07 | El cliente se identifica solo con nombre y teléfono (obligatorios en la web); no hay cuentas de cliente. |
| RN-08 | El sistema **no envía notificaciones** de ningún tipo (email, SMS, WhatsApp, push) al cliente ni al local. |
| RN-09 | La web de reservas muestra en un lugar visible y en la confirmación que **para cancelar o cambiar un turno hay que escribir por WhatsApp** al local, con el número configurado como link de contacto. |
| RN-10 | El cliente no puede cancelar ni modificar turnos desde la app; solo el admin cancela o mueve turnos. |
| RN-11 | Los turnos cancelados liberan el horario, se conservan en la agenda como cancelados y no cuentan en la línea de base de ausencias. |
| RN-12 | Solo se marca asistencia en turnos vigentes cuya hora de inicio ya pasó. |
| RN-13 | El precio es informativo: no se cobra ni se pide seña. |
| RN-14 | Bloqueos, cambios de horario o desactivaciones **nunca** cancelan turnos automáticamente; el admin decide. |

## 8. Requisitos no funcionales (nivel producto)

| ID | Requisito |
|---|---|
| RNF-01 | **Mobile-first:** el flujo de reserva completo (CU-01 a CU-04) se puede hacer en un celular de 360 px de ancho sin zoom ni scroll horizontal. El panel del admin se usa al menos en celular y computadora (P-11). |
| RNF-02 | **Accesibilidad:** la web del cliente cumple WCAG 2.1 nivel AA (contraste, uso con teclado y lector de pantalla, etiquetas en formularios, errores asociados al campo, objetivos táctiles cómodos). |
| RNF-03 | **Integridad de la agenda:** cero turnos superpuestos para un mismo profesional, incluso con reservas simultáneas (RN-01, RN-02; métrica M-2). |
| RNF-04 | **Privacidad de datos personales:** nombre y teléfono del cliente solo los ve el admin identificado; la web pública nunca muestra datos de otros clientes; se piden solo los datos necesarios; se informa al cliente para qué se usa su teléfono (contacto del local por su turno). Plazo de conservación y texto legal: P-12. |
| RNF-05 | **Seguridad del panel:** toda pantalla y acción del admin requiere estar identificado (CU-05). |
| RNF-06 | **Rapidez percibida:** los horarios disponibles se muestran en menos de 3 s en un celular con 4G (umbral a validar por el Architect). |
| RNF-07 | **Medición:** se registra lo necesario para calcular M-1 a M-3 y la línea de base de ausencias (origen del turno, inicio y confirmación de reserva, marca de asistencia), sin herramientas de reportes avanzados. |
| RNF-08 | **Idioma:** interfaz en español (Argentina). |

## 9. Preguntas abiertas

| ID | Pregunta | Propuesta | Dueño |
|---|---|---|---|
| P-01 | ¿Cuántos turnos por semana y cuántas dobles reservas hay hoy? ¿Cuántos profesionales? | Relevarlo con el local antes del lanzamiento como línea de base de M-1 y M-2. | Producto (Ezequiel) |
| P-02 | ¿Se confirman las métricas M-1 a M-3 y sus metas? | Las de §2. | Producto (Ezequiel) |
| P-03 | ¿Hasta cuántos días hacia adelante se puede reservar? ¿Con cuánta anticipación mínima (por ejemplo, no reservar para dentro de 30 min)? | 30 días hacia adelante; sin anticipación mínima. | Producto (Ezequiel) |
| P-04 | En turnos cargados por el admin, ¿el teléfono es obligatorio u opcional? | Opcional y recomendado (D-11). | Producto (Ezequiel) |
| P-05 | ¿El profesional necesita ver su agenda en el MVP? | No; siguiente iteración (D-07). | Producto (Ezequiel) |
| P-06 | Con "cualquiera", si hay varios profesionales libres, ¿a quién se asigna? | El de menos turnos ese día; definir con el CDO cómo se comunica. | Producto + Diseño (CDO) |
| P-07 | ¿Qué formato de teléfono se acepta (solo Argentina, con o sin código de área)? | Celulares de Argentina; el admin puede cargar otros. | Producto + Diseño |
| P-08 | ¿El link de WhatsApp abre con un mensaje prellenado con los datos del turno? | Sí, facilita que el local encuentre el turno. | Diseño (CDO) + Tech (Architect) |
| P-09 | ¿Cómo se crean las credenciales del admin y cómo se recuperan sin emails? ¿Cuántas personas usan el panel? | Una cuenta por persona, creada en la puesta en marcha; recuperación manual por el equipo. | Tech (Architect) + Producto |
| P-10 | ¿El admin puede cargar turnos fuera del horario de atención o sobre un bloqueo (con confirmación)? | Sí, con advertencia; nunca superpuestos. | Producto (Ezequiel) |
| P-11 | ¿Desde qué dispositivo usa el admin el panel (celular en el local, computadora en el mostrador)? | Ambos; mobile-first también en el panel. | Diseño (CDO) |
| P-12 | ¿Qué aviso de privacidad y plazo de conservación de datos de clientes se requiere? | Aviso breve en el paso de datos; conservación a definir. | Producto + Tech |
| P-13 | ¿Los horarios de inicio se ofrecen cada intervalo fijo (por ejemplo, cada 15 min) o según la duración del servicio? | Intervalo fijo configurable a nivel local, propuesta 15 min. | Producto + Tech |

## 10. Para el Architect

Evaluar factibilidad, costo y estimación de los CU Must y Should. Puntos a revisar (no se deciden en este documento):

- **Concurrencia (RN-01, RN-02):** garantizar cero superposiciones cuando dos clientes, o un cliente y el admin, toman el mismo hueco al mismo tiempo, incluido el caso "cualquiera".
- **Cálculo de disponibilidad (RN-04, P-13):** horarios por profesional, bloqueos de local y de profesional, duración del servicio, ventana de reserva y hora actual del local.
- **Zona horaria del local (RN-05):** consistencia de horarios aunque el cliente esté en otra zona; cambios de horario estacional si aplica.
- **Acceso del admin sin emails (CU-05, P-09):** alta y recuperación de credenciales.
- **Link de WhatsApp (P-08):** viabilidad del link de contacto con mensaje prellenado, sin integración con la API de WhatsApp.
- **Datos para métricas (RNF-07)** y **privacidad (RNF-04, P-12).**
- Indicar qué CU conviene postergar si el costo lo exige (candidato: CU-12).

## 11. Para el CDO

Diseñar los flujos y pantallas a partir de los CU (este documento no define pantallas). Puntos a resolver:

- **Flujo de reserva mobile-first en pocos pasos** (CU-01 a CU-03), con la opción "cualquiera" y la forma de navegar días y horarios.
- **Confirmación como único comprobante** (CU-04, D-02): cómo lograr que el cliente retenga los datos del turno sin email (por ejemplo, invitar a capturar pantalla) y cómo destacar el aviso de WhatsApp para cancelar o cambiar.
- **Aviso visible de WhatsApp** en toda la web de reservas (RN-09).
- **Mensajes de error y de horario ocupado** (CU-03 E2), conservando lo cargado.
- **Agenda del admin** diaria y semanal por profesional (CU-10), y el flujo rápido para resolver un pedido de cancelación o cambio recibido por WhatsApp (CU-11: buscar, mover o cancelar).
- **Accesibilidad WCAG 2.1 AA** (RNF-02) y textos en español claro.
- Preguntas a cerrar con producto: P-06, P-07, P-08, P-11.
