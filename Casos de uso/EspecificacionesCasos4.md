# Especificación de Casos de Uso - Bloque 4: Presupuestos, facturación y ciclo de vida documental

---

## Caso de Uso: Gestionar presupuestos

* **ID / Código:** CU-401
* **Actor principal:** Usuario
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El usuario propone un presupuesto para servicios puntuales, registrando la aceptación o el rechazo del cliente, permitiendo adjuntar la evidencia de dicha aceptación y convirtiendo el presupuesto aceptado directamente en una factura sin necesidad de volver a introducir los datos.

**Interesados:**

* **Usuario:** Quiere crear los presupuestos rápidamente, registrar la decisión del cliente y convertirlos en facturas sin tener que reescribir la información.
* **Cliente:** Quiere recibir una propuesta económica clara para servicios puntuales y poder aceptarla mediante o rechazarla sin complicaciones.
* **Turbine (Administración):** Quiere asegurar que cualquier factura emitida a partir de un presupuesto cuente con el respaldo de una evidencia válida de aceptación.

**Precondiciones:**

* El usuario ha iniciado sesión en el sistema y tiene permisos para gestionar presupuestos y facturación.
* El cliente para el que se emite el presupuesto está registrado previamente en la plataforma.
* Existen conceptos, tarifas o servicios puntuales configurados en el sistema para poder añadirlos.

**Disparador:** El usuario solicita crear un nuevo presupuesto para un cliente o accede a un presupuesto ya emitido para actualizar su estado o convertirlo en factura.

**Garantía de éxito:**

* El presupuesto aceptado y su evidencia documental quedan registrados en el sistema, y se genera automáticamente una nueva factura vinculada que hereda todos los conceptos, precios y datos del cliente.

**Garantía mínima:**

* Si el proceso se interrumpe, el cliente rechaza la propuesta o faltan datos obligatorios, el sistema no crea ninguna factura, no consume numeración correlativa y mantiene los datos consolidados sin alteraciones.

**Escenario principal de éxito:**

1. El usuario solicita la creación de un nuevo presupuesto para un cliente.
2. El sistema comprueba los datos comerciales y fiscales del cliente.
3. El usuario define las líneas del servicio puntual (concepto, unidades, precio de referencia, descuento e impuestos aplicables).
4. El sistema calcula las bases imponibles, el desglose impositivo y el importe total, guardando el presupuesto.
5. El usuario registra que el cliente ha aceptado el presupuesto.
6. El sistema solicita el documento de evidencia externa que respalda la aceptación.
7. El usuario aporta el archivo de evidencia externa sin requerir firma dentro de la plataforma.
8. El sistema vincula la evidencia al presupuesto y actualiza su estado a *Aceptado*.
9. El usuario solicita convertir el presupuesto aceptado en factura.
10. El sistema genera una nueva factura en estado borrador, traspasando automáticamente todos los datos del cliente, conceptos e importes sin exigir su reintroducción manual.
11. El sistema vincula la factura con el presupuesto original y notifica la correcta finalización del proceso.

**Extensiones:**

* **3a. Los datos introducidos son incompletos o erróneos:** El sistema informa de los errores detectados y permite corregirlos sin registrar el presupuesto.
* **5a. El cliente rechaza el presupuesto:**
  1. El usuario registra el rechazo del presupuesto indicando opcionalmente el motivo.
  2. El sistema actualiza el estado del presupuesto a *Rechazado*, inhabilita la opción de convertirlo en factura y finaliza.
* **7a. El usuario no aporta la evidencia de aceptación:** El sistema avisa de la falta del documento y mantiene el presupuesto sin marcar como formalmente aceptado (o como *Pendiente de evidencia*), impidiendo su conversión directa a factura.
* **9a. El presupuesto ya ha sido convertido a factura previamente:** El sistema avisa de que el documento ya fue facturado para evitar duplicidades y muestra el enlace a la factura existente.
* **Cualquier paso. El usuario cancela la operación:** El sistema cancela el proceso sin guardar cambios no confirmados.

---

## Caso de Uso: Emitir factura puntual

* **ID / Código:** CU-402
* **Actor principal:** Usuario
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El usuario elabora y formaliza una factura directa para un servicio puntual no vinculado a una suscripción periódica, definiendo los conceptos e impuestos aplicables, validando los datos fiscales del cliente, previsualizando el documento resultante y emitiendo la factura definitiva con su número correlativo y su correspondiente archivo en formato PDF.

**Interesados:**

* **Usuario:** Quiere preparar y emitir facturas puntuales de manera sencilla, comprobando que los importes y los datos fiscales sean correctos antes de emitirlas.
* **Cliente:** Quiere recibir una factura clara con sus datos fiscales, los servicios cobrados y los impuestos aplicados.
* **Turbine (Administración / Auditoría):** Requiere asegurar que las facturas sigan el orden correcto de numeración, cumplan las normas fiscales y queden guardadas en PDF sin modificaciones posteriores.

**Precondiciones:**

* El usuario ha iniciado sesión en el sistema y cuenta con permisos para crear y emitir facturas.
* El cliente destinatario está dado de alta en la plataforma y dispone de sus datos fiscales obligatorios.
* Existen conceptos o servicios disponibles en el catálogo para poder añadirlos a la factura.

**Disparador:** El usuario solicita crear una nueva factura puntual para un cliente determinado.

**Garantía de éxito:**

* La factura queda emitida en el sistema con un número correlativo único asignado, su documento en PDF generado y vinculada a la ficha del cliente con los importes, impuestos y conceptos correctamente calculados.

**Garantía mínima:**

* Si la operación se cancela, faltan datos obligatorios o la factura queda guardada como borrador, el sistema no asigna número de serie, no altera el registro de facturas emitidas y mantiene la información pendiente sin generar errores.

**Escenario principal de éxito:**

1. El usuario solicita crear una nueva factura para un cliente.
2. El sistema comprueba los datos comerciales y fiscales del cliente.
3. El usuario añade una o varias líneas indicando concepto, cantidad, precio, descuento e impuesto correspondiente.
4. El sistema calcula los subtotales, impuestos y el importe total en tiempo real.
5. El usuario solicita previsualizar la factura antes de su emisión definitiva.
6. El sistema valida los datos obligatorios y muestra la vista previa con el desglose completo.
7. El usuario confirma la emisión de la factura.
8. El sistema asigna a la factura una serie y un número correlativo único.
9. El sistema genera el archivo de la factura en formato PDF.
10. El sistema guarda la factura como emitida, la vincula al cliente y notifica al usuario la correcta emisión.

**Extensiones:**

* **2a. Al cliente le faltan datos fiscales obligatorios:** El sistema informa de los datos faltantes e impide avanzar hasta que se completen en su ficha.
* **3a. Los datos de una línea son incompletos o erróneos:** El sistema resalta el error en la línea afectada y permite corregirlo.
* **4a. El usuario opta por no emitir la factura en el momento:** El sistema guarda la factura en estado *Borrador* sin asignar número correlativo ni generar el PDF definitivo, permitiendo retomarla después.
* **6a. El importe total es mucho más alto o más bajo de lo habitual en ese cliente:** El sistema muestra un aviso sobre la diferencia de importe y permite al usuario revisarlo o confirmar que desea continuar.
* **7a. La factura incluye condiciones especiales (descuentos altos o modificaciones sensibles):** El sistema asigna el estado *Pendiente de aprobación*, deriva la factura a la bandeja correspondiente y notifica al usuario sin emitirla de inmediato.
* **Cualquier paso. El usuario cancela la operación:** El sistema solicita confirmación y finaliza la acción sin guardar los cambios no confirmados.

---

## Caso de Uso: Generar facturación recurrente

* **ID / Código:** CU-403
* **Actor principal:** Sistema
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Resumen
* **Descripción:** El sistema ejecuta de forma automática la facturación periódica de todos los clientes con suscripciones activas al llegar el día de cobro, calculando las cuotas según el ciclo y los precios contratados, creando las facturas correspondientes, asignando su número de factura en orden y generando sus documentos en PDF.

**Interesados:**

* **Turbine (Administración):** Quiere que los cobros periódicos se generen puntualmente sin retrasos ni olvidos, con la numeración correcta y los documentos listos.
* **Cliente:** Quiere recibir su factura periódica a tiempo, con el importe exacto de su plan contratado y sin errores de cobro.
* **Usuario:** Quiere que el proceso sea automático y fiable, avisando únicamente si alguna factura no se pudo generar para poder revisarla a mano.

**Precondiciones:**

* Existen suscripciones activas en el sistema asociadas a clientes dados de alta con sus datos fiscales completos.
* Los planes, ciclos de facturación (mensual, trimestral o anual) y precios están configurados en el sistema.
* Existe al menos una serie de facturación activa para asignar la numeración a las facturas que se generen.

**Disparador:** El reloj del sistema alcanza la fecha o el día programado para la facturación periódica de las suscripciones.

**Garantía de éxito:**

* Todas las suscripciones que tocaba facturar quedan procesadas, con sus facturas emitidas, numeradas en orden correlativo, sus archivos en PDF generados y asociadas a cada cliente sin facturas repetidas.

**Garantía mínima:**

* Si el proceso falla o se detiene a mitad, las facturas ya creadas mantienen su validez sin saltos de numeración, las no procesadas quedan pendientes para el siguiente intento y se registra un aviso detallando el punto exacto donde se detuvo sin alterar datos incorrectamente.

**Escenario principal de éxito:**

1. El sistema inicia el proceso automático al cumplirse la fecha programada.
2. El sistema busca todas las suscripciones activas pendientes de facturar en ese ciclo.
3. El sistema comprueba los datos fiscales y los importes vigentes de cada suscripción.
4. El sistema calcula las cuotas, impuestos y totales correspondientes a cada cliente.
5. El sistema asigna a cada factura su serie y su número correlativo en orden.
6. El sistema crea el documento de cada factura en formato PDF.
7. El sistema guarda las facturas como emitidas y las añade al historial de cada cliente.
8. El sistema actualiza la fecha de la próxima facturación en cada suscripción procesada.
9. El sistema genera un resumen del proceso e informa al usuario del resultado.

**Extensiones:**

* **2a. No hay suscripciones pendientes de facturar en esta fecha:** El sistema registra que no hubo elementos para procesar, finaliza la tarea e informa al usuario.
* **3a. A un cliente le faltan datos fiscales obligatorios o su ficha tiene errores:** El sistema salta ese cliente, anota la incidencia en el informe y continúa procesando el resto de suscripciones.
* **4a. El importe calculado para un cliente es cero o negativo:** El sistema detiene la generación de esa factura concreta, la marca para revisión y continúa con las demás.
* **5a. Ocurre un error al asignar el número de factura o la serie no está disponible:** El sistema frena el proceso, asegura las facturas ya creadas y envía un aviso al usuario para que revise la configuración de series.
* **6a. Ocurre un fallo al crear el documento en PDF de una factura:** El sistema guarda la factura como emitida, registra el fallo del documento y programa su reintento sin frenar el lote completo.

---

## Caso de Uso: Aplicar prorrateos y promociones

* **ID / Código:** CU-404
* **Actor principal:** Sistema
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Subfunción (extensión de CU-403)
* **Descripción:** El sistema calcula el importe proporcional correspondiente a los días restantes del periodo cuando una suscripción inicia a mitad de mes, o aplica las condiciones comerciales especiales acordadas al generar la cuota periódica del cliente.

**Interesados:**

* **Cliente:** Quiere que solo le cobren por los días que realmente ha disfrutado del servicio y que se le apliquen correctamente los descuentos prometidos.
* **Turbine (Administración):** Quiere ofrecer descuentos y promociones a los clientes sin que los cálculos de días sueltos tengan que hacerse a mano ni causen errores en las cuentas.
* **Usuario:** Quiere comprobar el cálculo exacto del prorrateo o descuento antes de que se confirme el cobro.

**Precondiciones:**

* El cliente cuenta con una suscripción activa o programada en el sistema, con su precio y plan definidos.
* La suscripción incluye una fecha de inicio posterior al día 1 del mes, una promoción comercial o un descuento temporal con fecha límite.

**Disparador:** El sistema debe calcular el importe a facturar para una suscripción sujeta a días proporcionales, promociones o descuentos vigentes.

**Garantía de éxito:**
* Se calcula la cantidad exacta a pagar según los días restantes del mes o el descuento acordado, y se actualiza el tiempo que le queda a la promoción.

**Garantía mínima:**
* Ante cualquier error en el cálculo, el sistema frena la factura para evitar cobros erróneos y deja un aviso para que un usuario revise los datos.

**Escenario principal de éxito:**

1. El sistema inicia el cálculo del cobro para la suscripción del cliente.
2. El sistema comprueba si la suscripción empezó a mitad de mes o si tiene promociones o descuentos activos.
3. Si la fecha de inicio no es el día 1, el sistema calcula los días de servicio que quedan de mes y obtiene el importe proporcional.
4. Si hay una promoción o descuento vigente, el sistema comprueba sus fechas y aplica la rebaja sobre el precio.
5. El sistema genera el desglose mostrando el precio base, el ajuste aplicado y el total final.
6. El sistema descuenta un mes a la promoción o la da por terminada si ya ha cumplido su plazo.
7. El sistema continúa con la creación de la factura usando este importe final.

**Extensiones:**

* **2a. La promoción o el descuento ya ha caducado:** El sistema no aplica ninguna rebaja, utiliza el precio habitual de la suscripción y continúa con el proceso.
* **4a. La promoción deja el importe en 0 € (por ejemplo, primer mes gratis):** El sistema genera la factura con coste 0 € o no emite cobro según la política configurada, y descuenta el mes de promoción.
* **4b. El descuento introducido supera el límite permitido o requiere validación:** El sistema calcula el importe pero deja la factura marcada como *Pendiente de aprobación* para que la autorice un administrador.
* **5a. El resultado del cálculo da un importe negativo:** El sistema bloquea la factura, registra un error de cálculo y genera un aviso al usuario.

---

## Caso de Uso: Aprobar facturas con condiciones especiales

* **ID / Código:** CU-405
* **Actor principal:** Administrador
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El administrador revisa y gestiona las facturas que han quedado retenidas en estado pendiente de aprobación por contener condiciones específicas (como descuentos elevados, importes muy altos o cambios en la fecha de pago), pudiendo autorizar su emisión definitiva, rechazarlas con un motivo justificado o reasignar la revisión a otro responsable.

**Interesados:**

* **Administrador:** Quiere comprobar rápidamente por qué se ha frenado una factura y decidir si la aprueba, la rechaza o se la pasa a otro compañero sin perder tiempo.
* **Usuario solicitante:** Quiere conocer el estado de su petición y saber si su factura ha sido aceptada, rechazada o reasignada sin bloqueos innecesarios.
* **Turbine (Dirección):** Quiere garantizar la separación de funciones, evitando que un mismo usuario apruebe sus propios descuentos o que se emitan rebajas excesivas sin autorización.

**Precondiciones:**

* El administrador ha iniciado sesión en el sistema y tiene permisos para autorizar facturas.
* Existe al menos una factura en estado *Pendiente de aprobación* debido a un descuento alto, un importe muy elevado o un cambio en la fecha de cobro.
* El administrador que va a revisar no es la misma persona que solicitó la factura.

**Disparador:** El administrador accede a la bandeja de aprobaciones pendientes tras recibir un aviso o durante su revisión diaria.

**Garantía de éxito:**

* La factura cambia a estado *Aprobada* (quedando lista para emitirse) o *Rechazada* (con el motivo indicado), se avisa al solicitante y queda guardado quién tomó la decisión, la fecha y el comentario.

**Garantía mínima:**

* Si el administrador cancela la revisión o falla la conexión, la factura sigue retenida en estado *Pendiente de aprobación*, sin asignarle número ni emitir ningún documento.

**Escenario principal de éxito:**

1. El administrador accede a la bandeja de aprobaciones del sistema.
2. El sistema muestra la lista de facturas retenidas, indicando el cliente, el importe, la persona que solicitó la factura y el motivo por el que quedó pendiente.
3. El administrador selecciona una factura para ver su desglose completo.
4. El sistema muestra la vista previa con las líneas, los conceptos, el descuento o la condición especial aplicada.
5. El administrador comprueba que la condición aplicada es correcta y confirma su aprobación.
6. El sistema actualiza el estado de la factura a *Aprobada*.
7. El sistema registra la fecha, la hora y el usuario que ha aprobado la factura.
8. El sistema notifica al usuario solicitante que la factura ha sido aprobada y queda lista para su emisión.

**Extensiones:**

* **2a. El administrador intenta revisar una factura que él mismo creó:** El sistema bloquea las opciones de aprobación e indica que debe ser revisada por otro administrador o super administrador.
* **5a. El administrador rechaza la factura:** El sistema solicita obligatoriamente el motivo del rechazo, pasa la factura a estado *Rechazada*, guarda el comentario y notifica al solicitante para que realice las correcciones necesarias.
* **5b. El administrador reasigna la factura:** El administrador indica el motivo, selecciona al nuevo responsable y el sistema transfiere la factura a su bandeja, dejando constancia del traspaso y avisando a las partes implicadas.
* **5c. El descuento supera el límite configurado para un administrador ordinario:** El sistema avisa de que el importe o porcentaje requiere autorización de rango superior, restringe la acción y envía la solicitud a la bandeja del *Super administrador*.
* **Cualquier paso. El administrador cancela la revisión:** El sistema regresa a la bandeja de entrada manteniendo la factura en estado *Pendiente de aprobación* sin registrar cambios.

---

## Caso de Uso: Consultar bandeja de aprobaciones

* **ID / Código:** CU-406
* **Actor principal:** Usuario
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El usuario consulta el estado de las facturas que ha enviado a aprobación, comprobando si siguen pendientes, si han sido autorizadas o si han sido rechazadas, revisando en cada caso el motivo por el que quedaron retenidas y los comentarios dejados por el aprobador.

**Interesados:**

* **Usuario:** Quiere saber en todo momento cómo van sus solicitudes, entender por qué se frenó una factura y ver las explicaciones si fue rechazada para poder corregirla.
* **Turbine (Administración):** Quiere mantener una comunicación clara entre quien emite y quien autoriza, evitando que las dudas se gestionen fuera del sistema y agilizando la resolución de incidencias.

**Precondiciones:**

* El usuario ha iniciado sesión en el sistema y tiene acceso a la sección de facturación o a su bandeja personal de solicitudes.
* El usuario ha generado previamente al menos una factura que haya requerido validación por condiciones especiales.

**Disparador:** El usuario accede a la sección de aprobaciones para comprobar el avance de sus solicitudes o recibe una notificación sobre un cambio de estado en una factura enviada.

**Garantía de éxito:**

* El sistema muestra el listado actualizado de las solicitudes del usuario con su estado actual (*Pendiente*, *Aprobada* o *Rechazada*), el motivo original de la retención y, si ya fue revisada, el comentario y los datos de quien tomó la decisión.

**Garantía mínima:**

* La consulta es de solo lectura, ante cualquier fallo de red o cancelación, el sistema no altera los datos de las facturas ni el estado de las solicitudes.

**Escenario principal de éxito:**

1. El usuario accede a su bandeja de solicitudes de aprobación.
2. El sistema muestra la lista de sus facturas enviadas a validar, indicando cliente, importe, fecha de envío, estado actual (*Pendiente*, *Aprobada* o *Rechazada*) y el motivo por el que quedaron retenidas.
3. El usuario selecciona una factura para consultar el detalle de la solicitud.
4. El sistema muestra el desglose completo del documento, la condición especial que originó la retención y la información de la revisión (aprobador asignado, fecha de resolución y comentarios o justificación).
5. El usuario comprueba el estado y los comentarios registrados para decidir sus siguientes acciones.

**Extensiones:**

* **2a. El usuario no tiene facturas enviadas a aprobación:** El sistema muestra un mensaje informativo indicando que la bandeja está vacía y finaliza el caso de uso.
* **4a. La factura ha sido rechazada por el aprobador:** El sistema muestra de forma clara el motivo del rechazo junto al comentario explicativo y ofrece un enlace directo al borrador para corregir los datos.
* **4b. La factura ha sido aprobada:** El sistema muestra la confirmación de la autorización junto al enlace para proceder con su emisión definitiva o consultar el documento.
* **Cualquier paso. El usuario cierra o sale de la bandeja:** El sistema regresa a la vista general sin registrar cambios.

---

## Caso de Uso: Validar y previsualizar factura

* **ID / Código:** CU-407
* **Actor principal:** Usuario
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Subfunción (incluido por CU-402)
* **Descripción:** El sistema comprueba que los datos fiscales de la empresa emisora y del cliente estén completos, evalúa si el importe total calculado se desvía mucho de la media habitual del cliente y genera una vista previa del documento para que el usuario verifique el desglose antes de su emisión definitiva.

**Interesados:**

* **Usuario:** Quiere comprobar el aspecto y los importes de la factura antes de emitirla, asegurándose de que no contenga errores fiscales ni descuadres.
* **Cliente:** Quiere recibir una factura correcta, sin datos fiscales erróneos o incompletos que invaliden el documento.
* **Turbine (Administración):** Requiere garantizar que ninguna factura se emita sin cumplir los requisitos fiscales legales y detectar importes fuera de lo común antes de formalizar el cobro.

**Precondiciones:**

* El usuario se encuentra en el proceso de creación o edición de una factura puntual.
* Se han introducido los conceptos y líneas de la factura con sus importes y tipos impositivos calculados.

**Disparador:** El caso de uso base CU-402 solicita la validación previa del documento antes de autorizar su emisión definitiva.

**Garantía de éxito:**

* Los datos fiscales quedan validados, el sistema confirma que el importe se encuentra dentro de los valores habituales (o se acepta la advertencia en caso de desviación) y se muestra la previsualización del documento lista para confirmar la emisión.

**Garantía mínima:**

* Si la validación detecta datos fiscales incompletos o el usuario cancela la vista previa, la factura no se emite, no se le asigna número de serie y se mantiene en estado borrador.

**Escenario principal de éxito:**

1. El sistema recibe la solicitud de emisión.
2. El sistema comprueba que los datos fiscales obligatorios tanto de la empresa emisora como del cliente receptor estén completos (razón social, NIF y domicilio fiscal).
3. El sistema consulta el historial de facturación del cliente y comprueba que el importe total no se desvíe de lo habitual.
4. El sistema genera y muestra la vista previa con el desglose de conceptos, bases imponibles, impuestos, retenciones e importes finales.
5. El usuario comprueba el documento en la vista previa y confirma que los datos son correctos para continuar con la emisión.

**Extensiones:**

* **2a. Faltan datos fiscales obligatorios de la empresa o del cliente:** El sistema bloquea el paso a la emisión, indica qué datos faltan y devuelve el control al borrador para subsanarlos.
* **3a. El importe total es mucho más alto o más bajo de lo habitual en ese cliente:** El sistema muestra una advertencia con la desviación detectada, el usuario puede confirmar que es correcto para seguir o cancelar para corregir las líneas.
* **5a. El usuario detecta errores en la vista previa o decide cancelar:** El sistema regresa a la pantalla de edición manteniendo la factura en estado borrador, sin asignar serie ni numeración.

---

## Caso de Uso: Gestionar estados, numeración y documentación de facturas

* **ID / Código:** CU-408
* **Actor principal:** Sistema
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Subfunción
* **Descripción:** El sistema gestiona las transiciones de estado de las facturas a lo largo de su ciclo de vida, asigna la serie y número correlativo único solo al momento de la emisión, genera automáticamente el documento en formato PDF y almacena una copia que no es modificable con el registro de emisión y destinatarios.

**Interesados:**

* **Turbine (Administración):** Necesita asegurar que las facturas sigan un orden numérico correlativo sin saltos ni duplicados y que los borradores no gasten número.
* **Auditoría / Asesoría fiscal:** Necesita copias digitales no modificables de las facturas emitidas con la información exacta de quién la generó, cuándo y a quién se dirigió.
* **Cliente:** Requiere disponer de un documento formal con validez fiscal y formato estándar.

**Precondiciones:**

* Una factura en estado borrador recibe la orden de emisión definitiva, o se produce un evento de cobro o rectificación que altera su estado.
* La serie de facturación correspondiente está configurada y habilitada en el sistema.

**Disparador:** El sistema detecta una solicitud de emisión definitiva o un cambio de estado en el ciclo de vida de una factura.

**Garantía de éxito:**

* La factura actualiza su estado, recibe un número correlativo y único solo al emitirse, genera su documento en PDF y se archiva una copia inalterable con el registro de emisión y destinatarios.

**Garantía mínima:**

* Si ocurre un error al asignar la numeración o al generar el archivo, la operación se cancela, no se gasta número de serie y la factura mantiene su estado previo.

**Escenario principal de éxito:**

1. El sistema recibe la orden de emisión definitiva para una factura.
2. El sistema actualiza el estado de la factura a *Emitida*.
3. El sistema identifica la serie correspondiente y asigna de manera correlativa y única el siguiente número disponible.
4. El sistema genera el archivo de la factura en formato PDF con todos los datos fiscales, comerciales y líneas de cálculo.
5. El sistema conserva una copia inalterable del PDF generado junto con la fecha, el cliente, el usuario emisor y los destinatarios del envío.
6. El sistema deja el documento disponible para su consulta, descarga o envío por correo.

**Extensiones:**

* **3a. Conflicto o fallo al asignar el número correlativo:** El sistema quita la asignación sin generar saltos en la serie, cancela la emisión y mantiene la factura en estado borrador notificando el error técnico.
* **4a. Error durante la generación del archivo PDF:** El sistema deshace el cambio de estado y la asignación del número, evitando que existan facturas emitidas sin su soporte documental correspondiente.
* **6a. Se produce un cambio de estado posterior en el ciclo de vida (vencida, en disputa, pagada, rectificada):** El sistema actualiza el estado de la factura conservando intactos el PDF original emitido, el número asignado y la fecha de emisión.

---

## Caso de Uso: Emitir factura rectificativa

* **ID / Código:** CU-409
* **Actor principal:** Usuario
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El usuario crea una factura rectificativa a partir de una factura ya emitida para corregir errores o anularla, eligiendo una serie especial de rectificación y explicando el motivo del cambio sin perder la relación entre ambas.

**Interesados:**

* **Usuario:** Quiere corregir errores en facturas emitidas o tramitar devoluciones de forma sencilla y legal.
* **Cliente:** Necesita un documento rectificativo claro que anule o ajuste los importes de la factura anterior.
* **Turbine (Administración):** Requiere garantizar que toda rectificativa use una serie específica, quede ligada a su factura de origen con motivo justificado y preserve el histórico inalterable.

**Precondiciones:**

* El usuario tiene permisos de edición.
* Existe al menos una factura previa en estado emitida.
* El periodo contable de la factura no está cerrado de forma que impida rectificaciones.
* Existen series de facturación rectificativa configuradas y disponibles.

**Disparador:** El usuario solicita crear una factura rectificativa desde el detalle de una factura emitida o desde el listado de facturas.

**Garantía de éxito:**

* Se genera la factura rectificativa vinculada a la original y se actualiza la deuda del cliente.
* Si el importe requiere autorización previa, queda pendiente de aprobación, si no la requiere o ya está aprobada, se emite con su número correlativo y la original pasa a *Rectificada*.

**Garantía mínima:**

* Si la operación se cancela o se rechaza, no se emite ningún documento, la deuda del cliente no cambia y la factura original se mantiene intacta.

**Escenario principal de éxito:**

1. El usuario accede al detalle de una factura emitida cuyo periodo no esté cerrado administrativamente y solicita rectificarla.
2. El sistema muestra el formulario de rectificación vinculado a la factura original.
3. El usuario define los importes a corregir e introduce el motivo del ajuste.
4. El sistema comprueba que el importe de la rectificativa no supera el umbral que exige aprobación previa.
5. El usuario confirma la emisión de la rectificativa.
6. El sistema emite la factura rectificativa con su serie correspondiente, vincula ambos documentos y actualiza el estado de la factura original a *Rectificada*.
7. El sistema recalcula y actualiza la deuda pendiente del cliente.

**Extensiones:**

* **1a. El periodo administrativo de la factura está cerrado:** El sistema bloquea la acción e indica que no se permiten rectificaciones en periodos cerrados.
* **4a. El importe de la rectificativa supera el umbral configurado:** El sistema retiene el documento en estado *Pendiente de aprobación*, avisa al usuario y envía la solicitud a la bandeja de aprobaciones del administrador correspondiente.
* **Cualquier paso. El usuario cancela la rectificación:** El sistema regresa al detalle de la factura original sin registrar cambios ni alterar la deuda del cliente.

---

## Caso de Uso: Gestionar periodos administrativos

* **ID / Código:** CU-410
* **Actor principal:** Administrador
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El administrador cierra meses o trimestres ya revisados para bloquear cualquier cambio en las facturas, o reabre un periodo cerrado indicando un motivo si dispone del permiso requerido.

**Interesados:**

* **Turbine (Administración):** Necesita asegurar el cierre contable de los periodos para garantizar que los datos fiscales ya liquidados no se alteren.
* **Auditoría / Asesoría fiscal:** Requiere un registro estricto de auditoría con la traza, fecha y motivo de cualquier reapertura excepcional de un periodo cerrado.
* **Usuario:** Requiere claridad sobre qué meses o trimestres están bloqueados para la emisión o edición ordinaria.

**Precondiciones:**

* El administrador se encuentra autenticado en el sistema.
* Existen periodos fiscales (meses o trimestres) definidos en el sistema.
* Para reabrir un periodo cerrado, el administrador cuenta con el permiso superior requerido.

**Disparador:** El administrador accede al módulo de periodos administrativos y solicita cerrar o reabrir un mes o trimestre.

**Garantía de éxito:**

* Si se cierra el periodo, este queda marcado como cerrado y se bloquean automáticamente los cambios sobre sus facturas y elementos económicos.
* Si se reabre el periodo, el sistema registra el motivo justificado, la fecha y el administrador responsable, dejando el periodo temporalmente habilitado para modificaciones.

**Garantía mínima:**

* Si la operación se cancela o si para una reapertura no se aporta el motivo o falta el permiso superior, el estado del periodo no varía y se mantiene el bloqueo existente.

**Escenario principal de éxito:**

1. El administrador accede a la sección de periodos administrativos.
2. El sistema muestra el listado de meses y trimestres con su estado actual (abierto o cerrado).
3. El administrador selecciona un periodo abierto y revisa el resumen de las facturas y elementos económicos asociados.
4. El administrador confirma el cierre administrativo del periodo.
5. El sistema actualiza el estado del periodo a *Cerrado*.
6. El sistema bloquea de forma inmediata la edición o cambios ordinarios en todas las facturas y elementos económicos comprendidos en dicho periodo.

**Extensiones:**

* **3a. El administrador solicita reabrir un periodo cerrado:** El sistema valida el permiso superior, exige e ingresa el motivo justificado, pasa el periodo a abierto, habilita la edición y registra la operación con fecha y responsable.
* **3b. El administrador carece del permiso superior para reabrir:** El sistema deniega la reapertura, mantiene el bloqueo del periodo y notifica la falta de privilegios.
* **3c. No se proporciona un motivo justificado para la reapertura:** El sistema no permite continuar con la operación hasta completar el campo obligatorio.
* **Cualquier paso. El administrador cancela la acción:** El sistema vuelve a la vista de periodos sin alterar el estado actual ni modificar los bloqueos existentes.

---

## Caso de Uso: Importar facturas antiguas

* **ID / Código:** CU-11
* **Actor principal:** Administrador
* **Ámbito:** Sistema de facturación de Turbine
* **Nivel:** Objetivo de Usuario
* **Descripción:** El administrador sube al sistema facturas de años anteriores para conservarlas como historial, guardándolas con la etiqueta "Migradas", manteniendo su número y documento original, sin avisar al cliente ni alterar las series activas.

**Interesados:**

* **Administrador:** Necesita cargar facturas antiguas fácilmente, asegurando que se etiqueten como migradas y no interfieran con la facturación actual.
* **Turbine (Administración / Finanzas):** Necesita centralizar el histórico de facturación en una única plataforma sin descuadrar la contabilidad ni duplicar datos.
* **Cliente:** No debe recibir notificaciones ni correos por facturas antiguas ya emitidas en el pasado.

**Precondiciones:**

* El administrador se encuentra autenticado en el sistema.
* Se dispone de los datos y de los archivos de las facturas que se van a importar.

**Disparador:** El administrador accede a la sección de importación de facturas históricas y solicita subir facturas antiguas.

**Garantía de éxito:**

* Las facturas quedan registradas como historial con la etiqueta *Migradas*, conservando su número y documento original.
* Las series de numeración en curso no sufren alteraciones ni gastan números.
* El sistema bloquea cualquier envío de notificación o correo al cliente.

**Garantía mínima:**

* Si la importación se cancela o falla, no se registra ninguna factura en el historial y las series activas se mantienen intactas.

**Escenario principal de éxito:**

1. El administrador accede al módulo de importación de facturas históricas.
2. El administrador sube los archivos de las facturas antiguas y completa o confirma sus datos asociados (número original, fecha e importes).
3. El sistema valida los datos introducidos y los documentos adjuntos.
4. El administrador confirma la importación de las facturas.
5. El sistema registra las facturas asignándoles de forma automática la etiqueta *Migradas*.
6. El sistema guarda cada documento con su número original, manteniendo inalteradas las series de numeración en curso y sin enviar notificaciones a los clientes.

**Extensiones:**

* **3a. Falta información obligatoria (número original, fecha o importe):** El sistema señala los datos incompletos y detiene el proceso hasta que se completen.
* **3b. El archivo adjunto está dañado o no tiene un formato válido:** El sistema rechaza el documento, notifica el problema y permite volver a subirlo.
* **Cualquier paso. El administrador cancela la operación:** El sistema descarta los archivos y datos aportados sin registrar facturas ni modificar las series activas.