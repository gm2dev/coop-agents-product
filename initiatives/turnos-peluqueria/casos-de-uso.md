# Turnos para peluquería — Casos de uso

> Estado: borrador · Dueño de producto: Ezequiel Avila · Autor: PO

## Contexto y problema

Las peluquerías gestionan los turnos por teléfono o WhatsApp. Eso consume tiempo del staff,
genera errores de agenda y no reduce las ausencias.

**Supuestos sin datos todavía**
- *S1 — Ausencias:* las ausencias son un dolor relevante (frecuentes y con costo para el local).
- *S2 — Tiempo del staff:* coordinar turnos por teléfono/WhatsApp le quita al staff un tiempo
  significativo por día.

**Cómo validarlos:** entrevistar a 3–5 peluquerías y preguntarles cuántas ausencias tienen por
semana, qué les cuestan y cuánto tiempo por día dedican a coordinar turnos.

**Usuarios**
- **Clienta**: reserva desde el celular (mobile).
- **Staff de la peluquería**: recepción o dueña; consulta y gestiona la agenda.

## Objetivo

Ezequiel Avila (dueño de producto) quiere una **demo funcional** para validar el concepto antes de
invertir en el MVP completo.

---

## Fase 0 — Demo (decidida por Ezequiel; foco actual)

### CU-01 Reservar turno (clienta)

Flujo: elige servicio → profesional o "cualquiera disponible" → día y horario libre → completa
nombre, celular y email → revisa resumen → confirma → ve la confirmación en pantalla.

- **Dado** que la clienta abre la página de reservas en el celular, **cuando** ve la lista de
  servicios, **entonces** cada servicio muestra nombre, duración y precio (el precio es solo
  informativo; no se cobra online).
- **Dado** que eligió un servicio, **cuando** pasa al paso siguiente, **entonces** puede elegir un
  profesional que ofrece ese servicio o "cualquiera disponible".
- **Dado** que eligió servicio y profesional, **cuando** elige un día, **entonces** ve solo los
  horarios de inicio libres según las reglas de armado de horarios (R2, R7–R9).
- **Dado** que eligió "cualquiera disponible", **cuando** elige un día, **entonces** ve los horarios
  en los que al menos un profesional que hace ese servicio está libre.
- **Dado** que eligió horario, **cuando** deja vacío nombre, celular o email, o los completa con
  formato inválido (ver R10), **entonces** no puede avanzar y ve un mensaje de error junto a cada
  campo.
- **Dado** que completó datos válidos, **cuando** avanza, **entonces** ve un resumen (servicio,
  duración, precio, profesional o "cualquiera", día, horario, sus datos) y puede volver a editar.
- **Dado** que revisó el resumen, **cuando** confirma y el horario sigue libre, **entonces** la
  reserva se guarda y ve una pantalla de confirmación con los datos del turno. No se envía email.
- **Dado** que eligió "cualquiera disponible", **cuando** confirma, **entonces** el sistema asigna
  el primer profesional libre en ese horario según el orden fijo de la lista de profesionales
  (ver R11) y la confirmación muestra su nombre.

**Estados vacíos y errores** (prototipos `horario-vacio.html` y `horario-error-red.html` en
[gm2dev/coop-agents-design](https://github.com/gm2dev/coop-agents-design/tree/design/turnos-peluqueria/prototypes/turnos-peluqueria),
rama `design/turnos-peluqueria`, carpeta `prototypes/turnos-peluqueria/`):

- **Dado** que la clienta eligió servicio y profesional, **cuando** elige un día sin horarios
  libres o en que el local está cerrado, **entonces** ve el mensaje "No hay horarios libres este
  día" y una sugerencia con el próximo día con lugar (dentro de los 14 días), que puede elegir con
  un toque.
- **Dado** que no hay ningún horario libre en los próximos 14 días, **cuando** la clienta busca
  día, **entonces** ve un mensaje que lo informa y le sugiere elegir otro profesional o
  "cualquiera disponible".
- **Dado** que la clienta está en cualquier paso, **cuando** falla la carga de servicios,
  profesionales u horarios, **entonces** ve un mensaje de error y un botón "Reintentar" que vuelve
  a cargar ese paso sin perder lo que ya eligió.

### CU-02 Horario tomado

- **Dado** que la clienta eligió un horario, **cuando** está en el paso de datos, **entonces** el
  horario sigue apareciendo libre para otras personas (no queda retenido para ella).
- **Dado** que otra persona reservó ese horario (mismo profesional, o ya no queda ningún
  profesional libre si eligió "cualquiera"), **cuando** la clienta confirma, **entonces** no se
  crea la reserva, ve el mensaje "horario tomado" y hasta 3 horarios alternativos libres.
- **Dado** que la confirmación falló por "horario tomado", **cuando** se muestran las
  alternativas, **entonces** son los horarios libres más cercanos al
  elegido (antes o después) en el mismo día, con el mismo profesional, o con cualquier profesional
  que haga el servicio si eligió "cualquiera disponible".
- **Dado** que no hay horarios libres ese día, **cuando** ve "horario tomado", **entonces** no se
  muestran alternativas y se le ofrece elegir otro día.
- **Dado** que dos clientas confirman a la vez el mismo profesional, día y horario, **cuando** el
  sistema procesa ambas confirmaciones, **entonces** se crea una sola reserva y la otra clienta ve
  "horario tomado" con alternativas.
- **Dado** que en un horario queda un solo profesional libre y dos clientas que eligieron
  "cualquiera disponible" confirman a la vez, **cuando** el sistema procesa ambas confirmaciones,
  **entonces** se crea una sola reserva y la otra clienta ve "horario tomado" con alternativas.
- **Dado** que ve las alternativas, **cuando** elige una, **entonces** vuelve al resumen con sus
  datos ya cargados y puede confirmar.

### CU-03 Ver agenda del día (staff)

- **Dado** que el staff abre la URL de agenda (no pública, no enlazada, sin login), **cuando** carga
  la página, **entonces** ve los turnos del día agrupados por profesional, con horario, servicio y
  nombre de la clienta.
- **Dado** que está viendo la agenda, **cuando** quiere ver otro día, **entonces** puede navegar
  entre días.
- **Dado** que se creó una reserva nueva, **cuando** el staff recarga la página, **entonces** la
  reserva aparece (no hace falta actualización en tiempo real).
- La agenda es **solo lectura**: no permite crear, editar ni cancelar turnos.
- **Dado** que el staff abre la agenda, **cuando** el día elegido no tiene turnos, **entonces** ve
  el mensaje "No hay turnos para este día" y puede navegar a otro día.
- **Dado** que el staff abre la agenda, **cuando** falla la carga de turnos, **entonces** ve un
  mensaje de error y un botón "Reintentar".
- **Dado** que el staff abre la agenda sin login, **cuando** carga la página, **entonces** ve
  arriba un aviso visible y permanente "Demo: no cargues datos reales" (R6).

### Reglas de negocio

- **R1** Nunca hay dos turnos superpuestos del mismo profesional, aunque dos reservas se confirmen
  al mismo tiempo (el sistema debe garantizarlo, no solo la interfaz). Ver los criterios de
  confirmación simultánea en CU-02.
- **R2** Los horarios de inicio se ofrecen cada 30 minutos (10:00, 10:30, 11:00…).
- **R3** Zona horaria única del local: `America/Argentina/Buenos_Aires`.
- **R4** Un servicio por reserva.
- **R5** El pago se hace en el local.
- **R6** Privacidad de la demo: se usan solo datos ficticios y la agenda sin login muestra un aviso
  visible "Demo: no cargues datos reales". *Criterio:* ver CU-03 (aviso en la agenda). En la fase 1
  la agenda real requiere login (CU-13).
- **R7** Un horario solo se ofrece si el servicio completo termina antes del cierre o del inicio
  de la pausa, y no se superpone con otro turno del profesional.
  *Criterio:* **Dado** el horario de "Tijera Fina" (pausa 14–15, cierre 19) y Ana sin turnos ese
  día, **cuando** la clienta elige Color (90 min) con Ana, **entonces** no ve 13:00 ni 13:30 ni
  18:00 ni 18:30, y sí ve 12:30 y 17:30. **Dado** que Ana ya tiene un turno de 11:00 a 11:45,
  **cuando** la clienta elige Corte mujer (45 min) con Ana, **entonces** no ve 10:30 ni 11:00 ni
  11:30, y sí ve 10:00 y 12:00.
- **R8** No se ofrecen horarios ya pasados del día actual.
  *Criterio:* **Dado** que hoy son las 11:10, **cuando** la clienta elige hoy, **entonces** el
  primer horario ofrecido es 11:30 o posterior.
- **R9** Se puede reservar desde hoy hasta 14 días hacia adelante (hoy + 14).
  *Criterio:* **Dado** que hoy es 15/10, **cuando** la clienta navega días, **entonces** puede
  elegir hasta el 29/10 y no días posteriores.
- **R10** Validación de datos de la clienta:
  - nombre: obligatorio;
  - celular: número argentino de 10 dígitos (código de área + número, sin 0 ni 15); se aceptan
    espacios y guiones (p. ej. `11 2345-6789` es válido; `011 15 2345 6789` no);
  - email: formato `usuario@dominio.tld`.
- **R11** "Cualquiera disponible": se asigna el primer profesional libre según el orden fijo de la
  lista de profesionales (en los datos de ejemplo: Ana, Bruno, Carla). Una distribución equitativa
  entre profesionales queda para después de la demo.

### Datos de ejemplo

Peluquería ficticia **"Tijera Fina"** precargada. Todos los datos son ficticios (R6); precios en
ARS ficticios.

**Servicios**

| Servicio | Duración | Precio (ARS, ficticio) |
|---|---|---|
| Corte mujer | 45 min | $ 15.000 |
| Corte hombre | 30 min | $ 10.000 |
| Color | 90 min | $ 35.000 |
| Corte y barba | 45 min | $ 14.000 |

**Profesionales** (en este orden fijo, usado por R11)

| Orden | Profesional | Servicios que hace |
|---|---|---|
| 1 | Ana | Corte mujer, Color |
| 2 | Bruno | Corte hombre, Corte y barba |
| 3 | Carla | Corte mujer, Corte hombre, Color |

**Horario semanal** (igual para los 3 profesionales): martes a sábado de 10:00 a 19:00, con pausa
de 14:00 a 15:00. Domingo y lunes cerrado.

### No funcionales

- Mobile-first: el flujo de la clienta se usa a 360 px de ancho sin scroll horizontal.
- Accesibilidad WCAG 2.2 AA, verificable así:
  - el flujo de la clienta se completa de punta a punta solo con teclado (foco visible);
  - todos los campos tienen etiqueta asociada;
  - los mensajes de error y "horario tomado" se anuncian a lectores de pantalla.
- Referencia de diseño:
  [`initiatives/turnos-peluqueria/accessibility.md`](https://github.com/gm2dev/coop-agents-design/blob/design/turnos-peluqueria/initiatives/turnos-peluqueria/accessibility.md)
  en gm2dev/coop-agents-design, rama `design/turnos-peluqueria`.

### Guion de demo

1. Una clienta reserva un turno desde el celular (CU-01).
2. Abrir la agenda y recargar: el turno aparece, con el aviso "Demo: no cargues datos reales"
   visible (CU-03, R6).
3. Las clientas A y B, en dos celulares, eligen el mismo profesional, día y horario. A confirma;
   después B confirma y ve "horario tomado" con alternativas; elige una y confirma (CU-02).

### Fuera de fase 0

Emails, recordatorio, cancelación, login, bloqueos, carga manual de turnos y panel de administración.

---

## Fase 1 — MVP completo (resumen)

- **CU-10 Email de confirmación** con link de gestión del turno (token largo, solo link, sin código).
  *Criterio:* al confirmar, la clienta recibe un email con los datos del turno y el link; un token
  inválido no da acceso a ningún turno.
- **CU-11 Cancelar por link** hasta X horas antes (configurable; propuesto 2 h). Reprogramar =
  cancelar y reservar de nuevo. *Criterio:* fuera de plazo, el link informa que ya no se puede
  cancelar online; al cancelar, el horario vuelve a quedar libre.
- **CU-12 Recordatorio**: un email diario "tus turnos de mañana". *Criterio:* cada clienta con
  turno al día siguiente recibe un solo email; los turnos cancelados no se incluyen.
- **CU-13 Login del staff** por link mágico por email, un solo rol. **Requisito (R6):** la agenda
  real, con datos reales de clientas, solo se habilita con login. *Criterio:* solo emails dados
  de alta reciben el link; la agenda deja de estar en una URL abierta y sin login no se muestra
  ningún turno.
- **CU-14 Administrar servicios y profesionales** (alta, edición, baja). *Criterio:* los cambios se
  reflejan en la reserva; dar de baja no borra turnos ya reservados.
- **CU-15 Horario semanal por profesional**. *Criterio:* solo se ofrecen horarios dentro del
  horario de cada profesional.
- **CU-16 Bloqueos** (feriados, ausencias). *Criterio:* un bloqueo oculta esos horarios; bloquear
  **no** cancela turnos existentes: el sistema los señala y el staff decide.
- **CU-17 Carga manual de turno** por el staff (teléfono/mostrador). *Criterio:* aplica las mismas
  reglas (R1–R5, R7) que la reserva online.
- **CU-18 Cancelación por el staff**. *Criterio:* al cancelar, la clienta recibe un email de aviso.

## Fuera del MVP

| Ítem | Por qué queda afuera |
|---|---|
| Pago o seña online | Requiere integración con un proveedor de pagos y tiene costo; +1–2 semanas según el Architect. |
| WhatsApp/SMS | Costo por mensaje y alta del proveedor; +1–2 semanas según el Architect. |
| Varias sucursales | El MVP valida con un solo local. |
| Integración con Google Calendar | Integración OAuth que no hace falta para validar el concepto. |
| Cuentas de clienta | Agregan fricción; el link de gestión por email alcanza. |
| Reseñas | No aportan a la hipótesis central (reservar online sin llamar). |
| Lista de espera | No aporta a la hipótesis central (reservar online sin llamar). |

**Estimaciones:** fase 0 en 3–5 días (Vercel free), fase 1 en 3–4 semanas y +1–2 semanas si se
suman pagos o WhatsApp. *Fuente:* Architect, factibilidad publicada en el hilo de Slack de
discovery del 15/10/2026.

## Métricas propuestas (a validar)

- % de turnos reservados online sobre el total.
- Tasa de ausencias.
- Conversión del flujo: reservas completadas / iniciadas.

## Links

- Diseño: [gm2dev/coop-agents-design](https://github.com/gm2dev/coop-agents-design/tree/design/turnos-peluqueria),
  rama `design/turnos-peluqueria` — `initiatives/turnos-peluqueria/` y `prototypes/turnos-peluqueria/`.

## Preguntas abiertas

| Pregunta | Dueño |
|---|---|
| Plazo de cancelación online (propuesto 2 h) | Ezequiel |
| Aprobar el recordatorio como email diario único | Ezequiel |
| Fecha y audiencia de la demo | Ezequiel |
| Métricas de éxito y metas | Ezequiel |
| Validar supuestos S1 (ausencias) y S2 (tiempo del staff) con 3–5 peluquerías | Ezequiel / PO |
