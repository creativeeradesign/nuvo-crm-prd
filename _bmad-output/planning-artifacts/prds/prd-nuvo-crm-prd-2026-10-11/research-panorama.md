# Panorama competitivo — agentes de IA de ventas y marketing (investigación 2026-10-11)

> Digest de un subagente de investigación web. Los precios vienen sobre todo de blogs de terceros, algunos de ellos competidores. Hay que verificarlos en las páginas oficiales antes de citarlos.

## Comparables
| Producto | Enfoque | Modelo de agente | Permisos / aprobación | Precio (aprox.) |
|---|---|---|---|---|
| HubSpot Breeze Agents | PyME y mid-market en HubSpot | Prospecting Agent programado + chat | Permisos Studio/Run; "Take action" con aprobación manual opcional | Créditos (~$10 por 1k); ~$1 por lead recomendado desde abr-2026; exige plan Pro o Enterprise |
| Salesforce Agentforce | Enterprise | Temas + acciones | Permission sets, Trust Layer | ~$0.10 por acción o $2 por conversación, más licencias |
| Relevance AI | Constructor de "AI workforce" | BDR agéntico | Escalations | Actions + Vendor Credits; BYOK; Team ~$349/mes |
| Lindy | Asistentes con disparadores + chat | Email, horario, webhook | — | Créditos por tarea; se pausa al agotarse; costo opaco |
| Clay | Enriquecimiento/prospección en tablas | Claygent | — | Data Credits + Actions; sync con CRM desde $495/mes |
| Copy.ai → Fullcast | Workflows de GTM + chat | Workflows | — | ~$12k/año o por cotización |
| 11x | SDR autónomo | Empleado digital | Poca supervisión | ~$3,750/mes con contrato anual |
| Artisan (Ava) | BDR autónomo | Autónomo | Sin aprobación por mensaje | $1.5k–5k/mes |
| Jasper | Contenido de marketing | Agentes con brand voice | — | ~$59–69 por asiento |
| Zapier Agents | Genérico | Agentes + 8k integraciones | Checkpoints humanos, cola "Needs action" | Por activities |
| GoHighLevel AI Employee | Agencias | Sub-cuentas por cliente | Multi-tenencia nativa, rebilling | ~$97/mes por sub-cuenta; la agencia revende a $197–397 |

## Patrones a considerar en el PRD
- **Medición y control de costos.**
  - Presupuesto por cliente, rol y agente; costo estimado antes de correr.
  - Tope duro con pausa automática.
  - Separar el costo del modelo del costo de los datos; opción BYOK.
- **Aprobación humana antes de escribir en el CRM o enviar.**
  - Bandeja de propuestas para aprobar o rechazar, una por una o en lote.
  - Aprobación configurable por agente y por herramienta; notificación push.
- **Auditoría inmutable de cada corrida.** Entradas, herramientas, fuentes, salidas, quién aprobó, costo y diff de lo que se escribió en el CRM.
- **CRM por cliente.**
  - Conexión OAuth propia con alcances mínimos.
  - Tokens cifrados y alertas si la conexión se rompe.
  - Deduplicación antes de crear registros e idempotencia en los reintentos.
- **Roles y multi-tenencia.**
  - Aislamiento por cliente.
  - Permisos separados para configurar, ejecutar, aprobar y ver.
  - Jerarquía agencia → sub-cuentas.
- **Privacidad.**
  - **California:** CCPA/CPRA y Delete Act. El sistema de borrado DROP se puede exigir en 2026. Riesgo de que la plataforma cuente como data broker si comparte datos entre clientes.
  - **México:** LFPDPPP (mar-2025), consentimiento y derechos ARCO.
  - **Envío de correos:** CAN-SPAM.
  - Origen de cada dato, propagación de solicitudes de borrado y listas de supresión.

## Huecos y diferenciadores posibles
1. Bilingüe español/inglés de forma nativa.
2. Para agencias: marca blanca, rebilling y catálogo de agentes plantilla que se clonan por cliente.
3. Precio predecible para PyME, con tope visible.
4. Independiente del CRM y móvil primero (aprobar desde el teléfono; funciona con HubSpot Free o Starter, que no incluye Breeze).
5. Cumplimiento transfronterizo como función del producto.
6. Señales locales en verticales y regiones hispanas.
7. "Nunca escribe ni envía sin tu OK" como mensaje de confianza.

## Fuentes
- https://knowledge.hubspot.com/ai/use-breeze-tools
- https://knowledge.hubspot.com/ai/manage-automated-agent-run-notifications
- https://resolve247.ai/blog/hubspot-breeze-pricing-change
- https://www.eesel.ai/blog/agentforce-pricing
- https://relevanceai.com/pricing-new
- https://www.ringg.ai/blog/lindy-ai-pricing
- https://www.joinvalley.co/blog/clay-pricing-and-credits-explained-2026
- https://www.fullcast.com/content/fullcast-announces-the-acquisition-of-copy-ai/
- https://www.landbase.com/blog/11x-ai-pricing
- https://landbase.com/blog/artisan-ai-pricing
- https://eesel.ai/blog/jasper-ai-pricing
- https://pipeline.zoominfo.com/sales/zapier-features
- https://ecosire.com/blog/gohighlevel-ai-employee-guide
- https://www.coblentzlaw.com/news/navigating-californias-data-broker-requirements-in-2026/
- https://www.truevault.com/blog/californias-drop-is-now-enforceable
- https://www.littler.com/news-analysis/asap/mexico-tiene-nueva-ley-en-materia-de-proteccion-de-datos-personales
