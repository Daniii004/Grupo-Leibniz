# Descripción de Componentes del Sistema

## 1. Aplicación Web
* **De qué se ocupa:** Es el Frontend (la interfaz gráfica). Se encarga exclusivamente de renderizar las pantallas (dashboard, listado de facturas, formularios) y de capturar las interacciones de los empleados de Turbine (el Administrador y el Usuario Operativo) desde sus navegadores web o móviles.
* **Por qué está en el diagrama:** En diseño de software, el frontend es un componente arquitectónico independiente de la lógica de negocio (Backend). Se coloca para demostrar visualmente que vuestro sistema está desacoplado: la web no procesa las reglas de facturación, sino que se limita a consumir las interfaces internas (IAuth, IFacturas, IClientes) pidiendo y enviando datos por HTTP/REST.

## 2. Sistema Brain (Externo)
* **De qué se ocupa:** Es el producto principal de la startup Turbine (el sistema operativo de IA que venden a sus clientes). Actúa como un sistema de terceros ajeno a vuestra aplicación de facturación.
* **Por qué está en el diagrama:** Alejandro (el cliente) insistió mucho en que vuestra aplicación era independiente de Brain, pero exigió un caso de uso clave de integración (vuestro Bloque 7 / FR31): Brain necesita poder preguntarle a vuestro sistema si un cliente tiene deuda pendiente antes de permitirle darse de baja.
* **Justificación UML:** Se dibuja como un componente externo para dar sentido y justificar la existencia de la interfaz `[API Gateway Externa]`. Si no dibujas a Brain consumiendo esa API (IConsultaAPI), tendrías un puerto de entrada en vuestro sistema que nadie utiliza, lo cual es un error de diseño. Ponerlo demuestra que habéis comprendido el límite exacto entre vuestra aplicación y el ecosistema del resto de la empresa.

---

## 3. API Gateway Externa
* **De qué se ocupa:** Actúa como escudo, enrutador y validador exclusivo para las máquinas. Se encarga de recibir las peticiones de sistemas de terceros (como Brain), autenticarlas utilizando credenciales independientes, verificar los límites de uso razonables y enrutar la petición hacia el módulo interno correspondiente (en este caso, hacia la lógica de deuda).
* **Por qué está en el diagrama:** Da respuesta al requisito de la interfaz programática segura (FR31). A nivel arquitectónico, un sistema robusto no expone sus controladores internos directamente a internet. El Gateway garantiza que cualquier sistema externo de Turbine consuma únicamente los endpoints estrictamente documentados y permitidos (como consultar deuda o solicitar bajas), impidiendo que accedan a módulos críticos como la emisión de facturas o la gestión de roles.

## 4. Reportes y Dashboard
* **De qué se ocupa:** Procesa y consolida las métricas financieras (total facturado, cobrado, pendiente y vencido). Gestiona las operaciones de drill-down para listar las facturas subyacentes de una métrica, calcula la evolución frente a los objetivos configurados y orquesta la extracción, formateo y exportación estructurada de datos (CSV, paquetes para la gestoría e informes PDF).
* **Por qué está en el diagrama:** Cubre todas las funcionalidades del panel de control financiero y exportaciones (FR29). En el diseño de software, separar los reportes en su propio componente aísla las operaciones analíticas (lecturas masivas, cruces de tablas y cálculos de agregación) de las operaciones transaccionales. Si un administrador solicita un cierre trimestral pesado, este módulo absorbe el esfuerzo computacional sin bloquear el rendimiento del módulo de `[Emisión de Facturas]` ni retrasar la conciliación en tiempo real del módulo de `[Cobros]`.

---

## 5. Autenticación y Sesiones
* **De qué se ocupa:** Este componente gestiona exclusivamente la identidad del usuario (el quién eres). Se encarga de validar el inicio de sesión con correo y contraseña, verificar que el email pertenezca al dominio corporativo (@turbine), emitir y validar los tokens de sesión (ej. JWT), gestionar el bloqueo por intentos fallidos, el cierre por inactividad y el flujo de recuperación de contraseñas.
* **Por qué está en el diagrama:** En ingeniería de software, la autenticación debe ser un bloque transversal y aislado. Si no lo separas, acabarías programando la validación del usuario dentro del módulo de facturas y del módulo de cobros, duplicando código. Al extraerlo aquí, la Aplicación Web se comunica con este componente para iniciar sesión; una vez logado, el resto de módulos simplemente confían en el token de sesión que este componente ha emitido.

## 6. Gestión de Roles
* **De qué se ocupa:** Este componente gestiona la autorización (el qué puedes hacer y qué puedes ver). Implementa el control de acceso basado en roles (RBAC: Superadministrador, Administrador, Operativo). Además, gestiona la lógica de asignación de la cartera de clientes: decide a qué empleado pertenece cada cliente, procesa las transferencias de cuentas y controla las delegaciones temporales por vacaciones o bajas.
* **Por qué está en el diagrama:** Es vital aplicar el principio de separación de responsabilidades (Separation of Concerns). Autenticación y Autorización son conceptos distintos. Un usuario puede estar perfectamente autenticado (su sesión es válida), pero la Gestión de Roles es quien le bloquea el paso si intenta aprobar un descuento para el que no tiene permisos de administrador, o si intenta ver la factura de un cliente que no tiene asignado. Aislarlo permite cambiar la política de permisos en el futuro sin tener que reescribir el código de emisión de facturas.

---

## 7. Conciliación de Pagos
* **De qué se ocupa:** Es el motor de emparejamiento financiero. Recibe los movimientos bancarios o eventos entrantes (desde la pasarela Stripe simulada o extractos de Revolut) y los cruza con las facturas emitidas. Si el importe y la referencia coinciden, marca la factura como cobrada. Si hay descuadres (pagos parciales, pagos en exceso o referencias dudosas), detiene el proceso automático y levanta una alerta para que un usuario operativo realice la conciliación manual. También gestiona la reversión (deshacer) de cobros erróneos.
* **Por qué está en el diagrama:** Aísla la volatilidad de las integraciones bancarias. Las APIs de los bancos y pasarelas de pago cambian con frecuencia y tienen formatos de datos muy específicos. Al encerrar esta lógica en su propio componente, si Turbine decide cambiar Stripe por PayPal el año que viene, solo tendréis que modificar este bloque, dejando intacto el resto del sistema.

## 8. Gestión de Deuda e Impagos
* **De qué se ocupa:** Aplica las reglas de negocio sobre el dinero que los clientes deben a la empresa. Controla los vencimientos, cambia los estados de las facturas (a Vencida, En disputa, Incobrable), calcula el saldo total pendiente de un cliente y orquesta el flujo de persecución (avisando al módulo de comunicaciones para que dispare los recordatorios de impago cada X días).
* **Por qué está en el diagrama (y separado de la conciliación):** Por el principio de Responsabilidad Única. Conciliar es un problema de cuadre matemático e integración externa; gestionar la deuda es un problema puramente interno de reglas de negocio y plazos legales. Además, separar este bloque es vital para la API Externa: cuando el sistema Brain pregunte si un cliente tiene deuda para dejarle darse de baja, la respuesta la calcula este componente basándose en sus reglas internas, sin tener que consultar a Stripe ni recalcular todos los cobros en ese instante.

---

## 9. Comunicaciones Externas
* **De qué se ocupa:** Es el motor de mensajería y notificaciones hacia el exterior. Recibe órdenes de otros módulos (por ejemplo, `[Facturas]` le pide enviar una factura recién generada, o `[Deuda]` le pide enviar un aviso de impago), ensambla el mensaje utilizando las plantillas configuradas y se encarga de hablar con los proveedores externos (servidores SMTP de correo o APIs de mensajería).
* **Por qué está en el diagrama:** Para garantizar un bajo acoplamiento. Si el módulo de emisión de facturas enviara los correos directamente, su código estaría contaminado con librerías de red, puertos SMTP y gestión de reintentos de conexión. Al extraerlo a su propio componente, el módulo de facturas simplemente llama a la interfaz `IComunicacion` y se desentiende. Además, si el día de mañana Turbine decide cambiar de proveedor de correo (por ejemplo, de SendGrid a Amazon SES), solo tendréis que modificar el código de este bloque.

## 10. Incidencias Internas
* **De qué se ocupa:** Es el sistema de ticketing o colaboración de los propios empleados de Turbine. Gestiona la apertura de incidencias, los hilos de comentarios, las menciones, las notas restringidas (solo para administradores) y el guardado de archivos adjuntos vinculados a la resolución de un problema.
* **Por qué está en el diagrama (y separado de comunicaciones):** Por el principio de alta cohesión. Podría parecer que ambos módulos tratan sobre "mensajes", pero su naturaleza técnica es opuesta. Comunicaciones Externas interactúa con el mundo exterior y con pasarelas de terceros; Incidencias Internas es una máquina de estados puramente interna que requiere un acceso intensivo a la base de datos local y al sistema de ficheros (`IArchivos`). Juntarlos en un solo bloque crearía un "Objeto Dios" (God Object o antipatrón arquitectónico) que mezclaría la lógica de enviar correos a clientes con la de resolver problemas técnicos de los empleados.

---

## 11. Pasarela de pago
* **De qué se ocupa:** Representa la pasarela de pagos externa. Es la entidad que procesa los movimientos financieros reales (cargos en tarjetas de crédito, adeudos bancarios), custodia los fondos y notifica a vuestro sistema (mediante webhooks o peticiones HTTP) si un cobro ha tenido éxito o ha sido rechazado por el banco.
* **Por qué está en el diagrama:** Para delimitar claramente la frontera de responsabilidad del sistema. En arquitectura de software, una aplicación de facturación nunca procesa los datos crudos de las tarjetas de crédito por cuestiones de seguridad y normativas (PCI-DSS). Al colocar a Stripe como un servicio en la nube externo, demostráis que Turbine delega esa responsabilidad compleja. Vuestro componente interno de `[Conciliación de Pagos]` se acopla a él únicamente a través de la interfaz `IPagosStripe`. Esto refleja un diseño robusto: si el año que viene la empresa decide usar PayPal o Redsys en lugar de Stripe, solo tendréis que reprogramar esa pequeña conexión, dejando intacto todo el núcleo de la aplicación.

## 12. Email / WhatsApp
* **De qué se ocupa:** Es el proveedor de infraestructura externo (como un servidor SMTP corporativo tipo Amazon SES, SendGrid, o la API de WhatsApp Business) que realiza la entrega física de los mensajes a través de internet hasta los dispositivos de los clientes.
* **Por qué está en el diagrama:** Demuestra el desacoplamiento de la capa de transporte. Vuestro módulo interno de `[Comunicaciones Externas]` tiene la lógica para redactar el texto de un recordatorio de impago y adjuntar la factura en PDF, pero no debe responsabilizarse de enrutar paquetes de red para que el email no caiga en la carpeta de spam de Gmail. Al modelarlo como un nodo externo consumido mediante la interfaz `IMensajeriaExterna`, dejáis constancia de que vuestro sistema es agnóstico al proveedor. Podéis cambiar de servidor de correo o añadir avisos por SMS en el futuro sin alterar ni una sola línea de código del bloque de emisión de facturas.

---

## 13. Emisión de Facturas
*(Nota: Se mantiene la numeración original del documento)*
* **De qué se ocupa:** Es el motor central legal y contable de Turbine (FR14, FR15, FR17, FR18, FR19). Recibe peticiones para cobrar servicios (ya sean puntuales o recurrentes) y genera el documento legal definitivo. Se encarga de asignar la numeración secuencial obligatoria, calcular impuestos, aplicar prorrateos, gestionar facturas rectificativas y componer el archivo PDF inmutable.
* **Por qué está en el diagrama:** Representa el núcleo (Core Domain) de vuestra lógica de negocio. Arquitectónicamente, este componente debe estar muy aislado porque contiene las reglas más críticas y estrictas de la aplicación.
  * Se separa de `[Cobros]` porque emitir una obligación de pago (facturar) es un proceso completamente distinto a recibir el dinero en el banco (cobrar).
  * Tiene acceso exclusivo a la interfaz `IArchivos` del almacenamiento porque es el único módulo autorizado para generar y guardar el documento PDF legal definitivo en el sistema de ficheros, garantizando que nadie más pueda alterarlo.

---

## 14. Catálogo y Tarifas
* **De qué se ocupa:** Gestiona la lista maestra de productos, servicios y conceptos facturables de Turbine (FR12), así como las tablas de precios por rangos y las reglas para las revisiones anuales (FR11). Actúa como la base de datos central de precios de la empresa.
* **Por qué está en el diagrama:** Para aislar los datos maestros de negocio (el Master Data). Al separarlo, se centraliza la definición de qué se vende y a cuánto. Si la empresa decide subir los precios un 5% el año que viene, este componente actualiza su tabla interna. Luego, otros módulos (como las suscripciones o los presupuestos) consumen la interfaz `ITarifas` para consultar el precio vigente. Esto garantiza que no haya precios duplicados o programados a fuego (hardcoded) en distintas partes del código.

## 15. Suscripciones Recurrentes
* **De qué se ocupa:** Gestiona el ciclo de vida de los contratos vivos que tienen los clientes (FR9, FR10). Controla cuándo se da de alta un servicio continuo, cuándo se pausa, cuándo se reanuda, cuándo se da de baja y qué cambios están programados para el mes siguiente. También procesa reglas de negocio comerciales complejas, como calcular los prorrateos si un cliente contrata el día 15 en lugar del día 1.
* **Por qué está en el diagrama (y separado de tarifas y facturas):** Aplica la separación estricta entre un acuerdo comercial y un documento fiscal.
  * **Frente a Tarifas:** El catálogo define de forma estática "qué servicios existen", mientras que la suscripción guarda el estado dinámico de "qué ha contratado el Cliente X y desde cuándo".
  * **Frente a Facturas:** Una suscripción es una promesa de cobro a lo largo del tiempo, no es un documento legal per se. Este componente monitoriza el contrato y, cuando llega el día de cobro recurrente, le envía los datos crudos al componente de `[Emisión de Facturas]` mediante su interfaz para que este último genere el documento PDF final con validez fiscal.

---

## 16. Gestión de Fichas y Contactos
* **De qué se ocupa:** Centraliza toda la información de las empresas a las que Turbine presta servicio (razón social, NIF, direcciones) y de las personas físicas que trabajan en ellas (contactos). Gestiona todo su ciclo de vida: altas, modificaciones, control del estado (activo/inactivo), fusión de clientes duplicados, importación masiva desde CSV y el registro de todo el historial de cambios (Casos de Uso del Bloque 2: CU201 a CU209).
* **Por qué está en el diagrama:** En arquitectura de software, este componente actúa como la "Fuente Única de la Verdad" (Single Source of Truth) o Master Data para los datos de los clientes. Es vital tenerlo en un bloque aislado porque casi todo el resto del sistema depende de él:
  * El módulo de `[Emisión de Facturas]` consume la interfaz `IClientes` para obtener el NIF y el domicilio fiscal en el momento exacto de generar el PDF legal.
  * El módulo de `[Comunicaciones Externas]` (o los cobros) le preguntan a este bloque "¿Cuál de todos los contactos de esta empresa es el contacto de facturación?" para saber a qué email enviar el aviso de impago.
  * El módulo de `[Gestión de Roles]` necesita leer la lista de clientes para poder asignar carteras de cuentas a los distintos usuarios operativos.
  
  Al centralizarlo, garantizáis que si un cliente cambia de dirección o de correo, se actualiza en un solo sitio y el resto del sistema utiliza automáticamente la información correcta.

---

## 17. Base de Datos
* **De qué se ocupa:** Es el repositorio estructurado donde se guarda el estado persistente y las entidades de negocio de toda la aplicación. Aquí residen todas las tablas del sistema (Usuarios, Roles, Clientes, Facturas, Suscripciones, Incidencias, Tarifas, etc.), sus relaciones (claves foráneas) y las reglas de integridad referencial.
* **Por qué está en el diagrama:** Representa la persistencia de datos tradicional (el modelo Entidad-Relación que estáis diseñando). En este diagrama se puede ver visualmente que todos los módulos de negocio necesitan conectarse a ella (`IDatabase`) de forma independiente para persistir sus propios dominios, asegurando que la información sobreviva si el servidor web se reinicia. Es una dependencia de infraestructura fundamental en cualquier arquitectura web.