# Descripción de los Diagramas de Secuencia

Los diagramas de secuencia muestran cómo colaboran las clases del sistema interno de facturación y cobros de Turbine para resolver escenarios concretos de los casos de uso de la Entrega 1. Cada diagrama agrupa varios casos de uso relacionados y los presenta en **secciones** separadas, una por escenario.

| Diagrama | Archivo | Casos de uso | Qué muestra |
|---|---|---|---|
| A1 | `Secuencia_A1_AltaDesactivacionUsuario.puml` | CU-101, CU-103, CU-104, CU-110, CU-112 | Alta de usuario con control de permisos y validación del dominio `@turbine`; desactivación de la cuenta conservando su historial y cerrando sus sesiones activas. |
| A2 | `Secuencia_A2_AltaClienteContactosEstado.puml` | CU-201, CU-203, CU-204, CU-205, CU-206 | Alta de cliente con datos comerciales y fiscales (también extranjeros); alta y desactivación de contactos con el aviso del único contacto de facturación; cambio de estado con sus validaciones. |
| B3 | `Secuencia_B3_CobrosImpagosDisputa.puml` | CU-501, CU-502, CU-503, CU-504, CU-505, CU-601 | Carga de movimientos sin contar dos veces un cobro; conciliación manual y reversión; avisos de impago respetando el calendario laboral; disputa y pausa por incidencia; deuda incobrable con aprobación. |
| B4 | `Secuencia_B4_PanelInformesConfiguracionAPI.puml` | CU-704, CU-707, CU-710, CU-711, CU-713 | Panel financiero con filtros; exportación de informes y paquetes para la gestoría; cambios globales auditados; consulta de deudas vía API (401 / 429 / 200); proceso en segundo plano que aísla los errores. |

## Cómo leer los diagramas

* **Participantes:** se nombran como `objeto : Clase`, igual que en UML. Todas las clases existen en el diagrama de clases: los servicios en la vista 2/2 (`DiagramaClasesServicios.puml`) y los objetos de datos en la vista 1/2 (`DiagramaClasesDominio.puml`).
* **Recuadros:** cada recuadro es un paquete de la vista 2/2 del diagrama de clases (`DiagramaClasesServicios.puml`), con el mismo nombre: `Acceso, autorización y usuarios`, `Clientes, suscripciones y catálogo`, `Facturación y aprobaciones`, `Cobros y deuda`, `Comunicaciones e incidencias` y `Operativa, informes, configuración, API y procesos`. Las clases del modelo de dominio van con fondo gris y fuera de paquete, igual que en esa vista. Así cada participante está en el mismo sitio que en el diagrama de clases.
* **Color de los mensajes** (ver la leyenda de cada diagrama), con la misma paleta que las dependencias del diagrama de clases: negro entre los actores y los servicios y entre servicios, azul hacia `ServicioAutorizacion` (comprobación de permisos), morado para los procesos que lanza el `Planificador`, verde hacia los adaptadores de sistemas externos (`ImportadorMovimientos`, `ProveedorMensajeria`) y gris hacia los objetos del modelo de dominio y los DTO. Línea continua para las llamadas y discontinua para las respuestas.
* **Estereotipos:** `<<control>>` en las clases de servicio, como en el diagrama de clases.
* **Mensajes:** cada mensaje es una operación de una de esas clases, con los mismos parámetros. Las respuestas van en línea discontinua, con el valor devuelto y su tipo.
* **Comprobaciones sin operación propia:** las consultas a la base de datos (por ejemplo, "¿existe ya un cliente con este NIF?") no son una operación de ninguna clase, así que se escriben como condición del fragmento `alt` y no como mensaje.
* **Fragmentos:** `alt` para alternativas (extensiones del caso de uso), `opt` para pasos opcionales, `loop` para repeticiones y `ref` para un flujo que se detalla en otro sitio. Cada alternativa indica entre paréntesis la extensión del caso de uso que representa (por ejemplo, `3a`, `4b`).
* **Secciones:** separadas con `== título ==`. La numeración de los mensajes empieza en 1 en cada sección.
* **Confirmaciones del usuario:** cuando el caso de uso muestra consecuencias o avisos antes de confirmar (CU-103, CU-204, CU-205 y CU-206), el servicio devuelve esa información al actor y el actor responde con `confirmación`. Es la respuesta de la interfaz, no una operación del diagrama de clases.
* **Leyenda:** cada diagrama tiene una leyenda abajo a la derecha con estas convenciones.

---

## Decisiones de diseño

### 1. Autorización en un único punto, detallada una sola vez
Todo escenario iniciado por una persona empieza con `ServicioAutorizacion.autorizar(actor, permiso)` (FR3, RNF-07), como indica el diagrama de clases. El flujo completo se dibuja en la sección 1 de A1 (CU-110):

1. El servicio llama a `autorizar(actor, permiso)`.
2. `ServicioAutorizacion` pregunta a la cuenta del actor `tienePermiso(permiso)`.
3. `Usuario` mira primero si tiene un `PermisoIndividual` para ese permiso y, si no, pide a su `Rol` los permisos efectivos, que incluyen los heredados (`LECTOR` < `ADMINISTRADOR` < `SUPERADMINISTRADOR`).
4. Si no tiene permiso, `autorizar` lanza `AccesoDenegado`. El servicio registra el intento en la auditoría y devuelve un error sin cambiar ningún dato (CU-112).

En el resto de secciones y en A2 se usa un fragmento `ref autorizar(actor, PERMISO)` para no repetirlo. Cuando la operación es sobre un cliente se usa `autorizar(actor, permiso, c)`, que además comprueba que el actor puede ver ese cliente (asignado o cedido, CU-111).

### 2. Los servicios preguntan por permisos, no por roles
Los permisos usados son códigos de `Permiso`. La nota del diagrama de clases sobre `Rol` da solo algunos ejemplos (`CONCILIAR_COBRO`, `CREAR_INCIDENCIA`, `CONFIGURAR_SISTEMA`, `APROBAR_OPERACIONES_CRITICAS`...); estos diagramas usan esos y completan la lista:

| Diagrama | Permisos añadidos |
|---|---|
| A1 | `CREAR_USUARIOS`, `DESACTIVAR_USUARIOS` |
| A2 | `CREAR_CLIENTES`, `GESTIONAR_CONTACTOS`, `DESACTIVAR_CONTACTOS`, `CAMBIAR_ESTADO_CLIENTE` |
| B3 | `ANULAR_CONCILIACION`, `GESTIONAR_DISPUTAS`, `SOLICITAR_INCOBRABLE` |
| B4 | `VER_PANEL_FINANCIERO`, `EXPORTAR_INFORMES`, `CONSULTAR_ESTADO_SISTEMA` |

### 3. Auditoría al final de cada cambio
Toda operación que cambia datos termina con `ServicioAuditoria.registrar(...)` (RNF-08). También se registran los accesos denegados (CU-112, paso 4) y el cierre de sesiones (CU-104, paso 3).

### 4. Nada se borra
* **Usuarios:** `Usuario.desactivar()` pone `activo = false`, pero sus facturas, comentarios y registros de auditoría siguen enlazados a la cuenta (FR1.3, RNF-08).
* **Contactos:** se desactiva el vínculo con el cliente (`VinculoContacto.activo = false`) y, solo si la persona no es contacto de otra empresa, la propia `PersonaContacto`. El historial de envíos a sus direcciones se conserva (FR5.3, FR18.4).

---

## Diagrama A1: alta de usuario, autorización por roles y desactivación

### Sección 1: alta de usuario con autorización (CU-101, CU-110, CU-112)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 | El administrador pide `registrarUsuario(actor, nombre, email, r)` a `ServicioUsuarios`. | CU-101 |
| 2 a 6 | Autorización centralizada: permiso `CREAR_USUARIOS`, comprobado con el `PermisoIndividual` y los permisos efectivos del rol. | FR3, CU-110 |
| Alternativa: acceso denegado | Un usuario sin permiso (un Lector, o alguien que intenta registrarse por sí mismo) recibe un error. El intento queda en la auditoría y no cambia ningún dato, aunque la opción se viera en la interfaz por error. | CU-112, CU-101 (sin autorregistro) |
| Alternativa 3a | El correo no pertenece a `ConfiguracionSistema.dominioCorreo` (`@turbine`): se rechaza el alta. | FR1.1 |
| Alternativa 3b | Ya existe una cuenta con ese correo: no se crea otra. Si estaba desactivada, se indica para valorar reactivarla. | CU-101 |
| Datos válidos | Se crea `nuevo : Usuario` con su rol. `ServicioAutenticacion.solicitarRestablecimiento(email)` crea el `TokenRestablecimiento` del enlace temporal, y `ServicioNotificaciones` envía la invitación. Por último se registra el alta en la auditoría. | FR1, FR1.2 |

### Sección 2: desactivación con cierre de sesiones (CU-103, CU-104)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `desactivarUsuario(actor, u)` y autorización `DESACTIVAR_USUARIOS`. | CU-110 |
| Alternativa 2b | `u` es el único superadministrador activo: se impide la desactivación. | ACT-01 |
| Consecuencias y confirmación | El sistema muestra que el usuario perderá el acceso y si tiene clientes asignados. El administrador confirma o cancela. | CU-103, paso 2 |
| Opción 2a | Si tiene clientes asignados, se pueden reasignar antes (`ref` a CU-107). Si no, se avisa al administrador de que quedan sin responsable. | FR2.1 |
| `u.desactivar()` | Se bloquea el acceso conservando el historial. | FR1.3, RNF-08 |
| `cerrarSesionesDe(u)` | Bucle que cierra cada `Sesion` activa en cualquier dispositivo y lo registra en la auditoría. Si no hay sesiones, no hace nada. Si alguna falla, `activo = false` sigue impidiendo el acceso. | FR1.4, CU-104 |
| Final | Se registra la desactivación en la auditoría. | RNF-08 |

---

## Diagrama A2: alta de cliente, contactos y cambio de estado

### Sección 1: alta de cliente (CU-201)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `darDeAlta(actor, comerciales, fiscales)` y autorización `CREAR_CLIENTES`. | CU-201 |
| `fiscales.estanCompletos()` | Valida los datos fiscales obligatorios. El identificador fiscal y el domicilio no siguen el formato español: un cliente extranjero no necesita provincia. | FR4.1, FR4.2 (3a) |
| Alternativa 4a | Faltan campos obligatorios: se indican cuáles. | CU-201 |
| Alternativa 4b | El NIF ya existe: se rechaza el alta, se indica el cliente que lo tiene y se sugiere fusionar (CU-207). | FR6.1 |
| Datos válidos | Se crea `c : Cliente` en estado `PENDIENTE_ACTIVACION`, con sus `DatosComerciales` y `DatosFiscales` como partes y el primer `EventoHistorial` (`ALTA`). Se registra en la auditoría y se ofrece asignarlo (CU-106) y añadir contactos. | FR4, FR8.1 |

### Sección 2: alta de una persona de contacto (CU-203)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `gestionarContacto(actor, c, p, rol)` con autorización `GESTIONAR_CONTACTOS` sobre el cliente: la puede hacer el administrador o el responsable del cliente. | FR5, CU-111 |
| Alternativa 6a | Correo o teléfono no válidos. | CU-203 |
| Alternativa 3a | La persona ya es contacto de otra empresa: se reutiliza sin duplicarla. | FR5.1 |
| `VinculoContacto` | Guarda el rol de la persona en ese cliente. Si es `FACTURACION` o `COBROS`, pasa a ser destinataria de las facturas (4a). Los canales que admite se guardan en `PersonaContacto`. | FR5.2, FR5.5 |

### Sección 3: desactivar un contacto y aviso del único contacto de facturación (CU-204, CU-205)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `desactivarContacto(actor, c, p)` con autorización `DESACTIVAR_CONTACTOS`. | CU-204 |
| `c.getContactosFacturacion()` | Comprueba si `p` es el único contacto de facturación. | FR5.4 |
| Alternativa CU-205 | Si el cliente está activo y `p` es su único contacto de facturación, se avisa de que el cliente se quedará sin destinatario para sus facturas. Solo continúa con confirmación explícita. Si el administrador prefiere designar antes otro contacto, se cancela y se vuelve a CU-203. | FR5.4, CU-205 |
| Alternativa 3b | Si `p` es contacto de otras empresas, solo se desactiva su vínculo con `c`. Si no, también `p.desactivar()`. | FR5.3 |
| Final | Se conservan los datos y el historial de envíos, se anota en el historial del cliente y en la auditoría. La operación devuelve los avisos mostrados (`List<String>`). | FR5.3, FR18.4 |

### Sección 4: cambio de estado del cliente (CU-206)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `cambiarEstado(actor, c, nuevo)` con autorización `CAMBIAR_ESTADO_CLIENTE`. La interfaz solo ofrece los estados permitidos desde el actual. | FR6 |
| Alternativa 4a | Al reactivar un cliente, si otro cliente tiene el mismo NIF, se rechaza e indica el cliente coincidente. | FR6.1 |
| Alternativa 4b | Al activar la recurrencia (`nuevo == ACTIVO`), `c.tieneDatosFiscalesCompletos()` consulta `DatosFiscales.estanCompletos()`. Si faltan datos, `c.datosFiscalesFaltantes()` dice cuáles y se bloquea la activación hasta completarlos (CU-202). | FR6.4 |
| Consecuencias | `c.consecuenciasCambioEstado(nuevo)` devuelve lo que pasará (por ejemplo, que se dejarán de generar facturas o recordatorios) y se muestra antes de confirmar. | FR6.3 |
| Confirmación | `c.cambiarEstado(nuevo)` aplica el estado y anota el historial; se registra en la auditoría con el estado anterior y el nuevo. Si cancela, el cliente conserva su estado. | FR8.1, RNF-08 |

---

## Diagrama B3: cobros, impagos, disputa e incobrable

La recepción automática de cobros desde Stripe (lanzada por el `Planificador` a través de `PasarelaPago`) ya está en `Secuencia_ConciliarCobros.puml`. B3 dibuja la otra entrada de CU-501, la carga de movimientos de Revolut o del banco por un usuario, que aplica las mismas reglas.

### Sección 1: carga de movimientos con control de duplicados (CU-501)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 a 3 | `cargarMovimientos(actor, archivo, origen)` con autorización `CONCILIAR_COBRO`. `ImportadorMovimientos` (adaptador externo) convierte el archivo en una lista de `Cobro`. | FR21.1 |
| `esDuplicado(c)` | **Idempotencia:** `Cobro.idExterno` es único. Si el evento ya se recibió, se descarta y se anota en el log técnico (2a): el mismo pago nunca se cuenta dos veces. | FR21.3 |
| Coincidencia exacta | Una sola factura con la misma referencia e importe: se crea la `Conciliacion` con la regla aplicada y la factura pasa a `PAGADA`, con lo que `ServicioDeuda` deja de enviarle avisos. Se avisa al responsable. | FR21.2, FR21.4 |
| Alternativa 4b | La referencia coincide pero el importe no: la factura queda `PARCIALMENTE_CONCILIADA` y se avisa al responsable de lo que falta o de la diferencia. | FR22.1 |
| Alternativa 4a | Sin referencia clara o con varias facturas posibles: el cobro queda `PENDIENTE_CONCILIACION` para revisión manual. | FR22 |

### Sección 2: conciliación manual y reversión (CU-502)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| Conciliación manual | `conciliarManualmente(actor, c, f, importe)`. Si el importe no coincide con lo pendiente (4a), se avisa y el usuario confirma o cancela. La `Conciliacion` guarda quién la hizo (`realizadaPor`). | FR22.1 |
| Reversión (1a) | `anular(actor, con, motivo)` con motivo obligatorio. `Conciliacion.anular()` la marca como anulada sin borrar nada; la factura recupera su importe pendiente y su estado (`ENVIADA`, `VENCIDA` o `PARCIALMENTE_CONCILIADA`). | FR22.2, RNF-08 |

### Sección 3: avisos de impago respetando el calendario laboral (CU-503)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y 2 | El `Planificador` lanza `enviarAvisosImpago(hoy)`; los recordatorios previos y los avisos de vencimiento siguen el mismo esquema. Se crea una `EjecucionProceso`. | FR23, FR33 |
| `CalendarioLaboral` | `esLaborable(hoy)`. Si es fin de semana o festivo, los avisos se posponen a `siguienteLaborable(hoy)` (6a). | FR23.3 |
| Bucle | Solo facturas `VENCIDA` sin seguimiento pausado ni disputa y a las que les toca aviso según `cadenciaImpagoDias`. `ServicioComunicaciones` envía por `ProveedorMensajeria`. Si rebota, se abre una incidencia y se avisa al responsable (6b). | FR23.2, FR25.3 |
| Errores | Un fallo con una factura se anota en `EjecucionProceso.registrarError()` y el bucle sigue (CU-713). | FR33.1 |
| Fin de pausas | `revisarPausasVencidas(hoy)` avisa al responsable de cada factura cuya fecha de revisión ha llegado (CU-504, paso 7). | FR24.1 |

### Sección 4: disputa y pausa por incidencia (CU-504, CU-601)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| Disputa | `marcarEnDisputa(actor, f, motivo, revision)`. Sin motivo o con fecha no válida se rechaza (3a). Si no, la factura queda `EN_DISPUTA` con el seguimiento pausado, sigue contando como pendiente y se registra en la auditoría. | FR24.1, FR24.2 |
| Incidencia que afecta al cobro | `ServicioIncidencias.crear(...)`. Si `afectaCobro` (3a), se pausa el seguimiento de la factura con `ServicioDeuda.pausarSeguimiento(...)`. | FR27.1 |
| Cierre | `cerrar(actor, i, resolucion)` exige la resolución (7a). Si la incidencia afectaba al cobro, `reanudarSeguimiento` reactiva los avisos. | FR27.1 |

### Sección 5: deuda incobrable con aprobación (CU-505)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| Solicitud | `solicitarIncobrable(actor, f, motivo)` crea una `SolicitudAprobacion` de tipo `DEUDA_INCOBRABLE` y avisa al aprobador. La factura no cambia hasta la resolución. | FR24.3 |
| Resolución | El aprobador llama a `resolver(...)`. `ServicioAutorizacion` comprueba `APROBAR_OPERACIONES_CRITICAS` y la separación de funciones (el solicitante no puede aprobar su propia solicitud). | FR16.2 |
| Aprobada / rechazada (5a) | Aprobada: la factura pasa a `INCOBRABLE` (nunca a `PAGADA`) y se detienen para siempre sus avisos. Rechazada: conserva su estado. En los dos casos se avisa al solicitante y se registra en la auditoría. | FR24.3, RNF-08 |

---

## Diagrama B4: panel, informes, configuración, API y procesos

### Sección 1: panel financiero con filtros (CU-704)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `obtenerMetricas(actor, desde, hasta)` con permiso `VER_PANEL_FINANCIERO`: un usuario operativo no puede ver el panel global (CU-112). | FR30, FR3.2 |
| Cálculo | Totales del periodo, `ObjetivoFacturacion.porcentajeAlcanzado()` y `previsionIngresos()`. Se devuelven en el DTO `MetricasFinancieras`. Si no hay datos o el cálculo falla, se muestran métricas a cero con un aviso. | FR30.1 a FR30.3 |
| Filtros | Cada cambio de filtro vuelve a pedir las métricas; si la combinación no tiene datos, los gráficos salen vacíos (6a). | FR30.4, FR30.6 |

### Sección 2: informes y paquete para la gestoría (CU-707)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| Informe | `generarInforme(actor, filtros, PDF)`. Si el periodo está vacío se pregunta antes (3a). `Informe.generar()` y `exportar()` producen el PDF; la conversión de moneda es solo para el informe. | FR30.6, FR30.7, FR30.10 |
| Paquete | `generarPaqueteGestoria(actor, p)` crea un `PaqueteGestoria` con una versión nueva del periodo, para distinguir versiones si hay rectificativas. | FR30.8 |
| Errores | Si la exportación falla, no se registra ninguna versión. Si va bien, se registra en la auditoría. | RNF-08 |

### Sección 3: parámetros globales auditados (CU-710)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 y `ref` | `modificarParametros(actor, cfg)` con permiso `CONFIGURAR_SISTEMA`, que solo tiene el superadministrador. | FR31.2 |
| `cfg.validar()` | Si hay errores, se mantiene la configuración anterior. | FR31.2 |
| Guardado | Las facturas ya emitidas no cambian, porque sus líneas y su PDF guardan copia de los datos. El cambio queda en la auditoría con el valor anterior y el nuevo. | FR18.4, RNF-08 |

### Sección 4: consulta de deudas vía API (CU-711)
| Respuesta | Cuándo | Requisito |
|---|---|---|
| **401** | `CredencialAPI.validar(clave)` falla o la credencial está desactivada (2a). No se devuelve ningún dato financiero. | FR32.2 |
| **429** | Se supera `limitePeticionesMinuto` de la credencial (3a). Se indica el tiempo de espera (`Retry-After`). | FR32.2 |
| **200** | `ServicioDeuda.calcularDeuda(c)` devuelve el DTO `EstadoDeuda` con las mismas reglas que la web. La llamada se registra en la auditoría con la credencial como autor y se responde en JSON. | FR32.1, FR32.3 |

### Sección 5: proceso en segundo plano (CU-713)
| Pasos | Qué ocurre | Requisito |
|---|---|---|
| 1 a 3 | El `Planificador` lanza la facturación recurrente del día 1 (los demás procesos siguen el mismo esquema). Se crea la `EjecucionProceso` en estado `PROCESANDO`. | FR14.2, FR33 |
| Bucle | Cada suscripción activa genera su factura. Si una falla, se captura el error, se anota con `registrarError()` y se sigue con la siguiente (4a). | FR33.1 |
| Fin | La ejecución termina `COMPLETADO` o `COMPLETADO_CON_ERRORES`, con el total de procesados, los fallidos y el motivo de cada fallo. El administrador lo consulta con `consultarEstadoSistema()` (CU-712). | FR33.2, FR33.3 |

---

## Observaciones para el diagrama de clases

Al dibujar B3 y B4 salen tres puntos que no impiden los diagramas, pero conviene revisar en el diagrama de clases:

* `PanelFinanciero.obtenerMetricas(actor, desde, hasta)` no recibe los filtros de CU-704 (país, sector, moneda, tipo de factura). Se podría añadir un parámetro `filtros : Map<String, String>`.
* `ServicioIncidencias` pausa el seguimiento de una factura cuando la incidencia afecta al cobro (CU-601, 3a). Para eso usa `ServicioDeuda`, y esa dependencia no está dibujada en la vista 2/2.
* `APIDeudas.consultarDeuda(idCliente, clave)` recibe el identificador, pero `ServicioDeuda.calcularDeuda(c)` recibe un `Cliente`. En el diagrama se pasa directamente `c`, que es el cliente con ese identificador.

---

## Relación con los otros diagramas

* **Clases:** todos los participantes son clases del diagrama de clases y todos los mensajes son operaciones suyas, con los mismos parámetros. Las clases de dominio que aparecen son `Usuario`, `Rol`, `TokenRestablecimiento`, `Sesion`, `Cliente`, `DatosFiscales`, `PersonaContacto`, `VinculoContacto` y `EventoHistorial`.
* **Componentes:** cada paquete del diagrama de clases corresponde a uno o varios componentes (tabla de `clases_del_sistema.md`), así que las llamadas entre recuadros coinciden con las interfaces del diagrama de componentes (`IRoles`, `IAuditoria`, `INotificaciones`, `IComunicacion`, `IDeuda`...).
* **Entidad-Relación:** las comprobaciones de las condiciones (`identificadorFiscal` único, correo único, asignaciones vigentes, sesiones activas) se resuelven sobre las tablas `Cliente`, `Usuario`, `AsignacionDeCliente` y `Sesion`.

## Generar las imágenes

Las imágenes se generan con PlantUML, igual que las de los demás diagramas:

```
PLANTUML_LIMIT_SIZE=16384 plantuml -tpng Secuencia_A1_*.puml Secuencia_A2_*.puml Secuencia_B3_*.puml Secuencia_B4_*.puml
```

* `PLANTUML_LIMIT_SIZE=16384` es obligatorio: B3 y B4 miden más de 4.096 píxeles, que es el límite por defecto de PlantUML, y sin él las imágenes salen cortadas.
* Los diagramas de secuencia no dependen de Graphviz.
* La fuente está fijada a Arial (`skinparam defaultFontName Arial`) y los recuadros de los paquetes tienen borde negro, para que las imágenes se vean igual que las de los diagramas de clases, componentes y Entidad-Relación aunque se generen en otro sistema operativo.
