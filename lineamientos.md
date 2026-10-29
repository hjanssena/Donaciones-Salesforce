# Proyecto final SF Vibe Lab: portal de donaciones para Mexico Dreamin

**Fecha:** 25 de octubre de 2026 · **Autor:** @Someone  
**Programa:** SF Vibe Lab (UADY × Salesforce, Mérida)  
**Presentación de guía y equipos:** Martes 27 de octubre de 2026  
**Entrega 1:** Martes 10 de noviembre de 2026 · **Entrega 2:** Martes 17 de noviembre de 2026  
**Equipos:** 3 personas (asignados por el profesor). Roles a definir por el equipo.

> **Antes de empezar:** Todo el proyecto es una práctica. El dinero es simulado, la pasarela corre en modo de prueba, los datos son ficticios y nada llega a producción. Nadie recibe ni paga dinero real.

---

## 1. Contexto y Escenario

### Planteamiento del Cliente (Coordinación de SF Vibe Lab)
> "Queremos que más alumnos del programa vayan a Mexico Dreamin. El evento vale la pena por lo técnico y, sobre todo, por la red de contactos. El problema es el dinero: el boleto, el viaje y la estancia en la Ciudad de México no los puede pagar cualquiera.
>
> Hay gente dispuesta a ayudar: exalumnos, familias, profesores, empresas de la región. Lo que no tenemos es una forma sencilla de recibir esas donaciones en línea y llevar el control en Salesforce.
>
> Necesitamos una página pública, sin login, donde cualquier persona vea nuestras campañas, cuánto llevamos de cada meta y pueda donar con tarjeta. Los donantes no son todos iguales. Algunos no quieren que se sepa que donaron. Otros sí quieren aparecer como donantes. Las empresas casi siempre piden factura.
>
> Del lado interno, la coordinación necesita ver todas las donaciones, saber cuáles ya se pagaron y cuáles no, y que cada donante reciba su comprobante. Hoy confirmaríamos los pagos a mano, pero lo ideal es que, cuando la pasarela confirme un pago, Salesforce se entere solo.
>
> Algo nos preocupa mucho: la página es pública. Nadie de fuera debe poder ver los datos de otros donantes, y las claves de la pasarela no pueden quedar expuestas en ningún lado."

### Matriz: Real vs. Simulado

| Real | Simulado |
| :--- | :--- |
| **Mexico Dreamin 2026:** Viernes 20 de noviembre de 2026, Salesforce México, CDMX (fuente: *mexicodreamin.com*, consultada 2026-10-25). | El fondo de donaciones (no está ligado a la convocatoria de patrocinio oficial). |
| **Herramientas:** Salesforce Developer Edition y Stripe. | Los pagos: Stripe en modo de prueba con tarjetas de prueba. |
| El problema de negocio y los perfiles de donante. | Los donantes, sus datos y las facturas (no se emite CFDI). |

*Nota:* El precio del boleto del evento lo investigan como parte del *discovery*. Las metas de las campañas las define cada equipo con montos ficticios.

### Rol del Equipo
- **Consultoría e implementación:** El profesor actúa como cliente (dudas de negocio) y asesor (orientación técnica).
- La guía no impone la arquitectura técnica: la decisión y diseño son evaluados.
- Se contemplan componentes no vistos previamente en clase (sitios públicos, pasarelas de pago, webhooks/avisos externos).
- Se evalúa avance, comunicación formal de bloqueos y uso crítico documentado de IA.

---

## 2. Objetivos de Aprendizaje

| ID | Objetivo | Descripción |
| :--- | :--- | :--- |
| **OA1** | **Comunicación** | Formular preguntas concretas y reportar problemas con contexto: qué intentaron, qué esperaban, qué error vieron y qué consultaron con la IA. |
| **OA2** | **Consultoría** | Redactar user stories con acceptance criteria verificables para: donante anónimo, donante con crédito, donante con factura y staff. |
| **OA3** | **IA aplicada** | Usar IA para avanzar, registrar cada interacción (qué pidieron, obtuvieron, verificaron y corrigieron) y medir la aceleración. |
| **OA4** | **Técnico funcional** | Implementar en Salesforce las funciones del backlog cumpliendo acceptance criteria, priorizando funcionalidad sobre perfección técnica. |
| **OA5** | **Seguridad** | Demostrar mediante pruebas que un visitante no autenticado no lee datos ajenos y que ningún secreto/clave queda expuesto. |
| **OA6** | **Datos personales** | Clasificar los datos personales recopilados y justificar cuáles se almacenan y cuáles no. |
| **OA7** | **Trabajo individual/equipo** | Explicar y defender oralmente la solución y las decisiones de diseño arquitectónico tomadas. |

---

## 3. Alcance

### Incluye
- Sitio público sin login con listado de campañas, avance hacia la meta y formulario de donación.
- Tres modalidades de donación:
  - Anónima.
  - Con crédito (nombre visible).
  - Con factura (nombre y RFC).
- Registro previo de la donación en Salesforce (estado pendiente), cobro en Stripe (modo test) y redirección a página de agradecimiento.
- Consola interna para staff: campañas, donaciones, confirmación manual de pagos y envío de comprobante por correo.
- Confirmación automática de pagos vía avisos de la pasarela (para Entrega 2).
- Documentación de pruebas de seguridad (usuario visitante) y clasificación de datos.

### No incluye
- Cobros reales, activación de pasarelas ni dinero real.
- Emisión de comprobantes fiscales reales (CFDI) ni validación ante el SAT.
- Términos de uso, aviso de privacidad legal ni marcos de cumplimiento formal.
- Autenticación o asignación directa de fondos a alumnos específicos.
- Conciliación bancaria contra extractos de Stripe.
- Dashboards o métricas por estudiante.
- Datos de personas reales.
- Protección anti-spam (se reporta como riesgo conocido en seguridad, pero no se califica).

### Segundo Nivel (Opcional dentro de Entrega 2)
- **RF-V3b:** Validación de autenticidad criptográfica/firma de la pasarela y deduplicación de avisos (idempotencia) sin duplicar donaciones ni comprobantes.

---

## 4. Requisitos Funcionales

### Reglas de Refinamiento
1. Traspasar las historias al backlog y refinarlas (dividir historias complejas, agregar casos negativos, validar ambigüedades con el cliente).
2. Asegurar el flujo para las tres modalidades de donante sobre la misma base (RF-D1 a RF-D5).
3. Respetar y fortalecer los criterios mínimos de aceptación (formato Dado / Cuando / Entonces).

---

### Módulo: Donante (Cualquier Modalidad, Visitante sin Login)

#### RF-D1a · Elegir campaña y ver su avance · [Entrega 1]
- **Historia:** Como donante, quiero elegir una campaña y ver cuánto lleva de su meta, para decidir a cuál donar.
- **CA1:** Dado que hay al menos dos campañas activas con meta, cuando entro al sitio sin login, entonces veo cada campaña con su meta y su avance.
- **CA2:** Dado que una donación de una campaña pasa a pagada, cuando vuelvo a cargar el sitio, entonces el avance de esa campaña incluye ese monto. Las donaciones pendientes no cuentan para el avance.
- **CA3:** Dado que elegí una campaña, cuando continúo a donar, entonces puedo indicar el monto, ya sea eligiendo de una lista de montos sugeridos o capturándolo en la pasarela.

#### RF-D1b · Donar cualquier monto · [Entrega 2]
- **Historia:** Como donante, quiero donar el monto que yo decida, para aportar lo que puedo y no solo los montos sugeridos.
- **CA1:** Dado que elegí una campaña, cuando capturo un monto mayor a cero en el sitio, entonces la pasarela de prueba me cobra exactamente ese monto y la donación en Salesforce registra ese mismo monto.

#### RF-D2 · Donar de forma anónima · [Entrega 1]
- **Historia:** Como donante anónimo, quiero que mi nombre no aparezca en ningún lugar público, para apoyar sin exponerme.
- **CA1:** Dado que marqué mi donación como anónima, cuando reviso el sitio público (campañas, reconocimiento de donantes y página de agradecimiento), entonces mi nombre no aparece.
- **CA2:** Dado que mi donación anónima está pagada, cuando recibo el comprobante, entonces el comprobante no muestra mi nombre.
- **CA3:** Dado que marqué mi donación como anónima, cuando intento solicitar factura, entonces no puedo hacerlo (la modalidad anónima no lleva factura).
- **CA4:** Dado que existe una donación anónima, cuando staff la consulta, entonces la ve identificada como anónima.

#### RF-D3 · Registro antes de pagar · [Entrega 1]
- **Historia:** Como donante, quiero que mi donación quede registrada antes de pagar, para que el programa pueda reconocer mi pago cuando llegue.
- **CA1:** Dado que llené mis datos y elegí campaña, cuando envío el formulario, entonces existe en Salesforce una donación en estado pendiente con su campaña, su modalidad y su monto (si ya se conoce) antes de que se abra la pasarela.
- **CA2:** Dado que envié el formulario, cuando se abre la pasarela de prueba, entonces el pago lleva una referencia que permite a staff encontrar la donación correspondiente en Salesforce a partir del pago de prueba.

#### RF-D4 · Página de agradecimiento · [Entrega 1]
- **Historia:** Como donante, quiero ver una página de agradecimiento al terminar de pagar, para saber que mi donación se recibió.
- **CA1:** Dado que completé el pago con una tarjeta de prueba, cuando la pasarela me regresa al sitio, entonces veo una página de agradecimiento.
- **CA2:** Dado que el pago aún no está confirmado en Salesforce, cuando veo la página de agradecimiento, entonces la página no dice que mi pago está confirmado.
- **CA3:** Dado que estoy en la página de agradecimiento, cuando la reviso, entonces no muestra datos de otros donantes.

#### RF-D5 · Confirmación y comprobante automáticos · [Entrega 2]
- **Historia:** Como donante, quiero que mi donación se confirme y recibir mi comprobante sin que nadie tenga que revisarla a mano, para tener la constancia en cuanto pago.
- **CA1:** Dado que pagué con una tarjeta de prueba, cuando la pasarela confirma el pago, entonces la donación pasa a pagada sin que nadie de staff la modifique.
- **CA2:** Dado que la donación pasó a pagada de esa forma, cuando reviso el correo que capturé, entonces recibo un comprobante que me permite identificar mi donación. (El contenido del comprobante lo define el equipo en la historia).

---

### Módulo: Donante con Crédito

#### RF-C1 · Aparecer como donante · [Entrega 1]
- **Historia:** Como donante con crédito, quiero que mi nombre aparezca como donante de la campaña, para que se reconozca mi apoyo.
- **CA1:** Dado que pedí crédito y mi donación está pagada, cuando cualquier visitante abre la campaña, entonces ve mi nombre y el monto de mi donación en el reconocimiento de donantes de esa campaña. El reconocimiento es una lista por campaña que solo incluye donaciones pagadas.
- **CA2:** Dado que pedí crédito, cuando reviso el reconocimiento público, entonces solo aparecen mi nombre y el monto: no aparece mi correo ni ningún otro dato.

---

### Módulo: Donante con Factura

#### RF-F1 · Capturar datos para factura · [Entrega 1]
- **Historia:** Como donante con factura, quiero capturar mi nombre y mi RFC, para que el programa pueda emitir mi factura.
- **CA1:** Dado que elegí solicitar factura, cuando intento enviar el formulario sin nombre o sin RFC, entonces no puedo enviarlo y veo qué falta.
- **CA2:** Dado que capturé un RFC, cuando lo envío, entonces el sistema lo revisa contra el patrón completo del SAT (letras iniciales, fecha y homoclave; 12 caracteres para persona moral y 13 para persona física) y rechaza el que no lo cumpla. No lo consulta ante el SAT.
- **CA3:** Dado que el donante con factura completó su donación, cuando se revisa el sistema, entonces no se emite ningún comprobante fiscal (CFDI).

#### RF-F2 · Marcar la solicitud de factura · [Entrega 1]
- **Historia:** Como staff, quiero que las donaciones con factura queden marcadas, para atenderlas.
- **CA1:** Dado que existe una donación con factura, cuando staff la consulta, entonces la ve marcada como "solicita factura", con el nombre y el RFC capturados.
- **CA2:** Dado que hay donaciones de varias modalidades, cuando staff busca las que solicitan factura, entonces puede ver solo esas.

---

### Módulo: Staff (Coordinación / Tesorería Ficticia)

#### RF-S1 · Administrar campañas · [Entrega 1]
- **Historia:** Como coordinación del programa, quiero crear y administrar campañas con su meta, para organizar la recaudación.
- **CA1:** Dado que soy staff, cuando creo una campaña con nombre y meta, entonces aparece en el sitio público.
- **CA2:** Dado que existe una campaña, cuando staff cambia su meta, entonces el sitio público muestra la meta nueva.

#### RF-S2 · Ver todas las donaciones · [Entrega 1]
- **Historia:** Como tesorería, quiero ver todas las donaciones con su información clave, para llevar el control.
- **CA1:** Dado que existen donaciones de las tres modalidades y en distintos estados, cuando staff consulta las donaciones, entonces ve de cada una su estado, monto, modalidad y campaña.

#### RF-S3 · Confirmar un pago a mano · [Entrega 1]
- **Historia:** Como tesorería, quiero confirmar manualmente el pago de una donación pendiente, para que el donante reciba su comprobante aunque la confirmación automática no exista o falle.
- **CA1:** Dado que una donación está pendiente, cuando staff confirma su pago, entonces la donación pasa a pagada.
- **CA2:** Dado que staff confirmó el pago, cuando el donante revisa el correo que capturó, entonces recibe su comprobante.

#### RF-S4 · Ver confirmaciones automáticas y avisos rechazados · [Entrega 2]
- **Historia:** Como tesorería, quiero ver qué donaciones confirmó la pasarela y qué avisos se rechazaron, para confiar en los datos sin revisarlos uno por uno.
- **CA1:** Dado que una donación se confirmó por aviso de la pasarela, cuando staff la consulta, entonces puede distinguir que se confirmó de forma automática y no a mano.
- **CA2:** Dado que se rechazó un aviso, cuando staff revisa los avisos rechazados, entonces ve cuándo llegó y por qué se rechazó.

---

### Módulo: Visitante y Seguridad

#### RF-V1 · Sitio público acotado · [Entrega 1]
- **Historia:** Como visitante, quiero entrar al sitio sin crear cuenta, para donar sin fricción.
- **CA1:** Dado que no tengo sesión iniciada, cuando abro la dirección del sitio, entonces veo las campañas sin que se me pida login.
- **CA2:** Dado que navego el sitio como visitante, cuando recorro todas sus páginas, entonces solo encuentro campañas, avances, reconocimiento de donantes con crédito (nombre y monto) y el formulario de donación.

#### RF-V2 · Prueba de seguridad del visitante · [Entrega 1]
- **Historia:** Como coordinación del programa, quiero evidencia de que un visitante sin login no puede leer ni modificar donaciones de otros, para proteger a los donantes.
- **CA1:** Dado que existen donaciones de otras personas, cuando un visitante sin login intenta leerlas por el sitio y por cualquier otra vía que el equipo haya identificado, entonces no obtiene ningún dato de esas donaciones.
- **CA2:** Dado que existen donaciones de otras personas, cuando un visitante sin login intenta modificarlas, entonces el intento falla.
- **CA3:** Dado el documento de seguridad, cuando otra persona sigue sus pasos, entonces puede repetir la prueba y obtiene el mismo resultado.

#### RF-V3a · Recibir el aviso de pago (nivel mínimo) · [Entrega 2]
- **Historia:** Como tesorería, quiero que el aviso de pago de la pasarela llegue a Salesforce, para que las donaciones se actualicen solas.
- **CA1:** Dado que un donante pagó con tarjeta de prueba, cuando la pasarela envía su aviso de pago, entonces el aviso llega a Salesforce y la donación correspondiente pasa a pagada.

#### RF-V3b · Solo avisos auténticos y sin duplicados (segundo nivel) · [Entrega 2]
- **Historia:** Como tesorería, quiero que solo se acepten avisos auténticos y que un aviso repetido no duplique nada, para que nadie pueda marcar donaciones como pagadas sin pagar.
- **CA1:** Dado que alguien que no es la pasarela envía un aviso falso, cuando llega a Salesforce, entonces se rechaza, la donación no cambia y el rechazo queda visible para staff (RF-S4).
- **CA2:** Dado que la pasarela envía dos veces el mismo aviso, cuando ambos llegan, entonces hay una sola donación pagada y un solo comprobante enviado.

---

### Preguntas Abiertas Sugeridas para Discovery
- ¿Un donante con factura puede solicitar no aparecer en el reconocimiento público?
- ¿Qué ciclo de vida aplica para una donación pendiente que nunca se concreta?

---

## 5. Requisitos No Funcionales

### Mínimos Obligatorios (Pasa / No Pasa)
> *Penalización:* Cada mínimo no satisfecho en la Entrega 2 resta **10 puntos netos** de la calificación de equipo. En Entrega 1 se emite retroalimentación sin penalización.

| ID | Mínimo | Criterio de Verificación |
| :--- | :--- | :--- |
| **M1** | **Modo de prueba y datos ficticios** | Operación restringida a Stripe Test Mode, tarjetas de prueba y datos dummy. Cuentas reales estrictamente prohibidas. | Verificación en demo y auditoría de configuración. |
| **M2** | **Aislamiento del visitante sin login** | Configuración de privilegios mínimos (*least privilege*). El usuario público no puede leer ni escribir datos ajenos. | Ejecución reproducible de RF-V2 por el evaluador. |
| **M3** | **Cero secretos expuestos** | Ninguna API key (Stripe Secret Key/Webhook Secret) presente en código, metadata, repositorios públicos, historial de Git, capturas o bitácoras. | Inspección de commits, ramas y bundles del sitio. |

*Protocolo de fuga de credenciales:* En caso de commit con API keys, se debe rotar la clave de Stripe el mismo día, eliminarla y reportarlo inmediatamente al profesor para mitigar el impacto en evaluación de comunicación.

### Seguridad (S15)
- **Least privilege:** Permisos exclusivos para visualizar campañas y crear la donación correspondiente.
- **Integraciones:** Cuentas de integración con permisos restringidos exclusivamente a la recepción/actualización del webhook.
- **Privacidad y datos (OA6):** Clasificación sistemática de campos (nombre, correo, RFC, tokens de pago). Uso exclusivo de correos controlados por el equipo para pruebas.

### Buenas Prácticas de Desarrollo (Criterio C5)
- **Manejo de excepciones (S11):** Presentar pantallas descriptivas al usuario y evitar registros huérfanos o inconsistentes.
- **Governor limits y bulkificación (S12):** Apex y Flows libres de operaciones SOQL/DML dentro de bucles; soporte de operaciones por lotes.
- **Calidad de código (S14):** Ejecución limpia con Salesforce Code Analyzer; pruebas unitarias que aprueben sin depender de IDs cableados (*hard-coded*).
- **Ajustes de pasarela:** Moneda preferente en `MXN` (o `USD` si existe limitación en pruebas, dejando nota técnica).

---

## 6. Restricciones Técnicas

- **Entorno:** Salesforce Developer Edition (1 por equipo, sin add-ons de pago).
- **Pasarela:** Stripe en modo de prueba. Prohibido activar cuentas reales o vincular cuentas bancarias.
- **Presupuesto:** Costo cero. Cualquier paquete de AppExchange o servicio tercero requiere aprobación previa del profesor.
- **Stack permitido:** VS Code, Salesforce CLI, Developer Console, Flow Builder, Code Analyzer, GitHub y herramientas de IA gratuitas.
- **Prevención de expiración:** Ingreso semanal mínimo de un integrante a la org y respaldo regular de metadata con Salesforce CLI (`sf project retrieve start`).

### Matriz de Contingencia ante Fallas de Plataforma

| Situación Detectada | Acción del Asesor / Profesor |
| :--- | :--- |
| La Developer Edition no permite habilitar sitios públicos. | Provisión de alternativa técnica o ajuste en RF-V1, RF-V2 y M2. |
| El usuario invitado no puede crear el registro preliminar. | Modificación de RF-D3 o autorización de mecanismo alterno equivalente. |
| Bloqueo en recepción de webhooks en la org. | Plan de contingencia para RF-D5 y RF-V3a preservando la actualización autónoma. |
| Fallo en el relevo de correos salientes. | Admisión de logs/evidencias alternativas del comprobante generado. |

---

## 7. Lineamientos para el Uso de IA

1. **Validación obligatoria:** Prohibido incorporar código o configuraciones no comprendidas.
2. **Confidencialidad:** Prohibido ingresar tokens, secretos, credenciales o datos sensibles en prompts.
3. **Registro en tiempo real:** Documentación continua en `docs/bitacora_ia.md`.

### Estructura de Evidencias

| Documento | Ubicación | Requisitos Mínimos |
| :--- | :--- | :--- |
| **Bitácora de IA** | `docs/bitacora_ia.md` | Fecha, autor, herramienta, RF asociado, prompt/resumen, resultado, método de verificación, correcciones y cálculo de aceleración. |
| **Archivo de Contexto** | Raíz o `docs/` | `CLAUDE.md`, System Prompts o contexto maestro utilizado con los LLMs. |
| **Registro de Errores** | En bitácora | Casos destacados donde la IA alucinó o falló técnicamente y cómo se resolvió. |

---

## 8. Entregables e Hitos

> **Canal:** Slack oficial del programa.  
> **Límite horario:** 23:59 (hora de Mérida).  
> **Formato:** URL del repositorio público en GitHub + Tag del commit.

### Calendario de Entregas

| ID | Entregable | Formato | Fecha | Criterio de Aceptación |
| :--- | :--- | :--- | :--- | :--- |
| **E0** | Guía y equipos | Clase presencial | Mar 27 oct | Equipos conformados y repositorio creado y compartido antes del 28 oct. |
| **E1** | Entrega 1 | Repo (Tag: `entrega-1`) | Mar 10 nov | URL del sitio, video demo (5 min), backlog enlazado con % de avance, `docs/seguridad.md`, metadata respaldada y bitácoras al día. |
| **E2** | Entrega 2 | Repo (Tag: `entrega-2`) | Mar 17 nov | Actualización completa de E1 + demo de confirmación automática (RF-V3a) y validación de seguridad de webhooks si aplica (RF-V3b). |
| **E3** | Bitácora de IA | `docs/bitacora_ia.md` | Con E1 y E2 | Registros fechados con campos completos de todos los integrantes. |
| **E4** | Bitácora de contribución | `docs/contribucion.md` | Con E1 y E2 | Tablas individuales con tareas, evidencias (commits/capturas) y horas dedicadas. |
| **E5** | Defensa oral | Presencial | Mié 18 nov | Demo en vivo (5 min) + sesión de preguntas técnicas individuales (10 min). |
| **E6** | Preguntas y reportes | `docs/preguntas_y_reportes.md` | Continuo | Reportes técnicos estructurados en cinco partes. |

### Esquema del Backlog
Cada entrada del tablero debe incluir:
- `ID`
- `RF Asociado`
- `Hito (E1/E2)`
- `Historia de Usuario (Who / What / Why)`
- `Acceptance Criteria (Dado / Cuando / Entonces)`
- `Estado (Cumplida / No cumplida)`
- `Evidencia (Enlace a test/captura/video)`

### Estructura de `docs/seguridad.md`
1. Procedimiento de prueba reproducible del Guest User (lectura y modificación).
2. Justificación de privilegios asignados al visitante.
3. Validación de no exposición de secretos.
4. Matriz de clasificación de datos personales.
5. Análisis de riesgos residuales (incluyendo spam).
6. (E2) Protocolo de seguridad y validación de webhooks.

### Cronograma de Sesiones de Acompañamiento

| Fecha | Sesión | Enfoque Sugerido |
| :--- | :--- | :--- |
| Mié 28 oct | S18: Consulting Simulation | Levantamiento con cliente y estructuración del backlog. |
| Jue 29 oct | S19: Solution Design | Definición de arquitectura y diseño del modelo de datos. |
| Mié 04 nov | S20: Flow vs Apex / Standard vs Custom | Balance entre herramientas declarativas y programáticas. |
| Jue 05 nov | S21: Architecture Thinking | Mitigación de riesgos previo a Entrega 1. |
| **Mar 10 nov** | — | **Hito: Entrega 1** |
| Mié 11 nov | S22: APIs + Integration Basics | Arquitectura de recepción de avisos externos. |
| Jue 12 nov | S23: Project Architecture Review | Consultas abiertas y preparación de demos. |
| **Mar 17 nov** | — | **Hito: Entrega 2** |
| **Mié 18 nov** | S24: Evaluaciones | **Defensa Oral Presencial** |

---

## 9. Rúbrica de Evaluación

### Ponderaciones (Base: 100 Puntos)
- **Evaluación de Equipo (65%):** C1 a C6.
- **Evaluación Individual (35%):** C7 a C9.

| ID | Criterio | Ámbito | Peso |
| :--- | :--- | :--- | :--- |
| **C1** | Avance medido en Entrega 1 | Equipo | 10 pts |
| **C2** | Avance medido en Entrega 2 | Equipo | 15 pts |
| **C3** | Uso de IA documentado | Equipo | 20 pts |
| **C4** | User stories y acceptance criteria | Equipo | 10 pts |
| **C5** | Seguridad y datos personales documentados | Equipo | 5 pts |
| **C6** | Calidad técnica básica | Equipo | 5 pts |
| **C7** | Preguntas y reportes de problemas | Individual | 15 pts |
| **C8** | Defensa oral individual | Individual | 15 pts |
| **C9** | Contribución individual demostrada | Individual | 5 pts |

*Escala de cumplimiento:* Excelente (100%), Bueno (85%), Suficiente (70%), Insuficiente (0-50%).

### Descriptores de Desempeño

| Criterio | Excelente | Bueno | Suficiente | Insuficiente |
| :--- | :--- | :--- | :--- | :--- |
| **C1. Avance E1 (10)** | $\ge$ 70% de historias E1 cumplidas; muestra verificada pasa al 100%. | 50% a 69% cumplidas; muestra verificada pasa. | 30% a 49% cumplidas, o 1 fallo en muestra. | < 30%, o sin evidencia verificable. |
| **C2. Avance E2 (15)** | $\ge$ 60% del backlog global cumplido (incluye RF-V3a). | 40% a 59% cumplido. | 20% a 39% cumplido. | < 20% cumplido. |
| **C3. Uso IA (20)** | Registros completos, $\ge$ 3 errores de IA explicados y corregidos, contexto documentado, participación equitativa y estimación de aceleración. | Mayoría de campos completos, $\ge$ 1 error corregido, evidencia de los 3 integrantes. | Bitácora genérica, sin verificación clara o con autoría concentrada en 1 integrante. | Ausencia de bitácora o llenado de último momento sin dominio técnico. |
| **C4. User Stories (10)** | Cobertura total de RFs, sin "how", criterios propios y negativos incluidos, preguntas de negocio registradas. | Cobertura de RFs, máximo 2 menciones de "how", criterios verificables. | Historias ambiguas, criterios vagos o ausencia de RFs clave. | Copia textual no refinada o criterios inverificables. |
| **C5. Seguridad (5)** | Pruebas reproducibles en lectura/escritura, permisos justificados, matriz completa de datos personales y actualización para webhooks en E2. | Pruebas funcionales reproducibles, permisos listados, matriz con justificación breve. | Pruebas limitadas a la UI web o matriz incompleta. | Ausencia de reporte o fallas graves de reproducibilidad. |
| **C6. Calidad Técnica (5)** | Manejo de excepciones claro, sin queries/DMLs en bucles, sin IDs fijos, tests limpios, respaldos semanales completos. | Mayoría de estándares cubiertos, hallazgos de Code Analyzer parcialmente atendidos. | Errores no controlados visibles al donante o respaldos omitidos. | Sin respaldos en Git o registros huérfanos tras excepciones. |
| **C7. Reportes (15)** | $\ge$ 4 entradas propias con las 5 partes en $\ge$ 2 semanas, incluyendo seguimiento a la respuesta y dudas en clase. | $\ge$ 3 entradas, al menos 2 completas con seguimiento. | 1 o 2 entradas, o reportes sin traza técnica del error. | Sin registros o quejas sin fundamentación técnica. |
| **C8. Defensa Oral (15)** | Domina su sección, demuestra el funcionamiento, justifica decisiones técnicas y alternativas descartadas. | Explica su sección y justifica la solución; contesta la mayoría de preguntas. | Describe lo que hace pero no el fundamento arquitectónico. | No logra explicar su entregable o no asiste. |
| **C9. Contribución (5)** | Entradas verificables en ambas entregas alineadas con Git y bitácoras del equipo. | Entradas en ambas entregas con trazabilidad parcial. | Aportación reportada en una sola fase. | Sin evidencia de aportación real. |

---

## 10. Matriz de Trazabilidad

| Objetivo | Requisitos Base | Entregables Evaluados | Criterios Aplicables |
| :--- | :--- | :--- | :--- |
| **OA1 Comunicación** | Sección 12; Dudas de negocio | E6 (Reportes), E5 (Defensa) | C7, C8 |
| **OA2 Consultoría** | Sección 4 (Historias de usuario) | Backlog en E1 y E2 | C4 |
| **OA3 IA aplicada** | Sección 7 (Lineamientos) | E3 (Bitácora), Archivo de contexto | C3, C8 |
| **OA4 Técnico funcional** | Todos los RFs funcionales | E1, E2 (Sitio, Video, Repo, Backlog) | C1, C2, C6 |
| **OA5 Seguridad** | RF-V1, RF-V2, RF-V3b, M2, M3 | `docs/seguridad.md`, Repo | C5, M2, M3 |
| **OA6 Datos personales** | RF-D2, RF-C1, RF-F1 | `docs/seguridad.md` | C5 |
| **OA7 Desempeño individual** | Sección 8 | E4 (Contribución), E5 (Defensa) | C8, C9 |

---

## 11. Material de Apoyo Técnico

### Referencia de Sesiones Grabadas
- **Discovery y entrevistas de negocio:** Sesión S05
- **Redacción de User Stories y Criterios:** Sesiones S16, S17
- **Modelado de datos y permisos:** Sesiones S03, S04
- **Flows, validaciones y caminos de error:** Sesiones S06, S07, S11, S12
- **Apex, pruebas unitarias y Code Analyzer:** Sesiones S13, S14
- **Seguridad de plataformas y datos:** Sesión S15
- **Ingeniería de prompts y contexto de IA:** Sesiones S08, S09, S10
- **Integraciones y arquitectura de APIs:** Sesión S22 (11 nov)

### Temas de Autoestudio Recomendados
- **Seguridad de Guest Users en Experience Cloud:** Consultar guía oficial de Salesforce Developers sobre visibilidad y permisos del usuario invitado.
- **Entorno de Pruebas de Stripe:** `docs.stripe.com/testing-use-cases` y `docs.stripe.com/sandboxes`
- **Tarjetas de Simulación:** `docs.stripe.com/testing` (ej. `4242 4242 4242 4242`)
- **Gestión de Webhooks e Idempotencia:** `docs.stripe.com/webhooks`

---

## 12. Protocolo para Solicitud de Apoyo

### Estructura de las 5 Partes de un Reporte
1. **Objetivo:** Historia y criterio específico bajo prueba.
2. **Acciones ejecutadas:** Pasos técnicos detallados.
3. **Comportamiento esperado:** Resultado técnico previsto.
4. **Comportamiento obtenido:** Log de error o captura textual (sin secretos ni PII).
5. **Investigación previa:** Prompt utilizado, respuesta del LLM/documentación y motivo por el cual falló.

*Nota:* Al recibir feedback, actualizar el reporte indicando la resolución final.

### Canales de Soporte
- **Sesiones Presenciales (S18 a S23):** Espacio para dudas y validación de diseño. Cada duda debe registrarse en `docs/preguntas_y_reportes.md`.
- **Slack del Programa:** Dudas asíncronas con tiempo de respuesta de hasta 24 horas hábiles.
- **Canalización:** Asuntos funcionales/negocio dirigidos al profesor como cliente; arquitectura y bloqueos técnicos, como asesor.
