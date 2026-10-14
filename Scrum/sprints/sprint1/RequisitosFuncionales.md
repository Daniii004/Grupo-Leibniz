# Requisitos Funcionales (RF)

## 1. Gestión de Usuarios, Roles y Asignaciones

- **FR1**. El sistema permitirá a los administradores registrar a los usuarios.
- **FR1.1**. El sistema validará que el correo electrónico pertenezca al dominio corporativo autorizado (@turbine).
  - Dependencias: FR1
- **FR1.2**. El sistema permitirá a los usuarios solicitar el restablecimiento de contraseña mediante un enlace temporal.
  - Dependencias: FR1
- **FR1.3**. El sistema permitirá a los administradores desactivar cuentas de usuario conservando su historial de acciones.
  - Dependencias: FR1
- **FR1.4**. El sistema invalidará automáticamente las sesiones activas de un usuario en el momento de su desactivación.
  - Dependencias: FR1.3
- **FR1.5**. El sistema cerrará la sesión del usuario de forma automática tras un periodo de inactividad.
  - Dependencias: FR1
- **FR1.6**. El sistema bloqueará temporalmente el acceso tras superar cierto número de intentos fallidos consecutivos de inicio de sesión.
  - Dependencias: FR1<br><br>
- **FR2**. El sistema permitirá a los administradores asignar clientes específicos a usuarios operativos.
  - Dependencias: FR1
- **FR2.1**. El sistema permitirá reasignar un cliente a otro usuario, transfiriendo las tareas pendientes y conservando la autoría del responsable anterior.
  - Dependencias: FR2
- **FR2.2**. El sistema permitirá asignar temporalmente los clientes de un usuario a otro indicando una fecha de inicio y fin (por ejemplo, por vacaciones o baja médica).
  - Dependencias: FR2
- **FR2.3**. El sistema retirará automáticamente el acceso temporal al llegar la fecha de fin, devolviendo al cliente a su responsable inicial.
  - Dependencias: FR2.2

## 2. Gestión de Clientes y Contactos

* **RF-03. Separación de datos comerciales y fiscales:** Almacenamiento diferenciado de datos comerciales (nombre comercial, sector/temática, ubicación, etiquetas personalizadas como *estratégico*, *partner*, *beta*) y datos fiscales (razón social, NIF/identificador fiscal internacional, domicilio fiscal, país e idioma/moneda acordada).
* **RF-04. Múltiples contactos por cliente:** Asociación de múltiples personas de contacto por empresa según su rol (contratación, administración/recepción de facturas, recobro), permitiendo que un contacto esté vinculado a varias empresas (ej. gestorías externas). Permite desactivar contactos que dejan la empresa manteniendo el registro histórico de envíos pasados y registrar el consentimiento por canal (ej. WhatsApp).
* **RF-05. Ciclo de vida del cliente y control de duplicados:** Gestión de estados del cliente (*Pendiente de activación, Activo, Pausado, Baja solicitada, Inactivo, Bloqueado*). Detección preventiva de duplicados por NIF/ID fiscal al dar de alta o reactivar, e interfaz administrativa auditada para la fusión manual de empresas duplicadas.
* **RF-06. Importación masiva de clientes vía CSV:** Herramienta de carga con mapeo de columnas, vista previa antes de confirmar, validación de formatos y NIFs duplicados, gestión de filas erróneas y registro de resumen de la importación (creados, actualizados, rechazados y motivos).
* **RF-07. Línea temporal del cliente (Timeline):** Vista cronológica de negocio en la ficha del cliente que resume los eventos clave: fecha de alta, cambios de responsable, revisiones de tarifa, facturas emitidas, cobros, incidencias, pausas y bajas.

## 3. Suscripciones (Relaciones Comerciales), Tarifas y Catálogo

* **RF-08. Gestión de suscripciones:** Registro del acuerdo comercial mensual que origina la facturación recurrente: fecha de inicio (actual o futura), rango/tarifa asignada, condiciones particulares de pago (ej. vencimiento a 30 días que prevalece sobre el plazo estándar), responsable interno y adjunto de contratos/acuerdos firmados externamente.
* **RF-09. Pausa y baja de suscripciones:** Capacidad de pausar temporalmente una suscripción registrando motivo, fecha de inicio y fecha prevista de reactivación (avisando al responsable al llegar la fecha). Gestión de solicitudes de baja deteniendo la generación de nuevas mensualidades en la fecha efectiva sin cancelar la persecución de cobros pendientes.
* **RF-10. Revisión anual de rango tarifario:** Proceso anual que identifica clientes sujetos a revisión, registra su facturación anual verificada (manualmente con referencia documental o vía fuente simulada) y propone el cambio de tramo según la tabla de tarifas vigente. Requiere aprobación explícita de un responsable antes de aplicar la nueva cuota y no altera facturas emitidas con anterioridad.
* **RF-11. Catálogo ligero de conceptos reutilizables:** Mantenimiento de conceptos frecuentes para facturas puntuales (ej. *Instalación on-premise*, *Consultoría*, *Formación*, *Hardware*) con descripción, precio orientativo e impuesto por defecto modificables al crear la factura.

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

* **RF-27. Pantalla de trabajo diario, búsqueda global y acciones masivas:** Vista principal operativa con acceso directo a tareas pendientes (facturas por vencer/vencidas, cobros por conciliar, errores de entrega, aprobaciones y revisiones anuales). Buscador global (por razón social, nombre comercial, NIF, contacto, correo o nº de factura), filtros guardables, acceso a clientes recientes/favoritos y ejecución controlada de acciones masivas con confirmación y resumen de resultados (ej. reasignar cartera de un empleado).
* **RF-28. Dashboard financiero, informes y paquete para gestoría:** Panel exclusivo para Administradores con métricas reconciliables (total facturado, cobrado, pendiente, vencido, tiempo medio de cobro, ingresos recurrentes esperados vs. puntuales, ranking de deuda y cumplimiento de objetivos configurables). Exportación filtrada a CSV y PDF respetando permisos de visibilidad, informes programados y generación de paquetes de cierre mensual/trimestral para la gestoría (CSV + PDFs en ZIP) con control de versiones.
* **RF-29. Configuración global y panel de salud del sistema:** Administración de parámetros del sistema, datos fiscales del emisor (soportando evolución a multiempresa), monedas, tipos de impuestos, series, tablas de tarifas y umbrales. Panel de monitorización con el historial de ejecución de tareas programadas (generador mensual, recordatorios, backups y errores de integración).
* **RF-30. API externa:** Interfaz programática autenticada y documentada para que otros sistemas de la compañía (como Brain) consulten si un cliente tiene deuda pendiente o notifiquen solicitudes de baja comercial respetando las reglas de negocio.

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