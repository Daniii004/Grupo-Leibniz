# Requisitos Funcionales (RF)

## 1. Gestión de Usuarios, Roles y Asignaciones

- **FR1.** El sistema permitirá a los administradores registrar a los usuarios.
  - **FR1.1.** El sistema validará que el correo electrónico pertenezca al dominio corporativo autorizado (@turbine).
    - *Dependencias:* FR1
  - **FR1.2.** El sistema permitirá a los usuarios solicitar el restablecimiento de contraseña mediante un enlace temporal.
    - *Dependencias:* FR1
  - **FR1.3.** El sistema permitirá a los administradores desactivar cuentas de usuario conservando su historial de acciones.
    - *Dependencias:* FR1
  - **FR1.4.** El sistema invalidará automáticamente las sesiones activas de un usuario en el momento de su desactivación.
    - *Dependencias:* FR1.3
  - **FR1.5.** El sistema cerrará la sesión del usuario de forma automática tras un periodo de inactividad.
    - *Dependencias:* FR1<br><br>
- **FR2.** El sistema permitirá a los administradores asignar clientes específicos a usuarios disponibles.
    - *Dependencias:* FR1
  - **FR2.1.** El sistema permitirá reasignar un cliente a otro usuario, transfiriendo las tareas pendientes y conservando la autoría del responsable anterior.
    - *Dependencias:* FR2
  - **FR2.2.** El sistema permitirá asignar temporalmente los clientes de un usuario a otro indicando una fecha de inicio y fin (por ejemplo, por vacaciones o baja médica).
    - *Dependencias:* FR2
  - **FR2.3.** El sistema retirará automáticamente el acceso temporal al llegar la fecha de fin, devolviendo al cliente a su responsable inicial.
    - *Dependencias:* FR2.2

## 2. Gestión de Clientes y Contactos

- **FR3.** El sistema permitirá dar de alta y gestionar clientes diferenciando sus datos comerciales de sus datos fiscales.
  - **FR3.1.** El sistema almacenará por separado los datos comerciales (nombre, sector, ubicación y etiquetas) y fiscales (razón social, NIF, domicilio, país e idioma).
    - *Dependencias:* FR3<br><br>
- **FR4.** El sistema permitirá guardar varias personas de contacto para cada empresa cliente, indicando su función (por ejemplo: comercial, administración o cobros).
  - *Dependencias:* FR3
  - **FR4.1.** El sistema permitirá asignar una misma persona o gestor externo a más de una empresa cliente diferente.
      - *Dependencias:* FR4<br><br>
- **FR5.** El sistema controlará el estado de cada cliente (como Activo, Pausado, Baja o Bloqueado) a lo largo de su ciclo de vida.
    - *Dependencias:* FR3
  - **FR5.1.** El sistema no permitirá registrar o reactivar un cliente si su NIF ya existe en la base de datos para evitar duplicados.
    - *Dependencias:* FR5
  - **FR5.2.** El sistema permitirá a los administradores fusionar dos clientes duplicados, manteniendo todo su historial unificado.
    - *Dependencias:* FR5<br><br>
- **FR6.** El sistema permitirá importar una lista de clientes a la vez mediante un archivo CSV.
    - *Dependencias:* FR3
  - **FR6.1.** El sistema mostrará una vista previa del archivo para revisar los datos y avisar si algún NIF ya existe antes de guardarlos.
    - *Dependencias:* FR6
  - **FR6.2.** El sistema mostrará un informe al terminar la subida con los clientes guardados y los que dieron error.
    - *Dependencias:* FR6<br><br>
- **FR7.** El sistema mostrará un historial de cada cliente (fecha de alta, facturas, cobros, etc).
    - *Dependencias:* FR3

## 3. Suscripciones (Relaciones Comerciales), Tarifas y Catálogo

- **FR8.** El sistema permitirá crear y gestionar suscripciones mensuales para los clientes indicando fecha de inicio, tarifa, condiciones de pago y responsable.
  - **FR8.1.** El sistema permitirá adjuntar los contratos o acuerdos firmados a la ficha de la suscripción.
    - *Dependencias:* FR8<br><br>
- **FR9.** El sistema permitirá pausar temporalmente una suscripción indicando el motivo y una fecha prevista para reanudarla.
    - *Dependencias:* FR8
  - **FR9.1.** El sistema avisará al responsable cuando llegue la fecha de reactivar una suscripción en pausa.
    - *Dependencias:* FR9
  - **FR9.2.** El sistema permitirá dar de baja una suscripción dejando de generar nuevas cuotas sin borrar las deudas o cobros pendientes.
    - *Dependencias:* FR8<br><br>
- **FR10.** El sistema gestionará la revisión anual de tarifas proponiendo cambios de precio según la facturación del cliente.
    - *Dependencias:* FR8
  - **FR10.1.** El sistema exigirá que un responsable apruebe el nuevo precio antes de aplicarlo, sin modificar las facturas emitidas anteriormente.
    - *Dependencias:* FR10<br><br>
- **FR11.** El sistema permitirá guardar productos o servicios habituales (como formación, consultoría, instalaciones, etc) con una descripción y precio orientativo.
  - **FR11.1.** El sistema permitirá modificar el precio y los datos de estos servicios al momento de crear una factura.
    - *Dependencias:* FR11

## 4. Presupuestos, Facturación y Ciclo de Vida Documental

* **RF-12. Gestión de presupuestos previos:** Creación, envío y registro de aceptación o rechazo de presupuestos para servicios puntuales, permitiendo convertir un presupuesto aceptado en factura reutilizando sus líneas y manteniendo el vínculo histórico entre ambos documentos.
* **RF-13. Emisión de facturas puntuales y recurrentes:**
  * *Puntuales:* Compuestas por una o varias líneas (concepto, cantidad, precio unitario, descuento e impuesto aplicable) y asignación opcional a un proyecto o centro de coste.
  * *Recurrentes:* Generadas de forma automática el día 1 de cada mes a partir de las suscripciones activas.
* **RF-14. Prorrateo en altas y excepciones comerciales:** Si una suscripción comienza un día distinto al día 1 (ej. día 22), cálculo automático y previsualización del importe prorrateado por los días restantes del mes, salvo que se aplique una excepción comercial autorizada (ej. primer mes gratuito o inicio de cobro al mes siguiente).
* **RF-15. Flujo de aprobaciones (Workflow):** Emisión directa para facturas recurrentes estándar. Puesta en estado *Pendiente de aprobación* cuando existan descuentos manuales, alteraciones de vencimiento o importes puntuales que superen los umbrales configurados, requiriendo validación de un Administrador o Superadministrador (con separación de funciones para que el solicitante no apruebe su propia operación sensible).
* **RF-16. Vista previa y validaciones de emisión:** Previsualización obligatoria antes de emitir. Bloqueo de emisión si faltan datos fiscales obligatorios en el cliente o emisor, y alerta preventiva (no bloqueante) si el importe total resulta anómalo respecto al histórico del cliente.
* **RF-17. Ciclo de estados, numeración correlativa y generación de PDF:** Gestión de estados (*Borrador, Pendiente de aprobación, Aprobada, Emitida, Enviada, En disputa, Vencida, Parcialmente conciliada, Pagada, Anulada, Incobrable, Rectificada*). Asignación segura de serie y número correlativo único en el instante de la emisión (los borradores no consumen numeración definitiva). Generación y almacenamiento inmutable del PDF corporativo tal como se emitió.
* **RF-18. Facturas rectificativas y cierres de periodo:** Bloqueo de edición sobre facturas emitidas o pertenecientes a meses/trimestres marcados como cerrados administrativamente. Flujo guiado para generar facturas rectificativas o notas de crédito vinculadas a la factura original (requiriendo permisos administrativos y recalculando el saldo del cliente).
* **RF-19. Migración de facturas históricas:** Importación administrativa de facturas pasadas marcadas como *Migradas*, conservando su número y documento original sin enviar notificaciones al cliente ni alterar las series de numeración en curso.

## 5. Seguimiento de Cobros, Conciliación e Impagos

* **RF-20. Integración con pasarela de pagos (Stripe simulado) y conciliación multifuente:** Consulta y recepción de eventos de cobro desde Stripe (simulado) y carga de movimientos de otras fuentes bancarias (Revolut/cuentas bancarias). Conciliación automática cuando exista coincidencia inequívoca de referencia e importe, o propuesta de conciliación manual asistida en caso de duda.
* **RF-21. Tratamiento de descuadres y reversión de conciliaciones:** Si se recibe un pago inferior o superior al importe de la factura, mantener la factura como *Parcialmente conciliada* o con saldo pendiente de revisión sin darla por saldada automáticamente. Permitir deshacer una conciliación errónea registrando quién la revirtió y el motivo.
* **RF-22. Envío de factura y flujo automatizado de recordatorios e impagos:** Envío automático del PDF al emitir; envío de recordatorio previo (día 3 por defecto) y aviso en fecha de vencimiento (día 5 por defecto, o según el plazo particular del cliente). Tras el vencimiento, activación del flujo de impago con avisos periódicos (cada 2-3 días configurables) ajustados al calendario laboral/festivos hasta detectar el cobro o intervención manual.
* **RF-23. Gestión de disputas e incobrables:** Opción para que el responsable marque una factura *En disputa / Seguimiento pausado* (indicando motivo y fecha de revisión), suspendiendo temporalmente los recordatorios al cliente y generando una alerta interna al vencer la pausa. Procedimiento administrativo sujeto a aprobación para declarar una deuda como *Incobrable*.

## 6. Comunicaciones, Incidencias y Trabajo Colaborativo

* **RF-24. Plantillas de comunicación multidioma y control de rebotes:** Gestión de plantillas editables con variables dinámicas en Español e Inglés para envíos por Email y WhatsApp (simulado) según el idioma configurado en el cliente/contacto. Registro detallado de envíos (fecha, canal, destinatario, versión de plantilla, estado). Ante un rebote o fallo de entrega, generación de incidencia interna para corregir el contacto y reintentar sin duplicar la factura. Modo de prueba para simular envíos y previsualizar calendarios de recordatorios.
* **RF-25. Bandeja de notificaciones internas y alertas configurables:** Alertas en plataforma y por correo (con opción de resumen periódico) para responsables y administradores ante eventos relevantes (cobros, vencimientos, rebotes, disputas, revisiones anuales, aprobaciones o reglas parametrizadas como deuda acumulada > X €), diferenciando entre notificación *Leída* y tarea *Atendida/Resuelta*.
* **RF-26. Gestión de incidencias internas, comentarios y adjuntos:** Creación de incidencias administrativas asociadas a clientes o facturas (ej. *NIF erróneo*, *reclamación de importe*, *pago no localizado*) con estado, prioridad y responsable. Soporte de comentarios internos con menciones (`@usuario`), notas restringidas solo para administradores y carga de archivos adjuntos clasificados por tipo (presupuestos aceptados, justificantes de pago, órdenes de pedido).

## 7. Operativa Diaria, Informes, Configuración y API

* **FR27. Pantalla de trabajo diario y acciones masivas**
  * **FR27.1.** El sistema mostrará en la vista principal las tareas pendientes asignadas al usuario operativo (facturas próximas a vencer, vencidas, incidencias de entrega y cobros pendientes de conciliar).
  * **FR27.2.** El sistema permitirá realizar búsquedas globales de clientes y facturas combinando criterios (razón social, NIF, contacto, estado o número de factura).
  * **FR27.3.** El sistema permitirá a los usuarios guardar filtros de búsqueda recurrentes para consultas frecuentes.
    * *Dependencias: FR27.2*
  * **FR27.4.** El sistema ofrecerá una sección de acceso directo a clientes recientes o marcados como favoritos.
  * **FR27.5.** El sistema permitirá a los administradores ejecutar acciones masivas de reasignación de clientes entre usuarios, solicitando confirmación previa y mostrando un resumen de resultados.
    * *Dependencias: FR2.1*

* **FR28. Dashboard financiero y paquetes de exportación**
  * **FR28.1.** El sistema proporcionará a los administradores un panel de control con métricas financieras consolidadas (total facturado, cobrado, pendiente de cobro y saldo vencido).
  * **FR28.2.** El sistema permitirá desglosar cualquier métrica del panel de control para consultar el listado detallado de las facturas que la originan (*drill-down*).
    * *Dependencias: FR28.1*
  * **FR28.3.** El sistema permitirá a los administradores configurar objetivos de facturación mensual y anual para su seguimiento en el panel de control.
  * **FR28.4.** El sistema permitirá exportar informes y listados en formatos estructurados legibles (CSV) respetando los permisos de acceso del usuario.
  * **FR28.5.** El sistema registrará en la pista de auditoría interna la fecha, hora, filtros aplicados y usuario que generó cada exportación masiva.
    * *Dependencias: FR28.4*

* **FR29. Configuración global y panel de salud del sistema**
  * **FR29.1.** El sistema permitirá a los administradores registrar y modificar los datos fiscales y comerciales de la entidad emisora (razón social, NIF/CIF, domicilio fiscal, país y moneda predeterminada).
  * **FR29.2.** El sistema permitirá a los administradores configurar umbrales globales de alerta del sistema (por ejemplo, límite de días de gracia antes de escalar un impago o importe máximo permitido antes de requerir aprobación).
    * *Dependencias: FR29.1*

* **FR30. Servicios e integración externa (API)**
  * **FR30.1.** El sistema expondrá una interfaz programática segura para consultar el estado de deuda y obligaciones pendientes de un cliente mediante su identificador.
  * **FR30.2.** El sistema permitirá recibir solicitudes externas de baja comercial a través de la interfaz programática, registrándolas como tareas administrativas sin eliminar el histórico ni saltarse las reglas de cobro de deuda.
    * *Dependencias: FR30.1*

* Permitir asociar a una persona a varias empresas.
* Permitir asociar varias personas de contacto a una empresa con sus roles distinguidos dentro de esta misma.
* Desactivación/inactivación de personas de contacto de una empresa cliente manteniendo el historial de facturas enviadas a sus correos.
* El sistema al generar una factura debe registrar una copia para que si hay cambios o bajas se mantenga de:
  * La fecha en la que fue generada.
  * El cliente de la factura y quien la generó.
  * Destinatarios a los que se envió la factura.
* El sistema debe almacenar la fecha de inicio, servicio o plan contratado y la tarifa acordada para un cliente.
* El sistema debe tener un "dashboard" que proporcione una vista general de la situacion de los clientes y las facturas sin abrir cada cliente individualmente.
* El sistema debe poder generar un pdf con la factura.
  * Se debe poder mandar ese pdf a un correo de forma periodica. 