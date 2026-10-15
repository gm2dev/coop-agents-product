# coop-agents-product

Repositorio de documentación de producto: problema, objetivos, alcance, decisiones, casos de uso y
criterios de aceptación de cada iniciativa. No contiene código, diseño ni documentación técnica
(arquitectura/ADRs viven en sus propios repos).

## Estructura

```
initiatives/<iniciativa>/   # un directorio por iniciativa
  casos-de-uso.md           # problema, alcance, decisiones y casos de uso con criterios Given/When/Then
```

## Iniciativas

| Iniciativa | Estado | Documento |
|---|---|---|
| Reserva de turnos para peluquería (MVP) | Discovery – etapa 1 (problema y casos de uso) | [casos-de-uso.md](initiatives/turnos-peluqueria/casos-de-uso.md) |

## Cómo se trabaja

- Cambios por rama `po/<tema>` y Pull Request; no se commitea directo en `main`.
- Commits en Conventional Commits (`docs(<ámbito>): …`).
- Sin secretos ni datos personales reales en los documentos.
