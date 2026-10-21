# Especificación de Casos de Uso - Bloque 2: Gestión de clientes y contactos

---

## Caso de Uso: Dar de alta un cliente

* **ID / Código:** CU-201
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR4, FR4.1, FR4.2, FR6.1. Relacionados: FR2 (CU-106), FR5 (CU-203), RNF-01, RNF-11.
* **Descripción:** El administrador registra un nuevo cliente diferenciando sus datos comerciales (nombre, sector, ubicación y etiquetas) de sus datos fiscales (razón social, NIF, domicilio, país e idioma), junto con su moneda de trabajo. El identificador fiscal y el domicilio no se atan al formato español. El sistema no permite registrar un cliente cuyo NIF ya exista.

**Interesados:**

* **Turbine (Administración):** Quiere tener cada cliente registrado una sola vez y con los datos necesarios para facturarle.
* **Responsable del cliente:** Quiere disponer de la ficha completa para empezar a trabajar con él.

**Precondiciones:**

* El administrador ha iniciado sesión.

**Disparador:** Turbine incorpora un nuevo cliente.

**Garantía de éxito:**

* El cliente queda registrado con sus datos comerciales y fiscales por separado y en estado *Pendiente de activación*. El alta consta en su historial.

**Garantía mínima:**

* Si faltan datos obligatorios, el NIF está duplicado o el administrador cancela, no se crea ningún cliente.

**Escenario principal de éxito:**

1. El administrador elige dar de alta un cliente.
2. El administrador introduce los datos comerciales: nombre, sector, ubicación y etiquetas.
3. El administrador introduce los datos fiscales: razón social, NIF, domicilio, país e idioma, y la moneda de trabajo.
4. El sistema valida los campos obligatorios y comprueba que el NIF no exista ya en la base de datos.
5. El administrador confirma el alta.
6. El sistema crea el cliente, anota el alta en su historial y ofrece asignarlo a un usuario (CU-106) y registrar sus personas de contacto (CU-203).

**Extensiones:**

* **3a. El cliente es extranjero:** El sistema admite identificadores fiscales y domicilios en otros formatos, sin exigir, por ejemplo, la provincia.
* **4a. Faltan campos obligatorios:** El sistema señala cuáles son y el flujo vuelve al paso 2 o 3.
* **4b. El NIF ya existe:** El sistema rechaza el alta, indica el cliente que ya lo tiene y sugiere fusionar duplicados si procede (CU-207).
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Modificar la ficha del cliente

* **ID / Código:** CU-202
* **Actor principal:** Usuario (responsable del cliente).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR4, FR4.1, FR4.2. Relacionados: FR6.1, FR8.1, RNF-06, RNF-08.
* **Descripción:** El usuario actualiza los datos comerciales o fiscales de un cliente que tiene asignado. Los cambios no alteran las facturas ya emitidas y se registran en el historial con el valor anterior y el nuevo.

**Interesados:**

* **Responsable del cliente:** Quiere mantener la ficha al día en pocos pasos.
* **Turbine (Administración):** Quiere trazabilidad de los cambios, sobre todo de los datos fiscales.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente, o tiene rol de Administrador.

**Disparador:** Cambian los datos del cliente.

**Garantía de éxito:**

* La ficha queda actualizada y el cambio consta en el historial (quién, cuándo, valor anterior y nuevo).

**Garantía mínima:**

* Si los datos no son válidos o el usuario cancela, la ficha se mantiene como estaba.

**Escenario principal de éxito:**

1. El usuario abre la ficha del cliente y elige editarla.
2. El sistema muestra los datos comerciales y fiscales actuales.
3. El usuario modifica los campos necesarios.
4. El sistema valida los datos y comprueba que, si cambia el NIF, no coincida con el de otro cliente.
5. El usuario confirma.
6. El sistema guarda los cambios y los anota en el historial.

**Extensiones:**

* **4a. El nuevo NIF ya pertenece a otro cliente:** El sistema rechaza el cambio y lo indica.
* **4b. Los datos no son válidos:** El sistema señala los errores y el flujo vuelve al paso 3.
* **5a. Otro usuario ha modificado la ficha mientras tanto:** El sistema avisa del conflicto y no sobrescribe sin que el usuario revise los cambios.
* **Cualquier paso. El usuario cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Gestionar personas de contacto

* **ID / Código:** CU-203
* **Actor principal:** Usuario (responsable del cliente).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR5, FR5.1, FR5.2, FR5.5. Relacionados: FR4, FR25.1, RNF-09.
* **Descripción:** El usuario registra y mantiene varias personas de contacto de un cliente. A cada una le asigna un rol dentro de la empresa (comercial, administración, cobros u otro) y registra los canales de comunicación que admite (por ejemplo, correo o WhatsApp). Una misma persona o gestor externo puede asociarse a varias empresas clientes. Los datos de facturación no se utilizan para fines comerciales ajenos a la gestión administrativa.

**Interesados:**

* **Responsable del cliente:** Quiere saber a quién dirigirse para cada asunto y por qué canal.
* **Persona de contacto:** Quiere recibir solo las comunicaciones que le corresponden y por los canales que admite.
* **Turbine (Administración):** Quiere que las facturas lleguen al destinatario correcto.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente, o tiene rol de Administrador.
* El cliente existe en el sistema.

**Disparador:** Hay que añadir o actualizar una persona de contacto de un cliente.

**Garantía de éxito:**

* La persona de contacto queda asociada al cliente con su rol y sus canales, y el cambio consta en el historial.

**Garantía mínima:**

* Si los datos no son válidos o el usuario cancela, los contactos del cliente no cambian.

**Escenario principal de éxito:**

1. El usuario abre la ficha del cliente y accede a sus personas de contacto.
2. El usuario elige añadir un contacto nuevo o editar uno existente.
3. El usuario introduce el nombre y los datos de contacto.
4. El usuario asigna el rol de la persona en la empresa cliente.
5. El usuario indica los canales de comunicación que la persona admite.
6. El sistema valida los datos y los guarda, anotando el cambio en el historial.

**Extensiones:**

* **3a. La persona ya existe asociada a otra empresa cliente:** El sistema permite reutilizar el mismo contacto y asociarlo también a este cliente, sin duplicarlo.
* **4a. Se asigna el rol de facturación o cobros:** El sistema lo marca como destinatario de las facturas del cliente.
* **6a. El correo o el teléfono no son válidos:** El sistema señala el error y el flujo vuelve al paso 3.
* **Cualquier paso. El usuario cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Desactivar un contacto conservando su historial

* **ID / Código:** CU-204
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR5, FR5.3. Relacionados: FR5.4 (CU-205), FR18.4, RNF-09.
* **Descripción:** El administrador desactiva una persona de contacto de una empresa cliente. Deja de recibir comunicaciones, pero se conserva el historial de las facturas enviadas a sus direcciones de correo.

**Interesados:**

* **Turbine (Administración):** Quiere dejar de enviar documentos a quien ya no es interlocutor sin perder la trazabilidad de lo enviado.
* **Cliente:** Quiere que sus comunicaciones lleguen solo a personas vigentes.

**Precondiciones:**

* El administrador ha iniciado sesión.
* El contacto existe y está activo.

**Disparador:** Una persona deja de ser interlocutor del cliente.

**Garantía de éxito:**

* El contacto queda desactivado en esa empresa, no recibe nuevos envíos y su historial de facturas enviadas se conserva.

**Garantía mínima:**

* Si no se completa o el administrador cancela, el contacto sigue activo.

**Escenario principal de éxito:**

1. El administrador abre la ficha del cliente y selecciona el contacto.
2. El administrador elige desactivarlo.
3. El sistema comprueba si es el único contacto de facturación del cliente.
4. El administrador confirma la desactivación.
5. El sistema desactiva el contacto, conserva sus datos y el historial de envíos, y anota el cambio en el historial del cliente.

**Extensiones:**

* **3a. Es el único contacto de facturación de un cliente activo:** El sistema avisa antes de continuar (CU-205).
* **3b. El contacto está asociado a otras empresas:** El sistema desactiva solo su relación con este cliente y mantiene las demás.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Avisar del único contacto de facturación

* **ID / Código:** CU-205
* **Actor principal:** Sistema (extiende CU-204).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Subfunción.
* **Requisitos:** FR5.4. Relacionados: FR5.2, FR5.3.
* **Descripción:** Antes de desactivar o eliminar el único contacto de facturación de un cliente activo, el sistema avisa de que el cliente se quedaría sin destinatario para sus facturas. Este caso de uso extiende CU-204 cuando se cumple esa condición.

**Interesados:**

* **Turbine (Administración):** Quiere evitar facturas sin destinatario.

**Precondiciones:**

* Se está desactivando o eliminando un contacto (CU-204).
* Es el único contacto con rol de facturación de un cliente activo.

**Disparador:** El administrador pide desactivar al único contacto de facturación.

**Garantía de éxito:**

* El administrador conoce la consecuencia y decide con información.

**Garantía mínima:**

* No se desactiva el contacto sin confirmación explícita.

**Escenario principal de éxito:**

1. El sistema detecta que el contacto es el único de facturación del cliente activo.
2. El sistema muestra un aviso: el cliente quedaría sin destinatario para sus facturas.
3. El administrador decide continuar o volver atrás.
4. Si continúa, el sistema retoma CU-204.

**Extensiones:**

* **3a. El administrador decide designar otro contacto de facturación antes:** El sistema cancela la desactivación y lleva al administrador a gestionar los contactos (CU-203).

---

## Caso de Uso: Gestionar el estado del cliente

* **ID / Código:** CU-206
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR6, FR6.1, FR6.3, FR6.4. Relacionados: FR4.1, FR14.2, FR8.1.
* **Descripción:** El administrador cambia el estado de un cliente a lo largo de su ciclo de vida: *Pendiente de activación*, *Activo*, *Pausado*, *Baja solicitada*, *Baja* o *Bloqueado*. Antes de confirmar cada cambio, el sistema muestra sus consecuencias. No permite reactivar un cliente con un NIF ya existente en otro cliente, y bloquea la activación de la recurrencia si faltan datos fiscales, indicando cuáles.

**Interesados:**

* **Turbine (Administración):** Quiere que el estado refleje la situación real del cliente y que cada cambio sea consciente.
* **Responsable del cliente:** Quiere saber qué ocurrirá con la facturación al cambiar el estado.

**Precondiciones:**

* El administrador ha iniciado sesión.
* El cliente existe en el sistema.

**Disparador:** Cambia la situación comercial del cliente (alta efectiva, pausa, baja, bloqueo o reactivación).

**Garantía de éxito:**

* El cliente queda en el nuevo estado, se aplican sus consecuencias y el cambio consta en el historial.

**Garantía mínima:**

* Si no se cumplen las condiciones o el administrador cancela, el cliente conserva su estado.

**Escenario principal de éxito:**

1. El administrador abre la ficha del cliente y elige cambiar su estado.
2. El sistema muestra los estados permitidos desde el actual.
3. El administrador elige el nuevo estado.
4. El sistema valida las condiciones: NIF sin duplicados al reactivar y datos fiscales suficientes si se activa la recurrencia.
5. El sistema muestra de forma explícita las consecuencias del cambio (por ejemplo, que se dejarán de generar facturas o recordatorios).
6. El administrador confirma.
7. El sistema aplica el nuevo estado y lo anota en el historial del cliente.

**Extensiones:**

* **4a. Al reactivar, el NIF ya existe en otro cliente:** El sistema rechaza la reactivación e indica el cliente coincidente.
* **4b. Faltan datos fiscales para activar la recurrencia:** El sistema bloquea la activación e indica qué datos faltan. Si el usuario los completa (CU-202), el flujo vuelve al paso 4.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Fusionar clientes duplicados

* **ID / Código:** CU-207
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR6, FR6.2. Relacionados: FR8, FR8.1, RNF-08.
* **Descripción:** El administrador fusiona dos clientes que están duplicados en un único cliente, unificando todo su historial (facturas, cobros, contactos, suscripciones e incidencias).

**Interesados:**

* **Turbine (Administración):** Quiere una única ficha por cliente con todo su historial.
* **Responsables de ambos clientes:** Quieren no perder información tras la fusión.

**Precondiciones:**

* El administrador ha iniciado sesión.
* Existen dos fichas del mismo cliente.

**Disparador:** Se detecta que un mismo cliente está registrado dos veces.

**Garantía de éxito:**

* Queda una única ficha con el historial de ambas unificado, la ficha eliminada deja constancia de su fusión y la operación consta en la auditoría.

**Garantía mínima:**

* Si la fusión no se completa o se cancela, ambos clientes y su información permanecen intactos.

**Escenario principal de éxito:**

1. El administrador selecciona los dos clientes duplicados y elige fusionarlos.
2. El administrador indica cuál será el cliente que se conserva.
3. El sistema muestra una vista previa de los datos que se unificarán y los conflictos entre ambas fichas.
4. El administrador resuelve los conflictos eligiendo qué valor se conserva.
5. El administrador confirma la fusión.
6. El sistema unifica los datos y el historial en el cliente conservado, marca el otro como fusionado y lo registra en la auditoría.

**Extensiones:**

* **3a. Ambos clientes tienen suscripciones activas:** El sistema lo indica y exige al administrador decidir cuál se mantiene antes de continuar.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cambio.

---

## Caso de Uso: Importar clientes desde CSV

* **ID / Código:** CU-208
* **Actor principal:** Administrador.
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR7, FR7.1, FR7.2, FR7.3. Relacionados: FR4, FR6.1, RNF-02, RNF-03.
* **Descripción:** El administrador importa de una sola vez una lista de clientes desde un archivo CSV. Antes de guardar, el sistema muestra una vista previa, permite mapear las columnas del archivo con los campos del cliente y avisa de los NIF ya existentes. Al terminar genera un informe con los clientes guardados y los que han dado error.

**Interesados:**

* **Turbine (Administración):** Quiere cargar muchos clientes sin introducirlos uno a uno ni crear duplicados.

**Precondiciones:**

* El administrador ha iniciado sesión.
* Dispone de un archivo CSV con codificación UTF-8.

**Disparador:** Hay que dar de alta un conjunto de clientes, por ejemplo al migrar datos existentes.

**Garantía de éxito:**

* Los clientes válidos quedan guardados, y el administrador dispone de un informe con los guardados y los erróneos.

**Garantía mínima:**

* Si la importación se cancela antes de confirmar, no se guarda ningún cliente. Un error en una fila no impide procesar las demás.

**Escenario principal de éxito:**

1. El administrador elige importar clientes y selecciona el archivo CSV.
2. El sistema lee el archivo y propone el mapeo de sus columnas con los campos del cliente.
3. El administrador revisa y ajusta el mapeo.
4. El sistema muestra una vista previa de los datos y señala los errores y los NIF que ya existen.
5. El administrador decide, según la política definida, si corrige el archivo o importa solo las filas válidas.
6. El administrador confirma la importación.
7. El sistema guarda los clientes válidos y genera un informe con los clientes guardados y los que han producido errores, indicando el motivo.

**Extensiones:**

* **2a. El archivo no es un CSV válido o está vacío:** El sistema lo rechaza e indica el motivo.
* **4a. Hay NIF que ya existen o duplicados dentro del archivo:** El sistema marca esas filas y no las importa como nuevas.
* **5a. El administrador decide corregir el archivo:** El sistema cancela la importación y permite subir una versión corregida.
* **Cualquier paso. El administrador cancela la operación:** El sistema no guarda ningún cliente.

---

## Caso de Uso: Consultar el historial del cliente

* **ID / Código:** CU-209
* **Actor principal:** Usuario (responsable del cliente).
* **Ámbito:** Sistema interno de facturación y cobros de Turbine.
* **Nivel:** Objetivo de usuario.
* **Requisitos:** FR8, FR8.1. Relacionados: FR3.1, RNF-08.
* **Descripción:** El usuario consulta la evolución de un cliente como una línea temporal de eventos de negocio: alta, cambios de responsable y de tarifa, facturas emitidas, pagos, incidencias, pausas, baja y reactivación.

**Interesados:**

* **Responsable del cliente:** Quiere entender de un vistazo qué ha ocurrido con el cliente.
* **Turbine (Administración):** Quiere trazabilidad de la relación con cada cliente.

**Precondiciones:**

* El usuario ha iniciado sesión y tiene asignado el cliente, o tiene rol de Administrador.

**Disparador:** El usuario necesita revisar la situación o los antecedentes de un cliente.

**Garantía de éxito:**

* El usuario ve la línea temporal completa y ordenada del cliente.

**Garantía mínima:**

* La consulta no modifica ningún dato.

**Escenario principal de éxito:**

1. El usuario abre la ficha del cliente y accede a su historial.
2. El sistema muestra la fecha de alta y la línea temporal de eventos, de más reciente a más antiguo.
3. El usuario filtra por tipo de evento o por periodo, si lo necesita.
4. El usuario abre un evento para ver su detalle (por ejemplo, una factura o un pago).

**Extensiones:**

* **1a. El cliente no está asignado al usuario:** El sistema deniega el acceso.
* **3a. No hay eventos para el filtro elegido:** El sistema muestra un mensaje indicándolo.
