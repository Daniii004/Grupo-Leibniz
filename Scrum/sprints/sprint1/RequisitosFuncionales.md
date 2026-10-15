# Requisitos Funcionales (RF)

## 1. Gestión de usuarios, roles y asignaciones

- **FR1.** El sistema permitirá a los administradores registrar usuarios.
  - **FR1.1.** El sistema validará que la dirección de correo electrónico pertenezca al dominio corporativo autorizado (`@turbine`).
    - *Dependencias:* FR1.
  - **FR1.2.** El sistema permitirá a los usuarios solicitar el restablecimiento de su contraseña mediante un enlace temporal.
    - *Dependencias:* FR1.
  - **FR1.3.** El sistema permitirá a los administradores desactivar cuentas de usuario, conservando el historial de acciones.
    - *Dependencias:* FR1.
  - **FR1.4.** El sistema invalidará automáticamente las sesiones activas de un usuario en el momento de desactivar su cuenta.
    - *Dependencias:* FR1.3.
  - **FR1.5.** El sistema cerrará automáticamente la sesión del usuario tras un periodo de inactividad.
    - *Dependencias:* FR1.
  - **FR1.6.** El sistema no permitirá el autorregistro: solo un superadministrador o un administrador con permiso podrá crear o invitar a nuevos usuarios.
    - *Dependencias:* FR1.

- **FR2.** El sistema permitirá a los administradores asignar clientes específicos a usuarios disponibles.
  - *Dependencias:* FR1.
  - **FR2.1.** El sistema permitirá reasignar un cliente a otro usuario, transfiriendo las tareas pendientes y conservando la autoría de las acciones del responsable anterior.
    - *Dependencias:* FR2.
  - **FR2.2.** El sistema permitirá asignar temporalmente a otro usuario los clientes de un usuario, indicando las fechas de inicio y fin del periodo (por ejemplo, durante las vacaciones o una baja médica).
    - *Dependencias:* FR2.
  - **FR2.3.** Al llegar la fecha de fin, el sistema retirará automáticamente el acceso temporal y devolverá el cliente a su responsable inicial.
    - *Dependencias:* FR2.2.

- **FR2.0.1.** El sistema aplicará un modelo de autorización centralizado basado en roles (superadministrador, administrador y usuario operativo) que determine las acciones permitidas a cada usuario.
  - *Dependencias:* FR1.
  - **FR2.0.1.** El sistema restringirá al usuario operativo a los clientes que tenga asignados, permitiéndole consultar y modificar su información operativa, crear facturas si su permiso lo permite, seguir los cobros y descargar documentos.
    - *Dependencias:* FR2, FR31.
  - **FR2.0.2.** El sistema impedirá que un usuario operativo se asigne clientes a sí mismo, modifique tarifas globales, cree series de facturación o consulte el panel de control financiero global.
    - *Dependencias:* FR31.1.
  - **FR2.0.3.** El sistema reservará al superadministrador la configuración global profunda (series de facturación, tarifas, plazos y reglas de automatización) y las acciones administrativas de mayor impacto.
    - *Dependencias:* FR31.

## 2. Gestión de clientes y contactos

- **FR3.** El sistema permitirá dar de alta y gestionar clientes, diferenciando sus datos comerciales de sus datos fiscales.
  - **FR3.1.** El sistema almacenará por separado los datos comerciales (nombre, sector, ubicación y etiquetas) y los datos fiscales (razón social, NIF, domicilio, país e idioma).
    - *Dependencias:* FR3.
  - **FR3.2.** El sistema permitirá registrar para cada cliente su moneda de trabajo y un identificador fiscal y un domicilio que no estén atados al formato español (por ejemplo, sin exigir provincia).
    - *Dependencias:* FR3.1.

- **FR4.** El sistema permitirá registrar varias personas de contacto para cada empresa cliente.
  - *Dependencias:* FR3.
  - **FR4.1.** El sistema permitirá asociar una misma persona de contacto o un mismo gestor externo a varias empresas clientes.
    - *Dependencias:* FR4.
  - **FR4.2.** El sistema permitirá asignar a cada persona de contacto un rol diferenciado dentro de la empresa cliente (por ejemplo, comercial, administración o cobros).
    - *Dependencias:* FR4.
  - **FR4.3.** El sistema permitirá desactivar personas de contacto de una empresa cliente, conservando el historial de las facturas enviadas a sus direcciones de correo electrónico.
    - *Dependencias:* FR4, FR17.4.
  - **FR4.4.** El sistema avisará antes de desactivar o eliminar el único contacto de facturación de un cliente activo, ya que no habría destinatario para sus facturas.
    - *Dependencias:* FR4.2, FR4.3.
  - **FR4.5.** El sistema registrará para cada persona de contacto los canales de comunicación que admite (por ejemplo, WhatsApp) y no utilizará los datos de facturación para fines comerciales ajenos a la gestión administrativa.
    - *Dependencias:* FR4, FR24.1.

- **FR5.** El sistema controlará el estado de cada cliente (por ejemplo, *Activo*, *Pausado*, *Baja* o *Bloqueado*) a lo largo de su ciclo de vida.
  - *Dependencias:* FR3.
  - **FR5.1.** El sistema no permitirá registrar ni reactivar un cliente si su NIF ya existe en la base de datos, a fin de evitar duplicados.
    - *Dependencias:* FR5.
  - **FR5.2.** El sistema permitirá a los administradores fusionar dos clientes duplicados y unificar todo su historial.
    - *Dependencias:* FR5.

  - **FR5.3.** El sistema permitirá además los estados *Pendiente de activación* y *Baja solicitada*, y mostrará de forma explícita las consecuencias de cada cambio de estado antes de confirmarlo.
    - *Dependencias:* FR5.
  - **FR5.4.** El sistema bloqueará la activación de la recurrencia de un cliente que no tenga datos fiscales suficientes e indicará qué datos faltan.
    - *Dependencias:* FR5, FR3.1, FR13.2.
- **FR6.** El sistema permitirá importar simultáneamente una lista de clientes mediante un archivo CSV.
  - *Dependencias:* FR3.
  - **FR6.1.** Antes de guardar los datos, el sistema mostrará una vista previa del archivo para revisarlos y avisará si algún NIF ya existe.
    - *Dependencias:* FR6.
  - **FR6.2.** Al finalizar la importación, el sistema mostrará un informe con los clientes guardados y aquellos cuya importación haya producido errores.
    - *Dependencias:* FR6.
  - **FR6.3.** El sistema permitirá mapear las columnas del archivo con los campos del cliente y decidir, según la política definida, si se corrige el archivo o se importan únicamente las filas válidas.
    - *Dependencias:* FR6.1.

- **FR7.** El sistema mostrará el historial de cada cliente, incluida la fecha de alta, las facturas y los cobros, entre otros datos.
  - *Dependencias:* FR3.
  - **FR7.1.** El sistema mostrará el historial del cliente como una línea temporal de eventos de negocio (alta, cambios de responsable y de tarifa, facturas emitidas, pagos, incidencias, pausas, baja y reactivación).
    - *Dependencias:* FR7.

## 3. Suscripciones (relaciones comerciales), tarifas y catálogo

- **FR8.** El sistema permitirá crear y gestionar suscripciones mensuales para los clientes, indicando la fecha de inicio, la tarifa acordada, las condiciones de pago y la persona responsable.
  - *Dependencias:* FR3.
  - **FR8.1.** El sistema permitirá adjuntar los contratos o acuerdos firmados a la ficha de cada suscripción.
    - *Dependencias:* FR8.
  - **FR8.2.** El sistema almacenará para cada cliente la fecha de inicio, el servicio o plan contratado y la tarifa acordada.
    - *Dependencias:* FR8.
  - **FR8.3.** El sistema permitirá registrar suscripciones con fecha de inicio futura, sin generar facturas ni recordatorios de cobro hasta esa fecha y mostrando al cliente como *Pendiente de activación*.
    - *Dependencias:* FR8, FR5.3.
  - **FR8.4.** El sistema generará las facturas recurrentes a partir de los conceptos configurados en la suscripción.
    - *Dependencias:* FR8, FR11, FR13.2.

- **FR9.** El sistema permitirá pausar temporalmente una suscripción e indicar el motivo y la fecha prevista para reanudarla.
  - *Dependencias:* FR8.
  - **FR9.1.** El sistema avisará a la persona responsable cuando llegue la fecha prevista para reactivar una suscripción en pausa.
    - *Dependencias:* FR9.
  - **FR9.2.** El sistema permitirá dar de baja una suscripción, dejando de generar nuevas cuotas sin eliminar las deudas ni los cobros pendientes.
    - *Dependencias:* FR8.
  - **FR9.3.** El sistema cerrará la recurrencia en la fecha efectiva de baja y, si el cliente tiene facturas pendientes, mantendrá activo el flujo de recobro de las facturas anteriores.
    - *Dependencias:* FR9.2, FR22.
  - **FR9.4.** Cuando la política configurada así lo exija, el sistema no reactivará automáticamente una suscripción en pausa y requerirá la confirmación de la persona responsable.
    - *Dependencias:* FR9.1.

- **FR10.** El sistema gestionará la revisión anual de las tarifas y propondrá cambios de precio según la facturación del cliente.
  - *Dependencias:* FR8.
  - **FR10.1.** El sistema exigirá que una persona responsable apruebe el nuevo precio antes de aplicarlo y garantizará que no se modifiquen las facturas emitidas anteriormente.
    - *Dependencias:* FR10.
  - **FR10.2.** El sistema almacenará los rangos de facturación anual y sus precios como configuración editable por el superadministrador, con fecha de efecto, sin modificar las facturas ya emitidas.
    - *Dependencias:* FR10.
  - **FR10.3.** El sistema listará los clientes cuya facturación anual deba revisarse, permitirá introducir el dato manualmente (con referencia de la fuente y fecha) y dejará la revisión pendiente si el dato no está disponible. Al aprobarse un cambio de rango, registrará quién lo aprobó, cuándo y los precios anterior y nuevo.
    - *Dependencias:* FR10, FR10.1.
  - **FR10.4.** El sistema no emitirá facturas retroactivas por la corrección de una revisión anual; cualquier regularización será una acción explícita y aprobada.
    - *Dependencias:* FR10.3, FR15.3.

- **FR11.** El sistema permitirá guardar productos o servicios habituales (como formación, consultoría o instalaciones) con una descripción y un precio orientativo.
  - **FR11.1.** El sistema permitirá modificar el precio y los datos de estos productos o servicios al crear una factura.
    - *Dependencias:* FR11.
  - **FR11.2.** El sistema permitirá asociar a cada producto o servicio un impuesto por defecto y garantizará que los cambios en el catálogo no modifiquen las facturas ya creadas.
    - *Dependencias:* FR11.

## 4. Presupuestos, facturación y ciclo de vida documental

- **FR12.** El sistema permitirá gestionar el ciclo de vida de los presupuestos para servicios puntuales y su conversión en facturas.
  - *Dependencias:* FR3, FR11, FR13.
  - **FR12.1.** El sistema permitirá crear y enviar presupuestos para servicios puntuales.
    - *Dependencias:* FR3, FR11.
  - **FR12.2.** El sistema permitirá registrar la aceptación o el rechazo de cada presupuesto.
    - *Dependencias:* FR12.1.
  - **FR12.3.** El sistema permitirá convertir un presupuesto aceptado en una factura, reutilizando sus líneas y conservando el vínculo histórico entre ambos documentos.
    - *Dependencias:* FR12.2, FR13.
  - **FR12.4.** El sistema permitirá adjuntar al presupuesto la evidencia de su aceptación, sin exigir la firma del cliente dentro de la plataforma.
    - *Dependencias:* FR12.2.

- **FR13.** El sistema permitirá emitir facturas puntuales y recurrentes.
  - *Dependencias:* FR3, FR8, FR11.
  - **FR13.1.** El sistema permitirá crear facturas puntuales compuestas por una o varias líneas con el concepto, la cantidad, el precio unitario, el descuento y el impuesto aplicable. También permitirá asignarlas, de forma opcional, a un proyecto o centro de coste.
    - *Dependencias:* FR3, FR11.
  - **FR13.2.** El sistema generará automáticamente facturas recurrentes el día 1 de cada mes a partir de las suscripciones activas.
    - *Dependencias:* FR8.
  - **FR13.3.** El sistema permitirá gestionar categorías administrables de proyectos o centros de coste, de modo que renombrar una categoría no destruya el histórico.
    - *Dependencias:* FR13.1.

- **FR14.** El sistema gestionará el prorrateo de importes cuando una suscripción comience a mitad de mes y permitirá aplicar excepciones comerciales autorizadas.
  - *Dependencias:* FR8, FR13.
  - **FR14.1.** Cuando una suscripción comience un día distinto del día 1 (por ejemplo, el día 22), el sistema calculará automáticamente y permitirá previsualizar el importe prorrateado correspondiente a los días restantes del mes.
    - *Dependencias:* FR8, FR13.2.
  - **FR14.2.** El sistema permitirá aplicar excepciones comerciales autorizadas al prorrateo, como ofrecer el primer mes gratis o iniciar el cobro el mes siguiente.
    - *Dependencias:* FR14.1.
  - **FR14.3.** El sistema permitirá aplicar descuentos temporales con fecha de caducidad (por ejemplo, un 10 % durante seis meses) y dejará de aplicarlos automáticamente al vencer.
    - *Dependencias:* FR14.2, FR15.2.

- **FR15.** El sistema gestionará el flujo de aprobación de las facturas que no cumplan las condiciones de emisión directa.
  - *Dependencias:* FR13, FR17.
  - **FR15.1.** El sistema emitirá directamente las facturas recurrentes estándar.
    - *Dependencias:* FR13.2.
  - **FR15.2.** El sistema establecerá el estado *Pendiente de aprobación* para las facturas que incluyan descuentos manuales, modificaciones de la fecha de vencimiento o importes puntuales que superen los umbrales configurados.
    - *Dependencias:* FR13, FR29.2.
  - **FR15.3.** El sistema exigirá que un administrador o superadministrador valide las facturas pendientes de aprobación y evitará que quien solicite una operación sensible pueda aprobarla.
    - *Dependencias:* FR15.2, FR1.
  - **FR15.4.** El sistema ofrecerá una bandeja de aprobaciones en la que el solicitante consulte el estado de su solicitud (*pendiente*, *aprobada* o *rechazada*) y el comentario del aprobador, y que explique el motivo por el que cada factura quedó pendiente.
    - *Dependencias:* FR15.2, FR15.3.
  - **FR15.5.** El sistema permitirá a un administrador reasignar una aprobación o designar un suplente cuando el aprobador no esté disponible, registrando la reasignación.
    - *Dependencias:* FR15.3.
  - **FR15.6.** El sistema exigirá la aprobación del superadministrador cuando un descuento supere el umbral configurado, y las operaciones que requieran separación de funciones serán configurables.
    - *Dependencias:* FR15.3, FR29.2.

- **FR16.** El sistema validará los datos y mostrará una vista previa antes de emitir una factura.
  - *Dependencias:* FR3, FR7, FR13, FR29.1.
  - **FR16.1.** El sistema mostrará obligatoriamente una vista previa de la factura antes de emitirla.
    - *Dependencias:* FR13.
  - **FR16.2.** El sistema bloqueará la emisión si faltan datos fiscales obligatorios del cliente o de la entidad emisora.
    - *Dependencias:* FR3, FR29.1.
  - **FR16.3.** El sistema mostrará una alerta preventiva, sin bloquear la emisión, cuando el importe total de una factura resulte anómalo en comparación con el historial del cliente.
    - *Dependencias:* FR7, FR13.

- **FR17.** El sistema gestionará los estados, la numeración y la documentación de las facturas emitidas.
  - *Dependencias:* FR13.
  - **FR17.1.** El sistema gestionará los siguientes estados de factura: *Borrador, Pendiente de aprobación, Aprobada, Emitida, Enviada, En disputa, Vencida, Parcialmente conciliada, Pagada, Anulada, Incobrable* y *Rectificada*.
    - *Dependencias:* FR13.
  - **FR17.2.** El sistema asignará de forma segura una serie y un número correlativo único en el momento de la emisión. Los borradores no consumirán numeración definitiva.
    - *Dependencias:* FR13.
  - **FR17.3.** El sistema generará un archivo PDF corporativo de cada factura en el momento de su emisión.
    - *Dependencias:* FR17.2.
  - **FR17.4.** El sistema conservará una copia inalterable de la factura tal como se emitió, junto con la fecha de generación, el cliente al que corresponde, la persona que la generó y los destinatarios a quienes se envió.
    - *Dependencias:* FR17.2, FR17.3, FR4.

- **FR18.** El sistema gestionará las restricciones de edición de facturas y la emisión de facturas rectificativas.
  - *Dependencias:* FR17.
  - **FR18.1.** El sistema bloqueará la edición de las facturas emitidas y de aquellas que pertenezcan a meses o trimestres cerrados administrativamente.
    - *Dependencias:* FR17.1, FR17.2.
  - **FR18.2.** El sistema ofrecerá un flujo guiado para generar facturas rectificativas o notas de crédito vinculadas a la factura original. Esta operación requerirá permisos administrativos y recalculará el saldo del cliente.
    - *Dependencias:* FR17.1, FR20, FR21.
  - **FR18.3.** El sistema permitirá eliminar únicamente borradores que nunca se hayan emitido, dejando traza cuando proceda, y no permitirá eliminar facturas emitidas.
    - *Dependencias:* FR17.1.
  - **FR18.4.** El sistema permitirá a un administrador autorizado cerrar administrativamente un mes o trimestre tras revisarlo, y no permitirá cambios ordinarios sobre elementos económicos de un periodo cerrado.
    - *Dependencias:* FR18.1.
  - **FR18.5.** El sistema exigirá un permiso superior y un motivo para reabrir un periodo cerrado, y registrará la operación.
    - *Dependencias:* FR18.4.
  - **FR18.6.** El sistema exigirá la aprobación de las facturas rectificativas en función de su importe.
    - *Dependencias:* FR18.2, FR15.3.

- **FR19.** El sistema permitirá migrar facturas históricas sin alterar el proceso de facturación en curso.
  - *Dependencias:* FR17.
  - **FR19.1.** El sistema permitirá importar administrativamente facturas anteriores marcadas como *Migradas*, conservando su número y el documento original, sin enviar notificaciones al cliente ni alterar las series de numeración en curso.
    - *Dependencias:* FR17.2, FR17.4.

## 5. Seguimiento de cobros, conciliación e impagos

- **FR20.** El sistema permitirá registrar cobros procedentes de distintas fuentes y conciliar los pagos con las facturas.
  - *Dependencias:* FR17.
  - **FR20.1.** El sistema permitirá consultar y recibir eventos de cobro de Stripe (simulado) y cargar movimientos procedentes de otras fuentes, como Revolut u otras cuentas bancarias.
    - *Dependencias:* FR17.1.
  - **FR20.2.** El sistema conciliará automáticamente los pagos cuando exista una coincidencia inequívoca entre la referencia y el importe. Si hay dudas, propondrá una conciliación manual asistida.
    - *Dependencias:* FR20.1, FR17.1.
  - **FR20.3.** El sistema dejará pendiente de conciliación manual los cobros sin referencia clara y no contabilizará dos veces un mismo evento de pago si este se recibe duplicado.
    - *Dependencias:* FR20.1, FR20.2.
  - **FR20.4.** El sistema registrará qué movimiento y qué regla o usuario justificó que una factura se marcara como *Pagada*.
    - *Dependencias:* FR20.2.

- **FR21.** El sistema permitirá gestionar los descuadres de pagos y revertir conciliaciones erróneas.
  - *Dependencias:* FR20.
  - **FR21.1.** Si el pago recibido es inferior o superior al importe de la factura, el sistema mantendrá la factura como *Parcialmente conciliada* o con el saldo pendiente de revisión, sin darla por saldada automáticamente.
    - *Dependencias:* FR17.1, FR20.2.
  - **FR21.2.** El sistema permitirá deshacer una conciliación errónea y registrará quién la revirtió y el motivo.
    - *Dependencias:* FR20.2.

- **FR22.** El sistema automatizará el envío de facturas por correo electrónico, los recordatorios de pago y los avisos de impago.
  - *Dependencias:* FR17.3, FR20, FR24.1.
  - **FR22.1.** El sistema enviará automáticamente el PDF al emitir la factura.
    - *Dependencias:* FR17.3, FR24.1.
  - **FR22.2.** El sistema enviará un recordatorio previo al vencimiento —programado por defecto para el día 3— y un aviso en la fecha de vencimiento —fijada por defecto para el día 5 o según el plazo particular del cliente—.
    - *Dependencias:* FR8, FR17.1, FR24.1.
  - **FR22.3.** Una vez vencida la factura, el sistema activará el flujo de impago y enviará avisos periódicos, configurables cada dos o tres días y adaptados al calendario laboral y a los días festivos, hasta que se detecte el cobro o se produzca una intervención manual.
    - *Dependencias:* FR20, FR22.2, FR24.1.
  - **FR22.4.** El sistema permitirá programar el envío periódico por correo electrónico del PDF de la factura a los destinatarios correspondientes.
    - *Dependencias:* FR17.3, FR17.4, FR24.1.

- **FR23.** El sistema permitirá gestionar las facturas en disputa y las deudas declaradas incobrables.
  - *Dependencias:* FR17.1, FR22.
  - **FR23.1.** El sistema permitirá que la persona responsable marque una factura como *En disputa* o *Seguimiento pausado*, indicando el motivo y la fecha de revisión.
    - *Dependencias:* FR17.1.
  - **FR23.2.** Al marcar una factura como *En disputa* o *Seguimiento pausado*, el sistema suspenderá temporalmente los recordatorios al cliente y generará una alerta interna cuando venza el periodo de pausa.
    - *Dependencias:* FR23.1.
  - **FR23.3.** El sistema exigirá un procedimiento administrativo sujeto a aprobación para declarar una deuda como *Incobrable*.
    - *Dependencias:* FR17.1, FR15.3.

## 6. Comunicaciones, incidencias y trabajo colaborativo

- **FR24.** El sistema gestionará las plantillas de comunicación, el registro de envíos y los errores de entrega.
  - *Dependencias:* FR3, FR4.
  - **FR24.1.** El sistema permitirá gestionar plantillas editables con variables dinámicas en español e inglés para los envíos por correo electrónico y WhatsApp (simulado), según el idioma configurado para el cliente o la persona de contacto.
    - *Dependencias:* FR3, FR4.
  - **FR24.2.** El sistema registrará cada envío, incluidos la fecha, el canal, el destinatario, la versión de la plantilla y el estado.
    - *Dependencias:* FR24.1.
  - **FR24.3.** Si se produce un rebote o un fallo de entrega, el sistema generará una incidencia interna para corregir los datos de contacto y permitir un nuevo intento de envío sin duplicar la factura.
    - *Dependencias:* FR24.2, FR26.1.
  - **FR24.4.** El sistema ofrecerá un modo de prueba para simular envíos y previsualizar los calendarios de recordatorios.
    - *Dependencias:* FR22.2, FR22.3, FR24.1.
  - **FR24.5.** El sistema distinguirá el envío original de sus reintentos y, si existen varios contactos de facturación, podrá intentar el envío a otro de ellos según la configuración.
    - *Dependencias:* FR24.2, FR24.3, FR4.2.
  - **FR24.6.** El sistema mostrará el estado de cada mensaje (*en cola*, *enviado*, *entregado* si el proveedor lo permite, *fallido* o *reintentado*) sin presuponer que el destinatario lo haya leído.
    - *Dependencias:* FR24.2.
  - **FR24.7.** El sistema avisará en la previsualización si falta alguna variable en una plantilla y conservará el historial de versiones de cada plantilla.
    - *Dependencias:* FR24.1.

- **FR25.** El sistema gestionará las notificaciones internas y las alertas configurables para responsables y administradores.
  - *Dependencias:* FR10, FR15, FR20, FR22, FR23, FR24.
  - **FR25.1.** El sistema enviará alertas a través de la plataforma y por correo electrónico, con opción de recibir resúmenes periódicos, ante eventos relevantes como cobros, vencimientos, rebotes, disputas, revisiones anuales, aprobaciones o reglas parametrizadas (por ejemplo, una deuda acumulada superior a X €).
    - *Dependencias:* FR10, FR15, FR20, FR22, FR23, FR24.
  - **FR25.2.** El sistema distinguirá entre el estado de una notificación (*Leída*) y el de una tarea (*Atendida/Resuelta*).
    - *Dependencias:* FR25.1.
  - **FR25.3.** El sistema permitirá a cada usuario configurar sus preferencias personales de notificación (en la plataforma, por correo electrónico o mediante un resumen diario) y ofrecerá a los administradores una bandeja global.
    - *Dependencias:* FR25.1.

- **FR26.** El sistema permitirá gestionar incidencias internas vinculadas a clientes o facturas y facilitar el trabajo colaborativo.
  - *Dependencias:* FR3, FR17.
  - **FR26.1.** El sistema permitirá crear incidencias administrativas asociadas a clientes o facturas (por ejemplo, *NIF erróneo*, *reclamación de importe* o *pago no localizado*), indicando su estado, prioridad y persona responsable.
    - *Dependencias:* FR3, FR17.1.
  - **FR26.2.** El sistema permitirá añadir comentarios internos con menciones (`@usuario`) y notas restringidas a los administradores.
    - *Dependencias:* FR26.1, FR1.
  - **FR26.3.** El sistema permitirá adjuntar archivos a las incidencias y clasificarlos por tipo, como presupuestos aceptados, justificantes de pago u órdenes de pedido.
    - *Dependencias:* FR26.1.
  - **FR26.4.** El sistema permitirá adjuntar archivos también a clientes y facturas, con tipo y descripción, limitando los formatos y tamaños admitidos y aplicando los permisos del elemento asociado.
    - *Dependencias:* FR26.3, FR3, FR17.
  - **FR26.5.** El sistema permitirá que una incidencia que afecte al cobro pause los recordatorios de la factura y exigirá registrar la resolución al cerrarla.
    - *Dependencias:* FR26.1, FR23.2.

## 7. Operativa diaria, informes, configuración y API

- **FR27. Pantalla de trabajo diario y acciones masivas.**
  - **FR27.1.** El sistema mostrará en la vista principal las tareas pendientes asignadas al usuario responsable de la operativa, como facturas próximas a vencer o vencidas, incidencias de entrega y cobros pendientes de conciliar.
  - **FR27.2.** El sistema permitirá realizar búsquedas globales de clientes y facturas combinando criterios como la razón social, el NIF, el contacto, el estado o el número de factura.
  - **FR27.3.** El sistema permitirá a los usuarios guardar filtros de búsqueda recurrentes para consultas frecuentes.
    - *Dependencias:* FR27.2.
  - **FR27.4.** El sistema ofrecerá una sección de acceso directo a los clientes recientes o marcados como favoritos.
  - **FR27.5.** El sistema permitirá a los administradores ejecutar acciones masivas de reasignación de clientes entre usuarios. Antes de ejecutarlas, solicitará confirmación y mostrará un resumen de los resultados.
    - *Dependencias:* FR2.1.
  - **FR27.6.** El sistema incluirá en la pantalla de trabajo diario las aprobaciones pendientes y las revisiones anuales que correspondan.
    - *Dependencias:* FR15.4, FR10.3.
  - **FR27.7.** El sistema permitirá otras acciones masivas (como exportar una selección o cambiar el sector de varias empresas), mostrará el número de elementos afectados, solicitará confirmación cuando la acción sea sensible y no permitirá marcar facturas como pagadas de forma masiva.
    - *Dependencias:* FR27.5.

- **FR28. Panel de control financiero y paquetes de exportación.**
  - **FR28.1.** El sistema proporcionará a los administradores un panel de control con métricas financieras consolidadas: total facturado, total cobrado, importe pendiente de cobro y saldo vencido.
  - **FR28.2.** El sistema permitirá desglosar cualquier métrica del panel de control para consultar el listado detallado de las facturas que la componen (*drill-down*).
    - *Dependencias:* FR28.1.
  - **FR28.3.** El sistema permitirá a los administradores configurar objetivos de facturación mensuales y anuales para hacer un seguimiento de ellos en el panel de control.
  - **FR28.4.** El sistema permitirá exportar informes y listados en formatos estructurados y legibles (CSV), respetando los permisos de acceso de cada usuario.
  - **FR28.5.** El sistema registrará en la pista de auditoría interna la fecha, la hora, los filtros aplicados y el usuario que haya generado cada exportación masiva.
    - *Dependencias:* FR28.4.
  - **FR28.6.** El sistema ofrecerá una vista general de la situación de los clientes y las facturas desde el panel de control, sin necesidad de abrir la ficha de cada cliente.
    - *Dependencias:* FR28.1, FR3, FR17.1.
  - **FR28.7.** El sistema ampliará el panel de control con la facturación por semana, mes, trimestre y año, el tiempo medio de cobro, la evolución, los clientes con mayor deuda, los ingresos recurrentes esperados del mes, la facturación puntual y una previsión basada en las suscripciones activas.
    - *Dependencias:* FR28.1.
  - **FR28.8.** El sistema permitirá filtrar los informes por periodo, cliente, responsable, país, sector, estado de la factura, tipo (recurrente o puntual) y moneda.
    - *Dependencias:* FR28.4.
  - **FR28.9.** El sistema permitirá exportar los informes también en un formato PDF legible.
    - *Dependencias:* FR28.4.
  - **FR28.10.** El sistema permitirá generar un paquete mensual o trimestral para la gestoría (facturas emitidas, rectificativas, cobros y PDF), registrar el periodo que cubre y la fecha de generación, y distinguir sus versiones.
    - *Dependencias:* FR17.4, FR18.4, FR28.4.
  - **FR28.11.** El sistema permitirá programar informes periódicos que se envíen únicamente a usuarios autorizados para ver su contenido.
    - *Dependencias:* FR28.8, FR31.
  - **FR28.12.** Como ampliación, el sistema permitirá convertir los importes a una moneda base con un tipo de cambio registrado exclusivamente para informes, sin alterar el importe legal de la factura.
    - *Dependencias:* FR28.1, FR3.2.

- **FR29. Configuración global y panel de estado del sistema.**
  - **FR29.1.** El sistema permitirá a los administradores registrar y modificar los datos fiscales y comerciales de la entidad emisora (razón social, NIF/CIF, domicilio fiscal, país y moneda predeterminada).
  - **FR29.2.** El sistema permitirá a los administradores configurar umbrales globales de alerta, como el número máximo de días de gracia antes de escalar un impago o el importe a partir del cual se requiere aprobación.
    - *Dependencias:* FR29.1.
  - **FR29.3.** El sistema permitirá al superadministrador configurar el plazo de pago por defecto, la cadencia de recordatorios, el dominio de correo permitido, el logo y la plantilla de factura, las series, las monedas, los impuestos disponibles y las políticas de automatización. Los cambios se validarán y auditarán, y no reescribirán las facturas ya emitidas.
    - *Dependencias:* FR29.1, FR29.2.
  - **FR29.4.** El sistema permitirá administrar los fines de semana y un calendario de festivos para trasladar al siguiente día laborable los recordatorios y vencimientos que caigan en día no laborable.
    - *Dependencias:* FR29.3, FR22.3.
  - **FR29.5.** Como ampliación, el sistema podrá gestionar más de una entidad emisora, cada una con sus series y cuentas, y permitirá a un usuario con permiso seleccionar cuál corresponde.
    - *Dependencias:* FR29.1.

- **FR30. Servicios e integración externa (API).**
  - **FR30.1.** El sistema expondrá una interfaz programática segura para consultar, mediante el identificador de un cliente, el estado de su deuda y sus obligaciones pendientes.
  - **FR30.2.** El sistema permitirá recibir solicitudes externas de baja comercial a través de la interfaz programática y registrarlas como tareas administrativas, sin eliminar el historial ni eludir las reglas de cobro de deudas.
    - *Dependencias:* FR30.1.
  - **FR30.3.** El sistema autenticará las llamadas a la interfaz programática con credenciales independientes de las de los usuarios, aplicará permisos y límites de uso razonables y registrará las llamadas importantes.
    - *Dependencias:* FR30.1.
  - **FR30.4.** El sistema documentará el contrato de la interfaz programática y evitará que esta permita eludir las reglas que aplica la interfaz de usuario.
    - *Dependencias:* FR30.1, FR30.2.

- **FR31. Monitorización y procesos automáticos.**
  - **FR31.1.** El sistema registrará la ejecución de cada proceso automático (generación mensual, recordatorios, conciliación e informes programados), indicando cuántos elementos procesó, cuáles fallaron y el motivo, sin interrumpir el procesamiento del resto.
  - **FR31.2.** El sistema ofrecerá una pantalla de estado con la última ejecución de la generación recurrente, la cola de correos pendientes, los errores recientes de integración y la última copia de seguridad correcta.
    - *Dependencias:* FR31.1.
  - **FR31.3.** El sistema mostrará el estado (*pendiente*, *procesando* o *completado con errores*) de las tareas en segundo plano, que seguirán existiendo aunque el usuario cierre la página.

- **FR32. Auditoría y protección de datos personales.**
  - **FR32.1.** El sistema permitirá al superadministrador consultar y filtrar el registro de auditoría de las operaciones sensibles, sin permitir su modificación.
  - **FR32.2.** El sistema permitirá localizar los datos personales de una persona de contacto y anonimizarlos en las fichas operativas cuando proceda, sin invalidar las facturas históricas emitidas.
    - *Dependencias:* FR4.3, FR17.4.
