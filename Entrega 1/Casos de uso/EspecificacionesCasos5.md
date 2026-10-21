# Especificación de Casos de Uso - Bloque 5: Seguimiento de cobros, conciliación e impagos

---

## Caso de Uso: Procesar y conciliar cobros entrantes

* **ID / Código:** CU-501
* **Actor principal:** Pasarela de Pagos (Stripe simulado) / Administrador (al cargar movimientos de Revolut u otras cuentas bancarias).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Descripción:** El sistema recibe un evento de pago desde Stripe (simulado) o procesa movimientos bancarios cargados desde otras fuentes (como Revolut u otras cuentas bancarias). El sistema debe verificar que el evento de pago no esté duplicado, buscar la factura correspondiente comprobando su referencia e importe, actualizar el estado de la factura y registrar qué movimiento y qué regla justificaron el cambio de estado.

**Interesados:**

* **Turbine (Administración):** Quiere comprobar qué facturas han sido pagadas de forma automatizada, no contabilizar dos veces un mismo cobro y detectar pagos incompletos o con importes erróneos.
* **Responsable interno del cliente:** Quiere conocer cuándo se ha confirmado un cobro para que se detenga el envío de recordatorios al cliente.
* **Cliente:** Quiere que su pago se refleje correctamente y dejar de recibir avisos de cobro sobre facturas ya abonadas.

**Precondiciones:**

* Existen facturas emitidas pendientes de cobro registradas en el sistema.
* La integración simulada con Stripe o el módulo de carga de movimientos bancarios está operativo.

**Disparador:** Se recibe una notificación de evento de cobro desde Stripe (simulado), se ejecuta una consulta de pagos o el Administrador carga movimientos procedentes de Revolut u otra cuenta bancaria.

**Garantía de éxito:**

* El cobro queda registrado una única vez, la factura pasa a estado *Pagada*, se detiene el flujo de recordatorios de esa factura y queda registrado el movimiento y la regla que justificó que se marcara como *Pagada*.

**Garantía mínima:**

* No se contabiliza dos veces un mismo evento de pago duplicado y, si la referencia no es clara o el importe no coincide, la factura no se marca erróneamente como *Pagada*, quedando el cobro pendiente de conciliación manual o la factura como *Parcialmente pagada*.

**Escenario principal de éxito:**

1. El actor envía o carga en el sistema los datos del movimiento de cobro (identificador del evento, importe, fecha, moneda, concepto y referencia).
2. El sistema comprueba que el evento de pago no se ha recibido ni contabilizado previamente.
3. El sistema registra el movimiento de cobro.
4. El sistema busca entre las facturas pendientes aquella cuya referencia y cuyo importe coincidan exactamente con los del movimiento de cobro.
5. El sistema asocia el movimiento de cobro a la factura y cambia el estado de la factura a *Pagada*.
6. El sistema registra en la trazabilidad qué movimiento y qué regla automática justificaron que la factura se marcara como *Pagada*.
7. El sistema desactiva los recordatorios de vencimiento o avisos de impago programados para esa factura y envía una notificación interna de pago confirmado al responsable del cliente.

**Extensiones:**

* **2a. El evento de pago se recibe duplicado:** El sistema detecta que el evento ya fue procesado anteriormente, descarta su contabilización para no duplicar el cobro, registra la incidencia en el log técnico y finaliza.
* **4a. El cobro no tiene una referencia clara o existen varias facturas con el mismo importe:** El sistema no asocia el cobro automáticamente, deja el movimiento en estado *Pendiente de conciliación manual* y genera una alerta en la pantalla de trabajo diario para su revisión por una persona.
* **4b. La referencia coincide con la factura, pero el cliente paga de menos o de más:** El sistema asocia el movimiento a la factura, cambia el estado de la factura a *Parcialmente pagada*, calcula e indica la cantidad exacta que falta por cobrar (o la diferencia a revisar), registra la trazabilidad del movimiento y alerta al responsable interno.

---

## Caso de Uso: Gestionar conciliación manual y reversión de cobros

* **ID / Código:** CU-502
* **Actor principal:** Administrador / Usuario Operativo (sobre sus clientes asignados).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Descripción:** El usuario administrativo revisa los cobros que han quedado pendientes de conciliación manual para asociarlos a la factura correspondiente, o bien anula un cobro que fue asociado por error a una factura, dejando registrado qué usuario realizó la operación y el motivo.

**Interesados:**

* **Usuario Operativo / Administrador:** Quiere resolver los pagos dudosos o incompletos y poder deshacer errores de conciliación de forma controlada.
* **Turbine (Auditoría):** Quiere mantener una trazabilidad completa de quién concilió manualmente un pago o quién anuló un cobro asociado por error y por qué motivo.

**Precondiciones:**

* El usuario está autenticado en el sistema y tiene permisos de Administrador o es el Usuario Operativo asignado al cliente.
* Existe un movimiento en estado *Pendiente de conciliación manual* o una factura con un cobro asociado.

**Disparador:** El usuario selecciona un cobro pendiente de conciliar en su pantalla de trabajo diario o solicita anular un cobro dentro de una factura.

**Garantía de éxito:**

* El cobro queda correctamente conciliado con la factura (marcándola como *Pagada* o *Parcialmente pagada*) o la asociación errónea queda anulada restaurando el saldo pendiente, quedando registrado el usuario autor y el motivo en el historial de auditoría.

**Garantía mínima:**

* Si el usuario cancela la operación o no introduce el motivo obligatorio al anular un cobro, no se modifica el estado de la factura ni se borra ningún movimiento de cobro.

**Escenario principal de éxito:**

1. El usuario consulta los cobros en estado *Pendiente de conciliación manual* y selecciona uno de ellos.
2. El sistema muestra los datos del movimiento (importe, fecha, moneda, concepto y referencia) y las posibles facturas pendientes.
3. El usuario selecciona la factura correspondiente y confirma la conciliación manual.
4. El sistema verifica que el importe del cobro coincide con el importe pendiente de la factura.
5. El sistema vincula el movimiento a la factura, cambia el estado de la factura a *Pagada* y suspende los recordatorios pendientes.
6. El sistema registra qué movimiento y qué usuario justificaron que la factura se marcara como *Pagada*.

**Extensiones:**

* **1a. El usuario desea anular un cobro asociado por error a una factura:**
  1. El usuario selecciona la factura afectada y solicita deshacer o anular la conciliación del cobro.
  2. El sistema solicita obligatoriamente el motivo de la anulación.
  3. El usuario introduce el motivo y confirma la operación.
  4. El sistema desvincula el cobro de la factura (manteniendo el evento del movimiento sin borrarlo), recalcula el importe pendiente de cobro de la factura y actualiza su estado (*Enviada*, *Vencida* o *Parcialmente pagada*).
  5. El sistema deja registrado en la trazabilidad quién realizó la anulación, cuándo y el motivo.
* **4a. El importe del cobro que se concilia manualmente es menor o mayor al importe de la factura:**
  1. El sistema informa al usuario de la discrepancia de importe.
  2. El usuario confirma la asociación del pago incompleto o con descuadre.
  3. El sistema cambia el estado de la factura a *Parcialmente pagada*, indica la cantidad que falta por cobrar y registra al usuario que justificó la operación.
* **Cualquier paso. El usuario cancela la operación:** El sistema detiene el proceso sin aplicar cambios en el movimiento ni en la factura.

---

## Caso de Uso: Ejecutar comunicaciones de facturación y seguimiento de impagos

* **ID / Código:** CU-503
* **Actor principal:** Temporizador del Sistema (Scheduler) / Usuario Operativo o Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Descripción:** El sistema automatiza el envío por correo electrónico del PDF de la factura al emitirse o de forma periódica programada, envía un recordatorio previo al vencimiento (por defecto el día 3) y un aviso en la fecha de vencimiento (por defecto el día 5 o según el plazo del cliente), y tras vencer activa el flujo de impago enviando avisos periódicos (cada 2 o 3 días configurables) adaptados al calendario laboral y festivos hasta que se detecte el cobro o se intervenga manualmente.

**Interesados:**

* **Turbine (Administración):** Quiere automatizar el envío de facturas y la persecución de cobros pendientes respetando los plazos pactados con cada cliente y el calendario laboral.
* **Cliente:** Quiere recibir el PDF de su factura y recordatorios claros con los datos de contacto de soporte en su idioma configurado.

**Precondiciones:**

* La factura ha sido emitida y su documento PDF corporativo ha sido generado.
* El cliente tiene al menos un contacto de facturación activo y existen plantillas de correo y calendario laboral/festivos configurados en el sistema.

**Disparador:** Se emite una factura, se cumple una programación de envío periódico o el Temporizador del sistema ejecuta la revisión diaria de vencimientos e impagos.

**Garantía de éxito:**

* Las comunicaciones (PDF de factura, recordatorio previo, aviso de vencimiento o reclamación de impago) se envían por correo electrónico a los destinatarios correspondientes en día laborable, y queda registrado el intento, resultado, fecha y destinatario.

**Garantía mínima:**

* Si un correo falla o rebota, el sistema no marca la factura como correctamente notificada, no duplica la factura, aísla el fallo para no detener el envío al resto de clientes y genera una incidencia de entrega avisando al responsable interno.

**Escenario principal de éxito:**

1. Al emitirse la factura, el sistema envía automáticamente por correo electrónico el PDF de la factura al contacto de facturación del cliente, registra el envío y marca la factura como *Enviada*.
2. El sistema supervisa diariamente el plazo de vencimiento de la factura (fijado por defecto para el día 5 o según las condiciones particulares pactadas con el cliente).
3. Al llegar el día del recordatorio previo (programado por defecto para el día 3) sin haberse cobrado la factura, el sistema comprueba el calendario laboral y envía un correo de recordatorio previo al cliente.
4. Al llegar la fecha de vencimiento (día 5 por defecto o plazo particular del cliente) sin haberse registrado el cobro, el sistema envía el aviso de vencimiento al cliente.
5. Si concluye la fecha de vencimiento sin detectarse el pago, el sistema cambia el estado de la factura a *Vencida* y activa el flujo de impago.
6. De forma periódica (configurable cada 2 o 3 días), el sistema verifica que la factura continúa impagada y sin pausa activa, comprueba que sea día laborable y no festivo, y envía un aviso de impago al cliente junto con una notificación interna al responsable del cliente.
7. El sistema repite el paso 6 hasta que se registra el cobro de la factura o se produce una intervención manual sobre el estado de la factura.

**Extensiones:**

* **1a. Programación de envío periódico del PDF de la factura:** El usuario configura el envío periódico por correo electrónico del PDF de la factura a los destinatarios seleccionados; el sistema almacena la programación y ejecuta el envío del PDF en las fechas pautadas.
* **3a / 4a / 6a. La fecha del recordatorio o aviso cae en fin de semana o día festivo:** El sistema pospone el envío de la comunicación y lo ejecuta en el siguiente día laborable según el calendario configurado.
* **1b / 3b / 4b / 6b. El correo electrónico rebota o falla en la entrega:**
  1. El sistema registra el resultado fallido del intento y no marca la factura como notificada.
  2. El sistema abre una incidencia de entrega y envía una alerta interna al responsable del cliente.
  3. El usuario responsable corrige el contacto o selecciona otro contacto de facturación y solicita reenviar la comunicación sin que el sistema genere una factura nueva ni cambie su numeración.
* **2a. El cliente tiene condiciones de pago pactadas (ej. vencimiento a 30 días):** El sistema hace prevalecer el plazo de vencimiento particular del cliente sobre el valor general por defecto de 5 días.

---

## Caso de Uso: Gestionar disputa y pausa de seguimiento de factura

* **ID / Código:** CU-504
* **Actor principal:** Usuario Operativo (responsable del cliente) / Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Descripción:** El usuario responsable de un cliente marca una factura pendiente como *En disputa* o *Seguimiento pausado* indicando un motivo y una fecha de revisión, con el fin de suspender temporalmente los recordatorios automáticos al cliente y recibir una alerta interna cuando venza el periodo de pausa.

**Interesados:**

* **Responsable del cliente:** Quiere evitar que el sistema envíe correos automáticos molestando al cliente mientras se investiga una incidencia o reclamación, pero asegurando que el sistema le avise al terminar la pausa.
* **Turbine (Administración):** Quiere que la factura siga figurando internamente como pendiente de cobro y que quede auditado quién pausó el seguimiento y por qué motivo.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente correspondiente o tiene rol de Administrador.
* La factura está emitida y pendiente de cobro (*Enviada*, *Vencida* o *Parcialmente pagada*).

**Disparador:** El usuario responsable selecciona una factura y solicita marcarla como *En disputa* o *Seguimiento pausado*.

**Garantía de éxito:**

* La factura queda en estado *En disputa* o *Seguimiento pausado*, se suspenden temporalmente los recordatorios automáticos hacia el cliente, se registra la operación en auditoría y se genera una alerta interna al alcanzar la fecha de revisión.

**Garantía mínima:**

* Si no se indican el motivo o la fecha de revisión, el sistema no altera el estado de la factura ni suspende los recordatorios.

**Escenario principal de éxito:**

1. El usuario responsable accede a la factura reclamada por el cliente y selecciona la opción de cambiar su estado a *En disputa* o *Seguimiento pausado*.
2. El sistema solicita el motivo de la pausa y la fecha de revisión.
3. El usuario introduce el motivo, establece la fecha de revisión y confirma la operación.
4. El sistema cambia el estado de la factura a *En disputa* (o *Seguimiento pausado*), manteniendo la factura contabilizada como pendiente de cobro a nivel interno.
5. El sistema registra en el log de auditoría el usuario autor, la fecha, el motivo y la fecha de revisión.
6. El sistema suspende temporalmente el envío de recordatorios de pago y avisos de impago al cliente para esa factura.
7. Al llegar la fecha de revisión establecida, el sistema genera una alerta interna para el responsable indicando que ha vencido el periodo de pausa y que la factura debe ser revisada.

**Extensiones:**

* **3a. El usuario omite el motivo o introduce una fecha de revisión no válida:** El sistema muestra un mensaje de error indicando el dato que falta y no guarda el cambio hasta que se complete correctamente.
* **7a. El usuario resuelve la disputa o reanuda el seguimiento:** El usuario cierra la disputa indicando la resolución; el sistema restablece el estado de la factura (*Enviada* o *Vencida*) y reactiva el envío de recordatorios automáticos.
* **Cualquier paso. Se detecta el cobro de la factura durante la pausa:** El sistema concilia el pago (según CU-501), cambia el estado de la factura a *Pagada* y desactiva la alerta de revisión pendiente.

---

## Caso de Uso: Tramitar declaración de deuda incobrable

* **ID / Código:** CU-505
* **Actor principal:** Administrador o Usuario Operativo (solicitante) / Administrador o Superadministrador (aprobador).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Descripción:** Ante una deuda que no se va a poder cobrar, un usuario administrativo inicia un procedimiento controlado indicando el motivo para declarar la factura como *Incobrable*, requiriendo la aprobación de un rol superior antes de aplicar el cambio de estado contable.

**Interesados:**

* **Turbine (Dirección / Contabilidad):** Quiere impedir que un usuario normal perdone una deuda sin control ni aprobación, y necesita distinguir contablemente una factura *Incobrable* de una factura *Pagada*, *Anulada* o *Rectificada*.

**Precondiciones:**

* La factura se encuentra emitida e impagada (*Vencida*, *En disputa* o *Parcialmente pagada*).
* Los usuarios solicitante y aprobador están autenticados y cuentan con los permisos requeridos.

**Disparador:** El usuario inicia el procedimiento administrativo para declarar una factura vencida como *Incobrable*.

**Garantía de éxito:**

* La solicitud es aprobada, la factura cambia su estado a *Incobrable* (nunca a *Pagada*), se detienen definitivamente los recordatorios de cobro al cliente y todo el flujo (solicitud, motivo y aprobación) queda registrado en auditoría.

**Garantía mínima:**

* Si la solicitud es rechazada o queda pendiente, la factura conserva su estado de deuda anterior y queda constancia del intento o del motivo de rechazo en el historial.

**Escenario principal de éxito:**

1. El usuario selecciona una factura impagada y solicita declararla como *Incobrable*.
2. El sistema solicita el motivo detallado que justifica la incobrabilidad de la deuda.
3. El usuario introduce el motivo, adjunta documentación de respaldo si procede y confirma la solicitud.
4. El sistema deja la operación pendiente de aprobación, comprueba las reglas de separación de funciones (para que el solicitante no apruebe su propia solicitud cuando el umbral lo exija) y envía una notificación a la bandeja de aprobaciones del Administrador o Superadministrador.
5. El usuario aprobador accede a su bandeja de aprobaciones, revisa el motivo y el historial de la factura, y aprueba la solicitud.
6. El sistema cambia el estado de la factura a *Incobrable* y detiene de forma definitiva el envío de avisos de impago al cliente.
7. El sistema registra en el log de auditoría quién solicitó la acción, el motivo, quién la aprobó y la fecha, y notifica la resolución al usuario solicitante.

**Extensiones:**

* **5a. El aprobador rechaza la declaración de deuda incobrable:**
  1. El aprobador rechaza la solicitud e introduce un comentario explicando el motivo del rechazo.
  2. El sistema mantiene la factura en su estado previo (*Vencida* o *En disputa*), registra el rechazo en la auditoría y envía una notificación interna al solicitante indicando el comentario del aprobador.
* **4a. El aprobador asignado se encuentra de vacaciones o ausente:** Un Administrador reasigna la aprobación pendiente a un aprobador suplente, quedando registrada la reasignación en la auditoría.