# Especificación de Casos de Uso - Bloque 7: Operativa diaria, informes, configuración y API

---

## Caso de Uso: Consultar tareas pendientes

- **ID / Código:** CU-701
- **Actor principal:** Usuario
- **Ámbito:** Entorno de trabajo centralizado (Vista principal)
- **Nivel:** Objetivo de Usuario
- **Descripción:** El usuario accede a su entorno de trabajo centralizado para visualizar y gestionar sus tareas diarias, como facturas próximas a vencer o incidencias.

**Interesados:**

- **Usuario:** Desea conocer rápidamente qué tareas urgentes o pendientes debe resolver en su jornada para organizarse.
- **Empresa:** Desea que la operativa fluya sin retrasos en cobros o entregas.

**Precondiciones:**

- El usuario ha iniciado sesión en el sistema.
- Existen tareas, incidencias o eventos asociados al perfil del usuario.

**Disparador:** El usuario inicia sesión en el sistema o navega manualmente a la vista principal (dashboard).

**Garantía de éxito:**

- El sistema muestra una lista actualizada de tareas pendientes categorizadas (facturas vencidas, incidencias, cobros por conciliar) y accesos directos a clientes recientes o favoritos.

**Garantía mínima:**

- Si no hay datos, el sistema muestra la vista principal vacía indicando la ausencia de tareas pendientes y mantiene el sistema estable.

**Escenario principal de éxito:**

1. El usuario accede a la vista principal del sistema.
2. El sistema recupera las tareas asignadas al usuario (facturas a vencer, incidencias de entrega, cobros pendientes).
3. El sistema recupera la lista de clientes recientes o marcados como favoritos.
4. El sistema muestra toda la información estructurada en la pantalla.

**Extensiones:**

- **2a. Error de conexión con la base de datos:** El sistema informa de que no se han podido cargar las tareas temporalmente y permite reintentar la carga.
- **Cualquier paso. El usuario navega a otra sección:** El sistema detiene la carga actual y redirige al usuario a la vista solicitada.

---

## Caso de Uso: Buscar clientes y facturas

- **ID / Código:** CU-702
- **Actor principal:** Usuario
- **Ámbito:** Sistema de facturación
- **Nivel:** Objetivo de Usuario
- **Descripción:** El usuario realiza búsquedas globales de clientes y facturas combinando diversos criterios (razón social, NIF, estado, etc.) para localizar información rápidamente. Además, puede guardar estas combinaciones de filtros para agilizar consultas recurrentes en el futuro.

**Interesados:**

- **Usuario:** Quiere localizar rápidamente información específica de clientes o facturas sin tener que navegar por listados enormes, y desea guardar sus filtros más usados para ahorrar tiempo en el día a día.
- **Turbine (Administración):** Necesita asegurar que los usuarios solo puedan buscar y visualizar la información sobre la que tienen permisos.

**Precondiciones:**

- El usuario ha iniciado sesión en el sistema y tiene permisos de lectura sobre el módulo de clientes y facturas.
- Existen datos (clientes o facturas) registrados en el sistema.

**Disparador:** El usuario accede a la barra de búsqueda global o a la sección de listados e introduce uno o varios criterios de filtro.

**Garantía de éxito:**

- El sistema muestra correctamente los resultados que coinciden con los criterios de búsqueda. Si el usuario decide guardar los filtros, estos quedan almacenados en su perfil y disponibles para futuras consultas rápidas.

**Garantía mínima:**

- El sistema protege la información confidencial, mostrando únicamente los registros a los que el usuario tiene acceso. Si ocurre un error de validación, no se guardan filtros corruptos ni se detiene el sistema.

**Escenario principal de éxito:**

1. El usuario accede a la vista de búsqueda y despliega las opciones de filtrado.
2. El usuario introduce o selecciona uno o varios criterios de búsqueda (razón social, NIF, contacto, estado de la factura o número de factura).
3. El usuario solicita ejecutar la búsqueda.
4. El sistema valida los criterios ingresados y consulta los registros aplicando los permisos del usuario.
5. El sistema muestra la lista consolidada de clientes y facturas que coinciden con los filtros aplicados.
6. El usuario consulta los resultados mostrados en pantalla.

**Extensiones:**

- **4a. Los criterios introducidos tienen un formato inválido:** El sistema resalta el campo erróneo, informa del formato correcto y detiene la búsqueda hasta que se corrija.
- **5a. No se encuentran resultados que coincidan con los criterios:** El sistema muestra un mensaje indicando que no hay coincidencias y mantiene los filtros aplicados para que el usuario pueda modificarlos.
- **5b. El usuario desea guardar los filtros aplicados:**
  1. El usuario selecciona la acción de guardar la configuración de búsqueda actual.
  2. El sistema solicita un nombre descriptivo para el nuevo filtro.
  3. El usuario introduce el nombre y confirma la acción.
  4. El sistema guarda la combinación de filtros y la añade a la lista de accesos rápidos o búsquedas guardadas del usuario.
- **Cualquier paso. El usuario cancela o limpia la búsqueda:** El sistema borra los criterios introducidos y vuelve a mostrar el listado por defecto.

---

## Caso de Uso: Ejecutar acciones masivas

- **ID / Código:** CU-703
- **Actor principal:** Administrador
- **Ámbito:** Módulo de gestión de clientes
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador aplica cambios, reasignaciones o exportaciones a un conjunto seleccionado de clientes simultáneamente, asegurando la trazabilidad de la operación y evitando modificaciones no permitidas.

**Interesados:**

- **Administrador:** Desea ahorrar tiempo modificando o exportando varios registros a la vez en lugar de hacerlo uno por uno.
- **Empresa / Auditoría:** Necesita que cualquier cambio masivo sensible quede registrado (quién, qué, cuándo) y no comprometa la integridad contable.

**Precondiciones:**

- El administrador ha iniciado sesión en el sistema y tiene permisos avanzados de gestión.
- Existe al menos un cliente registrado en el sistema.

**Disparador:** El administrador selecciona múltiples clientes en un listado y elige una opción del menú de acciones masivas.

**Garantía de éxito:**

- Los clientes seleccionados son actualizados o exportados, el sistema muestra el resumen de elementos afectados y la acción queda guardada detalladamente en la pista de auditoría interna.

**Garantía mínima:**

- Si la acción es cancelada o inválida, ningún dato es modificado y el intento fallido queda registrado si se considera una operación sensible.

**Escenario principal de éxito:**

1. El administrador selecciona varios clientes del listado.
2. El administrador selecciona una acción masiva (ej. reasignación de responsables, cambio de sector, exportación).
3. El sistema evalúa la petición, solicita confirmación y muestra un resumen de los resultados previstos (número de elementos afectados).
4. El administrador confirma la acción.
5. El sistema ejecuta la modificación o generación en los registros correspondientes.
6. El sistema registra en la pista de auditoría interna la fecha, hora, filtros aplicados y el usuario que ejecutó la acción.
7. El sistema muestra un mensaje de éxito informando de la finalización de la tarea.

**Extensiones:**

- **2a. El administrador intenta marcar facturas como pagadas de forma masiva:** El sistema bloquea la acción, informa que esta operación no está permitida por seguridad y cancela el flujo.
- **4a. El administrador cancela la confirmación:** El sistema detiene la operación y devuelve al administrador al listado original sin realizar cambios.

---

## Caso de Uso: Consultar panel financiero

- **ID / Código:** CU-704
- **Actor principal:** Administrador
- **Ámbito:** Panel de control financiero (Dashboard)
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador visualiza un panel de control interactivo con las métricas consolidadas del estado económico de la empresa, la evolución temporal y la situación general de facturas y clientes.

**Interesados:**

- **Administrador / Dirección:** Necesita una visión global, rápida y fiable de los ingresos, cobros pendientes y evolución para la toma de decisiones estratégicas.

**Precondiciones:**

- El administrador ha iniciado sesión.
- El sistema cuenta con datos financieros y contables procesados.

**Disparador:** El administrador navega a la sección del panel financiero desde el menú principal.

**Garantía de éxito:**

- El sistema muestra los gráficos y las métricas consolidadas comparados con los objetivos, permitiendo su filtrado y revisión ágil.

**Garantía mínima:**

- Si no hay datos suficientes o el cálculo falla, el sistema muestra el panel con métricas a cero o un aviso de no disponibilidad temporal, sin exponer datos inconsistentes.

**Escenario principal de éxito:**

1. El administrador accede al panel financiero.
2. El sistema calcula y consolida las métricas base (total facturado, total cobrado, pendiente de cobro, saldo vencido).
3. El sistema carga los objetivos de facturación configurados y las previsiones basadas en suscripciones activas.
4. El sistema carga métricas adicionales (facturación por periodos, tiempo medio de cobro, clientes con mayor deuda).
5. El sistema muestra los datos consolidados y los gráficos de evolución en pantalla.
6. El administrador selecciona un filtro (ej. por país, sector, moneda o tipo de factura).
7. El sistema actualiza el panel reflejando los datos ajustados a los nuevos filtros.

**Extensiones:**

- **6a. Filtros incompatibles o sin resultados:** El panel muestra los gráficos vacíos indicando que no hay datos para esa combinación de filtros específicos.

---

## Caso de Uso: Desglosar métricas (Drill-down)

- **ID / Código:** CU-705
- **Actor principal:** Administrador
- **Ámbito:** Panel de control financiero (Dashboard)
- **Nivel:** Subfunción
- **Descripción:** El administrador consulta el listado detallado de las facturas o elementos individuales que componen el valor numérico de una métrica mostrada en el panel financiero.

**Interesados:**

- **Administrador / Dirección:** Necesita auditar, verificar o detallar de dónde provienen exactamente los números reflejados en un indicador clave del panel.

**Precondiciones:**

- El administrador se encuentra visualizando datos cargados en el panel financiero (CU-704).

**Disparador:** El administrador hace clic sobre el valor numérico o el gráfico de una métrica específica dentro del panel financiero.

**Garantía de éxito:**

- El sistema despliega una vista o listado detallado de los registros (ej. facturas, cobros) cuya suma exacta forma el valor de la métrica seleccionada.

**Garantía mínima:**

- Si no se puede generar el detalle, el sistema mantiene el panel financiero activo y avisa de que el desglose no está disponible en este momento.

**Escenario principal de éxito:**

1. El administrador hace clic en una métrica específica en el panel (ej. "Importe pendiente de cobro").
2. El sistema captura los parámetros, periodo y filtros que aplican actualmente a esa métrica.
3. El sistema consulta la base de datos para recuperar el listado detallado de elementos asociados a esos parámetros.
4. El sistema abre una vista o ventana superpuesta mostrando la lista detallada.

---

## Caso de Uso: Configurar objetivos de facturación

- **ID / Código:** CU-706
- **Actor principal:** Administrador
- **Ámbito:** Panel financiero / Configuración
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador establece metas económicas mensuales o anuales en el sistema para poder hacer un seguimiento de su grado de cumplimiento desde el panel de control.

**Interesados:**

- **Administrador:** Quiere establecer y medir el rendimiento económico real frente a las previsiones estratégicas de la empresa.

**Precondiciones:**

- El administrador ha iniciado sesión y tiene permisos de configuración financiera.

**Disparador:** El administrador selecciona la opción de configurar objetivos dentro del entorno del panel financiero.

**Garantía de éxito:**

- Los nuevos objetivos quedan registrados en el sistema y el panel financiero se recalcula para mostrar las barras de progreso y comparativas respecto a las nuevas metas.

**Garantía mínima:**

- Si la configuración falla o se introducen datos no válidos, el sistema descarta los cambios y mantiene los objetivos anteriores sin alteración.

**Escenario principal de éxito:**

1. El administrador selecciona la opción "Configurar objetivos".
2. El sistema muestra el formulario correspondiente con los periodos disponibles (mensual, anual).
3. El administrador introduce los importes económicos objetivo para los periodos deseados.
4. El administrador solicita guardar los cambios.
5. El sistema valida que las cifras tengan un formato numérico correcto.
6. El sistema actualiza los registros en la base de datos.
7. El sistema redirige al panel financiero y actualiza los indicadores visuales respecto a los nuevos objetivos.

**Extensiones:**

- **Cualquier paso. El usuario cancela la operación:** El sistema cancela el proceso sin guardar cambios no confirmados y regresa al panel.

---

## Caso de Uso: Generar y exportar informes/paquetes

- **ID / Código:** CU-707
- **Actor principal:** Administrador
- **Ámbito:** Módulo de Informes
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador genera informes financieros y/o un paquete mensual/trimestral estructurado para la gestoría, exportándolo en formato PDF y registrando las distintas versiones generadas.

**Interesados:**

- **Administrador:** Quiere extraer información contable consolidada para enviarla o analizarla.
- **Gestoría:** Necesita datos precisos (facturas, cobros), ordenados por periodos y con distinción clara de versiones en caso de rectificaciones.

**Precondiciones:**

- El administrador ha iniciado sesión.
- Existen datos (facturas emitidas, rectificativas o cobros) registrados en el periodo a consultar.

**Disparador:** El administrador solicita la generación de un nuevo informe o un paquete para la gestoría desde el menú de informes.

**Garantía de éxito:**

- El sistema genera un archivo PDF legible (o un paquete de archivos), registra en el sistema el periodo que cubre, la fecha de generación y la versión, y descarga el archivo en el equipo del usuario.

**Garantía mínima:**

- Si falla la exportación, el sistema informa del error al usuario sin corromper la base de datos fiscal ni registrar falsas versiones.

**Escenario principal de éxito:**

1. El administrador selecciona la opción de exportar informe o generar paquete para la gestoría.
2. El administrador define el periodo temporal y los filtros aplicables (ej. estado, moneda, cliente).
3. El sistema recopila toda la información contable solicitada para ese periodo.
4. El administrador decide opcionalmente aplicar un tipo de cambio para unificar monedas.
5. El sistema convierte los importes a la moneda base únicamente para la vista del informe, manteniendo intacto el importe legal original.
6. El sistema genera los documentos estructurados en formato PDF legible.
7. El sistema empaqueta los documentos y registra en su historial la versión, el periodo que cubre y la fecha y hora de generación.
8. El sistema ofrece el archivo final para su descarga.

**Extensiones:**

- **3a. No hay datos en el periodo:** El sistema informa de que el periodo está vacío y pregunta si desea generar el documento de todas formas o cancelar.

---

## Caso de Uso: Programar informes periódicos

- **ID / Código:** CU-708
- **Actor principal:** Administrador
- **Ámbito:** Módulo de Informes / Automatización
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador configura el sistema para que genere y envíe automáticamente informes de forma recurrente por correo a una lista de usuarios autorizados.

**Interesados:**

- **Administrador:** Quiere automatizar la entrega de información clave a ciertos departamentos sin tener que generarla y enviarla manualmente de forma continua.

**Precondiciones:**

- El administrador ha iniciado sesión.
- Los usuarios destinatarios existen en el sistema y tienen permisos suficientes para visualizar el contenido del informe.

**Disparador:** El administrador accede a la sección de automatización de informes y añade una nueva programación.

**Garantía de éxito:**

- La regla de programación queda guardada en el sistema, que se encargará de encolar y ejecutar el envío del informe en los plazos definidos automáticamente.

**Garantía mínima:**

- La programación no se guarda si faltan datos obligatorios (ej. frecuencia o destinatarios) o si los destinatarios seleccionados carecen de permisos, avisando del error.

**Escenario principal de éxito:**

1. El administrador selecciona "Programar envío" para un informe específico.
2. El sistema solicita los parámetros de automatización: frecuencia (ej. mensual), formato y lista de destinatarios.
3. El administrador configura los parámetros y selecciona a los usuarios destinatarios.
4. El administrador confirma la programación de la tarea.
5. El sistema valida los permisos de lectura de todos los destinatarios seleccionados.
6. El sistema añade la configuración a la base de datos de tareas programadas (cron).
7. El sistema muestra un mensaje de confirmación de que la programación está activa.

**Extensiones:**

- **5a. Un destinatario no tiene permisos:** El sistema avisa de qué usuario concreto no tiene permisos para ver esa información e impide guardar la programación hasta que se retire al usuario o se ajusten sus roles.

---

## Caso de Uso: Modificar datos fiscales

- **ID / Código:** CU-709
- **Actor principal:** Administrador
- **Ámbito:** Configuración de empresa
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador actualiza la información legal y comercial de la propia entidad emisora (la empresa dueña del sistema de facturación).

**Interesados:**

- **Empresa:** Necesita que sus próximas facturas salgan emitidas con los datos legales actualizados si ha ocurrido un cambio de sede fiscal, razón social o identificación.

**Precondiciones:**

- El administrador ha iniciado sesión con permisos de configuración general.

**Disparador:** El administrador accede al perfil de configuración de la empresa para editar sus datos.

**Garantía de éxito:**

- Los nuevos datos fiscales son guardados, auditados y se usarán de forma predeterminada para cualquier factura o documento que se emita en el futuro.

**Garantía mínima:**

- Las facturas ya emitidas, cerradas o contabilizadas anteriormente no sufren ninguna modificación, respetando su validez legal original.

**Escenario principal de éxito:**

1. El administrador navega a la sección de datos fiscales de la entidad emisora.
2. El administrador modifica los campos deseados (razón social, NIF/CIF, domicilio fiscal, país o moneda predeterminada).
3. El administrador solicita guardar los cambios.
4. El sistema valida que los campos obligatorios tengan el formato adecuado.
5. El sistema actualiza los datos en la base de datos y los registra en el log de auditoría.
6. El sistema confirma visualmente el guardado exitoso y avisa de que el cambio aplicará solo a documentos futuros.

---

## Caso de Uso: Configurar parámetros globales

- **ID / Código:** CU-710
- **Actor principal:** Superadministrador
- **Ámbito:** Panel de configuración global
- **Nivel:** Objetivo de Usuario
- **Descripción:** El superadministrador ajusta las reglas de negocio base del sistema, plantillas y políticas generales de la aplicación.

**Interesados:**

- **Superadministrador:** Necesita adaptar el comportamiento y las restricciones globales del sistema a las políticas de la compañía.

**Precondiciones:**

- El usuario ha iniciado sesión con el rol máximo de Superadministrador.

**Disparador:** El superadministrador entra en la sección de parámetros globales e intenta guardar modificaciones.

**Garantía de éxito:**

- Las nuevas políticas (ej. cadencias, logos, plazos) son guardadas y el sistema las aplica inmediatamente a todos los nuevos flujos y documentos.

**Garantía mínima:**

- El sistema rechaza los cambios si son inconsistentes, manteniendo la configuración previa, y nunca reescribe de forma retroactiva las facturas ya emitidas.

**Escenario principal de éxito:**

1. El superadministrador modifica parámetros globales (ej. plazo de pago por defecto, cadencia de recordatorios, dominios de correo, plantillas, series fiscales o impuestos disponibles).
2. El superadministrador solicita guardar la nueva configuración.
3. El sistema valida las reglas de los nuevos parámetros.
4. El sistema almacena la configuración y garantiza la inmutabilidad del historial previo.
5. El sistema registra el cambio detallado en el log de auditoría interna de configuraciones.
6. El sistema muestra un aviso de que la configuración global se ha actualizado con éxito.

---

## Caso de Uso: Consultar deudas vía API

- **ID / Código:** CU-711
- **Actor principal:** Cliente API
- **Ámbito:** Interfaz de Programación de Aplicaciones (API)
- **Nivel:** Subfunción / Objetivo de Sistema
- **Descripción:** Un sistema externo (ej. CRM u otra plataforma) consulta automáticamente el estado financiero y las deudas de un cliente mediante una interfaz programática segura proporcionada por el sistema.

**Interesados:**

- **Cliente API (Sistema externo):** Necesita saber de forma instantánea si un cliente tiene deuda pendiente para autorizar o bloquear servicios.
- **Empresa:** Desea exponer la información necesaria de forma estructurada pero asegurando que el acceso esté autenticado y el consumo controlado.

**Precondiciones:**

- El Cliente API posee credenciales válidas y exclusivas para la interfaz programática.
- El identificador del cliente a consultar existe en el sistema.

**Disparador:** El Cliente API realiza una petición HTTP al *endpoint* de consulta de deuda adjuntando el identificador del cliente.

**Garantía de éxito:**

- La API responde exitosamente con el estado de la deuda y las obligaciones pendientes del cliente solicitado, registrando la llamada en el sistema.

**Garantía mínima:**

- Si las credenciales son inválidas o se superan los límites de uso, la API devuelve un código de error estándar sin exponer ninguna información financiera ni saturar el servidor.

**Escenario principal de éxito:**

1. El Cliente API envía una petición telemática con sus credenciales y el identificador de un cliente específico.
2. El sistema valida las credenciales independientes.
3. El sistema comprueba que no se hayan excedido los límites de uso razonables definidos para la API (*rate limiting*).
4. El sistema busca la información financiera consolidada del cliente solicitado aplicando las mismas reglas de negocio que la interfaz visual.
5. El sistema registra la petición en el log de llamadas y auditoría.
6. El sistema devuelve la información sobre el estado de la deuda en formato de datos estructurado (ej. JSON).

**Extensiones:**

- **2a. Credenciales inválidas:** El sistema rechaza la petición y devuelve un error de autorización (HTTP 401).
- **3a. Límite de peticiones excedido:** El sistema deniega la petición temporalmente, devuelve un código HTTP 429 e informa del tiempo de espera necesario.

---

## Caso de Uso: Monitorizar estado del sistema

- **ID / Código:** CU-712
- **Actor principal:** Administrador
- **Ámbito:** Pantalla de estado / Monitorización
- **Nivel:** Objetivo de Usuario
- **Descripción:** El administrador revisa desde una vista técnica el estado operativo de las tareas automatizadas en segundo plano, colas de envío y salud general del sistema.

**Interesados:**

- **Administrador:** Quiere asegurarse visualmente de que no hay procesos atascados, correos fallidos ni errores en las copias de seguridad.

**Precondiciones:**

- El administrador ha iniciado sesión en el sistema.

**Disparador:** El administrador accede a la pestaña o pantalla de estado del sistema.

**Garantía de éxito:**

- El sistema muestra un panel de control técnico actualizado con la última ejecución de tareas, el volumen de la cola de correos y los errores recientes.

**Garantía mínima:**

- Si el motor de segundo plano está caído, el sistema muestra la última información que pudo registrar e informa de que los servicios podrían estar detenidos.

**Escenario principal de éxito:**

1. El administrador navega a la pantalla de monitorización de estado.
2. El sistema consulta las bases de datos técnicas de colas, procesos automáticos e integraciones.
3. El sistema recupera la fecha y resultado de la última copia de seguridad correcta.
4. El sistema muestra la información consolidada en pantalla, reflejando qué procesos están *pendientes*, *procesando* o *completados con errores*.

---

## Caso de Uso: Ejecutar procesos en segundo plano

- **ID / Código:** CU-713
- **Actor principal:** Sistema
- **Ámbito:** Motor de procesamiento (Background Jobs)
- **Nivel:** Subfunción
- **Descripción:** El sistema ejecuta de forma desatendida y silenciosa tareas en lote (como la generación mensual de facturas, recordatorios e informes), gestionando errores individuales sin interrumpir la ejecución global.

**Interesados:**

- **Empresa:** Necesita que todas las automatizaciones de facturación y avisos se emitan de forma puntual y autónoma, asegurando que un solo fallo no paralice a los demás clientes.

**Precondiciones:**

- El motor de tareas en segundo plano (*Background Worker*) está encendido y conectado a la base de datos.
- Existen tareas pendientes en la cola de trabajo programado.

**Disparador:** Un temporizador interno del sistema (cron) alcanza una hora configurada, o un usuario encola manualmente un trabajo asíncrono.

**Garantía de éxito:**

- El sistema procesa secuencialmente todos los elementos de la cola, y registra detalladamente cuántos elementos se procesaron, cuáles fallaron y por qué.

**Garantía mínima:**

- Si un elemento del lote provoca un fallo crítico, el sistema aísla el error, marca únicamente ese elemento como fallido, documenta el motivo en el log y prosigue con el siguiente de la cola.

**Escenario principal de éxito:**

1. El temporizador o disparador asíncrono inicia la ejecución de un proceso de lotes (ej. envío masivo de recordatorios de impago).
2. El sistema obtiene de la base de datos el lote completo de elementos a procesar.
3. El sistema actualiza el estado global de la tarea a *procesando*, visible para el administrador.
4. El sistema procesa secuencialmente cada elemento de la lista.
5. El sistema actualiza el estado de la tarea a *completado*.
6. El sistema guarda en el registro de la ejecución un resumen: total de elementos procesados y total de elementos exitosos.

**Extensiones:**

- **4a. Un elemento específico genera un error (ej. correo inválido):**
  1. El sistema captura la excepción y evita la caída del proceso.
  2. El sistema registra el motivo exacto del fallo y marca ese elemento individual con el estado *completado con errores*.
  3. El sistema retoma inmediatamente la ejecución en el paso 4 para el siguiente elemento del lote.