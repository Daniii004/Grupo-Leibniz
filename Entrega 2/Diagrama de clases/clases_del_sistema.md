# Descripción del Diagrama de Clases

El diagrama de clases representa la estructura estática del sistema interno de facturación y cobros de Turbine. Por tamaño se presenta en **dos vistas** del mismo modelo:

| Vista | Archivo | Qué contiene |
|---|---|---|
| 1/2 Modelo de dominio | `DiagramaClasesDominio.puml` | Las clases que guardan información (clientes, facturas, cobros...), con sus atributos, operaciones, asociaciones y multiplicidades. |
| 2/2 Servicios y procesos | `DiagramaClasesServicios.puml` | Las clases que ejecutan los casos de uso y los procesos automáticos, y de qué clases del dominio dependen. |

Las clases de dominio aparecen en la vista 2/2 solo con su nombre y con fondo gris, para no repetir sus atributos.

Las dos vistas se dibujan de izquierda a derecha (`left to right direction`): en vertical salían imágenes de más de 12.000 píxeles de ancho.

## Cómo leer los diagramas

* **Paquetes:** cada paquete corresponde a un bloque de casos de uso de la Entrega 1 (bloques 1 a 7).
* **Visibilidad:** `-` privado, `#` protegido, `+` público.
* **`[0..1]` en un atributo:** el valor es opcional.
* **`/` delante de un nombre de asociación:** es derivada, se calcula a partir de otros datos y no se guarda.
* **Atributos de tipo `Usuario`** (`creadoPor`, `aprobador`, `cerradoPor`, `anuladaPor`...): son asociaciones con `Usuario` que indican quién hizo una operación. Se muestran como atributo, con su multiplicidad entre corchetes, porque dibujadas como líneas más de veinte llegaban a `Usuario` y el diagrama no se podía leer. En la base de datos son claves foráneas a la tabla `Usuario`.
* **Estereotipos de la vista 2/2:**
  * `<<control>>`: clase que coordina un caso de uso o un proceso.
  * `<<boundary>>`: clase que habla con un sistema externo.
  * `<<DTO>>`: objeto que solo transporta datos de respuesta.
  * `<<singleton>>`: clase con una única instancia.
* **Parámetro `actor : Usuario`:** la operación la inicia una persona; el servicio comprueba su permiso antes de ejecutarla. Las operaciones sin `actor` las lanza el `Planificador` u otro servicio.

---

## Decisiones de diseño

### 1. Los roles son datos, no tipos de usuario
Hay una sola clase `Usuario`. Cada usuario tiene un `Rol`, cada rol tiene una lista de `Permiso` y hereda los del rol indicado por la asociación `heredaDe`. Los tres roles del sistema son instancias de `Rol`:

| Rol | Hereda de | Permisos que añade (ejemplos) |
|---|---|---|
| LECTOR | — | `VER_CLIENTES_ASIGNADOS`, `CREAR_FACTURA`, `CONCILIAR_COBRO`, `CREAR_INCIDENCIA` |
| ADMINISTRADOR | LECTOR | `VER_TODOS_LOS_CLIENTES`, `ASIGNAR_CLIENTES`, `APROBAR_SOLICITUDES`, `CERRAR_PERIODO` |
| SUPERADMINISTRADOR | ADMINISTRADOR | `CONFIGURAR_SISTEMA`, `GESTIONAR_TARIFAS`, `REABRIR_PERIODO`, `APROBAR_OPERACIONES_CRITICAS` |

* **Por qué:** FR3 pide un modelo de autorización centralizado basado en roles; RU-30 y FR1.6 exigen conceder o retirar permisos a un usuario concreto (clase de asociación `PermisoIndividual`); y cambiar el rol de alguien no debe crear un usuario nuevo, porque se perdería el rastro de lo que hizo (FR1.3, RNF-08).
* **Coherencia con los casos de uso:** la herencia entre actores (el administrador hace todo lo del usuario y el superadministrador todo lo del administrador) se conserva en la asociación `heredaDe` entre roles.
* **Restricción:** debe existir siempre al menos un usuario activo con rol SUPERADMINISTRADOR (ACT-01).

### 2. Los casos de uso están en los servicios
Las clases de dominio solo tienen operaciones sobre sus propios datos (`Factura.emitir()`, `Suscripcion.pausar()`). Las operaciones de los casos de uso (`registrarUsuario`, `transferirCliente`, `conciliarManualmente`...) están en las clases `<<control>>` de la vista 2/2.

* **Por qué:** si `Usuario` tuviera las operaciones de todos los casos de uso dependería de casi todas las clases del sistema. Con servicios, cada bloque cambia por separado y coincide con un componente del diagrama de componentes.

### 3. Autorización y auditoría en un único punto
Todos los servicios que atienden a una persona dependen de `ServicioAutorizacion`, que es la única clase que decide si un usuario tiene un permiso y si puede ver un cliente (todos, o solo los asignados o cedidos). Los servicios preguntan por permisos, nunca por roles. Las operaciones que cambian datos se registran con `ServicioAuditoria`.

* **Por qué:** FR3, RNF-07 (la autorización se valida en el servidor en cada petición) y RNF-08 (auditoría inalterable).

### 4. Generalizaciones
| Superclase | Subclases | Motivo |
|---|---|---|
| `DocumentoComercial` (abstracta) | `Presupuesto`, `Factura` | Comparten cliente, autor, líneas y cálculo de totales; un presupuesto aceptado se convierte en factura sin reescribir los datos (FR13.2). |
| `Factura` | `FacturaRectificativa` | Una rectificativa es una factura completa que además apunta a la que corrige (FR19.2). |
| `Informe` | `PaqueteGestoria` | El paquete para la gestoría es un informe con versión y periodo (FR30.8). |
| `Adjuntable` (interfaz) | `Cliente`, `Suscripcion`, `DocumentoComercial`, `IncidenciaInterna` | Todos admiten archivos adjuntos con las mismas reglas (FR9.1, FR13.3, FR27.3, FR27.4). |
| `PasarelaPago` (interfaz) | `StripeSimulado` | Permite cambiar de pasarela sin tocar la conciliación (RNF-11). |
| `ProveedorMensajeria` (interfaz) | `CorreoSMTP`, `WhatsAppAPI`, `MensajeriaSimulada` | Permite cambiar de proveedor o canal; la versión simulada es el modo de prueba (FR25.4). |

### 5. Composición y agregación
* **Composición (`*--`):** la parte no existe sin el todo. Ejemplos: `Cliente` y sus `DatosFiscales`, `DocumentoComercial` y sus `LineaDocumento`, `Factura` y su `DocumentoPDF`, `Plantilla` y sus `VersionPlantilla`, `IncidenciaInterna` y sus `Comentario`.
* **Agregación (`o--`):** el todo agrupa elementos que existen por sí mismos. Ejemplos: `PaqueteGestoria` agrupa `Factura` y `Cobro`; `ConfiguracionSistema` agrupa `SerieFacturacion`, `Impuesto`, `Plantilla` y `TablaTarifas`.

### 6. Clases de asociación
* `VinculoContacto` (entre `Cliente` y `PersonaContacto`): guarda el rol que tiene esa persona en esa empresa. Una misma persona puede ser contacto de varias empresas (FR5.1).
* `PermisoIndividual` (entre `Usuario` y `Permiso`): guarda si el permiso se concede o se retira y quién lo decidió.
* `Conciliacion` **no** es clase de asociación aunque une `Cobro` y `Factura`: tras anular una conciliación (FR22.2), el mismo cobro puede volver a aplicarse a la misma factura, y una clase de asociación solo admite un enlace por pareja.

### 7. Datos derivados
* El responsable de un cliente (`Cliente.getResponsable()`) y el gestor de una suscripción (`Suscripcion.getGestorResponsable()`) se obtienen de la `AsignacionCliente` vigente. Así, al transferir un cliente (FR2.1) no queda ningún dato desactualizado.
* El periodo administrativo de una factura (`/perteneceA`) se obtiene de su fecha de emisión: cada factura cae en un mes y en un trimestre (FR19.1).

### 8. Lo emitido no cambia
`LineaDocumento` copia la descripción, el precio y el IVA del `Concepto` en el momento de crear la línea, y `DocumentoPDF` guarda la copia inalterable de la factura con su *hash*. Así los cambios en el catálogo, en las tarifas o en la configuración no alteran las facturas ya emitidas (FR11.1, FR12.2, FR18.4, FR31.2).

---

## Vista 1/2: modelo de dominio

### Bloque 1: Usuarios, roles y asignaciones (FR1, FR2, FR3)
| Clase | De qué se ocupa |
|---|---|
| `Usuario` | Identidad y credenciales de un empleado. Sabe responder si tiene un permiso. |
| `Rol` | Conjunto de permisos con nombre. Hereda los de otro rol. |
| `Permiso` | Capacidad concreta que se puede conceder (por ejemplo, `CREAR_FACTURA`). |
| `PermisoIndividual` | Excepción de un usuario respecto a su rol: permiso concedido o retirado. |
| `Sesion` | Sesión abierta de un usuario. Caduca por inactividad y se cierra al desactivar la cuenta (FR1.4, FR1.5). |
| `TokenRestablecimiento` | Enlace temporal para restablecer la contraseña (FR1.2). |
| `AsignacionCliente` | Indica qué usuario lleva qué cliente. Puede ser principal o temporal, con fechas y titular original (FR2.2). |

### Bloque 2: Clientes y contactos (FR4 a FR8)
| Clase | De qué se ocupa |
|---|---|
| `Cliente` | Empresa a la que Turbine presta servicio, con su estado y su moneda de trabajo. |
| `DatosComerciales` | Nombre, sector, ubicación y etiquetas, separados de los datos fiscales (FR4.1). |
| `DatosFiscales` | Razón social, identificador fiscal y domicilio, sin atarse al formato español (FR4.2). Se reutiliza para la entidad emisora. |
| `PersonaContacto` | Persona de una empresa cliente, con los canales por los que admite comunicación (FR5.5). |
| `VinculoContacto` | Rol de una persona de contacto en un cliente concreto (FR5.2). |
| `EventoHistorial` | Entrada de la línea temporal del cliente: alta, cambios de responsable, facturas, pagos... (FR8.1). |
| `ImportacionCSV` | Importación de clientes desde un archivo, con mapeo de columnas, vista previa y detección de NIF duplicados (FR7). |
| `InformeImportacion` | Resultado de la importación: clientes guardados y errores (FR7.2). |

### Bloque 3: Suscripciones, tarifas y catálogo (FR9 a FR12, FR15)
| Clase | De qué se ocupa |
|---|---|
| `Suscripcion` | Acuerdo de cobro mensual con un cliente. Se puede pausar, reanudar y dar de baja; calcula prorrateos (FR15.1). |
| `LineaSuscripcion` | Concepto contratado dentro de la suscripción, con su precio pactado (FR9.4). |
| `Promocion` | Primer mes gratis, cobro desde el mes siguiente o descuento temporal con caducidad (FR15.2, FR15.3). |
| `Concepto` | Elemento del catálogo (formación, consultoría...) con precio de referencia e impuesto por defecto (FR12). |
| `Impuesto` | Tipo impositivo disponible. |
| `TablaTarifas` | Tabla de precios por rangos de facturación anual, con fecha de efecto (FR11.2). |
| `RangoTarifa` | Un tramo de la tabla: mínimo, máximo y precio. |
| `RevisionAnual` | Revisión de la tarifa de una suscripción: dato de facturación, precio anterior y nuevo, y quién la aprobó (FR11.3). |

### Bloque 4: Presupuestos y facturación (FR13 a FR20)
| Clase | De qué se ocupa |
|---|---|
| `DocumentoComercial` | Lo común a presupuestos y facturas: cliente, autor, líneas y totales. |
| `LineaDocumento` | Línea con concepto, cantidad, precio, descuento e IVA (FR14.1). |
| `Presupuesto` | Oferta para un servicio puntual; se acepta o rechaza y se convierte en factura (FR13). |
| `Factura` | Documento fiscal. Se valida, se emite con número correlativo y pasa por los doce estados de FR18.1. |
| `FacturaRectificativa` | Factura que corrige a otra ya emitida (FR19.2). |
| `SerieFacturacion` | Serie y contador que asignan el número al emitir; los borradores no gastan número (FR18.2). |
| `DocumentoPDF` | Copia inalterable de la factura tal como se emitió (FR18.4). |
| `CategoriaProyecto` | Proyecto o centro de coste; se puede renombrar sin perder el histórico (FR14.3). |
| `PeriodoAdministrativo` | Mes o trimestre que se cierra y, con permiso superior y motivo, se reabre (FR19.4, FR19.5). |
| `SolicitudAprobacion` | Petición de aprobar una operación sensible, con su estado y el comentario del aprobador (FR16.3). Quien la solicita no puede aprobarla (FR16.2). |

### Bloque 5: Cobros y conciliación (FR21, FR22)
| Clase | De qué se ocupa |
|---|---|
| `Cobro` | Movimiento de dinero recibido de Stripe, Revolut, banco o registrado a mano. Su identificador externo es único para no contarlo dos veces (FR21.3). |
| `Conciliacion` | Aplicación de un cobro, total o parcial, a una factura. Guarda qué regla o usuario la justificó y, si se anula, quién y por qué (FR21.4, FR22.2). |

### Bloque 6: Comunicaciones e incidencias (FR23, FR25 a FR27)
| Clase | De qué se ocupa |
|---|---|
| `Plantilla` | Modelo de mensaje en español o inglés (FR25.1). |
| `VersionPlantilla` | Versión concreta de una plantilla; se conserva el historial (FR25.7). |
| `Envio` | Cada mensaje enviado a un cliente: fecha, canal, destinatario, versión de la plantilla y estado. Enlaza con sus reintentos (FR25.2, FR25.5). |
| `EnvioProgramado` | Envío periódico del PDF de una factura a sus destinatarios (FR23.4). |
| `Notificacion` | Aviso interno a un empleado; puede estar leída o no (FR26.2). |
| `Tarea` | Trabajo pendiente de un empleado sobre un cliente o una factura; se atiende y se resuelve (FR26.2, FR28.1). |
| `PreferenciasNotificacion` | Cómo quiere cada usuario recibir sus avisos (FR26.3). |
| `IncidenciaInterna` | Incidencia administrativa sobre un cliente o una factura, con estado, prioridad y responsable (FR27.1). |
| `Comentario` | Comentario de una incidencia, con menciones y posibilidad de restringirlo a administradores (FR27.2). |
| `Adjuntable` | Interfaz de los elementos que admiten archivos adjuntos. |
| `Adjunto` | Archivo adjunto con tipo, formato y tamaño limitados (FR27.3, FR27.4). |

### Bloque 7: Operativa, informes y configuración (FR28 a FR33)
| Clase | De qué se ocupa |
|---|---|
| `FiltroGuardado` | Búsqueda frecuente que guarda un usuario (FR28.3). |
| `AccionMasiva` | Operación sobre muchos elementos, con recuento y confirmación previa (FR29). |
| `ObjetivoFacturacion` | Objetivo mensual o anual para el panel (FR30.3). |
| `TipoCambio` | Tipo de cambio registrado solo para informes (FR30.10). |
| `Informe` | Informe generado con sus filtros y formato (FR30.6, FR30.7). |
| `InformeProgramado` | Informe periódico y sus destinatarios autorizados (FR30.9). |
| `PaqueteGestoria` | Paquete mensual o trimestral para la gestoría, con versión (FR30.8). |
| `EntidadEmisora` | Turbine como emisor de las facturas, con sus datos fiscales (FR31.1). |
| `ConfiguracionSistema` | Parámetros globales: plazos, cadencias, dominio de correo, umbrales (FR31.2). |
| `CalendarioLaboral` | Sabe si un día es laborable para adaptar los avisos de impago (FR23.3). |
| `DiaFestivo` | Día festivo del calendario. |
| `CredencialAPI` | Credencial de un sistema externo, independiente de las de los usuarios (FR32.2). |
| `RegistroAuditoria` | Entrada inalterable de la pista de auditoría, con valor anterior y nuevo (RNF-08). |
| `EjecucionProceso` | Resultado de un proceso automático: elementos procesados, fallidos y motivo (FR33.1). |

### Enumerados
| Enumerado | Valores | Requisito |
|---|---|---|
| `TipoAsignacion` | PRINCIPAL, TEMPORAL | FR2.2 |
| `EstadoCliente` | PENDIENTE_ACTIVACION, ACTIVO, PAUSADO, BAJA_SOLICITADA, BAJA, BLOQUEADO | FR6, FR6.3 |
| `RolContacto` | COMERCIAL, ADMINISTRACION, COBROS, FACTURACION, GESTOR_EXTERNO | FR5.1, FR5.2 |
| `CanalComunicacion` | EMAIL, TELEFONO, WHATSAPP | FR5.5 |
| `EstadoSuscripcion` | PENDIENTE_ACTIVACION, ACTIVA, PAUSADA, BAJA | FR9.3, FR10 |
| `TipoPromocion` | PRIMER_MES_GRATIS, COBRO_DESDE_MES_SIGUIENTE, DESCUENTO_TEMPORAL | FR15.2, FR15.3 |
| `EstadoRevision` | PENDIENTE_DATO, PENDIENTE_APROBACION, APROBADA, RECHAZADA | FR11.3 |
| `EstadoPresupuesto` | BORRADOR, ENVIADO, ACEPTADO, RECHAZADO, FACTURADO | FR13 |
| `EstadoFactura` | BORRADOR, PENDIENTE_APROBACION, APROBADA, EMITIDA, ENVIADA, EN_DISPUTA, VENCIDA, PARCIALMENTE_CONCILIADA, PAGADA, ANULADA, INCOBRABLE, RECTIFICADA | FR18.1 |
| `TipoFactura` | PUNTUAL, RECURRENTE, MIGRADA | FR14, FR20.1 |
| `TipoPeriodo` | MENSUAL, TRIMESTRAL | FR19.4 |
| `TipoOperacion` | FACTURA_CONDICIONES_ESPECIALES, DESCUENTO_SUPERIOR_UMBRAL, RECTIFICATIVA, DEUDA_INCOBRABLE | FR16, FR19.6, FR24.3 |
| `EstadoAprobacion` | PENDIENTE, APROBADA, RECHAZADA | FR16.3 |
| `OrigenCobro` | STRIPE, REVOLUT, BANCO, MANUAL | FR21.1 |
| `EstadoCobro` | PENDIENTE_CONCILIACION, CONCILIADO, PARCIALMENTE_APLICADO, ANULADO | FR21.3, FR22 |
| `TipoPlantilla` | ENVIO_FACTURA, RECORDATORIO_PREVIO, AVISO_VENCIMIENTO, AVISO_IMPAGO | FR23 |
| `EstadoEnvio` | EN_COLA, ENVIADO, ENTREGADO, FALLIDO, REINTENTADO | FR25.6 |
| `EstadoTarea` | PENDIENTE, ATENDIDA, RESUELTA | FR26.2 |
| `EstadoIncidencia` | ABIERTA, EN_CURSO, CERRADA | FR27.1 |
| `Prioridad` | BAJA, MEDIA, ALTA | FR27.1 |
| `TipoAdjunto` | CONTRATO, PRESUPUESTO_ACEPTADO, JUSTIFICANTE_PAGO, ORDEN_PEDIDO, OTRO | FR27.3 |
| `FormatoExportacion` | PDF, CSV | FR30.7 |
| `EstadoEjecucion` | PENDIENTE, PROCESANDO, COMPLETADO, COMPLETADO_CON_ERRORES | FR33.3 |

*Seguimiento pausado* (FR24.1) no es un estado de `EstadoFactura`: es el atributo `seguimientoPausado`, porque una factura vencida puede tener el seguimiento pausado sin dejar de estar vencida.

---

## Vista 2/2: servicios y procesos

Cada servicio corresponde a un componente del diagrama de componentes.

| Clase | De qué se ocupa | Casos de uso | Componente |
|---|---|---|---|
| `ServicioAutenticacion` | Inicio y cierre de sesión, cierre por inactividad y restablecimiento de contraseña. | Bloque 1: restablecer contraseña, cerrar sesiones | Autenticación |
| `ServicioAutorizacion` | Decide si un usuario tiene un permiso y qué clientes puede ver. Verifica la separación de funciones. | Bloque 1: aplicar autorización, bloquear acciones no permitidas | Gestión de Roles |
| `ServicioUsuarios` | Alta, desactivación, cambio de rol y permisos individuales. Valida el dominio del correo (FR1.1). | Bloque 1: registrar y desactivar cuentas | Gestión de Roles |
| `ServicioAsignaciones` | Asigna, transfiere y cede clientes; devuelve las cesiones al acabar su fecha. | Bloque 1: asignar, transferir, ceder y devolver cliente | Gestión de Roles |
| `ServicioClientes` | Altas, fichas, contactos, estados, fusión de duplicados, importación CSV e historial. | Bloque 2 completo | Gestión de Fichas y Contactos |
| `ServicioSuscripciones` | Ciclo de vida de las suscripciones y cambios programados. | Bloque 3: alta, modificación, pausa, reanudación y baja | Suscripciones Recurrentes |
| `ServicioCatalogo` | Catálogo de conceptos y tabla de tarifas. | Bloque 3: catálogo y tabla de tarifas | Catálogo y Tarifas |
| `ServicioRevisionAnual` | Prepara las revisiones anuales, registra el dato de facturación y tramita su aprobación. | Bloque 3: revisión anual | Catálogo y Tarifas |
| `ServicioFacturacion` | Presupuestos, facturas puntuales, previsualización, emisión, rectificativas, periodos e importación de histórico. | Bloque 4 | Emisión de Facturas |
| `ServicioFacturacionRecurrente` | Genera las facturas mensuales el día 1 (FR14.2). | Bloque 4: facturación recurrente | Emisión de Facturas |
| `ServicioAprobaciones` | Solicitud, resolución y reasignación de aprobaciones; bandeja del solicitante. | Bloque 4: aprobar facturas, bandeja de aprobaciones | Emisión de Facturas |
| `ServicioConciliacion` | Recibe cobros, descarta duplicados, concilia de forma automática o manual y anula cobros mal asociados. | Bloque 5: procesar y conciliar cobros | Conciliación de Pagos |
| `PasarelaPago`, `StripeSimulado`, `ImportadorMovimientos` | Adaptadores de las fuentes de cobro. | Bloque 5 | Conciliación de Pagos |
| `ServicioDeuda` | Calcula la deuda de un cliente, marca vencidas, gestiona disputas, pausas e incobrables y lanza recordatorios e impagos. | Bloque 5: seguimiento de impagos, disputa, incobrable | Gestión de Deuda e Impagos |
| `ServicioComunicaciones` | Envía mensajes a clientes con las plantillas, registra los envíos, reintenta y programa envíos periódicos. | Bloque 5: comunicaciones; bloque 6: registro de envíos, plantillas, modo prueba | Comunicaciones Externas |
| `ProveedorMensajeria` y adaptadores | Entrega real del mensaje por correo o WhatsApp, o simulada. | Bloque 6 | Comunicaciones Externas |
| `ServicioNotificaciones` | Avisos internos, tareas pendientes, bandeja global y resúmenes diarios. | Bloque 6: notificaciones; bloque 7: tareas pendientes | Notificaciones Internas |
| `ServicioIncidencias` | Incidencias, comentarios con menciones, adjuntos y cierre con resolución. | Bloque 6: incidencias | Incidencias Internas |
| `BuscadorGlobal` | Búsqueda de clientes y facturas y filtros guardados. | Bloque 7: buscar | Reportes y Dashboard |
| `PanelFinanciero` | Métricas, desglose, previsión y objetivos. Devuelve `MetricasFinancieras`. | Bloque 7: panel, desglose, objetivos | Reportes y Dashboard |
| `ServicioInformes` | Informes, paquetes para la gestoría, informes programados y acciones masivas. | Bloque 7: informes y acciones masivas | Reportes y Dashboard |
| `ServicioConfiguracion` | Datos del emisor, parámetros globales, series, festivos y estado del sistema. | Bloque 7: datos fiscales, parámetros globales, monitorizar | Configuración del Sistema |
| `APIDeudas` | Punto de entrada para sistemas externos; devuelve `EstadoDeuda`. | Bloque 7: consultar deudas vía API | API Gateway Externa |
| `ServicioAuditoria` | Escribe y consulta la pista de auditoría. | RU-11 | Auditoría |
| `Planificador` | Lanza los procesos automáticos y registra cada ejecución. Es el actor *Temporizador* de los casos de uso. | Bloques 1, 3, 5 y 7: procesos automáticos | Planificador de Tareas |

---

## Elementos que pide la guía de la entrega

| Elemento | Dónde se ve |
|---|---|
| Clases, atributos y operaciones | En las dos vistas. |
| Asociaciones y multiplicidades | Vista 1/2: todas las asociaciones dibujadas llevan multiplicidad en los dos extremos; las referencias de trazabilidad a `Usuario` van como atributos con su multiplicidad. |
| Generalizaciones | `DocumentoComercial`, `Factura` e `Informe` con sus subclases; realizaciones de `Adjuntable`, `PasarelaPago` y `ProveedorMensajeria`. |
| Agregaciones y composiciones | Ver la decisión de diseño 5. |
| Coherencia con requisitos y casos de uso | Cada paquete es un bloque de casos de uso; cada clase indica su requisito en las tablas de este documento. |

## Relación con los otros diagramas

* **Entidad-Relación:** cada clase de la vista 1/2 tiene su tabla. La correspondencia y las reglas de traducción están en `tablas_del_sistema.md`.
* **Componentes:** cada servicio de la vista 2/2 pertenece a un componente. La correspondencia está en `componentes_del_sistema.md`.
* **Secuencia:** los mensajes de los diagramas de secuencia son operaciones de estas clases. Todo escenario iniciado por una persona empieza con `ServicioAutorizacion.autorizar(actor, permiso)`.
