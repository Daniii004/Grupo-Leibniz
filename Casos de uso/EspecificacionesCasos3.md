# Especificación de Casos de Uso - Bloque 3: Suscripciones, tarifas y catálogo

---

## Caso de Uso: Dar de alta una suscripción

* **ID / Código:** CU-301
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR9, FR9.1, FR9.2, FR9.3. Relacionados: FR6.3, FR6.4; FR14.2, FR15.1 y FR15.2 (bloque 4).
* **Descripción:** El usuario registra el acuerdo comercial mensual de un cliente: la fecha de inicio (actual o futura), el plan o servicio contratado, la tarifa acordada, las condiciones de pago y el responsable interno, y adjunta el contrato si se firmó fuera de la plataforma. A partir de la fecha de inicio, el sistema factura al cliente cada mes de forma automática («Generar facturación recurrente», bloque 4).

**Interesados:**

* **Turbine (Administración):** Quiere que la relación comercial quede registrada una sola vez y que la facturación mensual se genere sola y sin errores.
* **Responsable interno del cliente:** Quiere dejar preparada la suscripción en pocos pasos y ver, antes de confirmar, qué se va a facturar.
* **Cliente:** Quiere que se le facture lo acordado y desde la fecha pactada.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente está dado de alta en el sistema.
* Existe una tabla de tarifas vigente (CU-310).

**Disparador:** Turbine cierra un acuerdo de suscripción con un cliente.

**Garantía de éxito:**

* La suscripción queda registrada con su fecha de inicio, plan, tarifa, condiciones de pago, responsable y, si lo hay, el contrato adjunto. El cliente pasa a *Activo*, la facturación recurrente queda programada para el día 1 de cada mes y el alta se registra en el historial del cliente.

**Garantía mínima:**

* Si falta un dato obligatorio o el usuario cancela, no se crea la suscripción ni se genera ninguna factura.

**Escenario principal de éxito:**

1. El usuario abre la ficha del cliente y elige crear una suscripción.
2. El sistema propone valores por defecto: el plan, la tarifa sugerida según el rango de facturación anual del cliente y el plazo de pago estándar.
3. El usuario indica la fecha de inicio, confirma o ajusta el plan, la tarifa y las condiciones de pago, y asigna el responsable interno.
4. El usuario adjunta, si existe, el contrato o acuerdo firmado.
5. El sistema comprueba que el cliente tiene los datos fiscales necesarios para facturar.
6. Si la fecha de inicio no es día 1, el sistema calcula el importe prorrateado de los días restantes del mes y lo muestra.
7. El sistema muestra un resumen de lo que va a ocurrir: el importe de la primera factura, la fecha de la siguiente mensualidad y las condiciones de pago.
8. El usuario confirma el alta.
9. El sistema registra la suscripción, cambia el estado del cliente a *Activo*, programa la facturación recurrente y anota el alta en el historial del cliente.

**Extensiones:**

* **3a. La fecha de inicio es futura:** El sistema registra la suscripción, deja al cliente en estado *Pendiente de activación* y no genera facturas ni recordatorios hasta esa fecha. La activación la realiza el sistema al llegar la fecha (CU-306).
* **3b. Se acuerda una excepción comercial (primer mes gratis o empezar a facturar el mes siguiente sin prorrateo):** El usuario indica la excepción, que queda registrada de forma explícita en la suscripción. Si supera el umbral configurado, queda pendiente de aprobación antes de activarse (FR16.5, bloque 4).
* **5a. Faltan datos fiscales del cliente:** El sistema bloquea la activación de la recurrencia, indica qué datos faltan y no permite confirmar el alta hasta que se completen.
* **Cualquier paso. El usuario cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Modificar una suscripción

* **ID / Código:** CU-302
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR9, FR9.1. Relacionados: FR8.1, FR11.
* **Descripción:** El usuario cambia las condiciones de pago, el responsable interno o los documentos adjuntos de una suscripción ya registrada. Los cambios se aplican a partir de ese momento y no alteran las facturas ya emitidas. La tarifa no se cambia aquí: se revisa con la revisión anual (CU-308 y CU-309).

**Interesados:**

* **Turbine (Administración):** Quiere mantener al día las condiciones de cada cliente sin perder el rastro de lo que había antes.
* **Responsable interno del cliente:** Quiere poder corregir o actualizar la suscripción en pocos pasos.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente tiene una suscripción activa, pausada o pendiente de activación.

**Disparador:** Cambian las condiciones acordadas con el cliente o hay que adjuntar un documento nuevo.

**Garantía de éxito:**

* La suscripción queda actualizada, el cambio se registra en el historial del cliente (quién, cuándo, valor anterior y nuevo) y las facturas ya emitidas no cambian.

**Garantía mínima:**

* Si los datos no son válidos o el usuario cancela, la suscripción se mantiene como estaba.

**Escenario principal de éxito:**

1. El usuario selecciona la suscripción del cliente y elige modificarla.
2. El sistema muestra los datos actuales de la suscripción.
3. El usuario cambia las condiciones de pago, el responsable interno o los documentos adjuntos.
4. El sistema valida los datos y muestra qué va a cambiar y desde cuándo.
5. El usuario confirma la modificación.
6. El sistema guarda los cambios y los registra en el historial del cliente.

**Extensiones:**

* **3a. El usuario intenta cambiar la tarifa:** El sistema indica que el cambio de tarifa se tramita mediante la revisión anual (CU-308) o como excepción comercial aprobada, y no permite cambiarla desde aquí.
* **3b. Se cambia el responsable interno:** El cambio de responsable del cliente sigue las reglas de transferencia del bloque 1 («Transferir cliente», FR2.1): las tareas pendientes pasan al nuevo responsable.
* **4a. Algún dato no es válido:** El sistema indica qué dato hay que corregir y no permite confirmar.
* **Cualquier paso. El usuario cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Pausar una suscripción

* **ID / Código:** CU-303
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR10. Relacionados: FR6, FR10.1.
* **Descripción:** El responsable pausa temporalmente la suscripción de un cliente por un acuerdo con él, indicando el motivo, la fecha de inicio y, si se conoce, la fecha prevista de reactivación. Durante la pausa no se generan mensualidades, pero el cliente sigue existiendo y sus facturas anteriores pendientes se siguen cobrando.

**Interesados:**

* **Turbine (Administración):** Quiere dejar de facturar durante la pausa sin perder al cliente ni las deudas que tenga.
* **Cliente:** Quiere no recibir facturas durante la pausa acordada.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente tiene una suscripción activa.

**Disparador:** Turbine acuerda con el cliente una pausa temporal del servicio.

**Garantía de éxito:**

* La suscripción queda pausada con su motivo y sus fechas, el cliente pasa a *Pausado*, no se generan mensualidades durante la pausa y la fecha prevista de reactivación queda programada para avisar al responsable.

**Garantía mínima:**

* Si no se indica el motivo, la suscripción no se pausa. La pausa nunca cancela las facturas pendientes anteriores.

**Escenario principal de éxito:**

1. El usuario selecciona la suscripción del cliente y elige pausarla.
2. El sistema solicita el motivo, la fecha de inicio de la pausa y la fecha prevista de reactivación.
3. El usuario introduce los datos y confirma.
4. El sistema marca la suscripción como pausada, cambia el estado del cliente a *Pausado*, deja de generar mensualidades desde la fecha de inicio de la pausa y lo anota en el historial del cliente.
5. El sistema mantiene activo el seguimiento de cobro de las facturas anteriores que sigan pendientes.
6. El sistema programa el aviso de la fecha prevista de reactivación (CU-306).

**Extensiones:**

* **2a. No se conoce la fecha de reactivación:** La pausa queda abierta, sin aviso programado, hasta que el responsable la reanude (CU-304).
* **3a. El usuario no indica el motivo:** El sistema muestra un mensaje indicando el dato que falta y no guarda la pausa.
* **Cualquier paso. El usuario cancela la operación:** La suscripción sigue activa.

---

## Caso de Uso: Reanudar una suscripción

* **ID / Código:** CU-304
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR10.1, FR10.4. Relacionados: FR6, FR14.2.
* **Descripción:** El responsable reanuda una suscripción pausada, bien porque el sistema le avisa de que ha llegado la fecha prevista de reactivación, bien porque decide reanudarla antes. Si la política configurada exige confirmación, la suscripción no se reanuda hasta que el responsable la confirme.

**Interesados:**

* **Turbine (Administración):** Quiere volver a facturar al cliente en cuanto termine la pausa, sin que el sistema dé por hecho algo importante si la política exige confirmarlo.
* **Responsable interno del cliente:** Quiere enterarse a tiempo de que la pausa termina.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente tiene una suscripción pausada.

**Disparador:** El sistema avisa al responsable de que ha llegado la fecha prevista de reactivación (CU-306), o el responsable decide reanudar la suscripción antes.

**Garantía de éxito:**

* La suscripción vuelve a estar activa, el cliente pasa a *Activo*, se reactiva la facturación recurrente y la reanudación queda registrada en el historial del cliente.

**Garantía mínima:**

* Si el responsable no confirma la reanudación, la suscripción sigue pausada y la tarea queda pendiente en su pantalla de trabajo diario.

**Escenario principal de éxito:**

1. El responsable abre la suscripción pausada desde el aviso o desde su pantalla de trabajo diario.
2. El sistema muestra los datos de la pausa: motivo, fecha de inicio y fecha prevista de reactivación.
3. El responsable confirma la reanudación.
4. El sistema reactiva la suscripción, cambia el estado del cliente a *Activo*, reactiva la facturación recurrente y lo anota en el historial del cliente.

**Extensiones:**

* **3a. El responsable decide ampliar la pausa:** Indica una nueva fecha prevista de reactivación; el sistema la guarda, reprograma el aviso y la suscripción sigue pausada.
* **Cualquier paso. El responsable cancela la operación:** La suscripción sigue pausada.

---

## Caso de Uso: Dar de baja una suscripción

* **ID / Código:** CU-305
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR10.2, FR10.3. Relacionados: FR6.3, FR23; FR32.1 (bloque 7).
* **Descripción:** El responsable registra la baja de un cliente que deja el servicio. El sistema deja de generar mensualidades a partir de la fecha efectiva de la baja, pero, si el cliente tiene facturas pendientes, mantiene el flujo de recobro hasta que se cobren. La baja del producto se gestiona fuera de esta aplicación; antes de tramitarla, el producto puede consultar si el cliente tiene deudas mediante la interfaz programática («Consultar deudas vía API», bloque 7).

**Interesados:**

* **Turbine (Administración):** Quiere que la baja no haga desaparecer las deudas y que no se sigan facturando meses al cliente.
* **Responsable interno del cliente:** Quiere saber si el cliente se va con facturas pendientes.
* **Cliente:** Quiere no recibir nuevas facturas después de su baja.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente tiene una suscripción activa o pausada.

**Disparador:** El cliente comunica que quiere darse de baja del servicio.

**Garantía de éxito:**

* La baja queda registrada con su fecha efectiva, el cliente pasa a *Baja solicitada* y el cierre de la recurrencia queda programado para esa fecha (CU-306). Si el cliente tiene facturas pendientes, el recobro continúa; cuando no quedan, el cliente pasa a *Baja*. La ficha y el historial del cliente se conservan.

**Garantía mínima:**

* La baja nunca elimina las facturas, los cobros pendientes ni el historial del cliente.

**Escenario principal de éxito:**

1. El usuario selecciona la suscripción del cliente y elige darla de baja.
2. El sistema solicita la fecha efectiva de la baja.
3. El usuario indica la fecha y confirma.
4. El sistema comprueba si el cliente tiene facturas pendientes de cobro y muestra el resultado.
5. El sistema registra la baja, cambia el estado del cliente a *Baja solicitada*, programa el cierre de la recurrencia en la fecha efectiva y lo anota en el historial del cliente.

**Extensiones:**

* **4a. El cliente tiene facturas pendientes de cobro:** El sistema avisa al usuario de que la baja no cancela esas facturas y mantiene activo su flujo de recobro (CU-503, bloque 5). El cliente permanece en *Baja solicitada* hasta que se cobren.
* **Cualquier paso. El usuario cancela la operación:** El sistema no registra la baja y la suscripción sigue como estaba.

---

## Caso de Uso: Procesar los cambios programados de las suscripciones

* **ID / Código:** CU-306
* **Actor principal:** Temporizador del Sistema (Scheduler).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR9.3, FR10.1, FR10.3, FR10.4. Relacionados: FR6.4, FR33.1.
* **Descripción:** Cada día, el sistema revisa las suscripciones con cambios programados: activa las que empiezan ese día, avisa a los responsables de las pausas que llegan a su fecha prevista de reactivación y cierra la recurrencia de las bajas cuya fecha efectiva ha llegado.

**Interesados:**

* **Turbine (Administración):** Quiere que las altas futuras, las pausas y las bajas se apliquen solas en su fecha, y que un fallo con un cliente no detenga el proceso para los demás.
* **Responsable interno del cliente:** Quiere enterarse de los cambios que requieren su decisión.

**Precondiciones:**

* Existen suscripciones con una fecha de inicio, de reactivación o de baja programada.

**Disparador:** Se ejecuta el proceso diario programado.

**Garantía de éxito:**

* Cada cambio programado para ese día se aplica una sola vez, los responsables reciben los avisos que les corresponden y la ejecución queda registrada con los elementos procesados y los fallos.

**Garantía mínima:**

* Si falla el cambio de un cliente, el sistema registra el error y continúa con el resto; ningún cambio se aplica dos veces.

**Escenario principal de éxito:**

1. El Temporizador del sistema inicia el proceso diario.
2. El sistema activa las suscripciones cuya fecha de inicio es hoy y cambia el estado de esos clientes de *Pendiente de activación* a *Activo*.
3. El sistema localiza las suscripciones pausadas cuya fecha prevista de reactivación es hoy y envía un aviso a sus responsables.
4. Si la política configurada no exige confirmación, el sistema reanuda esas suscripciones; si la exige, deja la reanudación pendiente para el responsable (CU-304).
5. El sistema cierra la recurrencia de las suscripciones cuya fecha efectiva de baja es hoy; los clientes sin facturas pendientes pasan a *Baja*.
6. El sistema registra la ejecución: cuántas suscripciones procesó, cuáles fallaron y el motivo.

**Extensiones:**

* **2a. Un cliente que debe activarse no tiene datos fiscales suficientes:** El sistema no activa su recurrencia, lo deja en *Pendiente de activación* y avisa al responsable indicando qué datos faltan.
* **2–5a. Falla el procesamiento de una suscripción:** El sistema registra el error de esa suscripción y continúa con las demás.

---

## Caso de Uso: Preparar la revisión anual de tarifas

* **ID / Código:** CU-307
* **Actor principal:** Temporizador del Sistema (Scheduler).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR11, FR11.3. Relacionados: FR26, FR28.1.
* **Descripción:** Al llegar la fecha de revisión anual configurada, el sistema prepara la campaña de revisión: genera la lista de clientes cuya facturación anual hay que revisar y la muestra en la pantalla de trabajo diario de sus responsables.

**Interesados:**

* **Turbine (Dirección):** Quiere que la revisión anual no dependa de que alguien se acuerde de hacerla.
* **Responsable interno del cliente:** Quiere saber qué clientes tiene que revisar sin buscarlos uno a uno.

**Precondiciones:**

* La fecha de revisión anual está configurada.
* Existen clientes con suscripción activa.

**Disparador:** Llega la fecha de revisión anual configurada.

**Garantía de éxito:**

* Cada cliente sujeto a revisión tiene una revisión pendiente asignada a su responsable, que la ve en su pantalla de trabajo diario.

**Garantía mínima:**

* Si el proceso falla, se registra el error y la revisión puede volver a lanzarse sin crear revisiones duplicadas.

**Escenario principal de éxito:**

1. El Temporizador del sistema inicia la revisión anual.
2. El sistema selecciona los clientes con suscripción activa que deben revisarse.
3. El sistema crea una revisión pendiente para cada cliente y la asigna a su responsable.
4. El sistema avisa a cada responsable y muestra sus revisiones pendientes en la pantalla de trabajo diario.
5. El sistema registra la ejecución con el número de revisiones creadas.

**Extensiones:**

* **2a. No hay clientes que revisar:** El sistema registra la ejecución sin crear revisiones.

---

## Caso de Uso: Registrar la facturación anual de un cliente

* **ID / Código:** CU-308
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR11, FR11.3, FR11.4. Relacionados: FR11.2.
* **Descripción:** El responsable introduce el dato de facturación anual verificado de un cliente, con su fuente y su fecha. El sistema lo compara con la tabla de rangos vigente y, si el cliente cambia de rango, propone la nueva tarifa, que queda pendiente de aprobación (CU-309).

**Interesados:**

* **Turbine (Dirección):** Quiere cobrar a cada cliente según su tamaño, sin que un dato mal interpretado cambie la tarifa de muchos clientes a la vez.
* **Responsable interno del cliente:** Quiere que el sistema le diga qué rango corresponde sin calcularlo a mano.
* **Cliente:** Quiere que el cambio de tarifa sea correcto y no se le aplique con efecto retroactivo.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente o tiene rol de Administrador.
* El cliente tiene una revisión anual pendiente (CU-307).

**Disparador:** El responsable atiende una revisión pendiente de su pantalla de trabajo diario.

**Garantía de éxito:**

* La facturación anual del cliente queda registrada con su fuente y su fecha, y la revisión queda completada sin cambios o con una propuesta de nueva tarifa pendiente de aprobación.

**Garantía mínima:**

* Si falta el dato de facturación, la revisión queda pendiente y la tarifa no cambia.

**Escenario principal de éxito:**

1. El responsable selecciona una revisión pendiente.
2. El sistema muestra el cliente, su rango y su tarifa actuales.
3. El responsable introduce la facturación anual verificada, con la referencia de la fuente y la fecha.
4. El sistema compara el dato con la tabla de rangos vigente y propone el rango y la tarifa que corresponden.
5. Como el rango cambia, el sistema deja la propuesta pendiente de aprobación y avisa al aprobador (CU-309).

**Extensiones:**

* **1a. El responsable corrige una revisión ya aplicada porque el dato era erróneo:** El responsable corrige el dato, que queda registrado junto al anterior. Si cambia el rango, el sistema genera una nueva propuesta de aprobación. El sistema no emite facturas retroactivas: cualquier regularización debe ser una acción explícita y aprobada.
* **3a. El dato de facturación anual no está disponible:** La revisión del cliente queda pendiente y sigue en la lista hasta que se introduzca el dato.
* **4a. El rango no cambia:** El sistema registra la revisión como completada y mantiene la tarifa actual.

---

## Caso de Uso: Aprobar un cambio de tarifa

* **ID / Código:** CU-309
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR11.1, FR11.3. Relacionados: FR16.2 (bloque 4), FR26.
* **Descripción:** El aprobador revisa la propuesta de cambio de tarifa que ha generado la revisión anual de un cliente y la aprueba o la rechaza. Si la aprueba, la nueva tarifa se aplica desde su fecha de efecto, sin modificar las facturas ya emitidas.

**Interesados:**

* **Turbine (Dirección):** Quiere una validación humana antes de cambiar lo que se va a facturar a un cliente.
* **Responsable interno del cliente:** Quiere saber si la propuesta se ha aprobado o rechazado, y por qué.
* **Cliente:** Quiere que la nueva tarifa solo se aplique desde la fecha de efecto.

**Precondiciones:**

* El usuario ha iniciado sesión con rol de Administrador.
* Existe una propuesta de cambio de tarifa pendiente de aprobación (CU-308).

**Disparador:** El aprobador recibe el aviso de una propuesta de cambio de tarifa pendiente.

**Garantía de éxito:**

* La nueva tarifa se aplica a la suscripción desde su fecha de efecto y queda registrado quién aprobó el cambio, cuándo, y los precios anterior y nuevo. Las facturas ya emitidas no cambian.

**Garantía mínima:**

* Sin aprobación, la tarifa del cliente no cambia.

**Escenario principal de éxito:**

1. El aprobador abre la propuesta pendiente desde el aviso o desde su pantalla de trabajo diario.
2. El sistema muestra la facturación anual introducida, su fuente y su fecha, y el rango y la tarifa actuales y propuestos.
3. El aprobador indica la fecha de efecto y aprueba la propuesta.
4. El sistema aplica la nueva tarifa a la suscripción desde la fecha de efecto, sin modificar las facturas ya emitidas.
5. El sistema registra quién aprobó el cambio, cuándo, y los precios anterior y nuevo, y notifica la resolución al responsable del cliente.

**Extensiones:**

* **3a. El aprobador rechaza la propuesta:** El aprobador indica el motivo; la tarifa no cambia, el rechazo queda registrado y el sistema notifica al responsable el comentario del aprobador.
* **Cualquier paso. El aprobador cancela la operación:** La propuesta sigue pendiente.

---

## Caso de Uso: Configurar la tabla de tarifas por rangos

* **ID / Código:** CU-310
* **Actor principal:** Superadministrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR11.2. Relacionados: FR3.3.
* **Descripción:** El superadministrador mantiene la tabla de rangos de facturación anual y el precio mensual de cada rango, con su fecha de efecto. Los cambios se aplican a las nuevas altas y revisiones, pero no reescriben las facturas ya emitidas.

**Interesados:**

* **Turbine (Dirección):** Quiere poder cambiar los rangos y los precios sin tocar el código (hoy pueden ser tres rangos y mañana cinco).
* **Turbine (Administración):** Quiere que el histórico de facturas no cambie cuando se modifiquen las tarifas.

**Precondiciones:**

* El usuario ha iniciado sesión con rol de Superadministrador.

**Disparador:** La dirección de Turbine decide cambiar los rangos o los precios de sus planes.

**Garantía de éxito:**

* La nueva versión de la tabla queda guardada con su fecha de efecto, la versión anterior se conserva y el cambio queda registrado en la auditoría.

**Garantía mínima:**

* Si la tabla no es válida, no se guarda ningún cambio.

**Escenario principal de éxito:**

1. El superadministrador abre la configuración de tarifas.
2. El sistema muestra la tabla vigente, con sus rangos y precios.
3. El superadministrador añade, modifica o elimina rangos y precios e indica la fecha de efecto.
4. El sistema valida la tabla.
5. El superadministrador confirma los cambios.
6. El sistema guarda la nueva versión con su fecha de efecto, conserva la anterior y registra el cambio en la auditoría.

**Extensiones:**

* **4a. La tabla no es válida (por ejemplo, dos rangos se solapan):** El sistema indica el error y no permite confirmar hasta que se corrija.
* **Cualquier paso. El superadministrador cancela la operación:** El sistema mantiene la tabla vigente sin cambios.

---

## Caso de Uso: Gestionar el catálogo de conceptos

* **ID / Código:** CU-311
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR12, FR12.2. Relacionados: FR12.1, FR31.2.
* **Descripción:** El administrador mantiene una lista ligera de conceptos que se facturan a menudo (instalación, consultoría, formación, hardware…), con una descripción, un precio orientativo y un impuesto por defecto, para no escribirlos cada vez de una forma distinta. Al crear una factura, el usuario puede ajustar la descripción y el precio del concepto (FR12.1, «Emitir factura puntual», bloque 4). Los cambios en el catálogo no afectan a las facturas ya creadas.

**Interesados:**

* **Turbine (Administración):** Quiere facturar más rápido y de forma homogénea.
* **Turbine (Dirección):** Quiere que las facturas antiguas no cambien al modificar el catálogo.

**Precondiciones:**

* El usuario ha iniciado sesión con rol de Administrador.
* Los impuestos disponibles están configurados en el sistema.

**Disparador:** Hace falta un concepto nuevo o actualizar uno existente.

**Garantía de éxito:**

* El concepto queda creado o modificado con su descripción, su precio orientativo y su impuesto por defecto, y está disponible al crear facturas. Las facturas ya creadas no cambian.

**Garantía mínima:**

* Si faltan datos obligatorios, el concepto no se guarda.

**Escenario principal de éxito:**

1. El administrador abre el catálogo de conceptos.
2. El sistema muestra los conceptos existentes.
3. El administrador crea un concepto nuevo con su descripción, su precio orientativo y su impuesto por defecto.
4. El sistema valida los datos y guarda el concepto.
5. El concepto queda disponible para las facturas puntuales y para las suscripciones.

**Extensiones:**

* **3a. El administrador modifica un concepto existente:** El sistema guarda el cambio; las facturas ya creadas con ese concepto mantienen los datos con los que se emitieron.
* **4a. Faltan datos obligatorios:** El sistema indica qué dato falta y no guarda el concepto.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Trazabilidad del bloque 3

| Requisito | Caso de uso |
|---|---|
| FR9 | CU-301, CU-302 |
| FR9.1 | CU-301, CU-302 |
| FR9.2 | CU-301 |
| FR9.3 | CU-301 (3a), CU-306 |
| FR9.4 | «Generar facturación recurrente» (bloque 4) |
| FR10 | CU-303 |
| FR10.1 | CU-304, CU-306 |
| FR10.2 | CU-305 |
| FR10.3 | CU-305, CU-306 |
| FR10.4 | CU-304, CU-306 |
| FR11 | CU-307, CU-308 |
| FR11.1 | CU-309 |
| FR11.2 | CU-310 |
| FR11.3 | CU-307, CU-308, CU-309 |
| FR11.4 | CU-308 (1a) |
| FR12 | CU-311 |
| FR12.1 | «Emitir factura puntual» (bloque 4) |
| FR12.2 | CU-311 |

---

## Dudas para el cliente

Puntos que el caso no aclara. Se propone una interpretación, a la espera de confirmarla en la próxima revisión.

* **D-301. ¿Qué se factura al reanudar una suscripción a mitad de mes?** Propuesta: aplicar el mismo prorrateo que en un alta a mitad de mes (FR15.1).
* **D-302. ¿Puede aprobar un cambio de tarifa la misma persona que registró el dato de facturación?** Propuesta: no, aplicando la separación de funciones de FR16.2.
* **D-303. ¿Quién gestiona el catálogo de conceptos?** FR12 no lo dice. Propuesta: el Administrador.
* **D-304. ¿Puede el producto (Brain) solicitar la baja por la interfaz programática?** El cliente lo pidió en la reunión ampliada (p. 10) y estaba en los FR, pero se quitó al reorganizar el bloque 7. Propuesta: recuperarlo como requisito del bloque 7.
