# Reserva de turnos para peluquería — MVP

- **Etapa:** Discovery, etapa 1 (problema y casos de uso)
- **Dueño de producto:** Ezequiel Avila
- **Estado:** borrador, ronda 7 (registra las decisiones de Ezequiel D-33 a D-35: el cliente no cambia ni cancela desde la app, sino por WhatsApp con el local, se elimina el link privado y el botón "Agregar al calendario", y se confirma que las reservas por teléfono se hacen desde la web pública. D-36 es una propuesta del PO). CU-06 y CU-07 quedan eliminados. Hay preguntas abiertas para el CDO, el Architect y el CPO (§11).
- **Lectores:** CDO (flujos y pantallas) y Architect (factibilidad y estimación). Este documento define **qué** y **por qué**; no decide tecnología ni componentes.

## 1. Problema

Hoy los turnos se toman por teléfono o WhatsApp. Esto genera tres problemas:

1. **El personal pierde tiempo** atendiendo llamadas y mensajes para reservar, cambiar o cancelar, muchas veces mientras atiende a un cliente.
2. **Hay reservas duplicadas**: dos clientes quedan en el mismo horario con el mismo peluquero porque la agenda no se actualiza a tiempo.
3. **Hay clientes que faltan sin avisar** y no se registra quién falta, así que el local no puede medir el problema ni actuar sobre él.

## 2. Usuarios

| Usuario | Descripción | Necesidad principal |
|---|---|---|
| **Cliente** | Persona que quiere cortarse el pelo u otro servicio. Usa sobre todo el celular. No quiere crear una cuenta. | Ver horarios libres y reservar solo, en cualquier momento, sin llamar. Si necesita cambiar o cancelar, saber cómo contactar al local por WhatsApp (D-33). |
| **Admin del local** | Dueño o encargado de la peluquería (puede ser también peluquero). | Mantener la agenda al día sin duplicados, mover o cancelar turnos (incluidos los pedidos de clientes que llegan por WhatsApp, D-33) y registrar quién asistió. Cuando un cliente llama para reservar, el local reserva por él desde la misma web pública que usa el cliente (D-23). |
| **Peluquero** | Trabaja con su propia agenda. En el MVP **no usa el sistema directamente**: su agenda la gestiona el admin. | Saber qué turnos tiene y no tener turnos superpuestos. |

**Usuario del panel (D-25):** el panel (la parte web con usuario y contraseña que usa el local) tiene **un único usuario admin**. No hay roles, ni varias cuentas, ni pantalla para administrar usuarios. Como hay un solo usuario, el MVP no registra qué persona movió o canceló cada turno. En el ambiente de staging (datos de prueba: demo de H1 y pruebas de Ezequiel) ese usuario se crea con usuario `admin` y contraseña `admin` (D-26, RNF-10). Antes de cargar el primer dato real de un cliente o de empezar el piloto de H2, el equipo técnico cambia esa contraseña (D-27, D-28).

## 3. Objetivo

**Objetivo del MVP (confirmado por Ezequiel, D-16):** que el cliente **reserve solo**, sin llamar, y que **no haya reservas duplicadas**.

- **Cambios y cancelaciones siguen por WhatsApp** con el local en el MVP (D-33): el MVP ataca la reserva y los duplicados; el local atiende esos pedidos moviendo o cancelando desde el panel (CU-13).
- La ausencia de turnos superpuestos es una **regla de negocio** (RN-01, RN-02, RNF-03), no una métrica: se cumple siempre.
- **Métricas: fuera del MVP por decisión de Ezequiel (D-16); se retoman más adelante.** El MVP no define metas numéricas ni registra datos con el fin de medir.
- *Referencia futura (no se implementa en el MVP):* las métricas que se habían propuesto eran % de turnos online, tasa de ausencias, conversión de reserva y llamadas o mensajes por semana para reservar (relevados con el local). Se reconsideran cuando se retome la medición.

## 4. Alcance del MVP

- **Una peluquería**, **una sola sede**, **varios peluqueros**, cada uno con su agenda.
- **Una sola zona horaria:** la del local. Todos los horarios se muestran y se guardan en esa zona.
- **Cliente** (web pensada para celular y accesible):
  - ve los servicios con duración y precio de referencia, si el local lo cargó (no se cobra; si no hay precio, ve "Consultá el precio en el local", D-19);
  - reserva: servicio → peluquero o "cualquiera disponible" → horario (inicios cada 15 minutos, hasta 30 días hacia adelante) → datos → confirmar;
  - no crea cuenta; datos: nombre (obligatorio), teléfono y email (opcionales; el teléfono se recomienda, D-3);
  - al confirmar ve el resumen **solo en pantalla** (sin link privado ni "Agregar al calendario", D-33, D-34);
  - **no cambia ni cancela desde la app** (D-33): para eso se comunica con el local por WhatsApp. La confirmación y un lugar visible de la web de reservas lo indican, con el WhatsApp del local (link de contacto tipo `wa.me`).
- **Reservas que llegan por teléfono:** el local reserva por el cliente desde la misma web pública, con los datos del cliente (CU-01 a CU-05). Es una reserva web normal, con las mismas reglas (D-23). El panel **no** tiene carga manual de turnos.
- **Admin del local** (un único usuario con login, D-25; el mecanismo lo define el Architect):
  - configura los datos del local (nombre, dirección, teléfono de contacto y WhatsApp para cambios y cancelaciones, D-33, D-36);
  - carga servicios, peluqueros, el horario semanal de cada peluquero y bloqueos (feriados, ausencias), con un motivo opcional que solo ve el local (D-30);
  - ve la agenda del día y de la semana;
  - mueve y cancela turnos, incluidos los pedidos de clientes por WhatsApp (si lo mueve a otro peluquero, solo a uno que hace ese servicio, D-31);
  - marca cada turno pasado como "asistió" o "no asistió"; esa marca es interna del local y el cliente no la ve (RN-24).
- **El MVP no envía ninguna notificación** (ver D-1). El link de WhatsApp lo abre el cliente por su cuenta; no es una notificación.

## 5. Casos de uso

Hitos (D-8, confirmados por Ezequiel):

- **Hito 1 (H1) — demo interna:** reserva + agenda con **datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. H1 no depende de nada de H2: la confirmación muestra el resumen y el aviso de WhatsApp para cambios y cancelaciones (D-33).
- **Hito 2 (H2) — MVP operable:** completa lo necesario para operar con clientes reales en un piloto (bloqueos, mover y cancelar turnos desde el panel —que es como se atienden los pedidos por WhatsApp, D-33— y asistencia). **Condición para empezar el piloto de H2:** la contraseña del admin ya no es `admin` (D-28, RNF-10). **H1:** CU-01 a CU-05, CU-08 a CU-10, CU-12 y CU-16. **H2:** CU-11, CU-13, CU-14 y los criterios marcados (H2).

Los criterios marcados **(H2)** dentro de un CU de H1 se implementan en H2.

| ID | Título | Actor | Hito |
|---|---|---|---|
| CU-01 | Ver servicios | Cliente | 1 |
| CU-02 | Ver horarios libres | Cliente | 1 |
| CU-03 | Reservar un turno | Cliente | 1 |
| CU-04 | Resolver una reserva simultánea del mismo horario | Cliente / sistema | 1 |
| CU-05 | Ver la confirmación | Cliente | 1 |
| CU-06 | *Eliminado (D-33): el cliente no cambia el turno desde la app* | — | — |
| CU-07 | *Eliminado (D-33): el cliente no cancela el turno desde la app* | — | — |
| CU-08 | Ingresar al panel del local | Admin | 1 |
| CU-09 | Gestionar servicios | Admin | 1 |
| CU-10 | Gestionar peluqueros y su horario semanal | Admin | 1 |
| CU-11 | Gestionar bloqueos (feriados y ausencias) | Admin | 2 |
| CU-12 | Ver la agenda del día y de la semana | Admin | 1 |
| CU-13 | Mover y cancelar turnos desde el panel | Admin | 2 |
| CU-14 | Marcar asistencia | Admin | 2 |
| CU-15 | *Eliminado (D-13, D-33): no hay anticipación mínima que configurar* | — | — |
| CU-16 | Configurar datos del local | Admin | 1 |

### CU-01 — Ver servicios

**Como** cliente, **quiero** ver los servicios con su duración y precio de referencia **para** elegir qué reservar.

- **Given** el local tiene servicios activos, **When** el cliente entra a la web de reservas, **Then** ve la lista de servicios activos con nombre, duración y precio de referencia, y una aclaración de que el precio es orientativo y se paga en el local.
- **Given** un servicio activo no tiene precio de referencia cargado, **When** el cliente ve la lista, **Then** en lugar del precio ve "Consultá el precio en el local" (D-19).
- **Given** un servicio está inactivo, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** el local no tiene servicios activos, **When** el cliente entra, **Then** ve un mensaje que indica que por ahora no hay reservas online y que puede contactar al local, con el teléfono de contacto del local (CU-16).
- **Given** un servicio activo no tiene ningún peluquero activo asignado, **When** el cliente ve la lista, **Then** ese servicio no aparece.
- **Given** el local cargó su WhatsApp (CU-16), **When** el cliente entra a la web de reservas, **Then** ve en un lugar visible (ubicación a definir por el CDO, P-10) un aviso del tipo "¿Querés cambiar o cancelar tu turno? Escribinos por WhatsApp al <número>", con un link que abre WhatsApp con ese número (D-33).
- **Given** el local no cargó su WhatsApp, **When** el cliente ve ese aviso, **Then** el aviso indica comunicarse con el local al teléfono de contacto (CU-16), sin link de WhatsApp (D-36).

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
- **Given** el cliente está en el paso de datos, **When** ve el campo teléfono, **Then** figura como opcional con un texto que recomienda dejarlo (por ejemplo, "para que el local pueda contactarte si hay un cambio") (D-3).
- **Given** el cliente ingresa un email o teléfono con formato inválido, **When** intenta confirmar, **Then** ve un mensaje que indica qué corregir y no se crea el turno.
- **Given** el cliente está en cualquier paso, **When** vuelve atrás, **Then** conserva lo que ya eligió en los pasos anteriores.
- **Given** un cliente llama al local para reservar, **When** el personal del local reserva desde la web pública con el servicio, el horario y los datos del cliente, **Then** el turno se crea como cualquier reserva web: solo se ofrecen horarios libres dentro del horario semanal, sin bloqueos, con inicios cada 15 minutos (RN-19) y hasta hoy + 30 días (RN-14); no hay excepciones fuera de horario (D-23).

### CU-04 — Resolver una reserva simultánea del mismo horario

**Como** cliente, **quiero** saber claramente si mi horario se ocupó mientras reservaba **para** elegir otro sin confusión.

- **Given** dos clientes confirman al mismo tiempo el mismo horario con el mismo peluquero, **When** se procesan ambas reservas, **Then** se confirma una sola y en la agenda no queda ningún turno superpuesto.
- **Given** la reserva de un cliente no se pudo confirmar porque el horario se ocupó, **When** recibe la respuesta, **Then** ve un mensaje claro ("Ese horario se acaba de ocupar, elegí otro"), los horarios libres actualizados y conserva el servicio y los datos que ya cargó.
- **Given** el cliente eligió "cualquiera disponible" y el peluquero que se le iba a asignar se ocupó, **When** queda otro peluquero que hace ese servicio y está libre en ese horario, **Then** se le asigna ese (si queda más de uno, según RN-20) y la reserva se confirma; **When** no queda ninguno, **Then** ve el mensaje del criterio anterior.

### CU-05 — Ver la confirmación

**Como** cliente, **quiero** ver el resumen de mi turno y saber cómo cambiarlo o cancelarlo **para** ir tranquilo sin que me envíen nada.

- **Given** el turno se confirmó, **When** el cliente ve la pantalla de confirmación, **Then** ve servicio, peluquero, fecha, hora, duración, precio de referencia (o "Consultá el precio en el local" si el servicio no tiene precio, D-19) y nombre, dirección y teléfono de contacto del local (CU-16).
- **Given** el turno se confirmó y el local cargó su WhatsApp (CU-16), **When** el cliente ve la confirmación, **Then** ve un aviso de que para cambiar o cancelar debe escribirle al local por WhatsApp, con el número y un link que abre WhatsApp con ese número (D-33).
- **Given** el turno se confirmó y el local no cargó su WhatsApp, **When** el cliente ve la confirmación, **Then** el aviso indica comunicarse con el local al teléfono de contacto, sin link de WhatsApp (D-36).
- **Given** el cliente ve la confirmación, **When** busca cómo cambiar o cancelar, guardar un link o agregar el turno al calendario, **Then** esas opciones no existen: la confirmación es solo en pantalla (D-33, D-34).
- **Given** el turno se confirmó, **When** pasa cualquier tiempo, **Then** el sistema no envía email, SMS ni WhatsApp al cliente (D-1).

> **Nota (D-33):** después de cerrar la confirmación, la web no permite volver a ver el turno. Si el cliente necesita los datos, cambiar o cancelar, le escribe al local por WhatsApp. Cuando el local reserva por un cliente que llamó (D-23), le pasa los datos del turno por su cuenta, fuera del sistema.

### CU-06 — Eliminado (D-33)

Este caso de uso (cambiar el turno desde el link privado) se eliminó: en el MVP el cliente no cambia su turno desde la app; le escribe al local por WhatsApp y el admin lo mueve desde el panel (CU-13). El ID se conserva para no romper referencias externas (por ejemplo, del diseño).

### CU-07 — Eliminado (D-33)

Este caso de uso (cancelar el turno desde el link privado) se eliminó: en el MVP el cliente no cancela su turno desde la app; le escribe al local por WhatsApp y el admin lo cancela desde el panel (CU-13). El ID se conserva para no romper referencias externas.

### CU-08 — Ingresar al panel del local

**Como** admin, **quiero** un acceso protegido **para** que solo el personal autorizado gestione la agenda.

- **Given** un usuario no autenticado, **When** intenta abrir cualquier pantalla del panel, **Then** se le pide iniciar sesión y no ve datos de la agenda ni de clientes.
- **Given** el usuario admin con credenciales válidas, **When** inicia sesión, **Then** accede al panel.
- **Given** credenciales inválidas, **When** intenta iniciar sesión, **Then** ve un mensaje de error genérico y no accede.
- **Given** el admin está en el panel, **When** busca crear, invitar o administrar otros usuarios, **Then** esa opción no existe: el panel tiene un único usuario admin, sin roles (D-25).
- **Given** el ambiente de staging recién preparado, **When** alguien inicia sesión con usuario `admin` y contraseña `admin`, **Then** accede al panel sin que el equipo tenga que crear el usuario a mano (D-26, RNF-10).
- **Given** un ambiente con datos reales de clientes, **When** alguien intenta entrar con usuario `admin` y contraseña `admin`, **Then** no accede (D-27, D-28, RNF-10).
- **Given** el admin está en el panel, **When** busca cómo cambiar su contraseña, **Then** esa opción no existe: la cambia el equipo técnico (§8, D-28).
- El mecanismo de autenticación y la forma de crear el usuario lo define el Architect. Referencia (§10): usuario y contraseña, sin recuperación por email; el equipo resetea la contraseña a mano.

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
- **Given** el admin crea un bloqueo con un motivo (por ejemplo, "Feriado" o "Vacaciones de Ana"), **When** guarda, **Then** el bloqueo se guarda y el motivo se ve en la lista de bloqueos y en la agenda del panel (CU-12) (D-30).
- **Given** el admin crea un bloqueo sin motivo, **When** guarda, **Then** el bloqueo se guarda igual: el motivo es opcional (D-30).
- **Given** hay un bloqueo con motivo, **When** el cliente consulta horarios, **Then** no ve el motivo ni que hay un bloqueo: solo deja de ver esos horarios como libres (D-30, RN-22).
- **Given** el admin elimina un bloqueo, **When** el cliente consulta horarios, **Then** esos horarios vuelven a estar libres.

### CU-12 — Ver la agenda del día y de la semana

**Como** admin, **quiero** ver la agenda **para** saber quién viene, con quién y cuándo.

- **Given** el admin está en el panel, **When** abre la agenda del día, **Then** ve los turnos de ese día por peluquero, con hora, duración, servicio, nombre del cliente, contacto (si lo dejó) y estado. Todos los turnos se crean desde la web (D-23), incluidos los que el local reserva por un cliente que llamó.
- **Given** el admin cambia a la vista semanal, **When** se muestra, **Then** ve los turnos de los 7 días por peluquero, como una lista agrupada por día, y puede navegar a semanas anteriores y siguientes.

> **Nota (D-22):** en H1 la agenda semanal es una **lista por día**, no una grilla horaria. En la demo de H1 se valida si hace falta una grilla; no es una pregunta bloqueante.

- **Given** el admin filtra por peluquero, **When** aplica el filtro, **Then** solo ve los turnos de ese peluquero.
- **(H2)** **Given** hay un bloqueo del local o de un peluquero en el período que se muestra, **When** el admin ve la agenda, **Then** ve el bloqueo con su motivo, si lo tiene (CU-11, D-30).
- **Given** un cliente reservó desde la web, **When** el admin abre o actualiza la agenda, **Then** ve el turno nuevo.
- **(H2)** **Given** un turno fue cancelado, **When** el admin ve la agenda, **Then** puede distinguirlo de los vigentes y su horario figura como libre.

### CU-13 — Mover y cancelar turnos desde el panel

**Como** admin, **quiero** mover y cancelar turnos desde el panel **para** atender los pedidos de cambio o cancelación de los clientes (por WhatsApp o teléfono, D-33) y resolver cambios del local.

- **Given** un cliente llama para reservar, **When** el admin busca en el panel una opción para crear un turno, **Then** esa opción no existe: el local reserva desde la web pública (CU-03, D-23).
- **Given** el admin intenta mover un turno a un horario que se superpone con otro del mismo peluquero, **When** guarda, **Then** ve el error y el turno no se mueve. Esto no se puede forzar (RN-01).
- **Given** el admin mueve un turno a un horario fuera del horario semanal del peluquero o dentro de un bloqueo, **When** guarda, **Then** el sistema le advierte el motivo y le pide confirmación; si confirma, el turno se mueve; si no, queda como estaba (D-11). Esta excepción no aplica al servicio: el peluquero nuevo siempre hace ese servicio (D-31).
- **Given** el admin mueve un turno, **When** elige el horario nuevo, **Then** solo puede elegir inicios cada 15 minutos (RN-19).
- **Given** un turno de "Color" de Ana, y Beto no hace "Color" pero Carla sí, **When** el admin elige a qué peluquero moverlo, **Then** se le ofrece Carla y no se le ofrece Beto; no hay advertencia para confirmar ni otra forma de asignárselo a Beto (D-31, RN-23).
- **Given** el admin necesita asignar el turno a un peluquero que no hace ese servicio, **When** primero le agrega el servicio a ese peluquero (CU-10), **Then** ese peluquero pasa a ofrecerse al mover el turno (D-31).
- **Given** un cliente pide por WhatsApp cambiar su turno, **When** el admin lo mueve a otro horario (puede ser con otro peluquero que hace ese servicio) y guarda, **Then** el turno queda en el nuevo horario y el anterior se libera (D-33).
- **Given** un cliente pide por WhatsApp cancelar su turno, **When** el admin lo cancela y confirma, **Then** el turno queda cancelado y el horario se libera (D-33).
- **Given** el turno empieza en 30 minutos, **When** el admin lo mueve o lo cancela, **Then** puede hacerlo: no hay anticipación mínima (D-7).
- **Given** el admin mueve un turno a un día posterior a hoy + 30 días, **When** guarda, **Then** puede hacerlo: la ventana de 30 días rige para las reservas desde la web, no para mover desde el panel (RN-14, D-14).
- **Given** el admin mueve o cancela un turno, **When** lo hace, **Then** no se envía ninguna notificación; el panel muestra el teléfono del cliente (si lo tiene) para que el local lo contacte por su cuenta (D-1, D-3).

### CU-14 — Marcar asistencia

**Como** admin, **quiero** marcar si el cliente vino **para** registrar quién falta sin avisar.

- **Given** un turno vigente cuya hora de inicio ya pasó, **When** el admin lo marca "asistió" o "no asistió", **Then** el turno guarda esa marca y se ve en la agenda.
- **Given** un turno futuro, **When** el admin intenta marcar asistencia, **Then** no puede hacerlo.
- **Given** el admin se equivocó al marcar, **When** cambia la marca, **Then** queda la última marca.
- **Given** hay turnos pasados sin marcar, **When** el admin abre la agenda, **Then** los ve identificados como "sin marcar" para completarlos.
- **Given** un turno cancelado, **When** el admin ve la agenda, **Then** no se puede marcar asistencia.
- **Given** el admin marcó un turno como "no asistió" (o "asistió"), **When** el cliente entra a la web de reservas, **Then** no ve la marca: la web no muestra turnos ya reservados y la asistencia es un dato interno del local (D-33, RN-24).

### CU-15 — Eliminado (D-13)

Este caso de uso (configurar la anticipación mínima) se eliminó: primero se fijó en 2 horas sin configuración (D-13). El ID se conserva para no romper referencias externas (por ejemplo, del diseño). Desde D-33 el cliente tampoco cambia ni cancela desde la app, así que no hay anticipación mínima.

### CU-16 — Configurar datos del local

**Como** admin, **quiero** cargar el nombre, la dirección, el teléfono de contacto y el WhatsApp del local **para** que el cliente los vea, sepa a dónde ir y a quién escribirle para cambiar o cancelar.

- **Given** el admin está en el panel, **When** carga nombre, dirección y teléfono de contacto del local y guarda, **Then** esos datos se muestran en la web de reservas y en la confirmación del turno (CU-05).
- **Given** el admin carga el número de WhatsApp del local y guarda, **When** el cliente ve el aviso de cambios y cancelaciones (CU-01, CU-05), **Then** ve ese número con un link que abre WhatsApp con él (D-33).
- **Given** el admin deja vacío el WhatsApp, **When** guarda, **Then** se guarda igual (es opcional) y el panel le indica que, sin WhatsApp, el aviso al cliente mostrará el teléfono de contacto (D-36).
- **Given** el admin ingresa un WhatsApp con formato inválido, **When** intenta guardar, **Then** ve el error junto al campo y no se guarda.
- **Given** el admin deja vacío el nombre, la dirección o el teléfono de contacto, **When** intenta guardar, **Then** ve el error junto al campo y no se guarda.
- **Given** el admin cambia el teléfono de contacto, **When** un cliente ve un mensaje de "contactá al local", **Then** el mensaje muestra el teléfono nuevo.
- **Given** cualquier texto al cliente que le indique contactar al local, **When** se muestra, **Then** incluye el teléfono de contacto cargado en este CU.

## 6. Reglas de negocio

| ID | Regla |
|---|---|
| RN-01 | Un peluquero **nunca** tiene dos turnos vigentes superpuestos, ni al reservar desde la web ni al mover desde el panel. |
| RN-02 | Si dos reservas simultáneas piden el mismo horario del mismo peluquero, se confirma **una sola**; la otra recibe un mensaje claro para elegir otro horario. |
| RN-03 | La duración del turno la define el servicio, con el valor vigente al momento de reservar. |
| RN-04 | Un horario está libre si el peluquero hace el servicio, el horario cae completo dentro de su horario semanal, no hay bloqueos del local ni del peluquero y no se superpone con otro turno vigente. |
| RN-05 | Todos los horarios se expresan en la zona horaria del local. |
| RN-06 | El cliente no crea cuenta. Nombre obligatorio; teléfono y email opcionales; el teléfono se recomienda (D-3). Rige igual cuando el local reserva por un cliente desde la web (D-23). |
| RN-07 | ~~Cada turno tiene un link privado, difícil de adivinar, que permite ver solo ese turno (H1) y cambiarlo o cancelarlo (H2). El link no cambia aunque el cliente cambie el turno o el admin lo mueva, y siempre muestra el estado vigente: en H1, "Confirmado"; en H2, además, el aviso "El local cambió tu turno: ahora es…" si el admin lo movió, que está cancelado si el admin lo canceló (D-18), o "Turno pasado" si la hora de inicio ya pasó, sin la marca de asistencia y sin cambiar ni cancelar (D-32).~~ **Eliminada (D-33):** el cliente no cambia ni cancela desde la app y no hay link privado. |
| RN-08 | ~~El cliente puede cambiar o cancelar desde el link si faltan **2 horas o más** para el inicio; con **menos de 2 horas**, no. El valor es **fijo** para todos los locales y no se configura (D-13). El admin no tiene esa restricción (D-7).~~ **Eliminada (D-33):** el cliente no cambia ni cancela desde la app y no hay link privado. |
| RN-09 | ~~Si al cambiar el turno el horario nuevo ya no está libre, el cliente conserva el turno original sin cambios.~~ **Eliminada (D-33):** el cliente no cambia ni cancela desde la app y no hay link privado. |
| RN-10 | Los turnos cancelados liberan el horario y no se les puede marcar asistencia. |
| RN-11 | Solo se puede marcar asistencia en turnos vigentes cuya hora de inicio ya pasó. |
| RN-12 | El sistema no envía notificaciones de ningún tipo (email, SMS, WhatsApp, push). El link de contacto por WhatsApp lo abre el cliente; no es un envío del sistema (D-33). |
| RN-13 | El precio es solo de referencia: no se cobra ni se pide seña. Es **opcional** al crear el servicio; si falta, la web muestra "Consultá el precio en el local" (D-19). |
| RN-14 | No se puede reservar en horarios pasados. El cliente puede reservar desde hoy hasta **30 días corridos hacia adelante** (hoy + 30 días, inclusive) (D-14). Rige también cuando el local reserva por un cliente desde la web (D-23). La ventana no limita al admin al **mover** turnos desde el panel (D-14). |
| RN-15 | Un servicio sin peluqueros activos asignados no se ofrece al cliente. Un peluquero sin servicios asignados no aparece en la reserva. |
| RN-16 | El admin puede **mover** turnos fuera del horario semanal o sobre un bloqueo solo después de una advertencia y su confirmación. Nunca puede superponer turnos (RN-01). Al reservar desde la web (también el local por un cliente) no hay excepciones: solo se ofrecen horarios libres (RN-04, D-23). |
| RN-17 | Todo texto al cliente que indique contactar al local muestra el teléfono de contacto configurado (CU-16). El aviso de cambios y cancelaciones muestra el WhatsApp si está cargado (RN-25). |
| RN-18 | ~~Desde el link, el cliente solo cambia día y horario, con el mismo servicio y el mismo peluquero (o "cualquiera"). Para cambiar el servicio, cancela y reserva de nuevo.~~ **Eliminada (D-33):** el cliente no cambia ni cancela desde la app y no hay link privado. |
| RN-19 | Los turnos empiezan en **intervalos de 15 minutos** (:00, :15, :30, :45), tanto los que se reservan desde la web (por el cliente o por el local) como los que mueve el admin (D-21). |
| RN-20 | Con "cualquiera disponible", el turno se asigna, entre los peluqueros activos que hacen ese servicio y están libres en ese horario, al que tenga **menos turnos vigentes ese día** (sin contar cancelados). **Desempate:** si empatan, se asigna al que fue cargado primero en el panel (orden de carga). (D-15). |
| RN-21 | La duración de un servicio va de **5 a 480 minutos**, en múltiplos de 5 (D-20). |
| RN-22 | Un bloqueo puede tener un **motivo opcional** en texto libre, visible solo en el panel (lista de bloqueos y agenda). El cliente nunca ve el motivo: solo deja de ver esos horarios como libres (D-30). |
| RN-23 | Al **mover** un turno a otro peluquero desde el panel, solo se puede elegir uno que hace ese servicio. No es una advertencia confirmable: los que no lo hacen no se ofrecen. Para asignarlo igual, primero se agrega el servicio al peluquero (CU-10) (D-31). |
| RN-24 | La **marca de asistencia** ("asistió" / "no asistió") es interna del local: el cliente no la ve en ningún lado (la web no muestra turnos ya reservados, D-33). |
| RN-25 | El cliente **no cambia ni cancela** su turno desde la app. La confirmación y un lugar visible de la web de reservas le indican que lo haga por WhatsApp con el local, con el número y un link de contacto; si el local no cargó WhatsApp, se muestra el teléfono de contacto (D-33, D-36). Cambios y cancelaciones los hace el admin desde el panel (CU-13). |

## 7. Requisitos no funcionales

| ID | Requisito |
|---|---|
| RNF-01 | **Celular primero:** el flujo de reserva completo se puede hacer en un celular (pantalla de 360 px de ancho) sin zoom ni scroll horizontal. |
| RNF-02 | **Accesibilidad:** la web del cliente cumple WCAG 2.1 nivel AA (contraste, navegación por teclado, etiquetas en formularios, mensajes de error asociados al campo, compatibilidad con lector de pantalla). |
| RNF-03 | **Integridad de la agenda:** cero turnos superpuestos para un mismo peluquero, incluso con reservas simultáneas (RN-01, RN-02). |
| RNF-04 | **Privacidad:** los datos de clientes (nombre, teléfono, email) solo los ve el admin autenticado. La web pública no expone datos de turnos ni de clientes, ni el motivo de los bloqueos (D-30), ni la marca de asistencia (RN-24). Se guardan solo los datos necesarios. |
| RNF-05 | **Seguridad del panel:** todas las pantallas y acciones del admin requieren autenticación. |
| RNF-06 | **Rapidez percibida** *(validada por el Architect)*: los horarios libres se muestran en **menos de 3 s** en un celular con conexión 4G, medido desde que el cliente elige el día. Se exceptúa el primer acceso después de un rato de inactividad (arranque en frío señalado por el Architect). Según el Architect es factible: lo esperable es menos de 1 s, con la app y la base de datos en la misma región. |
| RNF-07 | **Costo:** el MVP opera con costo de infraestructura US$ 0 en Vercel Hobby (D-9, §10), incluido el piloto de H2, que se considera uso no comercial por decisión de Ezequiel (D-17). **Condición:** si el uso pasa a ser comercial, se revisa la licencia. |
| RNF-08 | *Eliminado (D-16): el MVP no tiene métricas; la medición queda fuera de alcance.* |
| RNF-09 | **Idioma:** interfaz en español. |
| RNF-10 | **Ambiente de staging (datos de prueba):** staging es el ambiente de pruebas con datos de prueba (demo de H1 y pruebas de Ezequiel). Ahí el único usuario admin se crea automáticamente al preparar el ambiente, con usuario `admin` y contraseña `admin` (D-26). Es un requisito **solo de staging, no de producción**; cómo se crea lo define el Architect. **Riesgo aceptado (D-27):** son credenciales conocidas, así que cualquiera con la URL puede entrar al panel; solo es aceptable con datos de prueba. **Cambio de contraseña (D-28):** antes de cargar el primer dato real de un cliente o de empezar el piloto de H2, en el ambiente que sea, el equipo técnico (Architect) cambia la contraseña del admin por una no conocida y se la pasa al local. No hay pantalla para hacerlo. Es condición para empezar el piloto de H2. En qué ambiente corre el piloto lo define el Architect. El reseteo manual de contraseña por el equipo sigue igual (CU-08, §10). |

## 8. Fuera de alcance del MVP

- **Notificaciones** de cualquier tipo: email de confirmación, recordatorio, avisos por email, WhatsApp o SMS (iteración futura).
- Pagos y señas.
- Varias sedes y varias zonas horarias.
- Fidelización, puntos o promociones.
- Reseñas y calificaciones.
- App nativa (iOS/Android).
- Lista de espera.
- Cuentas de cliente e historial del cliente.
- Botón **"Agregar al calendario"** e integración con Google Calendar u otros calendarios: la confirmación es solo en pantalla (D-34).
- **Autogestión del cliente para cambiar o cancelar** y **link privado** del turno: el cliente lo pide por WhatsApp y el admin lo resuelve desde el panel (D-33).
- Acceso propio de los peluqueros al sistema (lo gestiona el admin).
- **Métricas y medición** (% online, ausencias, conversión, llamadas): fuera del MVP por decisión de Ezequiel; se retoman más adelante (D-16).
- **Carga manual de turnos desde el panel:** cuando un cliente llama, el local reserva desde la web pública (D-23).
- Varios usuarios del panel, roles, pantalla para administrar usuarios y registro de qué persona movió o canceló cada turno (D-25).
- Pantalla para cambiar la contraseña del admin: la cambia el equipo técnico (D-28).
- Mostrarle al cliente la marca de asistencia o el motivo de un bloqueo (D-30, RN-24).

## 9. Decisiones

| ID | Decisión | Por qué | Quién |
|---|---|---|---|
| D-1 | El MVP **no envía ninguna notificación** (ni confirmación, ni recordatorio, ni links por email o WhatsApp). | Cero fricción y cero costo para validar rápido; evita proveedores, plantillas y costos de mensajería. | Ezequiel Avila |
| D-2 | ~~**Autogestión por link privado** mostrado en pantalla + botón "Agregar al calendario" que reemplaza al recordatorio (opción A del Architect). Si el cliente pierde el link, llama al local y el admin gestiona el turno.~~ **Obsoleta, reemplazada por D-33 y D-34:** no hay link privado ni "Agregar al calendario"; el cliente pide cambios y cancelaciones por WhatsApp. | Permite cambiar y cancelar sin cuentas ni notificaciones; el calendario del celular cumple el rol del recordatorio sin costo. | CPO (sobre la opción A del Architect) |
| D-3 | **Sin cuenta de cliente**; solo nombre obligatorio, teléfono y email opcionales; el teléfono se **recomienda** en la web. **Riesgo aceptado para el MVP (reformulado por D-33):** el cliente no tiene cómo ver su turno en la app, así que si el local lo mueve o cancela, solo se entera si el local lo contacta por su cuenta; si el cliente no dejó teléfono, el local no tiene cómo avisarle (el MVP no notifica, D-1). | Menos pasos, mayor conversión, menos datos personales. | CPO; recomendación del teléfono y riesgo aceptado: Ezequiel Avila (antes en D-12; reformulado con D-33) |
| D-4 | **Una sede, varios peluqueros, una zona horaria.** | Cubre el caso real de una peluquería y simplifica la validación. | CPO |
| D-5 | **Precio solo de referencia**, sin cobro. | Pagos y señas suman complejidad y costo; se evalúan después del MVP. | CPO |
| D-6 | ~~Al cambiar un turno, si el horario nuevo se ocupó, el cliente **conserva el original**.~~ **Obsoleta, reemplazada por D-33:** el cliente no cambia el turno desde la app. | El cliente nunca se queda sin turno por intentar cambiarlo. | CPO |
| D-7 | El admin puede **mover o cancelar** turnos desde el panel en cualquier momento, sin anticipación mínima (no carga turnos, D-23). Ajustada por D-33: el cliente ya no cambia ni cancela desde la app, así que la anticipación mínima desaparece. | El local necesita resolver casos de último momento (por ejemplo, un cliente que llama). | CPO (Ezequiel no la objetó al responder P-2) |
| D-8 | **H1 es una demo interna con datos de prueba, sin clientes reales**, para validar el flujo y el diseño temprano con Ezequiel y alguna peluquera. **H2 completa el MVP operable**, que es el que se usa en un piloto real. H1 muestra la confirmación con el aviso de WhatsApp; mover y cancelar desde el panel (CU-13) llegan en H2. | Es la opción más barata y coherente con Vercel Hobby (D-9, D-17). Además, operar con clientes reales sin que el local pueda mover o cancelar turnos desde el panel empeoraría el problema que se quiere resolver. (Ajustado por D-23: las reservas por teléfono se hacen desde la web, disponible desde H1. Ajustado por D-33: ya no hay link privado; los pedidos por WhatsApp se resuelven con CU-13, en H2.) | CPO (resuelve B1); **confirmada por Ezequiel Avila** (P-4) |
| D-9 | **Infraestructura: Vercel Hobby, US$ 0** (factibilidad del Architect). **Condición:** Hobby es solo para uso no comercial. El uso actual, incluido el piloto de H2, se considera no comercial (D-17); si pasa a uso comercial, hay que volver a decidir la licencia. | Costo cero para la demo interna, la validación y el piloto; coherente con D-8. | Ezequiel Avila (aprobación escrita: "Apruebo la factibilidad Hobby") |
| D-10 | ~~Desde el link, el cliente cambia solo **día y horario** (mismo servicio y peluquero o "cualquiera"); para cambiar el servicio cancela y reserva de nuevo. El link no cambia.~~ **Obsoleta, reemplazada por D-33:** el cliente no cambia el turno desde la app; no hay link. | Es lo más simple para el MVP. | CPO (pedido del PO Reviewer) |
| D-11 | El admin puede **mover** turnos fuera del horario semanal o sobre un bloqueo con advertencia y confirmación, pero nunca superponerlos. Ajustada por D-23: ya no hay carga desde el panel, y al reservar desde la web no hay excepciones. | El local resuelve excepciones reales (un cliente que necesita otro horario) sin romper la regla de no superposición. | CPO (pedido del PO Reviewer); ajuste: Ezequiel Avila (D-23) |
| D-12 | ~~En los turnos que carga el admin, el teléfono también es opcional.~~ **Obsoleta, reemplazada por D-23:** el panel ya no carga turnos. La recomendación del teléfono en la web y el riesgo aceptado por clientes sin teléfono siguen vigentes y pasan a D-3. | Ya no hay turnos cargados por el admin. | Ezequiel Avila |
| D-13 | ~~La **anticipación mínima** para que el cliente cambie o cancele es **2 horas, fija**, para siempre y sin configuración por local. Se elimina CU-15 (el ID se conserva marcado como eliminado).~~ **Obsoleta, reemplazada por D-33:** sin cambios ni cancelaciones del cliente desde la app no hay anticipación mínima. CU-15 sigue eliminado. | Simplifica el MVP: no hace falta pantalla ni validaciones de configuración. | Ezequiel Avila (P-2) |
| D-14 | **Ventana de reserva: 30 días hacia adelante** (hoy + 30 días corridos, inclusive) para reservar. No limita al admin al mover turnos desde el panel; sí rige cuando el local reserva por un cliente desde la web (D-23). | Evita reservas muy lejanas que el local no puede garantizar; el admin necesita resolver excepciones del local. | Ezequiel Avila (P-5); "30 días corridos, inclusive" y que no limite al admin al mover: propuesta PO, aceptada por el CPO |
| D-15 | Con **"cualquiera disponible"**, se asigna al peluquero que hace el servicio, está libre en ese horario y tiene **menos turnos vigentes ese día** (los cancelados no cuentan). **Desempate:** el que fue cargado primero en el panel. (Ajustada por D-33: se quita la reasignación al cambiar desde el link, que ya no existe.) | Reparte la carga del día; el desempate por orden de carga es simple, determinístico y no requiere configuración. | Ezequiel Avila (P-7); desempate y cancelados que no cuentan: propuesta PO, aceptada por el CPO |
| D-16 | **Por ahora sin métricas.** El objetivo del MVP es cualitativo: que el cliente reserve solo y sin duplicados. Las métricas propuestas quedan fuera del MVP y se retoman más adelante; se elimina RNF-08. La regla de cero superposiciones se mantiene como regla de negocio. | Prioriza validar el flujo antes de medir. | Ezequiel Avila (P-1) |
| D-17 | El uso actual, **incluido el piloto de H2, se considera no comercial**; se mantiene **Vercel Hobby**. La licencia se revisa solo si pasa a uso comercial. | "No es uso comercial esto por ahora." | Ezequiel Avila (P-9) |
| D-18 | ~~Estado en el **link privado**: en H1 solo muestra "Confirmado". En H2, si el admin movió el turno, muestra un aviso del tipo "El local cambió tu turno: ahora es…"; si lo canceló, muestra que está cancelado.~~ **Obsoleta, reemplazada por D-33:** no hay link privado; el cliente se entera de cambios del local solo si el local lo contacta (D-3). | Sin notificaciones (D-1), el link es la única vía para que el cliente se entere de cambios del local. | CPO (P-D1 del diseño) |
| D-19 | El **precio de referencia es opcional** al crear un servicio; si falta, la web muestra "Consultá el precio en el local". | Hay servicios con precio variable; no bloquear la carga del local. | CPO (P-D2 del diseño) |
| D-20 | La **duración de un servicio** va de **5 a 480 minutos**, en múltiplos de 5. | Cubre desde un retoque hasta un servicio de jornada completa y evita valores sin sentido. | CPO (P-D3 del diseño) |
| D-21 | Los turnos **empiezan cada 15 minutos** (intervalo de inicio). | Agenda prolija y fácil de leer, con suficiente flexibilidad. | CPO (pregunta del diseño) |
| D-22 | La **agenda semanal en H1 es una lista por día**; en la demo de H1 se valida si hace falta una grilla horaria. | Es lo más simple para H1; la grilla se decide con evidencia de la demo. | CPO (P-D6 del diseño) |
| D-23 | **Sin carga manual de turnos en el panel.** Cuando un cliente llama, el local reserva por él desde la misma web pública (CU-01 a CU-05), con los datos del cliente. Es una reserva web normal: rigen la ventana de 30 días (RN-14), los inicios cada 15 minutos (RN-19) y solo se ofrecen horarios libres, sin excepciones fuera de horario. CU-13 pasa a ser "Mover y cancelar turnos desde el panel"; mover y cancelar se mantienen. D-12 queda obsoleta; D-7 y D-11 se ajustan a mover y cancelar. | "Que eso lo hagan desde la app la misma peluquería, total no necesita una app distinta para eso." Menos alcance y un solo flujo de reserva. | Ezequiel Avila |
| D-24 | ~~El local puede **copiar el link privado** de la confirmación y mandárselo al cliente **por su cuenta** (WhatsApp u otro medio), fuera del sistema. El sistema no lo envía (D-1 sigue vigente).~~ **Obsoleta, reemplazada por D-33:** no hay link privado; el local le pasa los datos del turno al cliente por su cuenta. | Le da al cliente que reservó por teléfono la misma autogestión que al resto, sin agregar notificaciones. | CPO |
| D-25 | **Un único usuario admin** en el panel: sin roles, sin varias cuentas, sin pantalla para administrar usuarios. No se registra qué persona movió o canceló cada turno. Resuelve P-8. | "Hagamos que haya un único user admin." Es lo más simple para una prueba piloto. | Ezequiel Avila |
| D-26 | En el **ambiente de staging** (ambiente de pruebas con **datos de prueba**: demo de H1 y pruebas de Ezequiel), el usuario admin se crea automáticamente al preparar el ambiente, con usuario `admin` y contraseña `admin` (RNF-10). Es requisito de staging, no de producción; cómo se hace lo define el Architect. Staging, mientras tenga datos de prueba, no es el ambiente del piloto de H2: en qué ambiente corre el piloto lo define el Architect, y antes de cualquier dato real rige el cambio de contraseña de D-28. | "Es todo una prueba piloto [...] así lo puedo probar." | Ezequiel Avila; alcance de staging aclarado por el CPO (D-28) |
| D-27 | **Riesgo aceptado:** las credenciales conocidas (`admin`/`admin`) solo son aceptables en staging con **datos de prueba**; cualquiera con la URL puede entrar al panel. Antes de cargar datos reales de clientes o de empezar el piloto de H2, la contraseña se cambia según D-28. El reseteo manual de contraseña por el equipo sigue igual. | Habilita la prueba de Ezequiel sin exponer datos personales reales (RNF-04). | CPO |
| D-28 | **Staging = datos de prueba; cambio de contraseña antes de datos reales.** Staging es el ambiente de pruebas con datos de prueba (demo de H1 y pruebas de Ezequiel); ahí vale `admin`/`admin` (D-26). Antes de cargar el primer dato real de un cliente o de empezar el piloto de H2, sea en el ambiente que sea, el **equipo técnico (Architect)** cambia la contraseña del admin por una no conocida y se la pasa al local. No hay pantalla para hacerlo. Ese cambio es **condición para empezar el piloto de H2**. En qué ambiente corre el piloto lo define el Architect, no este documento. | Separa con claridad pruebas y piloto, y evita exponer datos reales con credenciales conocidas (RNF-04). | CPO |
| D-29 | ~~**Riesgo aceptado:** en una reserva por teléfono, si el local no copia el link privado en la pantalla de confirmación, el cliente se queda sin autogestión. Si necesita cambiar o cancelar, llama al local y el admin mueve o cancela desde el panel (mismo camino que cuando un cliente pierde el link, D-2). El panel **no muestra ni recupera** links privados en el MVP. Se revisa con lo que se vea en la demo.~~ **Obsoleta, reemplazada por D-33:** no hay link privado; todo pedido de cambio o cancelación pasa por el admin (CU-13). | Mantiene el MVP simple y sin nuevas pantallas; el camino alternativo ya existe (CU-13). | CPO |
| D-30 | **Motivo del bloqueo (CU-11):** el bloqueo admite un **motivo opcional** en texto libre (por ejemplo, "Feriado" o "Vacaciones de Ana"), visible **solo en el panel** (lista de bloqueos y agenda). El cliente nunca ve el motivo: solo deja de ver esos horarios libres. | Ayuda al local a recordar por qué bloqueó, sin exponer datos personales (por ejemplo, una ausencia por enfermedad). | CPO (pregunta del diseño) |
| D-31 | **Mover a otro peluquero (CU-13):** solo se puede elegir un peluquero que **hace ese servicio**. No es una advertencia confirmable (a diferencia de D-11): los que no lo hacen directamente no se ofrecen. Si el local necesita asignarlo igual, primero agrega el servicio a ese peluquero (CU-10). | El cliente reservó un servicio y tiene que recibirlo; mantiene la misma regla que la reserva web. | CPO (pregunta del diseño) |
| D-32 | ~~**Asistencia en el link privado (CU-05, CU-14):** el link privado **no muestra** "asistió" ni "no asistió". Un turno cuya hora de inicio ya pasó se ve como **"Turno pasado"** (neutro), sin cambiar ni cancelar. La marca de asistencia es interna del local.~~ **Obsoleta, reemplazada por D-33:** no hay link privado; la marca de asistencia sigue siendo interna (RN-24). | Evita un mensaje acusatorio al cliente y mantiene la asistencia como dato operativo del local. | CPO (pregunta del diseño) |
| D-33 | **El cliente no cambia ni cancela su turno desde la app en el MVP.** Para cambiar o cancelar le escribe al local por WhatsApp. La app lo indica al menos en la confirmación (CU-05) y en un lugar visible de la web de reservas (CU-01), con el número de WhatsApp del local y un link de contacto (tipo `wa.me`). El número lo carga el admin en los datos del local (CU-16). El admin resuelve los pedidos moviendo o cancelando desde el panel (CU-13, sin cambios). Se elimina el **link privado** y todo lo que dependía de él: CU-06 y CU-07 quedan eliminados (ID conservado); D-2, D-6, D-10, D-13, D-18, D-24, D-29 y D-32 quedan obsoletas; D-3, D-7, D-8, D-14 y D-15 se ajustan. D-31 sigue vigente. | Simplifica el MVP; el canal de WhatsApp ya existe y el local ya atiende esos pedidos. | Ezequiel Avila |
| D-34 | **Sin botón "Agregar al calendario".** La confirmación es solo en pantalla: servicio, peluquero, fecha y hora, precio de referencia (si hay) y el aviso de WhatsApp para cambios y cancelaciones. Pasa a fuera de alcance. | Menos alcance para el MVP. | Ezequiel Avila |
| D-35 | **Reservas por teléfono confirmadas:** el local las hace desde la misma web pública, sin carga manual en el panel. Ratifica D-23. | Un solo flujo de reserva. | Ezequiel Avila |
| D-36 | **WhatsApp del local opcional (CU-16):** si el admin no lo cargó, el aviso de cambios y cancelaciones indica comunicarse con el local al teléfono de contacto (obligatorio en CU-16), sin link de WhatsApp. | No bloquea la carga del local y el cliente siempre tiene a quién contactar. | Propuesta PO, pendiente de aceptación del CPO (P-12) |

## 10. Factibilidad (referencia)

Factibilidad del Architect, **aprobada por Ezequiel Avila** con el plan Hobby (D-9). Este documento no la decide; la registra como referencia.

- Entra en **Vercel Hobby a US$ 0**, sin dominio propio.
- **Estimación actualizada del Architect:** H1 de **1,5 a 2 semanas**; MVP completo (H1 + H2) de **3 a 4 semanas**. Confianza **media**.
- **Riesgo / condición:** Hobby es solo para uso no comercial. Por decisión de Ezequiel, el uso actual, incluido el piloto de H2, se considera no comercial (D-17), así que se mantiene Hobby. Si pasa a uso comercial, hay que volver a decidir la licencia (D-9).
- **Nota del Architect sobre el admin:** entra con usuario y contraseña. Como el MVP no envía emails, no hay recuperación de contraseña: si el admin la olvida, el equipo la resetea a mano (ver CU-08).
- **Link privado reemplazado (D-33):** la factibilidad y la estimación se hicieron sobre la opción A del Architect (autogestión por link privado + "Agregar al calendario", D-2). Esa opción se reemplaza: no hay link privado, ni cambio o cancelación por el cliente, ni archivo de calendario; se agrega el WhatsApp del local (CU-16) y el aviso con link de contacto. **El Architect debe revalidar factibilidad y estimación (P-11).** Este documento no anticipa el resultado.
- Puede haber un **arranque en frío** en el primer acceso después de un rato de inactividad (ver RNF-06).

Los detalles técnicos (arquitectura, autenticación del admin, manejo de concurrencia, link de contacto por WhatsApp) los define el Architect en su documentación.

## 11. Preguntas abiertas

| ID | Pregunta | Para | ¿Bloquea? |
|---|---|---|---|
| P-10 | ¿Dónde va el aviso de WhatsApp para cambios y cancelaciones en la web de reservas (además de la confirmación)? Por ejemplo, encabezado, pie o pantalla de servicios; tiene que ser visible en celular (RNF-01). | CDO | No (diseño de H1) |
| P-11 | Revalidar factibilidad y estimación sin link privado ni "Agregar al calendario", con el WhatsApp del local y el link de contacto (§10). | Architect | Sí, para cerrar la estimación |
| P-12 | Aceptar D-36: WhatsApp opcional en CU-16, con el teléfono de contacto como alternativa si no está cargado. | CPO | No |

### Preguntas resueltas

| ID | Resolución |
|---|---|
| P-1 | Sin métricas por ahora; objetivo cualitativo (D-16). |
| P-2 | Anticipación mínima fija de 2 horas, sin configuración; CU-15 eliminado (D-13). D-7 confirmada. Superada por D-33: ya no hay anticipación mínima. |
| P-4 | Hitos de D-8 confirmados por Ezequiel. |
| P-5 | Ventana de reserva de 30 días (D-14). |
| P-7 | Peluquero con menos turnos ese día; desempate por orden de carga (D-15). |
| P-8 | Un único usuario admin, sin roles ni pantalla de usuarios (D-25). En staging se crea con usuario `admin` y contraseña `admin` (D-26), con el riesgo aceptado de D-27; antes de cargar datos reales o empezar el piloto de H2, el equipo técnico cambia la contraseña (D-28). |
| P-9 | Uso no comercial, incluido el piloto; se mantiene Vercel Hobby (D-17). |
