# Especificación de Casos de Uso - Bloque 1: Gestión de usuarios, roles y asignaciones

---

## Caso de Uso: Registrar / invitar una cuenta de usuario

* **ID / Código:** CU-101
* **Actor principal:** Administrador (con permiso para crear usuarios) o Superadministrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR1, FR1.1, FR1.6. Relacionados: FR3, RNF-07.
* **Descripción:** El administrador crea la cuenta de un nuevo empleado de Turbine o le envía una invitación para que la active. El sistema no permite el autorregistro: solo un superadministrador o un administrador con permiso pueden dar de alta usuarios. El sistema comprueba que el correo pertenezca al dominio de la empresa (`@turbine`).

**Interesados:**

* **Turbine (Administración):** Quiere controlar quién accede al sistema y con qué rol.
* **Nuevo empleado:** Quiere disponer de su cuenta para empezar a trabajar con los clientes que se le asignen.

**Precondiciones:**

* El administrador ha iniciado sesión y tiene permiso para crear usuarios.

**Disparador:** Se incorpora un nuevo empleado al departamento administrativo o comercial de Turbine.

**Garantía de éxito:**

* La cuenta queda creada con su rol, o la invitación queda enviada con un enlace temporal de activación. El alta se registra en el historial de auditoría (quién, cuándo).

**Garantía mínima:**

* Si el correo no es válido o el administrador cancela, no se crea ninguna cuenta ni se envía ninguna invitación.

**Escenario principal de éxito:**

1. El administrador elige crear un nuevo usuario.
2. El administrador indica el nombre, el correo corporativo y el rol del nuevo usuario.
3. El sistema comprueba que el correo pertenece al dominio de la empresa y que no existe ya otra cuenta con ese correo.
4. El administrador confirma el alta.
5. El sistema crea la cuenta y envía al correo indicado una invitación con un enlace temporal para que el usuario establezca su contraseña.
6. El sistema registra el alta en el historial de auditoría.

**Extensiones:**

* **3a. El correo no pertenece al dominio de la empresa:** El sistema rechaza el alta e indica que solo se admiten correos corporativos. El flujo vuelve al paso 2.
* **3b. Ya existe una cuenta con ese correo:** El sistema lo indica y no crea otra cuenta. Si la cuenta está desactivada, se informa al administrador para que valore reactivarla.
* **Cualquier paso. Un usuario sin permiso intenta crear la cuenta o intenta registrarse por sí mismo:** El sistema lo impide, porque no existe autorregistro.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Restablecer contraseña

* **ID / Código:** CU-102
* **Actor principal:** Usuario.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR1, FR1.2. Relacionados: RNF-07.
* **Descripción:** El usuario que ha olvidado su contraseña solicita un enlace temporal para establecer una nueva.

**Interesados:**

* **Usuario:** Quiere recuperar el acceso sin depender de un administrador.
* **Turbine (Administración):** Quiere que la recuperación sea segura y no revele si un correo existe en el sistema.

**Precondiciones:**

* El usuario tiene una cuenta activa.

**Disparador:** El usuario no recuerda su contraseña.

**Garantía de éxito:**

* El usuario establece una nueva contraseña, el enlace deja de ser válido y el usuario puede iniciar sesión.

**Garantía mínima:**

* Si el enlace caduca o la contraseña no es válida, la contraseña anterior se mantiene sin cambios.

**Escenario principal de éxito:**

1. El usuario elige la opción de restablecer la contraseña en la pantalla de inicio de sesión.
2. El usuario introduce su correo corporativo.
3. El sistema envía a ese correo un enlace temporal de restablecimiento.
4. El usuario abre el enlace dentro de su periodo de validez.
5. El usuario introduce la nueva contraseña, que el sistema valida según las reglas de seguridad.
6. El sistema guarda la contraseña de forma segura (hash), invalida el enlace y confirma el cambio.

**Extensiones:**

* **2a. El correo no corresponde a una cuenta activa:** El sistema muestra el mismo mensaje genérico que en el caso correcto y no envía ningún correo, para no revelar qué cuentas existen.
* **4a. El enlace ha caducado o ya se ha usado:** El sistema lo indica y permite solicitar uno nuevo.
* **5a. La contraseña no cumple las reglas de seguridad:** El sistema indica qué requisitos faltan y el flujo vuelve al paso 5.

---

## Caso de Uso: Desactivar cuenta de usuario

* **ID / Código:** CU-103
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR1, FR1.3. Relacionados: FR1.4 (CU-104), FR2, FR2.1 (CU-107), RNF-08, RNF-09.
* **Descripción:** El administrador desactiva la cuenta de un empleado que deja de trabajar en Turbine, sin borrar nada de lo que haya hecho. La cuenta no se elimina: se bloquea el acceso y se conserva todo su historial de acciones. Al desactivarla, el sistema cierra sus sesiones activas (CU-104).

**Interesados:**

* **Turbine (Administración):** Quiere cortar el acceso del empleado y conservar la trazabilidad de sus acciones.
* **Clientes del usuario:** Quieren seguir siendo atendidos por otro responsable.

**Precondiciones:**

* El administrador ha iniciado sesión.
* La cuenta a desactivar existe y está activa.
* No es la única cuenta de Superadministrador del sistema.

**Disparador:** Un empleado causa baja o deja de necesitar acceso.

**Garantía de éxito:**

* La cuenta queda desactivada, no puede iniciar sesión, sus sesiones activas están cerradas y todo su historial se conserva. La desactivación queda registrada en la auditoría.

**Garantía mínima:**

* Si el administrador cancela o no se puede completar, la cuenta sigue activa y sin cambios.

**Escenario principal de éxito:**

1. El administrador selecciona la cuenta del usuario y elige desactivarla.
2. El sistema muestra las consecuencias: el usuario perderá el acceso y, si tiene clientes asignados, se avisa de que habrá que reasignarlos.
3. El administrador confirma la desactivación.
4. El sistema marca la cuenta como desactivada, conservando su historial.
5. El sistema cierra las sesiones activas del usuario (CU-104).
6. El sistema registra la desactivación en la auditoría.

**Extensiones:**

* **2a. El usuario tiene clientes asignados:** El sistema avisa y ofrece reasignarlos antes de confirmar (CU-107). Si el administrador decide no reasignarlos, los clientes quedan pendientes de responsable y se le avisa de ello.
* **2b. Es la única cuenta de Superadministrador:** El sistema impide la desactivación, porque debe existir al menos una.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Cerrar sesiones activas al desactivar la cuenta

* **ID / Código:** CU-104
* **Actor principal:** Sistema.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR1.4. Relacionados: FR1.3 (CU-103), RNF-07.
* **Descripción:** Al desactivar una cuenta, el sistema cierra automáticamente todas las sesiones que el usuario tenga abiertas, en cualquier dispositivo, para que no pueda seguir operando.

**Interesados:**

* **Turbine (Administración):** Quiere que la baja de acceso sea inmediata.

**Precondiciones:**

* Se ha confirmado la desactivación de la cuenta (CU-103).

**Disparador:** El administrador confirma la desactivación de una cuenta.

**Garantía de éxito:**

* No queda ninguna sesión válida del usuario y cualquier petición posterior suya es rechazada.

**Garantía mínima:**

* Aunque el cierre de alguna sesión falle, la cuenta queda desactivada y el servidor rechaza cualquier petición de ese usuario.

**Escenario principal de éxito:**

1. El sistema localiza todas las sesiones activas del usuario desactivado.
2. El sistema invalida cada una de ellas.
3. El sistema registra el cierre en la auditoría.

**Extensiones:**

* **1a. El usuario no tiene sesiones activas:** El sistema no realiza ninguna acción adicional.

---

## Caso de Uso: Cerrar sesión por inactividad

* **ID / Código:** CU-105
* **Actor principal:** Temporizador (Scheduler).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR1, FR1.5. Relacionados: RNF-07.
* **Descripción:** El sistema cierra automáticamente la sesión de un usuario que lleva un periodo de tiempo configurado sin actividad, para evitar accesos indebidos desde equipos desatendidos.

**Interesados:**

* **Turbine (Administración):** Quiere proteger los datos de clientes y facturas.
* **Usuario:** Quiere que su trabajo no se pierda al caducar la sesión.

**Precondiciones:**

* El usuario tiene una sesión iniciada.

**Disparador:** Se supera el tiempo máximo de inactividad configurado.

**Garantía de éxito:**

* La sesión queda cerrada y, para volver a operar, el usuario debe autenticarse de nuevo.

**Garantía mínima:**

* Los formularios con borrador se conservan para que el usuario pueda retomarlos al volver a entrar.

**Escenario principal de éxito:**

1. El sistema detecta que la sesión del usuario ha superado el periodo de inactividad.
2. El sistema avisa al usuario, si aún tiene la pantalla abierta, de que la sesión va a caducar.
3. Si el usuario no reacciona, el sistema invalida la sesión.
4. En la siguiente acción del usuario, el sistema le redirige a la pantalla de inicio de sesión.

**Extensiones:**

* **2a. El usuario reacciona al aviso:** El sistema mantiene la sesión y reinicia el contador de inactividad.

---

## Caso de Uso: Asignar clientes a usuarios

* **ID / Código:** CU-106
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR2. Relacionados: FR1, FR3.1, FR3.2.
* **Descripción:** El administrador reparte clientes entre los usuarios que estén disponibles, de modo que cada usuario solo pueda trabajar con los que tiene asignados. Un usuario operativo no puede asignarse clientes a sí mismo.

**Interesados:**

* **Turbine (Administración):** Quiere repartir la carga de trabajo y que cada cliente tenga un responsable claro.
* **Usuario:** Quiere saber con qué clientes debe trabajar.

**Precondiciones:**

* El administrador ha iniciado sesión.
* El cliente existe en el sistema.
* El usuario destinatario tiene la cuenta activa.

**Disparador:** Hay clientes nuevos o sin responsable que deben repartirse.

**Garantía de éxito:**

* El cliente queda asignado al usuario, que pasa a verlo en su listado, y la asignación queda registrada en el historial del cliente.

**Garantía mínima:**

* Si la asignación no es válida o se cancela, el cliente conserva su responsable anterior.

**Escenario principal de éxito:**

1. El administrador selecciona uno o varios clientes y elige asignarlos.
2. El sistema muestra los usuarios disponibles.
3. El administrador elige el usuario destinatario.
4. El sistema muestra un resumen de la asignación.
5. El administrador confirma.
6. El sistema asigna los clientes, da acceso al usuario y registra el cambio en el historial de cada cliente.

**Extensiones:**

* **2a. No hay usuarios disponibles:** El sistema lo indica y los clientes siguen sin asignar.
* **Cualquier paso. Un usuario operativo intenta asignarse clientes a sí mismo:** El sistema lo bloquea (CU-112).
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Transferir cliente a otro usuario

* **ID / Código:** CU-107
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR2, FR2.1. Relacionados: FR8.1, RNF-08.
* **Descripción:** El administrador traspasa un cliente de un usuario a otro. Las tareas pendientes pasan al nuevo responsable, pero las acciones que hizo el responsable anterior se conservan. Reutiliza la asignación de clientes (CU-106).

**Interesados:**

* **Turbine (Administración):** Quiere que el cambio de responsable no pierda trabajo ni trazabilidad.
* **Nuevo responsable:** Quiere recibir el cliente con sus tareas pendientes.
* **Responsable anterior:** Quiere que quede constancia de lo que hizo.

**Precondiciones:**

* El administrador ha iniciado sesión.
* El cliente tiene un responsable asignado y el usuario destinatario está activo.

**Disparador:** Cambia la carga de trabajo, un empleado deja el puesto o se reorganiza la cartera.

**Garantía de éxito:**

* El cliente queda asignado al nuevo usuario con sus tareas pendientes traspasadas, el responsable anterior pierde el acceso, y el historial conserva sus acciones y el cambio de responsable.

**Garantía mínima:**

* Si el traspaso no se completa, el cliente y sus tareas permanecen con el responsable anterior.

**Escenario principal de éxito:**

1. El administrador selecciona el cliente y elige transferirlo.
2. El administrador elige el nuevo usuario responsable.
3. El sistema muestra un resumen: tareas pendientes que se traspasarán y acciones que se conservan a nombre del responsable anterior.
4. El administrador confirma.
5. El sistema asigna el cliente al nuevo usuario (CU-106), traspasa las tareas pendientes y mantiene las acciones anteriores.
6. El sistema anota el cambio de responsable en el historial del cliente.

**Extensiones:**

* **2a. El usuario destinatario no está disponible o su cuenta está desactivada:** El sistema no lo ofrece como opción.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Ceder cliente de forma temporal

* **ID / Código:** CU-108
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR2, FR2.2. Relacionados: FR8.1.
* **Descripción:** El administrador asigna los clientes de un usuario a otro durante un periodo (por baja o vacaciones), indicando las fechas de inicio y fin. Al llegar la fecha de fin, el sistema devuelve los clientes a su responsable inicial (CU-109).

**Interesados:**

* **Turbine (Administración):** Quiere mantener la atención a los clientes durante las ausencias sin reasignaciones manuales.
* **Usuario sustituto:** Quiere acceso a los clientes solo durante el periodo necesario.
* **Responsable inicial:** Quiere recuperar sus clientes al volver.

**Precondiciones:**

* El administrador ha iniciado sesión.
* El usuario sustituto tiene la cuenta activa.

**Disparador:** Un usuario va a ausentarse por vacaciones o baja.

**Garantía de éxito:**

* Los clientes quedan cedidos al sustituto durante el periodo indicado, con la fecha de devolución programada, y la cesión consta en el historial.

**Garantía mínima:**

* Si los datos no son válidos o se cancela, la asignación no cambia.

**Escenario principal de éxito:**

1. El administrador elige ceder los clientes de un usuario.
2. El administrador selecciona los clientes (o todos los del usuario) y el usuario sustituto.
3. El administrador indica la fecha de inicio y la fecha de fin del periodo.
4. El sistema valida que la fecha de fin sea posterior a la de inicio y muestra un resumen.
5. El administrador confirma.
6. El sistema registra la cesión, da acceso al sustituto desde la fecha de inicio, programa la devolución para la fecha de fin y anota la cesión en el historial de cada cliente.

**Extensiones:**

* **4a. Las fechas no son válidas:** El sistema lo indica y el flujo vuelve al paso 3.
* **Al llegar la fecha de fin:** El sistema devuelve los clientes a su responsable inicial (CU-109).
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Devolver cliente al responsable inicial

* **ID / Código:** CU-109
* **Actor principal:** Temporizador (Scheduler).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR2.2. Relacionados: CU-108.
* **Descripción:** Al llegar la fecha de fin de una cesión temporal, el sistema devuelve automáticamente los clientes a su responsable inicial. Este caso de uso extiende CU-108 cuando se cumple la fecha de fin.

**Interesados:**

* **Responsable inicial:** Quiere recuperar sus clientes sin gestiones manuales.
* **Turbine (Administración):** Quiere que ningún cliente quede en manos del sustituto más tiempo del previsto.

**Precondiciones:**

* Existe una cesión temporal vigente (CU-108).

**Disparador:** Llega la fecha de fin de la cesión.

**Garantía de éxito:**

* Los clientes vuelven al responsable inicial, el sustituto pierde el acceso a ellos y el cambio consta en el historial.

**Garantía mínima:**

* Si la devolución falla, el sistema lo registra, reintenta y avisa al administrador, y los clientes no quedan sin responsable.

**Escenario principal de éxito:**

1. El sistema detecta las cesiones cuya fecha de fin se ha cumplido.
2. El sistema devuelve cada cliente a su responsable inicial, junto con las tareas pendientes.
3. El sistema retira el acceso al usuario sustituto.
4. El sistema anota la devolución en el historial de cada cliente.

**Extensiones:**

* **1a. El responsable inicial tiene la cuenta desactivada:** El sistema no devuelve el cliente y avisa al administrador para que lo reasigne (CU-107).
* **1b. El administrador ha prorrogado la cesión antes de la fecha de fin:** El sistema aplica la nueva fecha y no devuelve el cliente todavía.

---

## Caso de Uso: Aplicar autorización centralizada por roles

* **ID / Código:** CU-110
* **Actor principal:** Sistema.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR3. Relacionados: FR1, RNF-07.
* **Descripción:** Para cada petición, el sistema consulta en un único punto el rol del usuario (superadministrador, administrador o usuario operativo) y decide si la acción está permitida. La comprobación se hace siempre en el servidor, aunque el usuario conozca la dirección directa de un recurso.

**Interesados:**

* **Turbine (Administración):** Quiere que cada persona haga solo lo que le corresponde.
* **Usuario:** Quiere ver solo las opciones que puede utilizar.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene un rol asignado.

**Disparador:** El usuario realiza cualquier petición al sistema.

**Garantía de éxito:**

* Las acciones permitidas por el rol se ejecutan y las no permitidas se rechazan.

**Garantía mínima:**

* Ante cualquier duda o fallo al determinar el permiso, el sistema deniega la acción.

**Escenario principal de éxito:**

1. El sistema recibe una petición del usuario.
2. El sistema identifica el rol del usuario y, si procede, el cliente sobre el que actúa.
3. El sistema comprueba si el rol permite la acción (y, para un usuario operativo, si el cliente le está asignado, CU-111).
4. El sistema ejecuta la acción.

**Extensiones:**

* **3a. La acción no está permitida para el rol:** El sistema deniega la acción y muestra un mensaje claro (CU-112).

---

## Caso de Uso: Acceder a los clientes asignados

* **ID / Código:** CU-111
* **Actor principal:** Usuario (usuario operativo).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR3, FR3.1. Relacionados: FR2.
* **Descripción:** El usuario operativo consulta y modifica la información operativa de los clientes que tiene asignados, crea facturas si su permiso lo permite, sigue los cobros y descarga documentos. No puede ver ni exportar datos de clientes que no le correspondan.

**Interesados:**

* **Usuario:** Quiere trabajar con su cartera de clientes de forma ágil.
* **Turbine (Administración):** Quiere que los datos de cada cliente solo los vea su responsable.

**Precondiciones:**

* El usuario ha iniciado sesión.
* Tiene al menos un cliente asignado, de forma permanente o temporal.

**Disparador:** El usuario necesita trabajar con uno de sus clientes.

**Garantía de éxito:**

* El usuario ve y gestiona únicamente los clientes que tiene asignados.

**Garantía mínima:**

* Ningún dato de clientes no asignados se muestra ni se exporta.

**Escenario principal de éxito:**

1. El usuario accede a su listado de clientes.
2. El sistema muestra solo los clientes asignados al usuario (CU-110).
3. El usuario abre la ficha de un cliente.
4. El sistema muestra la información operativa, las facturas y los cobros.
5. El usuario consulta o modifica los datos que su permiso le permite, o descarga documentos.

**Extensiones:**

* **2a. El usuario no tiene clientes asignados:** El sistema muestra un listado vacío con un mensaje informativo.
* **3a. El usuario accede por dirección directa a un cliente no asignado:** El sistema deniega el acceso (CU-112).

---

## Caso de Uso: Bloquear acciones no permitidas del usuario operativo

* **ID / Código:** CU-112
* **Actor principal:** Sistema.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR3, FR3.2. Relacionados: RNF-07, RNF-08.
* **Descripción:** El sistema impide que un usuario operativo se asigne clientes a sí mismo, modifique tarifas globales, cree series de facturación o consulte el panel de control financiero global.

**Interesados:**

* **Turbine (Administración):** Quiere evitar cambios y accesos fuera del rol.

**Precondiciones:**

* Un usuario operativo intenta una acción reservada a otros roles.

**Disparador:** Una petición del usuario operativo es rechazada por CU-110.

**Garantía de éxito:**

* La acción no se ejecuta, los datos no cambian y el usuario recibe un mensaje claro.

**Garantía mínima:**

* Los intentos reiterados quedan registrados para su revisión.

**Escenario principal de éxito:**

1. El sistema detecta que la acción solicitada no está permitida para el usuario operativo.
2. El sistema rechaza la petición sin modificar ningún dato.
3. El sistema muestra al usuario un mensaje indicando que no tiene permiso.
4. El sistema registra el intento en la auditoría.

**Extensiones:**

* **1a. La acción es visible en la interfaz por error:** El sistema la rechaza igualmente en el servidor.

---

## Caso de Uso: Configurar el sistema globalmente

* **ID / Código:** CU-113
* **Actor principal:** Superadministrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR3, FR3.3. Relacionados: RNF-08.
* **Descripción:** El superadministrador realiza la configuración global profunda del sistema: series de facturación, tarifas, plazos y reglas de automatización, además de las acciones administrativas de mayor impacto. Ningún otro rol puede hacerlo.

**Interesados:**

* **Turbine (Dirección):** Quiere que los parámetros críticos solo los cambie el rol de máxima responsabilidad.
* **Administradores y usuarios:** Quieren trabajar con reglas estables y coherentes.

**Precondiciones:**

* El superadministrador ha iniciado sesión.

**Disparador:** Hay que crear o modificar un parámetro global del sistema.

**Garantía de éxito:**

* El parámetro queda guardado, se aplica desde ese momento y el cambio se registra en la auditoría con el valor anterior y el nuevo.

**Garantía mínima:**

* Si el valor no es válido o se cancela, la configuración se mantiene como estaba.

**Escenario principal de éxito:**

1. El superadministrador accede a la configuración global.
2. El sistema muestra los parámetros actuales (series de facturación, tarifas, plazos y reglas de automatización).
3. El superadministrador modifica uno o varios parámetros.
4. El sistema valida los valores y muestra las consecuencias del cambio.
5. El superadministrador confirma.
6. El sistema guarda la configuración y registra el cambio en la auditoría.

**Extensiones:**

* **4a. Algún valor no es válido o es incoherente con la configuración existente:** El sistema lo indica y el flujo vuelve al paso 3.
* **Cualquier paso. Un usuario que no es superadministrador intenta acceder:** El sistema deniega el acceso (CU-110, CU-112).
* **Cualquier paso. El superadministrador cancela la operación:** El sistema no guarda ningún cambio.
