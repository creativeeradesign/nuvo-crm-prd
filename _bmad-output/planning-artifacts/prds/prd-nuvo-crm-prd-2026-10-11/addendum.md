# Addendum — Nuvo CRM

## Decisiones técnicas indicadas por el dueño del producto
- Cliente: React Native.
- Autenticación: Supabase Auth.
- Base de datos: Supabase (Postgres). El control de acceso por rol probablemente se apoye en Row Level Security; se definirá en arquitectura.

## Ejecución de agentes (insumo para arquitectura)
- La biblioteca `agency-agents` (MIT) trae agentes `.md` con frontmatter YAML pensados para Claude Code (`~/.claude/agents/`), con scripts de conversión para otras herramientas. En esencia son prompts de sistema.
- Claude Code es una herramienta interactiva que corre en la computadora del usuario, así que no sirve como motor para una app móvil o web multiusuario.
- Opciones de motor desde el backend: Claude API (Messages, con el `.md` como prompt de sistema y herramientas propias), Claude Agent SDK o Managed Agents (agente alojado con sandbox), o las rutinas programadas de Claude que ya usa el agente de prospección. Se decide en arquitectura.

## Motor de agentes recomendado: Claude Managed Agents (insumo para arquitectura)
- **Restricción:** la app (móvil o web) nunca llama a Claude directamente. La clave de la API vive solo en el backend.
- **Por qué no la rutina actual:** la rutina "Prospectos semanales California" vive en la cuenta de Claude de Creative Era. Sirve de modelo, pero no puede atender a varias empresas desde una app.
- **Mapa de piezas:**
  - Plantilla de agente → objeto Agent, versionado. Su prompt de sistema es el `.md` de agency-agents más la ficha del negocio.
  - Cada ejecución o conversación → Session. Corre en la infraestructura de Anthropic, así que las corridas largas no dependen del servidor propio.
  - Agentes programados → scheduled deployments, con cron y zona horaria.
  - Búsqueda e investigación → herramientas web_search y web_fetch integradas.
  - HubSpot → el token OAuth de cada empresa vive en el backend de Nuvo. El agente nunca lo recibe.
  - Revisión humana (F7) → el agente **no tiene herramientas de escritura**: ni MCP de HubSpot con permisos de escritura, ni envío de correo. Entrega resultados estructurados que Nuvo guarda como "por revisar". El backend escribe en HubSpot o envía el correo solo cuando una persona lo pide. Esto descarta el patrón `always_ask` con la sesión en pausa, que no permite editar antes de enviar, ni revisar 30 días después, ni enviar por lote.
  - Lecturas de HubSpot → pasan por herramientas propias de Nuvo que aplican el alcance del rol del usuario (NFR-19), no por un token con acceso a toda la empresa.
  - Control de costos → presupuestos por sesión.
- **Infraestructura:**
  - Supabase guarda empresas, usuarios, roles y resultados.
  - Las Edge Functions crean sesiones y reciben los webhooks de eventos.
  - Hostinger sirve el build web. Un hosting compartido no corre procesos largos, pero con Managed Agents no hace falta.
- **Alternativa:** Messages API con tool use, alojada por nosotros. Da más control, pero exige un servidor propio (por ejemplo, un VPS) para las corridas largas y programar el cron aparte.
- **Plataforma:** Managed Agents está en beta y disponible en la Claude API, no en Bedrock, Vertex ni Foundry.

## Autenticación (insumo para arquitectura)
- El inicio de sesión, la recuperación de contraseña y la gestión de sesiones van todos con Supabase Auth, por decisión del dueño del producto.
- La contraseña temporal la genera el sistema y se le muestra una sola vez al Admin, que se la entrega al usuario por su cuenta. Al primer acceso, el usuario debe cambiarla.

## Correo saliente (insumo para arquitectura)
- **Proveedor del piloto:** Creative Era usa **Titan Email**, el correo que incluye Hostinger. Titan no tiene una API tipo Gmail, así que la conexión va por **SMTP** para enviar e **IMAP** para leer respuestas, normalmente con contraseña de la cuenta o de aplicación.
- **Recomendación:** un conector genérico IMAP/SMTP como base, porque sirve para Titan, Zoho, cPanel y otros. En Fase 2 se agregan Gmail y Microsoft 365 por OAuth.
  - **Gmail:** el permiso de envío es "restringido" para apps públicas. Exige una verificación de seguridad de Google (CASA), que tarda semanas y tiene costo. Es un riesgo de calendario para la Fase 2.
  - **Microsoft 365:** retiró la autenticación básica en SMTP e IMAP, así que requiere OAuth.
- **Detección de respuestas:** leer por IMAP el buzón del usuario, ya sea con IDLE o con consultas periódicas, y relacionar cada respuesta con el correo enviado mediante los encabezados Message-ID e In-Reply-To.
- **Registro en HubSpot:** cada correo enviado y cada respuesta recibida se registran como actividad de correo (engagement EMAIL) asociada al contacto.
- **Credenciales:** las credenciales de correo se cifran en el servidor y nunca llegan al cliente, igual que las de HubSpot.

## Borrador de especificación de las 16 plantillas restantes de la Fase 1 (validar antes de construir)
| Agente | Rol | Tipo | Produce | Destino |
|---|---|---|---|---|
| Sales Outreach | Ventas | Conversacional | Respuestas a objeciones y seguimientos sobre un deal o contacto | Correo por revisar (F10) o nota adjuntada por una persona (FR-88) |
| Offer & Lead-Gen Strategist | Ventas | Conversacional | Ofertas de entrada e imanes de leads basados en la ficha | Entregable |
| Discovery Coach | Ventas | Conversacional | Guion de la llamada de diagnóstico y, después de la llamada, notas y siguiente paso | Entregable; una persona puede adjuntarlo como nota en el deal (FR-88) |
| Pipeline Analyst | Ventas | De tarea (semanal) | Deals que se enfrían, riesgos y acciones sugeridas | Entregable; las tareas sugeridas se crean en HubSpot tras revisión |
| Customer Success Manager | Ventas | Conversacional | Plan de onboarding y riesgos de cancelación de un cliente | Entregable; una persona puede adjuntarlo como nota (FR-88) |
| Account Strategist | Ventas | Conversacional | Oportunidades de venta adicional para un cliente | Entregable; deal de venta adicional tras revisión |
| SEO Specialist | Marketing | Conversacional o de tarea | Auditoría SEO local y propuestas de contenido | Entregable |
| Content Creator | Marketing | Conversacional | Calendario editorial y textos | Entregable |
| LinkedIn Content Creator | Marketing | Conversacional | Publicaciones para los fundadores | Entregable |
| Social Media Strategist | Marketing | Conversacional | Plan y publicaciones para varias redes | Entregable |
| Instagram Curator | Marketing | Conversacional | Publicaciones, carruseles y textos | Entregable |
| AI Citation Strategist | Marketing | De tarea | Auditoría de visibilidad de la marca en motores de IA | Entregable |
| PPC Strategist | Marketing | Conversacional | Estructura de campañas en Google Ads y palabras clave | Entregable |
| Paid Social Strategist | Marketing | Conversacional | Plan de campañas en Meta y LinkedIn | Entregable |
| Ad Creative Strategist | Marketing | Conversacional | Textos y variantes de anuncios | Entregable |
| Traductor español–inglés | Marketing | Conversacional | Traducción con adaptación cultural de entregables | Entregable |
