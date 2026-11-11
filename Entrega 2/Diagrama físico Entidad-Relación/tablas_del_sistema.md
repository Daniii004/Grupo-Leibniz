# Descripción del Diagrama Físico Entidad-Relación

El diagrama `DiagramaEntidadRelacion.puml` representa el diseño físico de la base de datos del sistema interno de facturación y cobros de Turbine. Tiene **60 tablas** agrupadas en los mismos siete bloques que el diagrama de clases, y es la traducción a tablas de su vista 1/2 (modelo de dominio).

## Cómo leer el diagrama

* **`*` delante de una columna:** es obligatoria (`NOT NULL`). Sin asterisco, admite `NULL`.
* **`<<PK>>`:** clave primaria. **`<<FK>>`:** clave foránea. **`<<PK, FK>>`:** forma parte de la clave primaria y además es foránea. **`<<UNIQUE>>`:** no admite valores repetidos.
* **Cardinalidades (notación pata de gallo):**

| Símbolo | Significado |
|---|---|
| `\|\|--o{` | Uno obligatorio a muchos (la FK es `NOT NULL`). |
| `\|o--o{` | Uno opcional a muchos (la FK admite `NULL`). |
| `\|\|--\|{` | Uno a uno o muchos (el hijo existe al menos una vez). |
| `\|\|--o\|` | Uno a cero o uno. |
| `\|\|--\|\|` | Uno a uno. |

* **Tipos de datos:** `INT` para claves, `DECIMAL` para importes y porcentajes (aritmética exacta, RNF-06), `DATE` para fechas de negocio y `DATETIME` para instantes (se guardan en UTC, RNF-11), `BOOL`, `VARCHAR(n)` y `TEXT`.

---

## Reglas de traducción desde el diagrama de clases

| En el diagrama de clases | En la base de datos | Ejemplo |
|---|---|---|
| Clase | Tabla con `id` como clave primaria | `Cliente`, `Factura`, `Cobro` |
| Asociación 1 a muchos | Clave foránea en el lado "muchos" | `Suscripcion.idCliente` |
| Asociación opcional (`0..1`) | Clave foránea que admite `NULL` | `Factura.idSuscripcion` |
| Asociación muchos a muchos | Tabla intermedia con clave primaria compuesta | `PermisosDeRol`, `ClienteFavorito`, `MencionDeComentario` |
| Clase de asociación | Tabla intermedia con sus atributos | `VinculoContacto` → `ContactoDeCliente`; `PermisoIndividual` → `UsuarioPermiso` |
| Asociación de una clase consigo misma | Clave foránea a la propia tabla | `Rol.idRolPadre`, `Envio.idEnvioOriginal`, `Factura.idFacturaRectificada` |
| Composición 1 a 1 | Columnas en la tabla del todo | `PreferenciasNotificacion` → columnas `notif...` de `Usuario`; `DatosComerciales` → columnas de `Cliente`; `InformeImportacion` → columnas de `ImportacionCSV` |
| Atributo con lista de valores | Tabla hija | `etiquetas` → `EtiquetaDeCliente`; `canales` → `CanalDeContacto` |
| Enumerado | Columna `VARCHAR` con los valores del enumerado | `Factura.estado`, `Cliente.estado` |
| Herencia `DocumentoComercial` → `Presupuesto` / `Factura` | Una tabla por clase: la hija usa como clave primaria la clave de la madre | `Factura.idDocumento` es PK y FK a `DocumentoComercial` |
| Herencia `Factura` → `FacturaRectificativa` | Una sola tabla: columnas `idFacturaRectificada` y `motivoRectificacion` | Si `idFacturaRectificada` tiene valor, es una rectificativa |
| Herencia `Informe` → `PaqueteGestoria` | Una sola tabla: columnas `idPeriodo` y `version` | `Informe.tipo` distingue el paquete |
| Interfaz `Adjuntable` | Cuatro claves foráneas opcionales en `Adjunto`, de las que solo una tiene valor | `Adjunto.idCliente`, `idSuscripcion`, `idDocumento`, `idIncidencia` |
| Asociación derivada (`/`) | No se guarda | El periodo de una factura se obtiene de `fechaEmision` |

**Por qué `DocumentoComercial` tiene tabla propia:** las líneas (`LineaDocumento`) y los adjuntos apuntan al documento sin importar si es presupuesto o factura. Con una tabla por cada subclase habría que duplicar la tabla de líneas.

**Clases sin tabla:** `CalendarioLaboral` (solo tiene comportamiento; sus datos son las filas de `DiaFestivo`) y todas las clases de la vista 2/2 (servicios), que no guardan datos.

---

## Bloque 1: Usuarios, roles y asignaciones

Implementa el control de acceso basado en roles (RBAC) con los roles como datos (FR3).

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `Rol` | Roles del sistema. Se cargan tres filas iniciales: LECTOR, ADMINISTRADOR y SUPERADMINISTRADOR. | `Rol` | `idRolPadre` apunta al rol del que hereda y admite `NULL` (LECTOR no hereda de nadie). `nombre` único. |
| `Permiso` | Catálogo de permisos. | `Permiso` | `codigo` único. |
| `PermisosDeRol` | Qué permisos añade cada rol. | Asociación `Rol`–`Permiso` | PK compuesta (`idRol`, `idPermiso`). |
| `UsuarioPermiso` | Permisos concedidos o retirados a un usuario concreto (RU-30). | `PermisoIndividual` | PK compuesta (`idUsuario`, `idPermiso`). `idConcedidoPor` guarda quién lo decidió. |
| `Usuario` | Empleados con acceso. Incluye sus preferencias de notificación (FR26.3). | `Usuario`, `PreferenciasNotificacion` | `email` único. `activo` permite desactivar sin borrar (FR1.3). |
| `Sesion` | Sesiones abiertas (FR1.4, FR1.5). | `Sesion` | `token` único. |
| `TokenRestablecimiento` | Enlaces temporales de recuperación de contraseña (FR1.2). | `TokenRestablecimiento` | `token` único. |
| `AsignacionDeCliente` | Qué usuario lleva cada cliente (FR2). | `AsignacionCliente` | `idTitularOriginal` y `fechaFin` solo tienen valor en las cesiones temporales (FR2.2). |
| `FiltroGuardado` | Búsquedas guardadas por cada usuario (FR28.3). | `FiltroGuardado` | `criterios` se guarda serializado. |
| `ClienteFavorito` | Clientes marcados como favoritos (FR28.4). | Asociación `favoritos` | PK compuesta. |

**Cómo se calculan los permisos de un usuario:** se parte de su `idRol`, se recorren los `idRolPadre` hasta llegar a `NULL` sumando los permisos de `PermisosDeRol`, y por último se aplican las filas de `UsuarioPermiso` (`concedido` verdadero añade el permiso; falso lo quita).

**Regla que no se expresa con claves:** debe existir siempre al menos un usuario activo con rol SUPERADMINISTRADOR (ACT-01). Se comprueba en la aplicación antes de desactivar o cambiar de rol a un usuario.

## Bloque 2: Clientes y contactos

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `Cliente` | Empresas cliente con sus datos comerciales, estado y moneda. | `Cliente`, `DatosComerciales` | `idDatosFiscales` único (relación 1 a 1). `eliminado` es el borrado lógico (RNF-09). `idImportacion` indica de qué importación viene, si viene de alguna. |
| `DatosFiscales` | Razón social, identificador fiscal y domicilio. Sirve para los clientes y para Turbine como emisor. | `DatosFiscales` | `identificadorFiscal` único: impide dar de alta dos clientes con el mismo NIF (FR6.1). `codigoPostal` es opcional para no atarse al formato español (FR4.2). |
| `EtiquetaDeCliente` | Etiquetas comerciales de cada cliente (FR4.1). | Atributo `etiquetas` | PK compuesta (`idCliente`, `etiqueta`). |
| `PersonaContacto` | Personas de contacto (FR5). | `PersonaContacto` | `activo` permite desactivar sin perder el historial de envíos (FR5.3). |
| `CanalDeContacto` | Canales que admite cada persona (FR5.5). | Atributo `canales` | PK compuesta. |
| `ContactoDeCliente` | Qué persona es contacto de qué cliente y con qué rol (FR5.1, FR5.2). | `VinculoContacto` | PK compuesta. |
| `EventoHistorial` | Línea temporal del cliente (FR8.1). | `EventoHistorial` | — |
| `ImportacionCSV` | Importaciones masivas de clientes y su resultado (FR7). | `ImportacionCSV`, `InformeImportacion` | `idUsuario` identifica a quien la ejecutó (RU-53). |

El cliente no tiene columna de correo: los correos son de sus personas de contacto, y las facturas se envían a los contactos con rol de facturación.

## Bloque 3: Suscripciones, tarifas y catálogo

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `Suscripcion` | Acuerdos de cobro mensual (FR9, FR10). | `Suscripcion` | `fechaBaja`, `motivoPausa` y `fechaReactivacionPrevista` opcionales. |
| `LineaSuscripcion` | Conceptos contratados y precio pactado (FR9.4). | `LineaSuscripcion` | Toda suscripción tiene al menos una línea. |
| `Promocion` | Promociones y descuentos temporales (FR15.2, FR15.3). | `Promocion` | `fechaCaducidad` opcional. |
| `Concepto` | Catálogo de conceptos facturables (FR12). | `Concepto` | `idImpuesto` es el impuesto por defecto (FR12.2). |
| `Impuesto` | Tipos impositivos disponibles. | `Impuesto` | — |
| `TablaTarifas` | Versiones de la tabla de tarifas, cada una con su fecha de efecto (FR11.2). | `TablaTarifas` | — |
| `RangoTarifa` | Tramos de facturación anual y su precio. | `RangoTarifa` | `maximo` admite `NULL` en el último tramo (sin límite superior). |
| `RevisionAnual` | Revisiones anuales de tarifa (FR11.3). | `RevisionAnual` | `idAprobadaPor` y `fechaAprobacion` se rellenan al aprobar. Guarda el precio anterior y el nuevo. |

El gestor responsable de una suscripción no se guarda en `Suscripcion`: es el responsable del cliente según `AsignacionDeCliente`.

## Bloque 4: Presupuestos y facturación

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `DocumentoComercial` | Lo común a presupuestos y facturas: cliente, autor y fecha. | `DocumentoComercial` | `tipoDocumento` indica si es presupuesto o factura. |
| `LineaDocumento` | Líneas con concepto, cantidad, precio, descuento e IVA (FR14.1). | `LineaDocumento` | `idConcepto` es opcional y solo informativo: la línea guarda su propia copia de descripción, precio e IVA (FR12.2). |
| `Presupuesto` | Presupuestos y su aceptación o rechazo (FR13). | `Presupuesto` | PK = FK a `DocumentoComercial`. |
| `Factura` | Facturas puntuales, recurrentes, migradas y rectificativas (FR14 a FR20). | `Factura`, `FacturaRectificativa` | PK = FK a `DocumentoComercial`. `UNIQUE (idSerie, numero)`. `idSerie`, `numero` y `fechaEmision` son `NULL` en los borradores (FR18.2). `idPresupuesto` único. |
| `DocumentoPDF` | Copia inalterable de la factura emitida (FR18.4). | `DocumentoPDF` | PK = FK a `Factura` (1 a 0..1). Guarda la ruta y el *hash* del fichero, no el fichero. |
| `SerieFacturacion` | Series y contador de numeración (FR18.2). | `SerieFacturacion` | `UNIQUE (prefijo, anio)`. |
| `CategoriaProyecto` | Proyectos o centros de coste (FR14.3). | `CategoriaProyecto` | Renombrar no afecta a las facturas porque apuntan al `id`. |
| `PeriodoAdministrativo` | Meses y trimestres con su estado de cierre (FR19.4, FR19.5). | `PeriodoAdministrativo` | `idCerradoPor` y `fechaCierre` se rellenan al cerrar. |
| `SolicitudAprobacion` | Aprobaciones de operaciones sensibles (FR16). | `SolicitudAprobacion` | `idAprobador` es `NULL` mientras está pendiente. La aplicación impide que `idSolicitante` e `idAprobador` coincidan (FR16.2). |

No hay clave foránea entre `Factura` y `PeriodoAdministrativo`: el periodo de una factura se deduce de su `fechaEmision`, y cada factura cae en un mes y en un trimestre.

## Bloque 5: Cobros y conciliación

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `Cobro` | Movimientos de dinero recibidos (FR21.1). | `Cobro` | `idExterno` único: un evento de pago recibido dos veces no se contabiliza dos veces (FR21.3, RNF-05). `referencia` opcional. |
| `Conciliacion` | Aplicación de un cobro a una factura, total o parcial (FR21, FR22). | `Conciliacion` | Tiene `id` propio y no clave compuesta: un cobro puede volver a aplicarse a la misma factura tras una anulación. `idRealizadaPor` es `NULL` si la hizo una regla automática; `idAnuladaPor` y `motivoAnulacion` se rellenan al anular (FR22.2). |

## Bloque 6: Comunicaciones e incidencias

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `Plantilla` | Plantillas de mensajes por tipo e idioma (FR25.1). | `Plantilla` | — |
| `VersionPlantilla` | Historial de versiones de cada plantilla (FR25.7). | `VersionPlantilla` | Toda plantilla tiene al menos una versión. |
| `Envio` | Registro de cada mensaje enviado (FR25.2, FR25.6). | `Envio` | `idEnvioOriginal` enlaza un reintento con el envío original (FR25.5). `destinatario` guarda la dirección usada aunque el contacto cambie después. |
| `EnvioProgramado` | Envíos periódicos del PDF de una factura (FR23.4). | `EnvioProgramado` | — |
| `DestinatarioDeEnvioProgramado` | Contactos que reciben cada envío programado. | Asociación `destinatarios` | PK compuesta. |
| `Notificacion` | Avisos internos a los empleados (FR26). | `Notificacion` | `leida` es independiente del estado de la tarea (FR26.2). |
| `Tarea` | Tareas pendientes de cada empleado (FR28.1). | `Tarea` | `idCliente` permite traspasar las tareas al transferir el cliente (FR2.1). |
| `IncidenciaInterna` | Incidencias administrativas (FR27.1). | `IncidenciaInterna` | `idCreadaPor` e `idResponsable` obligatorios. `idEnvio` tiene valor si la incidencia nace de un rebote (FR25.3). `resolucion` es obligatoria al cerrar (FR27.5). |
| `Comentario` | Comentarios de las incidencias (FR27.2). | `Comentario` | `restringido` limita la lectura a administradores. |
| `MencionDeComentario` | Usuarios mencionados en un comentario. | Asociación `menciona` | PK compuesta. |
| `Adjunto` | Archivos adjuntos (FR27.3, FR27.4). | `Adjunto`, `Adjuntable` | `CHECK`: exactamente una de las cuatro FK tiene valor. Guarda la ruta del fichero, no el fichero. |

## Bloque 7: Operativa, informes y configuración

| Tabla | De qué se ocupa | Clase de origen | Claves y restricciones |
|---|---|---|---|
| `AccionMasiva` | Operaciones masivas ejecutadas, con sus filtros y el número de elementos (FR29). | `AccionMasiva` | — |
| `ObjetivoFacturacion` | Objetivos mensuales y anuales (FR30.3). | `ObjetivoFacturacion` | `mes` es `NULL` en los objetivos anuales. |
| `TipoCambio` | Tipos de cambio para informes (FR30.10). | `TipoCambio` | No afecta al importe legal de las facturas. |
| `Informe` | Informes y paquetes para la gestoría generados (FR30.7, FR30.8). | `Informe`, `PaqueteGestoria` | `idPeriodo` y `version` solo tienen valor en los paquetes. |
| `InformeProgramado` | Informes periódicos (FR30.9). | `InformeProgramado` | — |
| `DestinatarioDeInforme` | Usuarios autorizados que reciben cada informe programado. | Asociación `destinatarios` | PK compuesta. |
| `EntidadEmisora` | Turbine como emisor de las facturas (FR31.1). | `EntidadEmisora` | `idDatosFiscales` único. |
| `ConfiguracionSistema` | Parámetros globales (FR31.2). | `ConfiguracionSistema` | Tiene una única fila. `monedas` es la lista de códigos ISO admitidos. |
| `DiaFestivo` | Festivos para adaptar los avisos de impago (FR23.3). | `DiaFestivo` | `fecha` única. |
| `CredencialAPI` | Credenciales de sistemas externos (FR32.2). | `CredencialAPI` | Se guarda el *hash* de la clave, nunca la clave. |
| `RegistroAuditoria` | Pista de auditoría (RNF-08). | `RegistroAuditoria` | Solo admite inserciones. `idUsuario` es `NULL` si la acción la hizo un proceso automático o la API (`idCredencialAPI`). |
| `EjecucionProceso` | Resultado de cada proceso automático (FR33.1). | `EjecucionProceso` | `fin` es `NULL` mientras se ejecuta. |

El contenido de un paquete para la gestoría (facturas, rectificativas y cobros) no se guarda en tablas intermedias: son los documentos y cobros cuyas fechas caen dentro del `PeriodoAdministrativo`, y el fichero generado queda en `Informe.rutaArchivo`.

---

## Requisitos no funcionales reflejados en el diseño

| Requisito | Cómo se refleja |
|---|---|
| RNF-05 Idempotencia | `Cobro.idExterno` único; `UNIQUE (idSerie, numero)` en `Factura`. |
| RNF-06 Exactitud aritmética | Todos los importes y porcentajes son `DECIMAL`. |
| RNF-07 Seguridad | Solo se guardan *hashes* de contraseñas y claves de API; los tokens son únicos. |
| RNF-08 Auditoría inmutable | `RegistroAuditoria` con valor anterior y nuevo, y solo inserciones. |
| RNF-09 Privacidad y conservación | Borrado lógico (`Cliente.eliminado`, columnas `activo`); las facturas conservan su copia en `DocumentoPDF`. |
| RNF-11 Internacionalización | `DatosFiscales` sin provincia ni formato de NIF español; `pais`, `idioma` y `moneda` con códigos ISO. |

## Relación con los otros diagramas

* **Clases:** la vista 1/2 (`DiagramaClasesDominio.puml`) es el origen de estas tablas; su descripción está en `clases_del_sistema.md`.
* **Componentes:** todas las tablas viven en el componente `[Base de Datos]`, al que cada componente del backend accede mediante `IDatabase`. Los ficheros (PDF, adjuntos, exportaciones) están en el componente `[Almacenamiento de Archivos]`; la base de datos solo guarda su ruta.
