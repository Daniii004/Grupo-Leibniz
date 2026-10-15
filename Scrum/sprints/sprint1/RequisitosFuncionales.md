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

- **FR2.** El sistema permitirá a los administradores asignar clientes específicos a usuarios disponibles.
  - *Dependencias:* FR1.
  - **FR2.1.** El sistema permitirá reasignar un cliente a otro usuario, transfiriendo las tareas pendientes y conservando la autoría de las acciones del responsable anterior.
    - *Dependencias:* FR2.
  - **FR2.2.** El sistema permitirá asignar temporalmente a otro usuario los clientes de un usuario, indicando las fechas de inicio y fin del periodo (por ejemplo, durante las vacaciones o una baja médica).
    - *Dependencias:* FR2.
  - **FR2.3.** Al llegar la fecha de fin, el sistema retirará automáticamente el acceso temporal y devolverá el cliente a su responsable inicial.
    - *Dependencias:* FR2.2.

## 2. Gestión de clientes y contactos

- **FR3.** El sistema permitirá dar de alta y gestionar clientes, diferenciando sus datos comerciales de sus datos fiscales.
  - **FR3.1.** El sistema almacenará por separado los datos comerciales (nombre, sector, ubicación y etiquetas) y los datos fiscales (razón social, NIF, domicilio, país e idioma).
    - *Dependencias:* FR3.

- **FR4.** El sistema permitirá registrar varias personas de contacto para cada empresa cliente.
  - *Dependencias:* FR3.
  - **FR4.1.** El sistema permitirá asociar una misma persona de contacto o un mismo gestor externo a varias empresas clientes.
    - *Dependencias:* FR4.
  - **FR4.2.** El sistema permitirá asignar a cada persona de contacto un rol diferenciado dentro de la empresa cliente (por ejemplo, comercial, administración o cobros).
    - *Dependencias:* FR4.
  - **FR4.3.** El sistema permitirá desactivar personas de contacto de una empresa cliente, conservando el historial de las facturas enviadas a sus direcciones de correo electrónico.
    - *Dependencias:* FR4, FR17.4.

- **FR5.** El sistema controlará el estado de cada cliente (por ejemplo, *Activo*, *Pausado*, *Baja* o *Bloqueado*) a lo largo de su ciclo de vida.
  - *Dependencias:* FR3.
  - **FR5.1.** El sistema no permitirá registrar ni reactivar un cliente si su NIF ya existe en la base de datos, a fin de evitar duplicados.
    - *Dependencias:* FR5.
  - **FR5.2.** El sistema permitirá a los administradores fusionar dos clientes duplicados y unificar todo su historial.
    - *Dependencias:* FR5.

- **FR6.** El sistema permitirá importar simultáneamente una lista de clientes mediante un archivo CSV.
  - *Dependencias:* FR3.
  - **FR6.1.** Antes de guardar los datos, el sistema mostrará una vista previa del archivo para revisarlos y avisará si algún NIF ya existe.
    - *Dependencias:* FR6.
  - **FR6.2.** Al finalizar la importación, el sistema mostrará un informe con los clientes guardados y aquellos cuya importación haya producido errores.
    - *Dependencias:* FR6.

- **FR7.** El sistema mostrará el historial de cada cliente, incluida la fecha de alta, las facturas y los cobros, entre otros datos.
  - *Dependencias:* FR3.

## 3. Suscripciones (relaciones comerciales), tarifas y catálogo

- **FR8.** El sistema permitirá crear y gestionar suscripciones mensuales para los clientes, indicando la fecha de inicio, la tarifa acordada, las condiciones de pago y la persona responsable.
  - *Dependencias:* FR3.
  - **FR8.1.** El sistema permitirá adjuntar los contratos o acuerdos firmados a la ficha de cada suscripción.
    - *Dependencias:* FR8.
  - **FR8.2.** El sistema almacenará para cada cliente la fecha de inicio, el servicio o plan contratado y la tarifa acordada.
    - *Dependencias:* FR8.

- **FR9.** El sistema permitirá pausar temporalmente una suscripción e indicar el motivo y la fecha prevista para reanudarla.
  - *Dependencias:* FR8.
  - **FR9.1.** El sistema avisará a la persona responsable cuando llegue la fecha prevista para reactivar una suscripción en pausa.
    - *Dependencias:* FR9.
  - **FR9.2.** El sistema permitirá dar de baja una suscripción, dejando de generar nuevas cuotas sin eliminar las deudas ni los cobros pendientes.
    - *Dependencias:* FR8.

- **FR10.** El sistema gestionará la revisión anual de las tarifas y propondrá cambios de precio según la facturación del cliente.
  - *Dependencias:* FR8.
  - **FR10.1.** El sistema exigirá que una persona responsable apruebe el nuevo precio antes de aplicarlo y garantizará que no se modifiquen las facturas emitidas anteriormente.
    - *Dependencias:* FR10.

- **FR11.** El sistema permitirá guardar productos o servicios habituales (como formación, consultoría o instalaciones) con una descripción y un precio orientativo.
  - **FR11.1.** El sistema permitirá modificar el precio y los datos de estos productos o servicios al crear una factura.
    - *Dependencias:* FR11.

## 4. Presupuestos, facturación y ciclo de vida documental

- **FR12.** El sistema permitirá crear presupuestos para servicios puntuales y convertirlos en facturas.
  - *Dependencias:* FR3, FR11, FR13.
  - **FR12.1.** El sistema permitirá registrar si un cliente acepta o rechaza un presupuesto.
    - *Dependencias:* FR12.1.
  - **FR12.2.** El sistema permitirá pasar un presupuesto aceptado directamente a factura sin tener que volver a escribir los datos.
    - *Dependencias:* FR12.2, FR13.

- **FR13.** El sistema permitirá emitir facturas puntuales y recurrentes.
  - *Dependencias:* FR3, FR8, FR11.
  - **FR13.1.** El sistema permitirá crear facturas manuales compuestas por una o varias líneas con concepto, cantidad, precio, descuento e IVA.
    - *Dependencias:* FR3, FR11.
  - **FR13.2.** El sistema generará automáticamente las facturas mensuales el día 1 de cada mes para los clientes con suscripción activa.
    - *Dependencias:* FR8.

- **FR14.** El sistema calculará el importe proporcional si una suscripción empieza a mitad de mes.
  - *Dependencias:* FR8, FR13.
  - **FR14.1.** El sistema permitirá aplicar promociones comerciales, como no cobrar el primer mes o empezar a cobrar al mes siguiente.
    - *Dependencias:* FR14.

- **FR15.** El sistema requerirá que un administrador apruebe las facturas que tengan condiciones especiales.
  - *Dependencias:* FR13, FR17.
  - **FR15.1.** Las facturas con descuentos manuales, cambios en la fecha de pago o importes muy altos quedarán en estado *Pendiente de aprobación*.
    - *Dependencias:* FR13, FR29.2.
  - **FR15.2.** El usuario que cree o modifique una factura con condiciones especiales no podrá autoaprobarla.
    - *Dependencias:* FR15

- **FR16.** El sistema validará los datos y mostrará una vista previa antes de emitir una factura.
  - *Dependencias:* FR3, FR7, FR13, FR29.1.
  - **FR16.1.** El sistema no permitirá emitir la factura si faltan datos fiscales obligatorios de la empresa o del cliente.
    - *Dependencias:* FR3, FR29.1.
  - **FR16.2.** El sistema mostrará un aviso si el total de la factura es mucho más alto o más bajo de lo habitual en ese cliente.
    - *Dependencias:* FR7, FR13.

- **FR17.** El sistema gestionará los estados, la numeración y la documentación de las facturas emitidas.
  - *Dependencias:* FR13.
  - **FR17.1.** El sistema gestionará los siguientes estados de factura: *Borrador, Pendiente de aprobación, Aprobada, Emitida, Enviada, En disputa, Vencida, Parcialmente conciliada, Pagada, Anulada, Incobrable* y *Rectificada*.
    - *Dependencias:* FR13.
  - **FR17.2.** El sistema asignará de forma segura una serie y un número correlativo único solo cuando la factura se emita (los borradores no gastan número).
    - *Dependencias:* FR13.
  - **FR17.3.** El sistema generará automáticamente la factura en formato PDF al emitirse.
    - *Dependencias:* FR17.2.
  - **FR17.4.** El sistema conservará una copia inalterable de la factura tal como se emitió, junto con la fecha de generación, el cliente al que corresponde, la persona que la generó y los destinatarios a quienes se envió.
    - *Dependencias:* FR17.2, FR17.3, FR4.

- **FR18.** El sistema gestionará las restricciones de edición de facturas y la emisión de facturas rectificativas.
  - *Dependencias:* FR17.
  - **FR18.1.** El sistema bloqueará la edición de las facturas emitidas y de aquellas que pertenezcan a meses o trimestres cerrados administrativamente.
    - *Dependencias:* FR17.1, FR17.2.
  - **FR18.2.** El sistema permitirá emitir facturas rectificativas vinculadas a la original para corregir importes y actualizar la deuda del cliente.
    - *Dependencias:* FR17.1, FR20, FR21.

- **FR19.** El sistema permitirá subir facturas antiguas de otros años como historial.
  - *Dependencias:* FR17.
  - **FR19.1.** El sistema permitirá guardar facturas antiguas con la etiqueta *Migradas*, conservando su número y el documento original, sin enviar notificaciones al cliente ni alterar las series de numeración en curso.
    - *Dependencias:* FR17.2, FR17.4.

## 5. Seguimiento de cobros, conciliación e impagos

- **FR20.** El sistema permitirá registrar los cobros y comprobar qué facturas han sido pagadas.
  - *Dependencias:* FR17.
  - **FR20.1.** El sistema permitirá consultar y recibir eventos de cobro de Stripe (simulado) y cargar movimientos procedentes de otras fuentes, como Revolut u otras cuentas bancarias.
    - *Dependencias:* FR17.1.
  - **FR20.2.** El sistema marcará como pagada una factura cuando el importe y la referencia del cobro coincidan exactamente.
    - *Dependencias:* FR20.1, FR17.1.

- **FR21.** El sistema gestionará pagos incompletos o errores al registrar cobros.
  - *Dependencias:* FR20.
  - **FR21.1.** Si un cliente paga de más o de menos, la factura quedará como *Parcialmente pagada* indicando la cantidad que falta por cobrar.
    - *Dependencias:* FR17.1, FR20.2.
  - **FR21.2.** El sistema permitirá anular un cobro asociado por error a una factura, dejando registrado quién lo hizo y el motivo.
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

- **FR23.** El sistema permitirá gestionar reclamaciones de clientes y deudas que no se van a poder cobrar.
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
  - **FR24.1.** El sistema dispondrá de plantillas editables en español e inglés que rellenan automáticamente los datos del cliente y de la factura.
    - *Dependencias:* FR3, FR4.
  - **FR24.2.** El sistema registrará cada envío con la fecha, el canal, el destinatario, la versión de la plantilla y el estado.
    - *Dependencias:* FR24.1.
  - **FR24.3.** Si se produce un rebote o un fallo de entrega, el sistema generará una incidencia interna para corregir los datos de contacto y permitir un nuevo intento de envío sin duplicar la factura.
    - *Dependencias:* FR24.2, FR26.1.
  - **FR24.4.** El sistema ofrecerá un modo de prueba para simular envíos y previsualizar los calendarios de recordatorios.
    - *Dependencias:* FR22.2, FR22.3, FR24.1.

- **FR25.** El sistema enviará avisos internos a los empleados sobre eventos importantes de su trabajo.
  - *Dependencias:* FR10, FR15, FR20, FR22, FR23, FR24.
  - **FR25.1.** El sistema enviará alertas a través de la plataforma y por correo electrónico, con opción de recibir resúmenes periódicos, ante eventos relevantes como cobros, vencimientos, rebotes, disputas, revisiones anuales, aprobaciones o reglas parametrizadas (por ejemplo, una deuda acumulada superior a X €).
    - *Dependencias:* FR10, FR15, FR20, FR22, FR23, FR24.
  - **FR25.2.** El sistema distinguirá entre el estado de una notificación (*Leída*) y el de una tarea (*Atendida/Resuelta*).
    - *Dependencias:* FR25.1.

- **FR26.** El sistema permitirá gestionar incidencias internas vinculadas a clientes o facturas y facilitar el trabajo colaborativo.
  - *Dependencias:* FR3, FR17.
  - **FR26.1.** El sistema permitirá crear incidencias administrativas asociadas a clientes o facturas (por ejemplo, *NIF erróneo*, *reclamación de importe* o *pago no localizado*), indicando su estado, prioridad y persona responsable.
    - *Dependencias:* FR3, FR17.1.
  - **FR26.2.** El sistema permitirá añadir comentarios internos con menciones (`@usuario`) y notas restringidas a los administradores.
    - *Dependencias:* FR26.1, FR1.
  - **FR26.3.** El sistema permitirá adjuntar archivos a las incidencias y clasificarlos por tipo, como presupuestos aceptados, justificantes de pago u órdenes de pedido.
    - *Dependencias:* FR26.1.

## 7. Operativa diaria, informes, configuración y API

- **FR27. Pantalla de trabajo diario y acciones masivas.**
  - **FR27.1.** El sistema mostrará en la vista principal las tareas pendientes asignadas al usuario responsable de la operativa, como facturas próximas a vencer o vencidas, incidencias de entrega y cobros pendientes de conciliar.
  - **FR27.2.** El sistema permitirá realizar búsquedas globales de clientes y facturas combinando criterios como la razón social, el NIF, el contacto, el estado o el número de factura.
  - **FR27.3.** El sistema permitirá a los usuarios guardar filtros de búsqueda recurrentes para consultas frecuentes.
    - *Dependencias:* FR27.2.
  - **FR27.4.** El sistema ofrecerá una sección de acceso directo a los clientes recientes o marcados como favoritos.
  - **FR27.5.** El sistema permitirá a los administradores ejecutar acciones masivas de reasignación de clientes entre usuarios. Antes de ejecutarlas, solicitará confirmación y mostrará un resumen de los resultados.
    - *Dependencias:* FR2.1.

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

- **FR29. Configuración global y panel de estado del sistema.**
  - **FR29.1.** El sistema permitirá a los administradores registrar y modificar los datos fiscales y comerciales de la entidad emisora (razón social, NIF/CIF, domicilio fiscal, país y moneda predeterminada).
  - **FR29.2.** El sistema permitirá a los administradores configurar umbrales globales de alerta, como el número máximo de días de gracia antes de escalar un impago o el importe a partir del cual se requiere aprobación.
    - *Dependencias:* FR29.1.

- **FR30. Servicios e integración externa (API).**
  - **FR30.1.** El sistema expondrá una interfaz programática segura para consultar, mediante el identificador de un cliente, el estado de su deuda y sus obligaciones pendientes.
  - **FR30.2.** El sistema permitirá recibir solicitudes externas de baja comercial a través de la interfaz programática y registrarlas como tareas administrativas, sin eliminar el historial ni eludir las reglas de cobro de deudas.
    - *Dependencias:* FR30.1.
