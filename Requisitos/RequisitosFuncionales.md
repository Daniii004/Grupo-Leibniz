# Requisitos Funcionales (RF)

## 1. Gestión de usuarios, roles y asignaciones

- **FR1.** El sistema permitirá que los administradores puedan registrar las cuentas de los usuarios.
  - **FR1.1.** El sistema comprobará que el correo electrónico introducido pertenezca al dominio de la empresa (`@turbine`).
    - *Dependencias:* FR1.
  - **FR1.2.** El sistema permitirá que los usuarios soliciten el restablecimiento de su contraseña mediante un enlace temporal.
    - *Dependencias:* FR1.
  - **FR1.3.** El sistema permitirá a los administradores desactivar cuentas de usuario sin perder el registro de lo que haya hecho previamente
    - *Dependencias:* FR1.
  - **FR1.4.** El sistema cerrará automáticamente las sesiones activas de un usuario al momento de desactivar su cuenta.
    - *Dependencias:* FR1.3.
  - **FR1.5.** El sistema cerrará automáticamente la sesión de un usuario tras un periodo de inactividad.
    - *Dependencias:* FR1.
  - **FR1.6.** El sistema no permitirá el autorregistro: solo un superadministrador o un administrador con permiso podrá crear o invitar a nuevos usuarios.
    - *Dependencias:* FR1.

- **FR2.** El sistema permitirá a los administradores repartir clientes a los usuarios que estén disponibles.
  - *Dependencias:* FR1.
  - **FR2.1.** El sistema permitirá transferir un cliente a otro usuario, traspasando las tareas pendientes pero conservando las acciones del responsable anterior.
    - *Dependencias:* FR2.
  - **FR2.2.** El sistema permitirá asignar los clientes de un usuario a otro usuario de forma temporal, indicando las fechas de inicio y fin del periodo (por bajas o vacaciones) y devolverá el cliente a su responsable inicial cuando llegue la fecha de fin.
    - *Dependencias:* FR2.

- **FR3.** El sistema aplicará un modelo de autorización centralizado basado en roles (superadministrador, administrador y usuario operativo) que determine las acciones permitidas a cada usuario.
  - *Dependencias:* FR1.
  - **FR3.1.** El sistema restringirá al usuario operativo a los clientes que tenga asignados, permitiéndole consultar y modificar su información operativa, crear facturas si su permiso lo permite, seguir los cobros y descargar documentos.
    - *Dependencias:* FR2.
  - **FR3.2.** El sistema impedirá que un usuario operativo se asigne clientes a sí mismo, modifique tarifas globales, cree series de facturación o consulte el panel de control financiero global.
  - **FR3.3.** El sistema reservará al superadministrador la configuración global profunda (series de facturación, tarifas, plazos y reglas de automatización) y las acciones administrativas de mayor impacto.

## 2. Gestión de clientes y contactos

- **FR4.** El sistema permitirá dar de alta y gestionar clientes, diferenciando sus datos comerciales de sus datos fiscales.
  - **FR4.1.** El sistema almacenará por separado los datos comerciales (nombre, sector, ubicación y etiquetas) y los datos fiscales (razón social, NIF, domicilio, país e idioma).
    - *Dependencias:* FR4.
  - **FR4.2.** El sistema permitirá registrar para cada cliente su moneda de trabajo y un identificador fiscal y un domicilio que no estén atados al formato español (por ejemplo, sin exigir provincia).
    - *Dependencias:* FR4.1.

- **FR5.** El sistema permitirá registrar varias personas de contacto para cada empresa cliente.
  - *Dependencias:* FR4.
  - **FR5.1.** El sistema permitirá asociar una misma persona de contacto o un mismo gestor externo a varias empresas clientes.
    - *Dependencias:* FR5.
  - **FR5.2.** El sistema permitirá asignar a cada persona de contacto un rol diferenciado dentro de la empresa cliente (por ejemplo, comercial, administración o cobros).
    - *Dependencias:* FR5.
  - **FR5.3.** El sistema permitirá desactivar personas de contacto de una empresa cliente, conservando el historial de las facturas enviadas a sus direcciones de correo electrónico.
    - *Dependencias:* FR5, FR18.4.
  - **FR5.4.** El sistema avisará antes de desactivar o eliminar el único contacto de facturación de un cliente activo, ya que no habría destinatario para sus facturas.
    - *Dependencias:* FR5.2, FR5.3.
  - **FR5.5.** El sistema registrará para cada persona de contacto los canales de comunicación que admite (por ejemplo, WhatsApp) y no utilizará los datos de facturación para fines comerciales ajenos a la gestión administrativa.
    - *Dependencias:* FR5, FR25.1.

- **FR6.** El sistema controlará el estado de cada cliente (por ejemplo, *Activo*, *Pausado*, *Baja* o *Bloqueado*) a lo largo de su ciclo de vida.
  - *Dependencias:* FR4.
  - **FR6.1.** El sistema no permitirá registrar ni reactivar un cliente si su NIF ya existe en la base de datos, a fin de evitar duplicados.
    - *Dependencias:* FR6.
  - **FR6.2.** El sistema permitirá a los administradores fusionar dos clientes duplicados y unificar todo su historial.
    - *Dependencias:* FR6.
  - **FR6.3.** El sistema permitirá además los estados *Pendiente de activación* y *Baja solicitada*, y mostrará de forma explícita las consecuencias de cada cambio de estado antes de confirmarlo.
    - *Dependencias:* FR6.
  - **FR6.4.** El sistema bloqueará la activación de la recurrencia de un cliente que no tenga datos fiscales suficientes e indicará qué datos faltan.
    - *Dependencias:* FR6, FR4.1, FR14.2.

- **FR7.** El sistema permitirá importar simultáneamente una lista de clientes mediante un archivo CSV.
  - *Dependencias:* FR4.
  - **FR7.1.** Antes de guardar los datos, el sistema mostrará una vista previa del archivo para revisarlos y avisará si algún NIF ya existe.
    - *Dependencias:* FR7.
  - **FR7.2.** Al finalizar la importación, el sistema mostrará un informe con los clientes guardados y aquellos cuya importación haya producido errores.
    - *Dependencias:* FR7.
  - **FR7.3.** El sistema permitirá mapear las columnas del archivo con los campos del cliente y decidir, según la política definida, si se corrige el archivo o se importan únicamente las filas válidas.
    - *Dependencias:* FR7.1.

- **FR8.** El sistema mostrará el historial de cada cliente, incluida la fecha de alta, las facturas y los cobros, entre otros datos.
  - *Dependencias:* FR4.
  - **FR8.1.** El sistema mostrará el historial del cliente como una línea temporal de eventos de negocio (alta, cambios de responsable y de tarifa, facturas emitidas, pagos, incidencias, pausas, baja y reactivación).
    - *Dependencias:* FR8.

## 3. Suscripciones (relaciones comerciales), tarifas y catálogo

- **FR9.** El sistema permitirá formalizar y gestionar acuerdos de cobro mensual con los clientes, indicando el día de inicio, cuota o tarifa pactada, plazo de cobro y gestor responsable.
  - *Dependencias:* FR4.
  - **FR9.1.** El sistema permitirá subir copias de los contratos o acuerdos firmados a la ficha de cada suscripción.
    - *Dependencias:* FR9.
  - **FR9.2.** El sistema guardará para cada cliente la fecha del inicio de la suscripción, el servicio o plan contratado y el importe acordado. 
    - *Dependencias:* FR9.
  - **FR9.3.** El sistema permitirá registrar suscripciones con fecha de inicio futura, sin generar facturas ni recordatorios de cobro hasta esa fecha y mostrando al cliente como *Pendiente de activación*.
    - *Dependencias:* FR9, FR6.3.
  - **FR9.4.** El sistema generará las facturas recurrentes a partir de los conceptos configurados en la suscripción.
    - *Dependencias:* FR9, FR12, FR14.2.

- **FR10.** El sistema permitirá poner en pausa temporal una suscripción, indicando el motivo y la fecha prevista de reactivación.
  - *Dependencias:* FR9.
  - **FR10.1.** El sistema avisará al responsable asignado cuando llegue la fecha prevista para reanudar una suscripción pausada.
    - *Dependencias:* FR10.
  - **FR10.2.** El sistema permitirá dar de baja una suscripción, dejando de generar nuevos recibos, manteniendo las deudas o los cobros que queden pendientes.
    - *Dependencias:* FR9.
  - **FR10.3.** El sistema cerrará la recurrencia en la fecha efectiva de baja y, si el cliente tiene facturas pendientes, mantendrá activo el flujo de recobro de las facturas anteriores.
    - *Dependencias:* FR10.2, FR23.
  - **FR10.4.** Cuando la política configurada así lo exija, el sistema no reactivará automáticamente una suscripción en pausa y requerirá la confirmación de la persona responsable.
    - *Dependencias:* FR10.1.

- **FR11.** El sistema gestionará el proceso anual de revisión de tarifas, calculando y sugiriendo ajustes de precio según la facturación del cliente.
  - *Dependencias:* FR9.
  - **FR11.1.** El sistema exigirá que algún responsable apruebe el cambio del precio antes de aplicarlo, garantizando que no se modifiquen las facturas ya emitidas.
    - *Dependencias:* FR11.
  - **FR11.2.** El sistema almacenará los rangos de facturación anual y sus precios como configuración editable por el superadministrador, con fecha de efecto, sin modificar las facturas ya emitidas.
    - *Dependencias:* FR11.
  - **FR11.3.** El sistema listará los clientes cuya facturación anual deba revisarse, permitirá introducir el dato manualmente (con referencia de la fuente y fecha) y dejará la revisión pendiente si el dato no está disponible. Al aprobarse un cambio de rango, registrará quién lo aprobó, cuándo y los precios anterior y nuevo.
    - *Dependencias:* FR11, FR11.1.
  - **FR11.4.** El sistema no emitirá facturas retroactivas por la corrección de una revisión anual; cualquier regularización será una acción explícita y aprobada.
    - *Dependencias:* FR11.3, FR16.3.

- **FR12.** El sistema permitirá crear una lista de conceptos (como formación, consultoría o instalaciones) con una descripción básica y un precio de referencia.
  - **FR12.1.** El sistema permitirá modificar el precio y los datos de estos conceptos en el momento de añadirlos a una factura.
    - *Dependencias:* FR12.
  - **FR12.2.** El sistema permitirá asociar a cada producto o servicio un impuesto por defecto y garantizará que los cambios en el catálogo no modifiquen las facturas ya creadas.
    - *Dependencias:* FR12.

## 4. Presupuestos, facturación y ciclo de vida documental

- **FR13.** El sistema permitirá crear presupuestos para servicios puntuales y convertirlos en facturas.
  - *Dependencias:* FR4, FR12, FR14.
  - **FR13.1.** El sistema permitirá registrar si un cliente acepta o rechaza un presupuesto.
    - *Dependencias:* FR13.
  - **FR13.2.** El sistema permitirá pasar un presupuesto aceptado directamente a factura sin tener que volver a escribir los datos.
    - *Dependencias:* FR13.1, FR14.
  - **FR13.3.** El sistema permitirá adjuntar al presupuesto la evidencia de su aceptación, sin exigir la firma del cliente dentro de la plataforma.
    - *Dependencias:* FR13.2.

- **FR14.** El sistema permitirá emitir facturas puntuales y recurrentes.
  - *Dependencias:* FR4, FR9, FR12.
  - **FR14.1.** El sistema permitirá crear facturas manuales compuestas por una o varias líneas con concepto, cantidad, precio, descuento e IVA.
    - *Dependencias:* FR4, FR12.
  - **FR14.2.** El sistema generará automáticamente las facturas mensuales el día 1 de cada mes para los clientes con suscripción activa.
    - *Dependencias:* FR9.
  - **FR14.3.** El sistema permitirá gestionar categorías administrables de proyectos o centros de coste, de modo que renombrar una categoría no destruya el histórico.
    - *Dependencias:* FR14.1.

- **FR15.** El sistema calculará el importe proporcional si una suscripción empieza a mitad de mes.
  - *Dependencias:* FR9, FR14.
  - **FR15.1.** Cuando una suscripción comience un día distinto del día 1 (por ejemplo, el día 22), el sistema calculará automáticamente y permitirá previsualizar el importe prorrateado correspondiente a los días restantes del mes.
    - *Dependencias:* FR9, FR14.2.
  - **FR15.2.** El sistema permitirá aplicar promociones comerciales, como no cobrar el primer mes o empezar a cobrar al mes siguiente.
    - *Dependencias:* FR15.
  - **FR15.3.** El sistema permitirá aplicar descuentos temporales con fecha de caducidad (por ejemplo, un 10 % durante seis meses) y dejará de aplicarlos automáticamente al vencer.
    - *Dependencias:* FR15.2, FR16.2.

- **FR16.** El sistema requerirá que un administrador apruebe las facturas que tengan condiciones especiales.
  - *Dependencias:* FR14, FR18.
  - **FR16.1.** Las facturas con descuentos manuales, cambios en la fecha de pago o importes muy altos quedarán en estado *Pendiente de aprobación*.
    - *Dependencias:* FR14, FR30.2.
  - **FR16.2.** El sistema exigirá que un administrador o superadministrador valide las facturas pendientes de aprobación y evitará que quien solicite una operación sensible pueda aprobarla.
    - *Dependencias:* FR16.1, FR1.
  - **FR16.3.** El sistema ofrecerá una bandeja de aprobaciones en la que el solicitante consulte el estado de su solicitud (*pendiente*, *aprobada* o *rechazada*) y el comentario del aprobador, y que explique el motivo por el que cada factura quedó pendiente.
    - *Dependencias:* FR16.2.
  - **FR16.4.** El sistema permitirá a un administrador reasignar una aprobación o designar un suplente cuando el aprobador no esté disponible, registrando la reasignación.
    - *Dependencias:* FR16.3.
  - **FR16.5.** El sistema exigirá la aprobación del superadministrador cuando un descuento supere el umbral configurado, y las operaciones que requieran separación de funciones serán configurables.
    - *Dependencias:* FR16.2, FR30.2.

- **FR17.** El sistema validará los datos y mostrará una vista previa antes de emitir una factura.
  - *Dependencias:* FR4, FR8, FR14, FR30.1.
  - **FR17.1.** El sistema no permitirá emitir la factura si faltan datos fiscales obligatorios de la empresa o del cliente.
    - *Dependencias:* FR4, FR30.1.
  - **FR17.2.** El sistema mostrará un aviso si el total de la factura es mucho más alto o más bajo de lo habitual en ese cliente.
    - *Dependencias:* FR8, FR14.

- **FR18.** El sistema gestionará los estados, la numeración y la documentación de las facturas emitidas.
  - *Dependencias:* FR14.
  - **FR18.1.** El sistema gestionará los siguientes estados de factura: *Borrador, Pendiente de aprobación, Aprobada, Emitida, Enviada, En disputa, Vencida, Parcialmente conciliada, Pagada, Anulada, Incobrable* y *Rectificada*.
    - *Dependencias:* FR14.
  - **FR18.2.** El sistema asignará de forma segura una serie y un número correlativo único solo cuando la factura se emita (los borradores no gastan número).
    - *Dependencias:* FR14.
  - **FR18.3.** El sistema generará automáticamente la factura en formato PDF al emitirse.
    - *Dependencias:* FR18.2.
  - **FR18.4.** El sistema conservará una copia inalterable de la factura tal como se emitió, junto con la fecha de generación, el cliente al que corresponde, la persona que la generó y los destinatarios a quienes se envió.
    - *Dependencias:* FR18.2, FR18.3, FR5.

- **FR19.** El sistema gestionará las restricciones de edición de facturas y la emisión de facturas rectificativas.
  - *Dependencias:* FR18.
  - **FR19.1.** El sistema bloqueará la edición de las facturas emitidas y de aquellas que pertenezcan a meses o trimestres cerrados administrativamente.
    - *Dependencias:* FR18.1, FR18.2.
  - **FR19.2.** El sistema permitirá emitir facturas rectificativas vinculadas a la original para corregir importes y actualizar la deuda del cliente.
    - *Dependencias:* FR18.1, FR21, FR22.
  - **FR19.3.** El sistema permitirá eliminar únicamente borradores que nunca se hayan emitido, dejando traza cuando proceda, y no permitirá eliminar facturas emitidas.
    - *Dependencias:* FR18.1.
  - **FR19.4.** El sistema permitirá a un administrador autorizado cerrar administrativamente un mes o trimestre tras revisarlo, y no permitirá cambios ordinarios sobre elementos económicos de un periodo cerrado.
    - *Dependencias:* FR19.1.
  - **FR19.5.** El sistema exigirá un permiso superior y un motivo para reabrir un periodo cerrado, y registrará la operación.
    - *Dependencias:* FR19.4.
  - **FR19.6.** El sistema exigirá la aprobación de las facturas rectificativas en función de su importe.
    - *Dependencias:* FR19.2, FR16.2.

- **FR20.** El sistema permitirá subir facturas antiguas de otros años como historial.
  - *Dependencias:* FR18.
  - **FR20.1.** El sistema permitirá guardar facturas antiguas con la etiqueta *Migradas*, conservando su número y el documento original, sin enviar notificaciones al cliente ni alterar las series de numeración en curso.
    - *Dependencias:* FR18.2, FR18.4.

## 5. Seguimiento de cobros, conciliación e impagos

- **FR21.** El sistema permitirá registrar los cobros y comprobar qué facturas han sido pagadas.
  - *Dependencias:* FR18.
  - **FR21.1.** El sistema permitirá consultar y recibir eventos de cobro de Stripe (simulado) y cargar movimientos procedentes de otras fuentes, como Revolut u otras cuentas bancarias.
    - *Dependencias:* FR18.1.
  - **FR21.2.** El sistema marcará como pagada una factura cuando el importe y la referencia del cobro coincidan exactamente.
    - *Dependencias:* FR21.1, FR18.1.
  - **FR21.3.** El sistema dejará pendiente de conciliación manual los cobros sin referencia clara y no contabilizará dos veces un mismo evento de pago si este se recibe duplicado.
    - *Dependencias:* FR21.1, FR21.2.
  - **FR21.4.** El sistema registrará qué movimiento y qué regla o usuario justificó que una factura se marcara como *Pagada*.
    - *Dependencias:* FR21.2.

- **FR22.** El sistema gestionará pagos incompletos o errores al registrar cobros.
  - *Dependencias:* FR21.
  - **FR22.1.** Si un cliente paga de más o de menos, la factura quedará como *Parcialmente pagada* indicando la cantidad que falta por cobrar.
    - *Dependencias:* FR18.1, FR21.2.
  - **FR22.2.** El sistema permitirá anular un cobro asociado por error a una factura, dejando registrado quién lo hizo y el motivo.
    - *Dependencias:* FR21.2.

- **FR23.** El sistema automatizará el envío de facturas por correo electrónico, los recordatorios de pago y los avisos de impago.
  - *Dependencias:* FR18.3, FR21, FR25.1.
  - **FR23.1.** El sistema enviará automáticamente el PDF al emitir la factura.
    - *Dependencias:* FR18.3, FR25.1.
  - **FR23.2.** El sistema enviará un recordatorio previo al vencimiento —programado por defecto para el día 3— y un aviso en la fecha de vencimiento —fijada por defecto para el día 5 o según el plazo particular del cliente—.
    - *Dependencias:* FR9, FR18.1, FR25.1.
  - **FR23.3.** Una vez vencida la factura, el sistema activará el flujo de impago y enviará avisos periódicos, configurables cada dos o tres días y adaptados al calendario laboral y a los días festivos, hasta que se detecte el cobro o se produzca una intervención manual.
    - *Dependencias:* FR21, FR23.2, FR25.1.
  - **FR23.4.** El sistema permitirá programar el envío periódico por correo electrónico del PDF de la factura a los destinatarios correspondientes.
    - *Dependencias:* FR18.3, FR18.4, FR25.1.

- **FR24.** El sistema permitirá gestionar reclamaciones de clientes y deudas que no se van a poder cobrar.
  - *Dependencias:* FR18.1, FR23.
  - **FR24.1.** El sistema permitirá que la persona responsable marque una factura como *En disputa* o *Seguimiento pausado*, indicando el motivo y la fecha de revisión.
    - *Dependencias:* FR18.1.
  - **FR24.2.** Al marcar una factura como *En disputa* o *Seguimiento pausado*, el sistema suspenderá temporalmente los recordatorios al cliente y generará una alerta interna cuando venza el periodo de pausa.
    - *Dependencias:* FR24.1.
  - **FR24.3.** El sistema exigirá un procedimiento administrativo sujeto a aprobación para declarar una deuda como *Incobrable*.
    - *Dependencias:* FR18.1, FR16.3.

## 6. Comunicaciones, incidencias y trabajo colaborativo

- **FR25.** El sistema gestionará las plantillas de comunicación, el registro de envíos y los errores de entrega.
  - *Dependencias:* FR4, FR5.
  - **FR25.1.** El sistema dispondrá de plantillas editables en español e inglés que rellenan automáticamente los datos del cliente y de la factura.
    - *Dependencias:* FR4, FR5.
  - **FR25.2.** El sistema registrará cada envío con la fecha, el canal, el destinatario, la versión de la plantilla y el estado.
    - *Dependencias:* FR25.1.
  - **FR25.3.** Si se produce un rebote o un fallo de entrega, el sistema generará una incidencia interna para corregir los datos de contacto y permitir un nuevo intento de envío sin duplicar la factura.
    - *Dependencias:* FR25.2, FR27.1.
  - **FR25.4.** El sistema ofrecerá un modo de prueba para simular envíos y previsualizar los calendarios de recordatorios.
    - *Dependencias:* FR23.2, FR23.3, FR25.1.
  - **FR25.5.** El sistema distinguirá el envío original de sus reintentos y, si existen varios contactos de facturación, podrá intentar el envío a otro de ellos según la configuración.
    - *Dependencias:* FR25.2, FR25.3, FR5.2.
  - **FR25.6.** El sistema mostrará el estado de cada mensaje (*en cola*, *enviado*, *entregado* si el proveedor lo permite, *fallido* o *reintentado*) sin presuponer que el destinatario lo haya leído.
    - *Dependencias:* FR25.2.
  - **FR25.7.** El sistema avisará en la previsualización si falta alguna variable en una plantilla y conservará el historial de versiones de cada plantilla.
    - *Dependencias:* FR25.1.

- **FR26.** El sistema enviará avisos internos a los empleados sobre eventos importantes de su trabajo.
  - *Dependencias:* FR11, FR16, FR21, FR23, FR24, FR25.
  - **FR26.1.** El sistema enviará alertas a través de la plataforma y por correo electrónico, con opción de recibir resúmenes periódicos, ante eventos relevantes como cobros, vencimientos, rebotes, disputas, revisiones anuales, aprobaciones o reglas parametrizadas (por ejemplo, una deuda acumulada superior a X €).
    - *Dependencias:* FR11, FR16, FR21, FR23, FR24, FR25.
  - **FR26.2.** El sistema distinguirá entre el estado de una notificación (*Leída*) y el de una tarea (*Atendida/Resuelta*).
    - *Dependencias:* FR26.1.
  - **FR26.3.** El sistema permitirá a cada usuario configurar sus preferencias personales de notificación (en la plataforma, por correo electrónico o mediante un resumen diario) y ofrecerá a los administradores una bandeja global.
    - *Dependencias:* FR26.1.

- **FR27.** El sistema permitirá gestionar incidencias internas vinculadas a clientes o facturas y facilitar el trabajo colaborativo.
  - *Dependencias:* FR4, FR18.
  - **FR27.1.** El sistema permitirá crear incidencias administrativas asociadas a clientes o facturas (por ejemplo, *NIF erróneo*, *reclamación de importe* o *pago no localizado*), indicando su estado, prioridad y persona responsable.
    - *Dependencias:* FR4, FR18.1.
  - **FR27.2.** El sistema permitirá añadir comentarios internos con menciones (`@usuario`) y notas restringidas a los administradores.
    - *Dependencias:* FR27.1, FR1.
  - **FR27.3.** El sistema permitirá adjuntar archivos a las incidencias y clasificarlos por tipo, como presupuestos aceptados, justificantes de pago u órdenes de pedido.
    - *Dependencias:* FR27.1.
  - **FR27.4.** El sistema permitirá adjuntar archivos también a clientes y facturas, con tipo y descripción, limitando los formatos y tamaños admitidos y aplicando los permisos del elemento asociado.
    - *Dependencias:* FR27.3, FR4, FR18.
  - **FR27.5.** El sistema permitirá que una incidencia que afecte al cobro pause los recordatorios de la factura y exigirá registrar la resolución al cerrarla.
    - *Dependencias:* FR27.1, FR24.2.

## 7. Operativa diaria, informes, configuración y API

- **FR28.** Proporcionar un entorno de trabajo centralizado para gestionar tareas diarias, agilizar la localización de información mediante búsquedas y filtros.
  - **FR28.1.** El sistema mostrará en la vista principal las tareas pendientes asignadas al usuario responsable de la operativa, como facturas próximas a vencer o vencidas, incidencias de entrega y cobros pendientes de conciliar.
  - **FR28.2.** El sistema permitirá realizar búsquedas globales de clientes y facturas combinando criterios como la razón social, el NIF, el contacto, el estado o el número de factura.
  - **FR28.3.** El sistema permitirá a los usuarios guardar filtros de búsqueda recurrentes para consultas frecuentes.
    - *Dependencias:* FR28.2.
  - **FR28.4.** El sistema ofrecerá una sección de acceso directo a los clientes recientes o marcados como favoritos.

- **FR29.** Los administradores podrán ejecutar operaciones masivas sobre los clientes de forma segura.
  - **FR29.1.** El sistema permitirá a los administradores ejecutar acciones masivas de reasignación de clientes entre usuarios. Antes de ejecutarlas, solicitará confirmación y mostrará un resumen de los resultados.
    - *Dependencias:* FR2.1.
  - **FR29.2.** El sistema permitirá otras acciones masivas (como exportar una selección o cambiar el sector de varias empresas), mostrará el número de elementos afectados, solicitará confirmación cuando la acción sea sensible y no permitirá marcar facturas como pagadas de forma masiva.
  - **FR29.5.** El sistema registrará en la pista de auditoría interna la fecha, la hora, los filtros aplicados y el usuario que haya generado cada exportación masiva.

- **FR30.** Proporcionar para los administradores un panel financiero con una visión global del estado económico de la empresa.
  - **FR30.1.** El sistema proporcionará a los administradores un panel de control con métricas financieras consolidadas: total facturado, total cobrado, importe pendiente de cobro y saldo vencido.
  - **FR30.2.** El sistema permitirá desglosar cualquier métrica del panel de control para consultar el listado detallado de las facturas que la componen (*drill-down*).
    - *Dependencias:* FR30.1.
  - **FR30.3.** El sistema permitirá a los administradores configurar objetivos de facturación mensuales y anuales para hacer un seguimiento de ellos en el panel de control.
  - **FR30.4.** El sistema ampliará el panel de control con la facturación por semana, mes, trimestre y año, el tiempo medio de cobro, la evolución, los clientes con mayor deuda, los ingresos recurrentes esperados del mes, la facturación puntual y una previsión basada en las suscripciones activas.
    - *Dependencias:* FR30.1.
  - **FR30.5.** El sistema ofrecerá una vista general de la situación de los clientes y las facturas desde el panel de control, sin necesidad de abrir la ficha de cada cliente.
    - *Dependencias:* FR30.1, FR4, FR18.1.
  - **FR30.6.** El sistema permitirá filtrar los informes por periodo, cliente, responsable, país, sector, estado de la factura, tipo (recurrente o puntual) y moneda.
    - *Dependencias:* FR30.4.
  - **FR30.7.** El sistema permitirá exportar los informes también en un formato PDF legible.
    - *Dependencias:* FR30.4.
  - **FR30.8.** El sistema permitirá generar un paquete mensual o trimestral para la gestoría (facturas emitidas, rectificativas, cobros y PDF), registrar el periodo que cubre y la fecha de generación, y distinguir sus versiones.
    - *Dependencias:* FR18.4, FR19.4, FR30.4.
  - **FR30.9.** El sistema permitirá programar informes periódicos que se envíen únicamente a usuarios autorizados para ver su contenido.
  - **FR30.10.** Como ampliación, el sistema permitirá convertir los importes a una moneda base con un tipo de cambio registrado exclusivamente para informes, sin alterar el importe legal de la factura.
    - *Dependencias:* FR30.1, FR4.2.

- **FR31.** Permitir al administrador configurar y modificar datos de las empresas
  - **FR31.1.** El sistema permitirá a los administradores registrar y modificar los datos fiscales y comerciales de la entidad emisora (razón social, NIF/CIF, domicilio fiscal, país y moneda predeterminada).
  - **FR31.2.** El sistema permitirá al superadministrador configurar el plazo de pago por defecto, la cadencia de recordatorios, el dominio de correo permitido, el logo y la plantilla de factura, las series, las monedas, los impuestos disponibles y las políticas de automatización. Los cambios se validarán y auditarán, y no reescribirán las facturas ya emitidas.

- **FR32.** El administrador tendrá acceso a una intefaz programática para consultar las deudas de un cliente
  - **FR32.1.** El sistema expondrá una interfaz programática segura para consultar, mediante el identificador de un cliente, el estado de su deuda y sus obligaciones pendientes.
  - **FR32.2.** El sistema autenticará las llamadas a la interfaz programática con credenciales independientes de las de los usuarios, aplicará permisos y límites de uso razonables y registrará las llamadas importantes.
    - *Dependencias:* FR32.1.
  - **FR32.3.** El sistema documentará el contrato de la interfaz programática y evitará que esta permita eludir las reglas que aplica la interfaz de usuario.
    - *Dependencias:* FR32.1.

- **FR33.** Controla y registra la ejecución en segundo plano de tareas automatizadas.
  - **FR33.1.** El sistema registrará la ejecución de cada proceso automático (generación mensual, recordatorios, conciliación e informes programados), indicando cuántos elementos procesó, cuáles fallaron y el motivo, sin interrumpir el procesamiento del resto.
  - **FR33.2.** El sistema ofrecerá una pantalla de estado con la última ejecución de la generación recurrente, la cola de correos pendientes, los errores recientes de integración y la última copia de seguridad correcta.
    - *Dependencias:* FR33.1.
  - **FR33.3.** El sistema mostrará el estado (*pendiente*, *procesando* o *completado con errores*) de las tareas en segundo plano, que seguirán existiendo aunque el usuario cierre la página.