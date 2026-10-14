---
title: "PRD — Nuvo CRM"
status: final
created: 2026-10-11
updated: 2026-10-11
---

# PRD — Nuvo CRM

## 1. Visión

Nuvo CRM es una aplicación móvil y web en la que cada negocio pone a trabajar **agentes de IA de ventas y marketing** adaptados a su empresa y ve en un solo lugar lo que producen: prospectos, propuestas, contenido, campañas.

A partir de la Fase 2, cualquier negocio puede **registrarse por su cuenta**. Al hacerlo obtiene un espacio propio y totalmente aislado, con sus usuarios, sus agentes, sus prospectos y sus clientes. Ninguna empresa puede ver los datos de otra.

Cada empresa organiza a su gente en tres roles:

- **Admin** — controla todo: da de alta, desactiva y elimina cuentas; mantiene la ficha del negocio; crea agentes para cualquier rol y decide qué agentes ve cada rol.
- **Ventas** — usa los agentes de ventas (prospección, contacto, propuestas, seguimiento del pipeline) y crea o ajusta agentes de su rol.
- **Marketing** — usa los agentes de marketing (email, SEO, contenido, redes, pauta) y crea o ajusta agentes de su rol.

Al empezar, la empresa recibe un **catálogo de agentes plantilla** listos para usar. Llena una **ficha de su negocio** una sola vez —qué vende, a quién, en qué regiones, en qué idioma y con qué tono— y todos los agentes la usan automáticamente. Si necesita algo que el catálogo no tiene, lo **describe en una frase** y la app arma el agente.

Los agentes trabajan de dos formas:

- **Agentes de tarea**, que corren al tocar "Ejecutar" o con un horario. Por ejemplo, "cada lunes encuentra entre 15 y 20 prospectos que encajen con mi negocio". Lo que producen queda como resultado por revisar antes de enviarse a HubSpot.
- **Agentes conversacionales**, expertos a los que el usuario les escribe un pedido. Por ejemplo, "redáctame la secuencia de contacto para estos 5 prospectos".

**HubSpot es el CRM de cada empresa.** Empresas, contactos, deals y tareas viven en el HubSpot de cada cliente, que es obligatorio para usar Nuvo. Nuvo se conecta a él, muestra el pipeline completo dentro de la app con las etapas que cada empresa ya tiene configuradas, y los agentes leen esos registros para hacer su trabajo. Nuvo guarda lo propio: usuarios, roles, ficha del negocio, agentes, corridas, conversaciones y resultados por revisar.

**Principio de confianza:** los agentes solo leen y proponen. Ningún agente tiene la capacidad de escribir en HubSpot ni de enviar correos: dejan sus resultados en Nuvo, una persona los revisa, corrige lo que haga falta y decide qué se envía. Quien escribe en HubSpot o envía un correo es siempre la app, por una acción de una persona.

El origen es el agente "Prospectos semanales California" que Creative Era Design ya opera como rutina programada sobre HubSpot. Nuvo CRM convierte ese modelo en producto para cualquier negocio.

## 2. Fases y alcance

| Fase | Quién lo usa | Qué incluye |
|---|---|---|
| **Fase 1 — Piloto interno** | Solo Creative Era Design | Todos los grupos de funciones F1–F10 completos: acceso, usuarios y roles (incluida la zona roja); ficha del negocio; las 20 plantillas del catálogo; crear agentes "en una frase" e importar `.md`; ejecución; revisión y envío a HubSpot; pipeline e historial; conexión con HubSpot; correo con detección de respuestas; privacidad y auditoría completas. Solo web (escritorio y navegador del celular). La ficha y la Prospección semanal de Creative Era replican la rutina "Prospectos semanales California" que ya opera. El aislamiento entre empresas existe desde el día uno. [ASSUMPTION] El autoregistro (FR-1) se construye en esta fase, pero permanece cerrado al público hasta la Fase 2. **Sin cobro.** |
| **Fase 2 — Lanzamiento a clientes** | Cualquier negocio que se registre | Todo lo de la Fase 1, más las apps móviles de iOS y Android y el cobro/suscripción (modelo por definir). |

**Criterio para cerrar el piloto (Fase 1):** [ASSUMPTION] un mes de uso, contado desde que la Fase 1 está completa, en Creative Era y al menos un cliente cerrado gracias a un prospecto o trabajo de un agente.

**Criterio para abrir a clientes (Fase 2):** [ASSUMPTION] además del piloto exitoso, las pruebas de aislamiento entre empresas (NFR-2) pasando.

**Fuera de alcance de la Fase 1:** cobro, planes y suscripciones; secuencias automáticas de correo (FR-85); publicación directa de entregables en redes sociales, blogs o plataformas de anuncios (FR-89).

## 3. Métricas de éxito

| Métrica | Qué mide | Cómo se obtiene |
|---|---|---|
| Conversión de prospectos | Prospectos generados por agentes que se vuelven clientes | [ASSUMPTION] Deals en *Closed Won* en HubSpot cuyo prospecto originó un agente de Nuvo |
| Horas ahorradas | Tiempo de trabajo manual que reemplazan los agentes | [ASSUMPTION] Cada agente tiene un tiempo manual equivalente por resultado útil (por ejemplo, un prospecto ≈ 20 min); se ajusta con datos del piloto |
| Retención | Empresas que siguen pagando el servicio | Aplica desde Fase 2 |

**Métricas de control:**

- Tasa de descarte en la revisión de resultados: si la mayoría de lo que producen los agentes se descarta, los agentes no sirven aunque produzcan mucho.
- Costo de IA por empresa frente a lo que paga.
- Quejas o bajas de los prospectos contactados.

**Metas numéricas:** se definen al cerrar el piloto, con los datos de uso y de costo. Responsable: Nacho.

## 4. Mapa de funciones

| # | Grupo de funciones | Qué cubre |
|---|---|---|
| F1 | Registro de empresa y acceso | Autoregistro de la empresa (quien registra = Admin); inicio de sesión; contraseña temporal y cambio obligatorio en el primer acceso |
| F2 | Usuarios y roles | El Admin crea, desactiva y elimina cuentas; asigna los roles Admin, Ventas y Marketing |
| F3 | Ficha del negocio | Perfil único de la empresa que alimenta a todos los agentes; solo lo edita el Admin |
| F4 | Catálogo y asignación de agentes | Plantillas de Ventas y de Marketing; el Admin decide qué agentes ve cada rol |
| F5 | Creación y personalización de agentes | Describir el agente → borrador → revisión → prueba → activar; edición avanzada e importación de `.md` (solo Admin); límites de lo que cada rol puede ajustar |
| F6 | Ejecución de agentes | Agentes de tarea (manual y programada) y agentes conversacionales |
| F7 | Revisión de resultados | Los resultados de los agentes quedan en Nuvo como resultados por revisar; una persona los revisa, edita y envía a HubSpot o los descarta; los entregables que no van a HubSpot se guardan en Nuvo |
| F8 | Pipeline, historial y costos | Vista completa del pipeline de HubSpot dentro de la app; historial y resúmenes de corridas; costos de IA (solo Admin) |
| F9 | Conexión con HubSpot | Conexión obligatoria de cada empresa con su propio HubSpot; estado de la conexión |
| F10 | Correo | Cada usuario conecta su correo de empresa; los agentes redactan, la persona revisa y envía; respuestas detectadas y registradas en Nuvo y HubSpot |

## 5. Requisitos funcionales

Los identificadores (FR-n) son estables: no siguen un orden consecutivo dentro de cada grupo porque se asignaron a medida que se tomaron las decisiones.

### F1 — Registro de empresa y acceso

- **FR-1** Un negocio puede registrarse por su cuenta y crear su empresa en Nuvo CRM; la persona que la registra queda como su Admin. Se construye en la Fase 1 y se abre al público en la Fase 2; el registro exige verificar el correo y no permite conectar un HubSpot que ya esté conectado a otra empresa.
- **FR-2** Los usuarios inician sesión con correo y contraseña en móvil y en web.
- **FR-3** Cualquier usuario puede recuperar su contraseña por correo sin intervención del Admin.
- **FR-4** Un usuario que entra con contraseña temporal debe cambiarla antes de usar cualquier otra parte de la app.
- **FR-5** La contraseña temporal caduca 24 horas después de generarse; una vez vencida no sirve para entrar y el Admin debe generar una nueva.

### F2 — Usuarios y roles

- **FR-6** Cada usuario pertenece a una sola empresa y tiene exactamente un rol: Admin, Ventas o Marketing.
- **FR-7** El Admin da de alta a un usuario con su nombre, correo y rol; el sistema genera una contraseña temporal y se la muestra al Admin para que él se la entregue.
- **FR-8** El Admin puede generar una nueva contraseña temporal para un usuario que no ha entrado o cuya contraseña caducó.
- **FR-9** El Admin puede cambiar el rol de un usuario, incluido convertirlo en Admin. Una empresa puede tener varios Admin. Un cambio de rol se aplica de inmediato, incluso en sesiones abiertas.
- **FR-10** El Admin puede desactivar a un usuario: el usuario no puede entrar hasta que el Admin lo reactive. Al desactivarlo, sus sesiones abiertas se cierran de inmediato y sus conexiones de correo dejan de usarse.
- **FR-11** El Admin puede eliminar a un usuario de forma definitiva. Sus sesiones se cierran y las credenciales de su correo conectado se borran.
- **FR-12** Al desactivar o eliminar a un usuario, sus agentes, conversaciones, corridas y resultados se conservan y siguen disponibles para la empresa. [ASSUMPTION] Los agentes personales no compartidos de un usuario eliminado pasan a ser visibles para el Admin, que puede reasignarlos o eliminarlos; sus programaciones se pausan.
- **FR-13** Siempre debe quedar al menos un Admin activo: la app no permite eliminar, desactivar ni cambiar de rol al último Admin de la empresa.

**Zona roja — borrar la empresa**

- **FR-14** Cualquier Admin puede solicitar el borrado de la empresa desde una zona separada y claramente marcada. La app explica qué se borrará: usuarios, agentes, conversaciones, corridas, prospectos y conexiones.
- **FR-15** Para confirmar, el Admin escribe el nombre exacto de la empresa.
- **FR-16** Antes de confirmar, el Admin puede descargar una copia de los datos de la empresa.
- **FR-17** El borrado queda programado durante un periodo de gracia de [ASSUMPTION] 30 días. En ese tiempo, cualquier Admin puede cancelarlo y todo queda como estaba.
- **FR-18** Al vencer el periodo de gracia, se eliminan de forma definitiva la empresa y todos sus registros.

### F3 — Ficha del negocio

- **FR-32** Cada empresa tiene una sola ficha del negocio que solo el Admin edita. Todos los agentes de la empresa la usan como contexto en cada ejecución y conversación.
- **FR-33** La ficha contiene:
  - nombre, sitio web y ubicación;
  - qué vende, cómo cobra, y precios o paquetes;
  - cliente ideal y a quién excluye;
  - verticales prioritarias y regiones donde vende;
  - diferenciadores y casos de éxito;
  - competidores;
  - idioma y tono;
  - logotipo y colores de marca;
  - temas o afirmaciones prohibidos.
- **FR-34** El Admin puede pegar la URL del sitio de la empresa o subir un PDF (presentación, brochure) y la app propone la ficha llena. El Admin revisa, corrige y confirma antes de guardar; nada se guarda sin su confirmación.
- **FR-35** Los campos que la app no pudo deducir quedan marcados como vacíos para que el Admin los complete; la app no inventa datos.
- **FR-36** Los agentes respetan los temas prohibidos de la ficha en todo lo que redactan.
- **FR-37** Un cambio en la ficha se aplica a todas las ejecuciones y conversaciones que empiecen después del cambio.

### F4 — Catálogo y asignación de agentes

- **FR-38** Al crearse, cada empresa recibe su propia copia del catálogo de agentes plantilla:
  - **Ventas (9):** Prospección semanal, Outbound Strategist, Sales Outreach, Offer & Lead-Gen Strategist, Discovery Coach, Proposal Strategist, Pipeline Analyst, Customer Success Manager y Account Strategist.
  - **Marketing (11):** Email Strategist, SEO Specialist, Content Creator, LinkedIn Content Creator, Social Media Strategist, Instagram Curator, AI Citation Strategist, PPC Strategist, Paid Social Strategist, Ad Creative Strategist y Traductor español–inglés.
- **FR-39** Las copias del catálogo pertenecen a la empresa. Si la plataforma mejora una plantilla después, las empresas que ya existen conservan su versión.
- **FR-40** El Admin decide qué agentes ve y usa cada rol. Un usuario solo ve los agentes asignados a su rol, más los suyos y los que otros miembros de su rol compartieron.
- **FR-41** El Admin puede desactivar cualquier agente de la empresa: deja de estar disponible y sus programaciones se pausan hasta que lo reactive.
- **FR-42** El Admin puede eliminar cualquier agente de la empresa. Las corridas y conversaciones pasadas de ese agente se conservan en el historial.

**Plantilla de Prospección semanal**

- **FR-75** La plantilla "Prospección semanal" toma de la ficha del negocio el cliente ideal, las exclusiones, las verticales, las regiones y el idioma. Además permite ajustar:
  - el número objetivo de prospectos por corrida (por defecto, de 15 a 20);
  - el puntaje mínimo para proponer un prospecto (por defecto, 6 sobre 10);
  - las señales de compra a buscar;
  - la rotación semanal de regiones;
  - el horario.
- **FR-76** Por cada prospecto propuesto, el agente entrega su puntaje con el desglose, por qué encaja, las señales encontradas, un ángulo de entrada concreto y las fuentes consultadas. Lo que no pudo confirmar lo marca como estimado.
- **FR-77** El agente nunca baja su estándar para completar la cuota. Si no encuentra suficientes prospectos que califiquen, entrega los que sí califican y explica qué faltó.

**Primeros agentes especificados**

| Agente | Tipo | Qué recibe | Qué produce | Destino tras revisión |
|---|---|---|---|---|
| Prospección semanal | De tarea (programado o manual) | Ficha del negocio + parámetros (FR-75) | 15–20 empresas calificadas con contacto del dueño si es público, puntaje, señales, ángulo de entrada y fuentes; resumen de la corrida | Empresa y contacto en HubSpot como lead de Ventas (FR-31) |
| Outbound Strategist | Conversacional | Uno o varios leads o contactos adjuntos (FR-57) + pedido del usuario | Primer correo y seguimientos sugeridos, personalizados por las señales de cada prospecto | Correos por revisar y enviar desde la cuenta del usuario (F10) |
| Proposal Strategist | Conversacional | Un deal adjunto, notas de la llamada, archivos | Propuesta de suscripción con resumen ejecutivo, alcance y precios de la ficha | Entregable descargable; se puede adjuntar al deal (FR-88) |
| Email Strategist | Conversacional | Segmento o lista de leads de Marketing + objetivo | Secuencia de nutrición: asuntos, cuerpos y momentos de envío sugeridos | Entregable; cada correo se envía manualmente (F10, FR-85) |

- **FR-90** Un resultado de un agente se considera útil si el revisor lo envía o lo usa sin reescribirlo por completo. La tasa de descarte (§3) se mide por agente.
- **FR-91** Las otras 16 plantillas del catálogo, que también entran en la Fase 1, deben quedar especificadas con el mismo formato (tipo, qué recibe, qué produce, destino) antes de construirse. El borrador de esa especificación está en el addendum.

### F5 — Creación y personalización de agentes

- **FR-43** Un usuario crea un agente describiendo en una frase lo que necesita. La app genera un borrador con:
  - nombre;
  - instrucciones que ya incorporan la ficha del negocio;
  - tipo (de tarea o conversacional);
  - horario sugerido, si aplica;
  - las conexiones que necesita.
- **FR-44** El usuario revisa y ajusta el borrador en un formulario sencillo, lo prueba y ve el resultado antes de activarlo. Las pruebas no escriben en HubSpot.
- **FR-45** El Admin puede crear agentes para cualquier rol. Ventas solo crea agentes de Ventas y Marketing solo de Marketing.
- **FR-46** Un agente creado por un usuario es personal por defecto. Su creador puede compartirlo con todo su rol y dejar de compartirlo.
- **FR-47** Cada usuario puede editar y eliminar los agentes que creó.
- **FR-48** Ventas y Marketing pueden ajustar detalles de los agentes de su rol (horario, regiones, tono, parámetros), pero no la ficha del negocio ni los agentes de otro rol.
- **FR-49** Solo el Admin tiene la edición avanzada: modificar las instrucciones completas de un agente o importar un agente desde un archivo `.md`.
- **FR-50** Todo agente queda ligado a un rol y solo usa las conexiones que la empresa autorizó. Ningún agente escribe en HubSpot: sus resultados siempre quedan como resultados por revisar (ver F7), sin excepciones.

### F6 — Ejecución de agentes

**Agentes de tarea**

- **FR-51** Un usuario puede ejecutar en cualquier momento un agente de tarea que tenga disponible, con el botón "Ejecutar".
- **FR-52** El Admin y las personas del rol del agente pueden programarle un horario recurrente (día, hora y zona horaria), cambiarlo y pausarlo.
- **FR-53** Un agente de tarea no puede tener dos corridas a la vez. Si alguien intenta ejecutarlo mientras ya hay una en curso, programada o manual, la app avisa quién la lanzó y desde cuándo corre, y no inicia otra.
- **FR-54** Mientras una corrida está en curso, el usuario ve que el agente está trabajando y puede salir de la app; la corrida continúa sin él.
- **FR-55** Si una corrida falla o termina sin resultados, la app muestra un aviso dentro de la app y envía un correo a quien la lanzó o la programó, y al Admin. El aviso explica el motivo, por ejemplo "HubSpot desconectado".

**Agentes conversacionales**

- **FR-56** Un usuario puede abrir una conversación con cualquier agente conversacional que tenga disponible y pedirle trabajo en lenguaje natural, en español o en inglés.
- **FR-57** En una conversación, el usuario puede adjuntar registros de su pipeline de HubSpot (deal, contacto o empresa) para que el agente trabaje sobre ellos. Solo puede adjuntar registros que él mismo puede ver. Como regla general, un agente solo puede leer de HubSpot lo que puede ver la persona que lo está usando (ver NFR-19).
- **FR-58** En una conversación, el usuario puede subir archivos (PDF e imágenes) para que el agente los use.
- **FR-59** El usuario puede volver a sus conversaciones anteriores y continuarlas.
- **FR-60** Las conversaciones de un usuario con sus agentes las ven él y el Admin. El Admin puede leerlas, pero no escribir en ellas. La app le indica al usuario que el Admin puede verlas.
- **FR-61** Si en una conversación el agente propone crear o cambiar algo en HubSpot, la propuesta queda como resultado por revisar (ver F7). No se escribe en HubSpot hasta que una persona la envíe.

### F7 — Revisión de resultados

- **FR-62** Todo lo que un agente produce para HubSpot (empresas, contactos, deals, tareas, notas o cambios de etapa) queda primero en Nuvo con el estado "por revisar". Nunca llega a HubSpot sin que una persona lo envíe.
- **FR-63** La persona que lanzó o programó el agente y el Admin pueden revisar y enviar sus resultados por revisar.
- **FR-64** Antes de enviar, el revisor puede:
  - ver cada resultado con su justificación y fuentes (por ejemplo: puntaje, señales y URLs);
  - editar sus campos;
  - descartarlo.
- **FR-65** El revisor puede enviar a HubSpot un resultado a la vez o todos los seleccionados en una sola acción.
- **FR-66** Antes de enviar, la app revisa si el registro ya existe en HubSpot (por dominio o nombre). Si existe, avisa al revisor y no crea un duplicado.
- **FR-67** Todo registro que se envía desde un resultado de agente queda marcado en HubSpot como "creado por agente", con el nombre del agente y la fecha, para saber de dónde vino y medir conversión.
- **FR-68** Si el envío de un resultado falla, este queda como "error al enviar" con el motivo, y se puede reintentar.
- **FR-69** Los revisores reciben una vez al día, en la app y por correo, un resumen de los resultados que tienen pendientes de revisar.
- **FR-70** [ASSUMPTION] Los resultados que nadie revisa reciben un recordatorio a los 3 días y se archivan automáticamente a los 30 días. Los archivados no se borran: se pueden recuperar y enviar después.

**Entregables que no van a HubSpot**

- **FR-86** Los textos que producen los agentes (propuestas, secuencias de correo, artículos, publicaciones, guiones, copys de anuncios) se guardan en Nuvo como entregables, ligados a la corrida o conversación que los produjo.
- **FR-87** Quien puede ver la corrida o la conversación puede ver, editar, copiar y descargar sus entregables en PDF o Word.
- **FR-88** Si un entregable está ligado a un deal, contacto o empresa, una persona puede adjuntarlo a ese registro de HubSpot como nota. El agente no puede hacerlo por su cuenta.
- **FR-89** Fuera de alcance de la Fase 1: publicar entregables directamente en redes sociales, blogs o plataformas de anuncios.

### F8 — Pipeline, historial y costos

**Pipeline de HubSpot dentro de Nuvo**

- **FR-23** La app muestra el pipeline completo de deals de la empresa con las etapas configuradas en su HubSpot, no solo los registros creados por agentes.
- **FR-24** Desde un deal, empresa o contacto, el usuario ve qué agente lo creó o modificó y cuándo, y puede abrir el registro en HubSpot.
- **FR-25** Si la empresa cambia sus etapas en HubSpot, Nuvo refleja el cambio sin intervención.
- **FR-26** Admin, Ventas y Marketing pueden editar desde Nuvo los registros que ven: mover un deal de etapa y cambiar sus campos. El cambio se guarda en HubSpot de inmediato. Las ediciones hechas por una persona no pasan por la revisión de resultados (F7).
- **FR-27** El Admin ve el pipeline completo de la empresa.
- **FR-28** Cada rol ve su propio pipeline, y dentro de él solo los registros asignados a esa persona:
  - **Ventas:** deals y leads ya entregados a Ventas (SQL); puede editarlos.
  - **Marketing:** leads y contactos de Lead a MQL; puede editarlos. Además ve, sin poder editarlos, los deals que se originaron en sus campañas.
- **FR-29** Cada usuario de Nuvo queda vinculado a su usuario (owner) de HubSpot, [ASSUMPTION] por correo electrónico, para saber qué registros le pertenecen. El Admin ve y corrige esa vinculación.
- **FR-30** Cuando un lead cumple el criterio de entrega, una persona lo pasa de Marketing a Ventas desde la app: cambia de responsable y de etapa de ciclo de vida en HubSpot. [PENDIENTE] El criterio de entrega se define durante el piloto (responsable: Nacho).
- **FR-31** Los leads que crea un agente de prospección en frío (como Prospección semanal) se asignan a Ventas, no a Marketing.

**Historial de corridas y costos**

- **FR-71** Cada agente tiene un historial de corridas con:
  - fecha y hora;
  - quién la lanzó, o si fue programada;
  - duración;
  - estado (en curso, terminada, fallida);
  - número de resultados.
- **FR-72** El historial de un agente lo ve quien puede ver ese agente: todo el rol si el agente está compartido o asignado al rol, solo su creador si es personal, y el Admin siempre. Ver el historial no da permiso para revisar ni enviar resultados (ver FR-63).
- **FR-73** Al terminar, cada corrida muestra un resumen:
  - qué produjo;
  - cuántas empresas candidatas evaluó y cuántas descartó, con el motivo;
  - cuáles ya existían en HubSpot;
  - los errores que hubo.
- **FR-74** El Admin ve el costo de IA de cada corrida y conversación, y el total de la empresa por periodo y por agente. Ningún otro rol ve costos.

### F9 — Conexión con HubSpot

- **FR-19** Cada empresa debe conectar su propia cuenta de HubSpot para usar agentes; sin conexión, la app guía al Admin para hacerla.
- **FR-20** Solo el Admin puede conectar, reconectar o desconectar HubSpot.
- **FR-21** Las credenciales de HubSpot de una empresa solo las usan los agentes de esa empresa.
- **FR-22** La app muestra el estado de la conexión y avisa al Admin si se rompe; mientras esté rota, los agentes que dependen de HubSpot no se ejecutan y el usuario ve por qué.

### F10 — Correo

- **FR-78** Cada usuario puede conectar su propia cuenta de correo de empresa (en el piloto, Titan Email) para enviar correos desde Nuvo. Las credenciales solo las usa ese usuario.
- **FR-79** Los agentes pueden redactar correos para prospectos y contactos, pero nunca los envían. El borrador queda como resultado por revisar; el usuario lo edita y toca "Enviar". El correo sale desde su cuenta conectada.
- **FR-80** Cuando una persona envía un correo desde Nuvo, la app lo registra en Nuvo y como actividad de correo en el contacto correspondiente de HubSpot.
- **FR-81** Nuvo detecta las respuestas a correos enviados desde Nuvo en la cuenta conectada (no lee el resto del buzón), avisa al usuario en la app, las registra en Nuvo y en HubSpot, y actualiza el *Lead Status* del lead (por ejemplo, a "Connected").
- **FR-82** Cada correo comercial incluye la dirección física de la empresa, tomada de la ficha, y un enlace para darse de baja que funciona con un clic y que Nuvo procesa automáticamente.
- **FR-83** Si un destinatario se da de baja o pide no recibir más correos, queda en la lista de exclusión de la empresa: Nuvo impide enviarle otro, lo marca en HubSpot y los agentes no lo vuelven a proponer como prospecto. Los correos que rebotan de forma permanente también entran en la lista.
- **FR-84** Cada cuenta conectada tiene un límite diario de correos enviados desde Nuvo, [ASSUMPTION] 50 por defecto, ajustable por el Admin. Al alcanzarlo, Nuvo no deja enviar más hasta el día siguiente.
- **FR-85** Fuera de alcance de Fase 1: secuencias automáticas de seguimiento. Los agentes pueden sugerir el siguiente correo, pero siempre lo envía una persona.

## 6. Requisitos no funcionales

### Aislamiento entre empresas

- **NFR-1** Ningún usuario, agente ni proceso de una empresa puede leer, modificar o inferir datos de otra empresa. Esto incluye usuarios, ficha, agentes, corridas, conversaciones, resultados, archivos, costos y credenciales de HubSpot.
- **NFR-2** El aislamiento se garantiza en la capa de datos, no solo en la interfaz. Debe verificarse con pruebas automáticas que intenten acceder a datos de otra empresa.
- **NFR-3** El aislamiento existe desde la Fase 1, aunque solo haya una empresa.

### Plataformas e idioma

- **NFR-4** En la Fase 1, Nuvo es una aplicación web que funciona en navegadores de escritorio y de celular. En la Fase 2 se agregan las apps de iOS y Android, con las mismas funciones que la web.
- **NFR-5** La interfaz es bilingüe (español e inglés) y cada usuario elige su idioma. Los agentes trabajan en el idioma que indique la ficha o el que use el usuario en la conversación.

### Disponibilidad y continuidad

- **NFR-6** La app está disponible 24/7, con una meta de disponibilidad de [ASSUMPTION] 99.5 % mensual.
- **NFR-7** Las corridas programadas se ejecutan a su hora aunque ningún usuario tenga la app abierta. Si una corrida no pudo ejecutarse, se registra y se avisa como fallo (FR-55).
- **NFR-8** Una corrida en curso no se pierde si el usuario cierra la app, pierde conexión o cambia de dispositivo.

### Seguridad

- **NFR-9** Las credenciales de IA, de HubSpot y de las cuentas de correo nunca se guardan en la app móvil ni en el navegador; solo existen cifradas en el servidor y nunca se le entregan a un agente.
- **NFR-10** Los permisos por rol (Admin, Ventas, Marketing) se aplican en el servidor; ocultar un botón no basta.
- **NFR-11** Los datos se cifran en tránsito y en reposo.
- **NFR-19** Los agentes solo tienen herramientas de lectura. No pueden escribir en HubSpot, enviar correos ni modificar datos: todo lo que proponen queda como resultado por revisar. Cuando un agente trabaja para una persona, sus lecturas de HubSpot se limitan a lo que esa persona puede ver según su rol.
- **NFR-20** El contenido externo que lee un agente (páginas web, PDF subidos, respuestas de correo, campos de HubSpot, archivos `.md` importados) se trata como datos, nunca como instrucciones. Los agentes no pueden enviar datos de la empresa a sitios externos: sus herramientas web solo buscan y leen información pública. Cada resultado indica de qué fuentes salió.
- **NFR-21** Los Admin deben usar verificación en dos pasos (MFA). Las acciones sensibles (borrar la empresa, exportar datos, dar rol de Admin, conectar o desconectar HubSpot) piden volver a confirmar la identidad.
- **NFR-22** Dos empresas no pueden conectar la misma cuenta de HubSpot.

### Privacidad (CCPA y LFPDPPP)

- **NFR-12** Cada dato de una persona que guarda Nuvo o envía a HubSpot conserva su origen (URL o fuente pública y fecha).
- **NFR-13** Los agentes solo recopilan datos de contacto de negocio publicados por la propia empresa o por fuentes públicas de negocio. Nunca recopilan correos personales, domicilios particulares ni datos de sitios que venden datos personales.
- **NFR-14** El Admin de cada empresa puede, desde la app, buscar todo lo que Nuvo guarda sobre una persona, exportarlo y borrarlo para atender solicitudes de acceso o borrado. Los datos de una empresa nunca se comparten ni se reutilizan con otra. Al borrar los datos de una persona, se conserva solo lo mínimo para mantenerla en la lista de exclusión, de modo que no vuelva a ser prospectada ni contactada.

### Auditoría

- **NFR-15** Nuvo registra quién hizo qué y cuándo en las acciones importantes:
  - alta, cambio de rol, desactivación y eliminación de usuarios;
  - cambios en la ficha;
  - creación, edición, compartición y eliminación de agentes;
  - ejecuciones;
  - envíos a HubSpot;
  - conexión o desconexión de HubSpot;
  - solicitudes de privacidad;
  - borrado de la empresa.
- **NFR-16** El Admin consulta y filtra el registro de auditoría desde la app. Nadie puede editar ni borrar sus entradas, salvo al borrarse la empresa completa.

### Costos de IA

- **NFR-17** Cada corrida y conversación registra su costo de IA y la empresa a la que pertenece, para medir el costo por empresa durante el piloto.
- **NFR-18** [ASSUMPTION] Cada corrida tiene un tope de costo; si lo alcanza, se detiene, guarda lo que lleva y lo reporta como resultado parcial.
- **NFR-23** [ASSUMPTION] Cada empresa tiene un presupuesto mensual de IA que fija la plataforma. Al acercarse al límite, la app avisa al Admin; al alcanzarlo, no se inician nuevas corridas ni conversaciones hasta el siguiente periodo o hasta que se amplíe.

## 7. Glosario

| Término | Significado en este PRD |
|---|---|
| Empresa | Cada negocio cliente de Nuvo; tiene su espacio aislado. |
| Prospecto / lead | Empresa o contacto que todavía no es oportunidad. En HubSpot se maneja con *Lifecycle Stage* y *Lead Status*. |
| Deal | Oportunidad de venta en el pipeline de HubSpot; nace cuando hay una reunión agendada. |
| MQL / SQL | Lead calificado por Marketing / lead aceptado por Ventas. |
| Agente de tarea | Agente que corre al tocar "Ejecutar" o con un horario y produce resultados. |
| Agente conversacional | Agente al que el usuario le escribe pedidos en una conversación. |
| Corrida | Una ejecución de un agente de tarea. |
| Resultado por revisar | Lo que un agente propone para HubSpot o para enviar por correo; espera a que una persona lo revise. |
| Entregable | Texto producido por un agente que no va a HubSpot (propuesta, secuencia, contenido). |
| Ficha del negocio | Perfil único de la empresa que todos sus agentes usan como contexto. |
| Lista de exclusión | Personas o correos a los que la empresa no puede volver a contactar ni prospectar. |

## 8. Pendientes y supuestos

Solo uno bloquea parte de la construcción: la especificación de las 16 plantillas (FR-91), que debe validarse antes de construir esos agentes; no bloquea la arquitectura. Responsable de todos: Nacho.

| Pendiente | Valor por defecto | Se revisa |
|---|---|---|
| Criterio para cerrar el piloto | 1 mes de uso y al menos 1 cliente cerrado gracias a un agente | Al cerrar el piloto |
| Criterio para abrir a clientes | Piloto exitoso y pruebas de aislamiento en verde | Al cerrar el piloto |
| Medición de horas ahorradas | Tiempo manual equivalente por resultado útil | Durante el piloto |
| Criterio de entrega de Marketing a Ventas (FR-30) | Lo decide una persona manualmente | Durante el piloto |
| Periodo de gracia del borrado de empresa (FR-17) | 30 días | Durante la construcción |
| Vínculo usuario de Nuvo con owner de HubSpot (FR-29) | Por correo; el Admin lo corrige | Durante la construcción |
| Resultados sin revisar (FR-70) | Recordatorio a los 3 días, archivado a los 30 | Durante el piloto |
| Límite diario de correos (FR-84) | 50 por cuenta | Durante el piloto |
| Disponibilidad (NFR-6) | 99.5 % mensual | Antes de la Fase 2 |
| Tope por corrida y presupuesto mensual (NFR-18, NFR-23) | Montos por definir | Al cerrar el piloto |
| Metas numéricas de éxito y modelo de cobro | — | Al cerrar el piloto |
| **Bloqueante parcial:** especificación de las 16 plantillas (FR-91) | Borrador en el addendum | Antes de construir esos agentes |
| Gmail y Microsoft 365 por OAuth | Fase 2 (riesgo: verificación de Google) | Antes de la Fase 2 |

