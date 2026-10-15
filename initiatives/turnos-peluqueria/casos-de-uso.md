# Turnos para peluquería — Casos de uso

> Estado: borrador · Dueño de producto: Ezequiel Avila · Autor: PO

## Contexto y problema

Las peluquerías gestionan los turnos por teléfono o WhatsApp. Eso consume tiempo del staff,
genera errores de agenda y no reduce las ausencias.

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
  horarios libres, en bloques según la duración del servicio y dentro del horario del local.
- **Dado** que eligió "cualquiera disponible", **cuando** elige un día, **entonces** ve los horarios
  en los que al menos un profesional está libre.
- **Dado** que eligió horario, **cuando** deja vacío nombre, celular o email, o los completa con
  formato inválido, **entonces** no puede avanzar y ve un mensaje de error junto a cada campo.
- **Dado** que completó datos válidos, **cuando** avanza, **entonces** ve un resumen (servicio,
  duración, precio, profesional o "cualquiera", día, horario, sus datos) y puede volver a editar.
- **Dado** que revisó el resumen, **cuando** confirma y el horario sigue libre, **entonces** la
  reserva se guarda y ve una pantalla de confirmación con los datos del turno. No se envía email.
- **Dado** que eligió "cualquiera disponible", **cuando** confirma, **entonces** el sistema asigna
  un profesional libre en ese horario y la confirmación muestra su nombre.

### CU-02 Horario tomado

- **Dado** que la clienta está completando datos, **entonces** el horario elegido **no** queda
  retenido para ella.
- **Dado** que otra persona reservó ese horario (mismo profesional, o ya no queda ningún
  profesional libre si eligió "cualquiera"), **cuando** la clienta confirma, **entonces** no se
  crea la reserva, ve el mensaje "horario tomado" y una lista de horarios alternativos libres.
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

### Reglas de negocio

- **R1** Nunca hay dos turnos superpuestos del mismo profesional, aunque dos reservas se confirmen
  al mismo tiempo (el sistema debe garantizarlo, no solo la interfaz).
- **R2** Los horarios se ofrecen en bloques según la duración del servicio.
- **R3** Zona horaria única del local: `America/Argentina/Buenos_Aires`.
- **R4** Un servicio por reserva.
- **R5** El pago se hace en el local.

### Datos de ejemplo

Peluquería ficticia **"Tijera Fina"** precargada con servicios (nombre, duración, precio),
profesionales (qué servicios ofrece cada uno) y horario semanal fijo. Sin datos reales de personas.

### No funcionales

- Mobile-first.
- Accesibilidad WCAG 2.2 AA.

### Guion de demo

1. Reservar un turno desde el celular (CU-01).
2. Abrir la agenda y recargar: el turno aparece (CU-03).
3. Una segunda persona intenta reservar el mismo horario y ve "horario tomado" con alternativas (CU-02).

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
- **CU-13 Login del staff** por link mágico por email, un solo rol. *Criterio:* solo emails dados
  de alta reciben el link; la agenda deja de estar en una URL abierta.
- **CU-14 Administrar servicios y profesionales** (alta, edición, baja). *Criterio:* los cambios se
  reflejan en la reserva; dar de baja no borra turnos ya reservados.
- **CU-15 Horario semanal por profesional**. *Criterio:* solo se ofrecen horarios dentro del
  horario de cada profesional.
- **CU-16 Bloqueos** (feriados, ausencias). *Criterio:* un bloqueo oculta esos horarios; bloquear
  **no** cancela turnos existentes: el sistema los señala y el staff decide.
- **CU-17 Carga manual de turno** por el staff (teléfono/mostrador). *Criterio:* aplica las mismas
  reglas (R1–R5) que la reserva online.
- **CU-18 Cancelación por el staff**. *Criterio:* al cancelar, la clienta recibe un email de aviso.

## Fuera del MVP

Pago o seña online, WhatsApp/SMS, multi-sucursal, integración con Google Calendar, cuentas de
clienta, reseñas, lista de espera.

**Por qué:** validar demanda rápido. El Architect estimó la fase 0 en 3–5 días (Vercel free), la
fase 1 en 3–4 semanas y +1–2 semanas si se suman pagos o WhatsApp.

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
