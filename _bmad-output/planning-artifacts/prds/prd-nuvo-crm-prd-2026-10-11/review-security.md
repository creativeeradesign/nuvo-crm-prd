---
title: "Revisión de seguridad y privacidad — PRD Nuvo CRM"
reviewed: prd.md + addendum.md (2026-10-11)
nivel: requisitos (no código)
date: 2026-10-11
---

# Revisión de seguridad y privacidad — PRD Nuvo CRM

## Veredicto

El PRD tiene buenas bases (aislamiento en la capa de datos con pruebas (NFR-1/2), permisos en el servidor (NFR-10), credenciales fuera del cliente (NFR-9), revisión humana antes de escribir en HubSpot (FR-50/62), procedencia de datos (NFR-12) y lineamientos básicos de CAN-SPAM (FR-82/83)). **No está listo para un lanzamiento multiempresa.** Hay tres huecos críticos:

1. El principio "ningún agente escribe en HubSpot" no tiene un mecanismo técnico que lo garantice, y el addendum lo contradice. Además, las lecturas que hacen los agentes sobre HubSpot no están limitadas por rol.
2. No se trata la inyección de instrucciones (prompt injection) ni la exfiltración de datos desde web, PDF, respuestas de correo e importación de `.md`.
3. Se guardan contraseñas IMAP/SMTP que dan acceso total al buzón, sin requisitos de custodia, alcance ni minimización.

También faltan requisitos sobre los roles legales (responsable/encargado, DPA), la retención de datos, MFA, la revocación de sesiones, el control de costos por empresa y el abuso del autoregistro.

| Severidad | Cantidad |
|---|---|
| Crítica | 3 |
| Alta | 10 |
| Media | 10 |
| Baja | 5 |

Las numeraciones propuestas (NFR-19 en adelante, FR-86 en adelante) son sugerencias. Ajústalas al integrar.

---

## Hallazgos críticos

### C1. "Ningún agente escribe en HubSpot" no tiene un mecanismo técnico que lo garantice, y las lecturas del agente se saltan los permisos por rol
**Refs:** FR-26, FR-28, FR-50, FR-57, FR-61, FR-62, NFR-10; addendum "Aprobación humana (F7) → política `always_ask`".

- El addendum propone darle al agente un servidor MCP de HubSpot con el token OAuth de la empresa y pausar la sesión con `always_ask` hasta recibir `user.tool_confirmation`. Eso significa que **el agente tiene herramientas de escritura y credenciales con permiso de escritura**, y que la "revisión" consiste en confirmar una llamada a herramienta dentro de la sesión. No coincide con F7 (los resultados quedan en Nuvo, se editan y se envían uno a uno o en lote). Además, cualquier error de configuración de la política convierte al agente en escritor directo.
- El token OAuth de HubSpot es **de toda la empresa**. Un agente lanzado por un usuario de Marketing o de Ventas puede leer todo el CRM, aunque FR-28 limita a cada rol a "solo los registros asignados a esa persona". FR-57 protege los adjuntos, pero no las búsquedas libres que hace el agente.

**Requisitos propuestos:**
- **NFR-19** Los agentes no tienen ninguna herramienta ni credencial capaz de escribir en HubSpot. El agente produce resultados estructurados en Nuvo, y la escritura en HubSpot la ejecuta exclusivamente el backend de Nuvo cuando un revisor autorizado la envía (FR-63/65). Debe haber una prueba automática que verifique que ningún toolset de agente expone operaciones de escritura sobre HubSpot.
- **NFR-20** Las lecturas de HubSpot que hace un agente se limitan a lo que puede ver la persona en cuyo nombre corre (FR-27/28). En corridas programadas, cuenta como esa persona quien programó el agente. El filtro lo aplica el backend de Nuvo (un proxy de herramientas con el owner y el pipeline permitidos), no el prompt.
- **NFR-21** El token OAuth de HubSpot se pide con los scopes mínimos necesarios. Si el motor de agentes requiere un token, se le entrega uno de solo lectura distinto del token de escritura que usa el backend, cuando HubSpot lo permita. Si no lo permite, el agente accede a HubSpot solo a través del proxy de NFR-20.

### C2. No se trata la inyección de instrucciones ni la exfiltración de datos
**Refs:** FR-34, FR-49, FR-56–58, FR-76, FR-81, NFR-1, NFR-13; addendum (`web_search`/`web_fetch`).

Los agentes leen **contenido controlado por terceros**: páginas web de prospectos, PDF e imágenes subidos (FR-58, FR-34), respuestas de prospectos (FR-81), notas y campos de HubSpot escritos por otros, y archivos `.md` importados (FR-49), que se convierten en prompt de sistema. Ese contenido puede contener instrucciones. La revisión humana protege las **escrituras**, pero no:

- **Lecturas y exfiltración:** el agente puede llamar a `web_fetch` hacia una URL del atacante con datos del contexto en la query: la ficha, los precios, registros de HubSpot, la conversación o los datos de otros prospectos. Esto no requiere ninguna escritura.
- **Manipulación del revisor:** se puede alterar el puntaje, la justificación o las "fuentes" (FR-64/76) para que el revisor apruebe algo falso.
- **Contenido de correos:** se pueden insertar enlaces maliciosos o afirmaciones prohibidas en un borrador (FR-79) que el usuario envía sin leerlo con atención.
- **Acciones automáticas sin revisión:** FR-81 actualiza el estatus del lead en HubSpot a partir de una respuesta. El prospecto controla la entrada de ese proceso (ver M5).

**Requisitos propuestos:**
- **NFR-22** Todo contenido externo (páginas web, archivos subidos, correos recibidos, campos de HubSpot, `.md` importados) se trata como dato no confiable. El sistema lo delimita y etiqueta como tal en el contexto del agente, y ninguna instrucción contenida en él puede ampliar las herramientas, conexiones o permisos del agente.
- **NFR-23** El contexto de un agente contiene solo los datos necesarios para su tarea. Nunca incluye credenciales, tokens, datos de otras conversaciones del usuario, datos de otros usuarios fuera de su alcance (NFR-20) ni datos de otra empresa.
- **NFR-24** Se controla el canal de salida de los agentes:
  - (a) `web_fetch` solo hace GET sin cuerpo y no puede incluir en la URL datos tomados del contexto privado. Esto se valida con heurística o lista de dominios permitidos para los agentes que no hacen investigación abierta.
  - (b) Los agentes conversacionales sin necesidad de navegación no tienen `web_fetch`.
  - (c) Se registra cada URL consultada en la corrida (complementa FR-76).
- **NFR-25** Antes de enviar a HubSpot o de mandar un correo, Nuvo resalta al revisor los enlaces, direcciones de correo, adjuntos y afirmaciones que no estén en la ficha, y marca las fuentes que no pudo volver a verificar.
- **NFR-26** Las respuestas de correo entrantes nunca se usan como instrucciones para un agente. Si un agente las resume, la salida es solo informativa y no dispara acciones.
- **FR-86** Al importar un `.md` (FR-49) o generar un agente a partir de una frase (FR-43), la app muestra al Admin el prompt completo y las herramientas o conexiones solicitadas. El agente solo puede usar herramientas de una lista permitida por la plataforma.
- **NFR-27** Antes de la Fase 2 se hace una batería de pruebas de inyección (web, PDF, correo, campo de HubSpot, `.md`), y cada cambio de prompt o herramientas de una plantilla la vuelve a correr.

### C3. Credenciales de buzón (IMAP/SMTP con contraseña) sin requisitos de custodia, alcance ni minimización
**Refs:** FR-78, FR-80, FR-81, NFR-9, NFR-11; addendum "Correo saliente".

- Una contraseña IMAP (de la cuenta o de aplicación) da **acceso total al buzón** del empleado: lectura de todo el correo, envío como esa persona y, a menudo, la misma contraseña del webmail. Robarla es peor que robar el token de HubSpot. NFR-9 menciona las credenciales "de IA y de HubSpot", pero no las de correo. El addendum solo dice "se cifran".
- FR-81 requiere leer el buzón para detectar respuestas, pero nada limita **qué** correos se leen, guardan o envían a un modelo. Sin un límite, Nuvo procesaría todo el correo del empleado: datos de terceros, correo personal y datos sensibles.
- Nada define qué pasa con la credencial cuando el usuario se desactiva o se elimina (FR-10/11/12) o cuando se borra la empresa (FR-18).
- FR-78 dice "solo las usa ese usuario", pero el lector IMAP es un proceso en segundo plano que actúa sin el usuario.

**Requisitos propuestos:**
- **NFR-28** Las credenciales de correo se guardan cifradas con una llave administrada (KMS o Supabase Vault), separada por empresa. Solo las descifra el servicio de envío y lectura de correo en el momento de usarlas. Nunca se exponen a agentes, al cliente, a logs ni a las Edge Functions de uso general, y nunca se muestran de nuevo en la interfaz.
- **NFR-29** Nuvo recomienda y, cuando el proveedor lo permita, exige una contraseña de aplicación en vez de la contraseña principal. Para proveedores con OAuth (Gmail, M365) se usa OAuth.
- **NFR-30** Minimización del lector de respuestas:
  - solo descarga encabezados para encontrar respuestas a correos enviados desde Nuvo (Message-ID/In-Reply-To/References);
  - solo guarda y procesa el cuerpo de esos mensajes;
  - no conserva ni manda a modelos ningún otro correo;
  - abre el buzón en modo de solo lectura (EXAMINE), sin marcar como leído ni mover mensajes.
- **NFR-31** Al desactivar o eliminar un usuario, al desconectar el correo o al borrar la empresa, la credencial se destruye de inmediato (al desactivar, se suspende su uso) y se detiene el lector de ese buzón. La app le pide al usuario que revoque la contraseña de aplicación en su proveedor.
- **NFR-32** Si el inicio de sesión IMAP/SMTP falla varias veces seguidas, Nuvo deja de intentar y avisa al usuario, para no provocar el bloqueo de la cuenta.
- **FR-87** El usuario ve en cualquier momento qué buzón tiene conectado, la última vez que se usó y un botón para desconectarlo.

---

## Hallazgos altos

### H1. El aislamiento entre empresas no cubre los componentes que están fuera de Postgres
**Refs:** NFR-1, NFR-2, NFR-3, FR-21; addendum (Managed Agents, Vault, scheduled deployments, Edge Functions, webhooks).

NFR-2 habla de la "capa de datos". En esta arquitectura, una parte grande del estado vive **fuera** de las tablas con RLS:

- sesiones, archivos y vaults en Anthropic;
- scheduled deployments;
- Supabase Storage;
- las colas y jobs del lector IMAP;
- webhooks;
- cachés.

Además, las Edge Functions suelen usar la llave `service_role`, que **ignora RLS**.

**Requisitos propuestos:**
- **NFR-33** La empresa de toda operación se toma de la sesión autenticada del usuario o, en procesos en segundo plano, del registro del job. Nunca se toma de parámetros enviados por el cliente.
- **NFR-34** Cada sesión, archivo, vault, programación y job externo se registra en Nuvo con su `company_id`. Antes de crear o reanudar una sesión, el backend valida que el vault, los archivos y el agente pertenezcan a la misma empresa.
- **NFR-35** Webhooks entrantes (motor de agentes, HubSpot, correo):
  - se verifica la firma;
  - se rechazan los repetidos (idempotencia y ventana de tiempo);
  - la empresa se deduce del recurso registrado, no del contenido del webhook.
- **NFR-36** El código que usa credenciales de servicio que ignoran RLS está limitado a funciones identificadas. Cada una aplica explícitamente el filtro por empresa y está cubierta por las pruebas de NFR-2.
- **NFR-37** Supabase Storage usa rutas con prefijo por empresa, políticas de acceso por empresa y rol, y URLs firmadas de corta duración (≤ 15 min).
- **NFR-38** Las pruebas de NFR-2 incluyen: Storage, Edge Functions, webhooks, jobs programados, sesiones del motor de agentes, búsquedas de privacidad (NFR-14), exportaciones (FR-16) y cachés.

### H2. No hay MFA ni reautenticación para las acciones de mayor impacto
**Refs:** FR-2, FR-9, FR-13–18, FR-20, FR-49, FR-60, NFR-14.

Un Admin puede:

- borrar la empresa;
- exportar todos los datos;
- conectar HubSpot;
- leer todas las conversaciones;
- convertir a cualquiera en Admin;
- importar prompts.

Todo eso se protege solo con correo y contraseña.

**Requisitos propuestos:**
- **NFR-39** MFA (TOTP como mínimo) obligatorio para los Admin y opcional para los demás roles. En Fase 2, cada empresa puede exigirlo a todos sus usuarios.
- **NFR-40** Estas acciones piden reautenticación reciente (≤ 5 min) con MFA:
  - borrar la empresa o cancelar su borrado;
  - exportar datos (FR-16, NFR-14);
  - dar el rol de Admin;
  - conectar o desconectar HubSpot o el correo;
  - borrado de privacidad;
  - cambiar el límite diario de correo;
  - importar un `.md`.
- **FR-88** Cuando alguien da de alta a un Admin, solicita o cancela el borrado de la empresa, o exporta datos, todos los Admin reciben un aviso por correo.

### H3. Faltan la gestión de sesiones y la política de contraseñas
**Refs:** FR-2–5, FR-9, FR-10, FR-11; addendum (Supabase Auth).

- Con Supabase, un JWT sigue siendo válido hasta que expira. Si el rol se lee del token, un **cambio de rol o una desactivación (FR-9/10) no surte efecto** hasta que se renueva.
- No hay requisitos de longitud de contraseña, de contraseñas filtradas ni de límite de intentos.
- No se dice dónde se guarda el token en el móvil.

**Requisitos propuestos:**
- **NFR-41** Desactivar, eliminar, cambiar el rol o cambiar la contraseña de un usuario cierra de inmediato todas sus sesiones (revoca los refresh tokens). La autorización en el servidor lee el rol y el estado vigentes, o el token de acceso dura ≤ 15 min.
- **NFR-42** Política de contraseñas:
  - mínimo 12 caracteres;
  - se rechazan las contraseñas filtradas conocidas;
  - límite de intentos por cuenta y por IP, con bloqueo progresivo;
  - recuperación (FR-3) con respuesta idéntica exista o no la cuenta, y un enlace de un solo uso que caduca en ≤ 1 h.
- **NFR-43** En el móvil, los tokens se guardan en el almacenamiento seguro del sistema (Keychain/Keystore). En la web se aplica una CSP estricta para reducir el riesgo de robo de tokens.
- **FR-89** Cada usuario ve sus sesiones activas y puede cerrarlas. El Admin puede cerrar todas las sesiones de un usuario.
- **NFR-44** Las sesiones vencen tras [ASSUMPTION] 30 días sin actividad.

### H4. No están definidos los roles legales (responsable/encargado), el DPA ni los subencargados
**Refs:** NFR-12–14, sección 2 (Fase 2), métrica "Quejas o bajas".

- En Fase 2, cada empresa cliente decide a quién prospectar y para qué. Es el **responsable** (en CCPA, el "business"), y Nuvo actúa como **encargado** ("service provider/contractor" en CCPA). Para eso se necesita un contrato con las cláusulas de 1798.100(d)/1798.140(ag) CCPA y las de la LFPDPPP. En el piloto, Creative Era es a la vez responsable y operador de la plataforma.
- Desde 2023, la exención B2B de CCPA terminó: el correo, el nombre y el cargo de un dueño de negocio en California **sí son información personal**.
- Hay subencargados (Anthropic, Supabase, Hostinger, Titan, HubSpot) y transferencias internacionales (EE. UU. ↔ México) que el PRD no menciona.
- **Riesgo de "data broker" (Delete Act / SB 362):** hoy Nuvo no vende datos y NFR-14 prohíbe reusarlos entre empresas. Pero si en el futuro se arma una base de prospectos común o se comparten "prospectos ya investigados" entre clientes, Nuvo podría calificar como data broker, con obligación de registro y de atender la plataforma DROP. Hay que cerrarlo por requisito.

**Requisitos propuestos:**
- **NFR-45** Nuvo actúa como encargado (service provider) de cada empresa cliente respecto de los datos de prospectos y contactos. Lo formaliza en Términos de servicio y un DPA aceptados en el registro (FR-1), que incluyen:
  - finalidad limitada;
  - prohibición de vender o compartir;
  - lista de subencargados y aviso de cambios;
  - asistencia con solicitudes de derechos;
  - aviso de incidentes;
  - devolución o borrado al terminar.
- **NFR-46** Nuvo nunca combina, reutiliza ni pone a disposición de otra empresa los datos de prospectos recopilados para una empresa, ni los usa para entrenar modelos o construir una base propia. (Esto amplía NFR-14 y evita que Nuvo califique como data broker.)
- **FR-90** Hay una Política de uso aceptable que prohíbe, entre otras cosas, el correo masivo no solicitado y la prospección de consumidores (no negocios). La empresa la acepta al registrarse.
- **NFR-47** Hay un aviso de privacidad de Nuvo para los usuarios de la app (empleados de las empresas) conforme a la LFPDPPP y a CCPA (CCPA cubre a empleados y contratistas). El aviso cubre la lectura de conversaciones por el Admin (FR-60), el correo conectado y las transferencias internacionales.
- **NFR-48** Hay un registro de subencargados con la región de los datos y los términos de retención de cada uno (en particular los datos de sesión de Managed Agents, que está en beta).

### H5. Los derechos de privacidad no llegan a todas las copias, y no hay lista de "no prospectar"
**Refs:** NFR-12–14, FR-18, FR-77, FR-83.

- NFR-14 borra datos "de Nuvo", pero los mismos datos están en **HubSpot** (FR-67), en sesiones y archivos del motor de agentes, en correos enviados y recibidos, en respaldos y en logs.
- **Sin una lista de supresión, el agente semanal (FR-75) volverá a encontrar y proponer a la persona que pidió el borrado.** Eso es una violación práctica del derecho de borrado y una fuente de quejas.
- FR-83 (baja de correo) existe por empresa, pero no se conecta con la prospección.

**Requisitos propuestos:**
- **FR-91** Lista de "no prospectar" por empresa. Una persona entra en ella cuando:
  - pide la baja (FR-83);
  - pide el borrado (NFR-14);
  - el Admin la agrega manualmente.

  La lista guarda un identificador mínimo (hash del correo o del dominio y el nombre). Los agentes descartan a esas personas antes de proponerlas y lo indican en el resumen (FR-73).
- **FR-92** Un borrado de privacidad (NFR-14):
  - (a) borra la información en Nuvo, incluidas las conversaciones, los resultados, los archivos y los correos guardados;
  - (b) ofrece borrar o anonimizar el contacto en HubSpot desde la misma acción, o deja una tarea con el enlace;
  - (c) solicita el borrado de las sesiones y archivos asociados en el motor de agentes;
  - (d) deja una entrada de auditoría con la fecha de atención.
- **NFR-49** Las solicitudes de derechos se atienden dentro de los plazos legales (45 días en CCPA; 20 días hábiles en la LFPDPPP). Nuvo muestra al Admin el plazo restante.
- **NFR-50** Los respaldos vencen en ≤ [ASSUMPTION] 35 días. Los datos borrados no se restauran a producción desde un respaldo sin volver a aplicar los borrados pendientes.

### H6. No hay periodos de retención; varios datos se guardan para siempre
**Refs:** FR-12, FR-42, FR-59, FR-70, FR-71, FR-73, FR-81, NFR-12, NFR-13.

- Los resultados archivados "no se borran" (FR-70).
- Las conversaciones, los archivos subidos, los correos recibidos, los descartes (con datos personales de prospectos rechazados, según FR-73) y las corridas no tienen caducidad.
- Los datos de usuarios eliminados (FR-12) se conservan indefinidamente.

Esto contradice la minimización de CPRA (1798.100(c)) y el principio de proporcionalidad de la LFPDPPP.

**Requisitos propuestos:**
- **NFR-51** Retención por tipo de dato (valores [ASSUMPTION], configurables por la plataforma):

  | Dato | Retención |
  |---|---|
  | Candidatos descartados | 30 días |
  | Resultados por revisar o archivados sin enviar | 90 días |
  | Conversaciones y archivos subidos | 12 meses sin actividad |
  | Cuerpos de correos recibidos | 12 meses |
  | Corridas (metadatos) | 24 meses |
  | Auditoría | 24 meses |

  Al vencer, el dato se borra o se anonimiza.
- **NFR-52** Los candidatos descartados solo guardan lo necesario para explicar el descarte (nombre de la empresa y motivo), no los datos de contacto de personas.
- Hay que modificar **FR-70**: "Los archivados se borran al cumplir el plazo de NFR-51 y la app avisa 7 días antes."

### H7. El control de costos se limita a cada corrida; no hay límites por empresa ni para conversaciones
**Refs:** FR-43, FR-51–53, FR-56, FR-74, NFR-17, NFR-18.

- NFR-18 pone un tope por corrida, pero nada limita el número de corridas ni el costo de las conversaciones, de la creación de agentes (FR-43) o del autollenado de la ficha (FR-34).
- Una frecuencia de programación sin límite, o un agente creado con "corre cada 5 minutos", puede generar costos sin control. Con autoregistro gratuito o en prueba (Fase 2), esto es un **vector directo de abuso económico**.

**Requisitos propuestos:**
- **NFR-53** Cada empresa tiene un presupuesto de IA diario y mensual (en Fase 1 lo fija la plataforma; en Fase 2, según el plan). Al llegar al 80 % se avisa al Admin. Al llegar al 100 %, se bloquean las nuevas corridas y mensajes hasta el siguiente periodo o hasta que la plataforma lo amplíe.
- **NFR-54** Toda conversación tiene un tope de costo por mensaje y por conversación. La creación de agentes y el autollenado de la ficha cuentan para el presupuesto.
- **NFR-55** La frecuencia mínima de programación es [ASSUMPTION] diaria. Hay límites de corridas manuales por usuario por hora y de sesiones simultáneas por empresa.
- **FR-93** La plataforma (no el Admin de la empresa) puede suspender la ejecución de agentes y el envío de correo de una empresa con un interruptor de emergencia que queda registrado en la auditoría.
- **NFR-56** Hay alertas a la plataforma ante gasto anómalo por empresa (por ejemplo, más de 3 veces su promedio diario).

### H8. Autoregistro y abuso del envío de correo
**Refs:** FR-1, FR-78, FR-82–84; sección 2 (la tabla incluye F1 en la Fase 1).

- Según la tabla de fases, el autoregistro (FR-1) **forma parte de la Fase 1**, aunque el piloto es solo para Creative Era. Abrirlo antes de tiempo expone costos (H7) y envío de correo.
- En Fase 2, cualquiera puede registrarse, conectar un buzón y mandar correo en frío redactado por IA. FR-84 lo limita a 50 por cuenta, pero el Admin lo puede subir sin tope y puede crear cuentas sin límite.

**Requisitos propuestos:**
- **FR-94** En Fase 1 el autoregistro está desactivado. Las empresas solo se crean por invitación de la plataforma.
- **FR-95** Requisitos del autoregistro (Fase 2):
  - verificar el correo de quien se registra;
  - protección anti-bots;
  - registrarse con un correo del dominio de la empresa (sin correos gratuitos) o pasar una revisión manual;
  - aceptar los Términos, la Política de uso aceptable y el DPA (NFR-45).
- **NFR-57** Límites de envío:
  - la plataforma define un máximo absoluto por buzón al día ([ASSUMPTION] 200) que el Admin no puede superar;
  - las empresas nuevas tienen un límite reducido durante [ASSUMPTION] 14 días;
  - hay un máximo de buzones conectados por empresa según el plan.
- **NFR-58** La plataforma vigila la tasa de rebotes y de quejas o bajas por empresa. Si se superan los umbrales ([ASSUMPTION] 5 % de rebotes, 0.3 % de quejas), el envío se suspende automáticamente y se avisa al Admin.

### H9. Las exportaciones con datos personales no tienen controles
**Refs:** FR-16, NFR-14, FR-14–18.

La exportación previa al borrado (FR-16) y la de NFR-14 contienen información personal de prospectos, de usuarios y conversaciones. No se dice cómo se entrega, quién la puede pedir, cuánto dura ni si queda registrada. Cualquier Admin, incluido uno recién promovido, puede descargar todo.

**Requisitos propuestos:**
- **FR-96** Las exportaciones:
  - las genera el servidor de forma asíncrona;
  - se descargan con un enlace firmado de un solo uso que caduca en ≤ 24 h y exige sesión de Admin con reautenticación (NFR-40);
  - quedan en auditoría, con aviso a todos los Admin (FR-88);
  - se borran del almacenamiento al caducar.
- **FR-97** La exportación de la empresa no incluye credenciales (HubSpot, correo) ni secretos, e indica su contenido en un índice.

### H10. La auditoría tiene huecos de cobertura e integridad
**Refs:** NFR-15, NFR-16, FR-26, FR-60, FR-78–84.

**Eventos que faltan:**
- inicios de sesión exitosos y fallidos;
- recuperación de contraseña;
- generación de contraseñas temporales (FR-7/8);
- alta y baja de MFA;
- **lectura de conversaciones ajenas por un Admin (FR-60)**;
- exportaciones;
- conexión y desconexión del correo;
- correos enviados;
- cambios en la lista de exclusión;
- **ediciones humanas directas en HubSpot (FR-26)**;
- cambios de asignación de agentes por rol (FR-40);
- cambios de límites o presupuestos;
- uso del interruptor de la plataforma.

**Integridad:** "nadie puede editar" debe aplicarse a nivel de base de datos (incluida la llave `service_role`). Además, borrar la auditoría junto con la empresa (NFR-16) elimina la evidencia de que el borrado y las solicitudes de privacidad se atendieron.

**Requisitos propuestos:**
- **NFR-59** La auditoría incluye los eventos anteriores, cada uno con actor, empresa, IP o dispositivo, fecha y objeto. No guarda contraseñas, tokens ni contenido de correos.
- **NFR-60** La tabla de auditoría solo admite inserciones: sin UPDATE ni DELETE para ningún rol de la aplicación. Las entradas se encadenan con hash o se exportan periódicamente a un almacén inmutable.
- **NFR-61** Al borrar la empresa, se conserva durante [ASSUMPTION] 24 meses un registro mínimo, sin datos de prospectos: id de la empresa, fecha de solicitud y de ejecución, Admin que lo pidió y solicitudes de privacidad atendidas.

---

## Hallazgos medios

### M1. Con la contraseña temporal, el Admin conoce la contraseña del usuario
**Refs:** FR-4, FR-5, FR-7, FR-8; addendum (autenticación).

Mostrar la contraseña al Admin rompe el no repudio: el Admin puede entrar como el usuario durante 24 h. Además, la contraseña suele viajar por canales inseguros (WhatsApp, chat). Supabase permite invitar por enlace.

**Propuesta:**
- Modificar **FR-7/8**: "El sistema envía al usuario un enlace de invitación de un solo uso que caduca en 24 h. Si el Admin elige la opción alternativa de contraseña temporal, esta es aleatoria (≥ 16 caracteres), de un solo uso, se muestra una vez y queda en auditoría."
- **NFR-62** Mientras la contraseña temporal no se cambie, la sesión solo permite cambiar la contraseña (esto refuerza FR-4 en el servidor).

### M2. Faltan detalles de CAN-SPAM y de entregabilidad
**Refs:** FR-79, FR-82, FR-83, FR-84.

- CAN-SPAM exige identificar el mensaje como publicidad si no hay consentimiento previo, encabezados y asunto no engañosos, y un mecanismo de baja que funcione al menos 30 días después del envío. Las bajas se respetan en ≤ 10 días hábiles. La responsabilidad alcanza a quien "inicia" el mensaje (la empresa) y potencialmente a la plataforma.
- No se dice si la baja se detecta cuando el prospecto la pide en una respuesta ("remove me").
- No hay verificación de SPF, DKIM ni DMARC del dominio del remitente, ni manejo de rebotes.

**Requisitos propuestos:**
- **FR-98** Cada correo comercial:
  - incluye un enlace de baja con token no adivinable, alojado por Nuvo, que funciona al menos 30 días tras el envío y no exige iniciar sesión;
  - incluye el encabezado `List-Unsubscribe` (y `List-Unsubscribe-Post`);
  - las bajas se aplican de inmediato a toda la empresa, a todos los buzones y usuarios.
- **FR-99** Nuvo detecta pedidos de baja en las respuestas (FR-81), los marca para confirmación del usuario y, mientras tanto, bloquea el envío a esa persona.
- **FR-100** El asunto del correo no puede ser engañoso y el remitente coincide con el buzón conectado. La plantilla incluye la identificación como mensaje comercial cuando no hay relación previa con el destinatario.
- **NFR-63** Al conectar un buzón, Nuvo verifica los registros SPF, DKIM y DMARC del dominio y avisa si faltan. Los rebotes duros agregan la dirección a la lista de supresión.

### M3. El Admin lee las conversaciones de otros sin transparencia ni registro
**Refs:** FR-60, NFR-15.

Es legítimo, pero en México (LFPDPPP) y en California (CPRA cubre a empleados) el empleado debe saberlo. Además, las conversaciones pueden contener datos personales del usuario.

**Propuesta:**
- **FR-101** La pantalla de conversaciones informa: "Los Admin de tu empresa pueden leer tus conversaciones".
- Cada lectura de un Admin sobre una conversación ajena queda en la auditoría (NFR-59). El Admin no puede exportar conversaciones individuales fuera de los flujos de FR-16/NFR-14.

### M4. Archivos subidos y URL de la ficha: validación, malware y SSRF
**Refs:** FR-33 (logotipo), FR-34, FR-58.

**Propuesta:**
- **NFR-64** Los archivos subidos:
  - se validan por tipo real (no por la extensión), solo PDF, PNG, JPG o WebP;
  - tienen un tamaño máximo de [ASSUMPTION] 20 MB;
  - pasan un análisis antimalware antes de estar disponibles;
  - se les eliminan los metadatos EXIF;
  - nunca se sirven desde el dominio de la app sin `Content-Disposition: attachment`.
- **NFR-65** Si el servidor de Nuvo descarga la URL de FR-34, solo acepta http/https hacia IPs públicas (bloquea rangos privados, `localhost` y metadata de la nube), limita las redirecciones, el tamaño y el tiempo, y no envía cookies ni credenciales.

### M5. Hay escrituras automáticas en HubSpot que contradicen el principio de confianza, y ediciones humanas que no se auditan
**Refs:** FR-26, FR-62, FR-80, FR-81; Visión ("ningún agente escribe en HubSpot").

FR-81 cambia el estatus del lead en HubSpot sin revisión, a partir de un correo que controla el prospecto. Un falso positivo, o una respuesta diseñada para eso, altera el pipeline. Por otra parte, FR-26 escribe en HubSpot sin dejar rastro en Nuvo.

**Propuesta:**
- Aclarar en **FR-80/81** que estas escrituras las hace el backend con reglas deterministas (encabezados), no un agente.
- El cambio de estatus solo ocurre si la respuesta coincide por `In-Reply-To` con un envío de Nuvo. Cualquier otra inferencia (intención, sentimiento) queda "por revisar".
- Cada escritura (FR-26, FR-80, FR-81) queda en la auditoría.

### M6. No hay límites sobre datos sensibles e inferencias de los agentes
**Refs:** NFR-13, FR-76.

NFR-13 limita las fuentes, pero no el **tipo** de dato. Un agente puede anotar en el "ángulo de entrada" datos sensibles según CPRA 1798.140(ae) (salud, origen étnico, religión, orientación sexual, situación migratoria), o inferirlos de redes sociales.

**Propuesta:**
- **NFR-66** Los agentes no recopilan, infieren ni registran datos personales sensibles (definición CPRA y LFPDPPP), ni datos de redes sociales personales de individuos. Su análisis se limita al negocio. Las pruebas de NFR-27 incluyen casos que lo verifican.

### M7. No hay respuesta a incidentes ni notificación de brechas
**Refs:** no existe (el más cercano es NFR-11).

California (Civ. Code 1798.82, y acción privada de 1798.150 ante brechas por falta de "seguridad razonable") y la LFPDPPP exigen notificar.

**Propuesta:**
- **NFR-67** Hay un procedimiento de respuesta a incidentes. Si un incidente afecta los datos de una empresa, se le notifica en ≤ 72 h, con la información necesaria para que ella notifique a los afectados. El procedimiento incluye la rotación de credenciales comprometidas (HubSpot, correo, llave de la API de IA).

### M8. El ciclo de vida del OAuth de HubSpot está incompleto
**Refs:** FR-19–22, FR-18, NFR-9.

**Propuesta:**
- **NFR-68** OAuth con `state` (y PKCE cuando aplique) y scopes mínimos documentados.
- Al desconectar o al borrar la empresa, el refresh token se revoca en HubSpot y se borra del vault.
- Si el mismo portal de HubSpot ya está conectado a otra empresa de Nuvo, se avisa y la plataforma lo revisa.
- El token nunca aparece en logs.

### M9. La gobernanza de agentes generados o importados es débil
**Refs:** FR-43, FR-47–50, FR-86 (propuesto en C2).

Un agente generado por una frase o importado de un `.md` define sus propias "conexiones necesarias" (FR-43). Nada impide que pida herramientas fuera de la lista, que ignore los temas prohibidos (FR-36) o que se programe con una frecuencia abusiva.

**Propuesta:**
- **NFR-69** Las herramientas, conexiones y frecuencias de un agente se eligen de una lista cerrada que define la plataforma y se validan en el servidor.
- Los parámetros que Ventas y Marketing pueden ajustar (FR-48) tienen rangos validados.
- Las instrucciones de la plataforma (no escribir en HubSpot, respetar los temas prohibidos, no recopilar datos sensibles) se anteponen siempre al prompt y el usuario no las puede editar.

### M10. Un solo Admin puede abusar del poder del rol, y no hay recuperación
**Refs:** FR-9, FR-13, FR-14, FR-17.

Cualquier Admin puede quitarles el rol a los demás Admin, programar el borrado y exportar todo. Si el único Admin pierde su MFA o su correo, la empresa queda sin acceso.

**Propuesta:**
- **FR-102** Quitarle el rol de Admin a otro Admin o eliminarlo avisa a todos los Admin (FR-88).
- Durante el periodo de gracia de FR-17 no se puede quitar el rol a ningún Admin.
- **FR-103** Hay un proceso de recuperación de cuenta del Admin vía soporte de la plataforma, con verificación de identidad y registro en auditoría.

---

## Hallazgos bajos

### L1. Logs y observabilidad
**Refs:** NFR-9, NFR-11.

**Propuesta: NFR-70** Los logs de aplicación, de Edge Functions y de errores no contienen credenciales, tokens, cuerpos de correo, contenido de conversaciones ni archivos. Los datos personales se enmascaran.

### L2. Correos de notificación
**Refs:** FR-55, FR-69, FR-70.

**Propuesta:** los avisos y resúmenes por correo no incluyen datos de prospectos ni contenido de resultados. Solo muestran conteos y un enlace que exige iniciar sesión.

### L3. Endurecimiento del cliente web y del hosting
**Refs:** NFR-4, NFR-11; addendum (Hostinger).

**Propuesta:** la web se sirve solo por HTTPS, con HSTS, CSP, `X-Frame-Options`/`frame-ancestors` y sin mapas de código fuente en producción. La configuración de cabeceras se verifica en el hosting compartido.

### L4. Vinculación con el owner de HubSpot por correo
**Refs:** FR-29, FR-28.

Si el correo no coincide o se reutiliza, el usuario podría ver los registros de otra persona o ninguno.

**Propuesta:** la vinculación la confirma el Admin. Si no hay vinculación, el usuario no ve registros (falla cerrada), y todo cambio de vinculación queda en la auditoría.

### L5. Pruebas de seguridad antes de la Fase 2
**Refs:** NFR-2; criterio de paso a la Fase 2.

**Propuesta:** agregar al criterio de paso a la Fase 2:
- pruebas de penetración externas sobre el aislamiento, la autenticación y la inyección;
- revisión de dependencias;
- revisión del modelo de amenazas de los agentes.

---

## Cobertura actual (lo que ya está bien)

- **Aislamiento:** NFR-1 a NFR-3, verificado con pruebas automáticas y vigente desde la Fase 1.
- **Autorización:** NFR-10 se aplica en el servidor. FR-57 y FR-63 limitan adjuntos y revisores.
- **Credenciales fuera del cliente:** NFR-9 y la restricción del addendum (el cliente nunca llama a Claude).
- **Revisión humana antes de escribir:** FR-50, FR-61, FR-62 y FR-79. Falta garantizarla técnicamente (C1).
- **Procedencia y fuentes permitidas:** NFR-12 y NFR-13.
- **Derechos de privacidad:** NFR-14 (búsqueda, exportación y borrado por persona) y no reutilización entre empresas.
- **Correo:** FR-82 a FR-85 (dirección física, baja, lista de exclusión, límite diario y sin secuencias automáticas).
- **Último Admin y periodo de gracia:** FR-13, FR-15 y FR-17.
- **Costos:** visibilidad por corrida y por empresa (FR-74, NFR-17) y tope por corrida (NFR-18).

## Orden sugerido de atención

1. C1, C2 y C3 antes de cerrar la arquitectura: definen el diseño del motor de agentes, el proxy de HubSpot y el servicio de correo.
2. H1, H2, H3, H7 y H10 antes de que el piloto use datos reales.
3. H4, H5, H6, H8 y H9 antes de abrir la Fase 2. FR-94 (autoregistro desactivado) ya aplica en la Fase 1.
4. Los hallazgos medios y bajos, durante el piloto.
