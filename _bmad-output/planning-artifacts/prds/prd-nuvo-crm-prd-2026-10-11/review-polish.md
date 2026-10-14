# Revisión editorial (estructura + prosa) — 2026-10-11

Lentes: bmad-review `structure`, `prose`. Lector: arquitecto, UX y redactores de historias. Ningún ID FR-/NFR- se renumeró, eliminó ni fusionó. Se conservan las etiquetas [ASSUMPTION]/[PENDIENTE] (12), el frontmatter y las tablas.

## Estructura (6)
1. prd §5 F8: FR-29 (vínculo con el owner de HubSpot) pasa a ir justo después de FR-28, antes de FR-30 y FR-31. FR-28 depende de él ("asignados a esa persona").
2. prd §4: el nombre de la fila F8 ahora coincide con el encabezado de la sección ("Pipeline, historial y costos") y la descripción incluye los costos (FR-74).
3. prd §4: la fila F7 menciona el descarte y los entregables, que la sección F7 ya cubre.
4. prd §2: "Fuera de alcance de la Fase 1" ahora cita FR-85 y añade FR-89 (publicación directa de entregables), que antes faltaba.
5. addendum: la sección "Autenticación" se movió después de "Motor de agentes recomendado", así "Ejecución de agentes" y "Motor" quedan juntas.
6. addendum, tabla de la Fase 1b: los destinos "nota" (Sales Outreach, Discovery Coach, Customer Success Manager) ahora dicen que una persona adjunta la nota (FR-88) y que el correo queda por revisar. Así coinciden con el modelo de revisión.

## Prosa y terminología (14)
- §1: "quedan listos para revisar y enviar" → "queda como resultado por revisar antes de enviarse a HubSpot".
- FR-26: "bandeja de aprobación" → "revisión de resultados (F7)". Era un resto del modelo de aprobación.
- FR-50: "no escribe directamente en HubSpot" → "no escribe en HubSpot"; "pasan por revisión" → "quedan como resultados por revisar". "Directamente" daba a entender que podía escribir de forma indirecta, lo que contradice NFR-19.
- FR-63: se reordenó la oración, que era difícil de leer.
- FR-57: "En general" → "Como regla general" (deja de sonar a duda; NFR-19 es absoluto).
- FR-79: "queda por revisar" → "queda como resultado por revisar".
- FR-81: "estatus del lead" → "*Lead Status* del lead" (término del glosario).
- FR-73: "candidatas" → "empresas candidatas" (el sustantivo faltaba).
- FR-28: "ventas" → "Ventas" (nombre del rol).
- FR-31: "el agente de prospección en frío" → "un agente de prospección en frío (como Prospección semanal)".
- §3, FR-30, §8: "Owner/owner: Nacho" → "Responsable". Así no se confunde con el owner de HubSpot (FR-29).
- addendum: "indicadas por el usuario" / "por decisión del usuario" → "dueño del producto". "Usuario" se confundía con los usuarios de la app.
- addendum: "llave de la API" → "clave de la API".

## No corregido (cambiaría el contenido)
- addendum, tabla de la Fase 1b: falta la columna "Qué recibe", que FR-91 exige. Agregarla sería inventar insumos.
- addendum, Pipeline Analyst y Account Strategist: crean tareas o deals en HubSpot "tras revisión". Son resultados por revisar que no se reflejan en la tabla de agentes de la Fase 1a; está bien, pero hay que confirmarlo al especificarlos.
- FR-12 frente a FR-11: no se dice qué pasa con los agentes personales (no compartidos) de un usuario eliminado ni quién puede verlos. Lo debe decidir el autor.
