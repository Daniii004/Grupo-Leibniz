# Especificación de Casos de Uso: Bloque 6 - Comunicaciones e Incidencias

### CU-601: Gestionar incidencias internas

*   **ID / Código:** CU-601
*   **Nombre del Caso de Uso:** Gestionar incidencias internas.
*   **Descripción:** El usuario registra, actualiza y cierra incidencias administrativas asociadas a clientes o facturas, colaborando con otros usuarios mediante comentarios y adjuntos.
*   **Actor Principal:** Usuario (Operativo o Administrador).
*   **Ámbito:** Sistema interno de Turbine.
*   **Nivel:** Objetivo de usuario.
*   **Interesados e Intereses:**
    *   **Usuario:** Quiere dejar constancia de un problema administrativo y coordinarse con el equipo para solucionarlo.
    *   **Sistema:** Quiere mantener la trazabilidad del problema y, si afecta al cobro, detener las comunicaciones automáticas de la factura afectada.
*   **Precondiciones:** El usuario debe estar autenticado en el sistema y tener permisos de acceso sobre el cliente o factura implicada.
*   **Disparador:** El usuario detecta una anomalía (ej. rebote de email, impago, NIF erróneo) y decide registrarla.
*   **Garantía de éxito:** La incidencia queda guardada, vinculada al cliente/factura, y el sistema actualiza su estado y notifica a los involucrados.
*   **Garantía mínima:** El sistema no altera ni corrompe los datos originales del cliente o de la factura si ocurre un error al guardar la incidencia.

**Escenario principal de éxito:**
1. El usuario solicita crear una nueva incidencia asociada a un cliente o factura.
2. El sistema solicita los datos de la incidencia (motivo, prioridad, estado, persona responsable y si afecta al cobro).
3. El usuario introduce los datos solicitados y confirma.
4. El sistema asocia la incidencia al cliente o factura y la registra.
5. El usuario solicita añadir un comentario en la incidencia con una mención a otro empleado.
6. El sistema registra el comentario y envía una notificación interna al empleado mencionado.
7. El usuario cambia el estado de la incidencia a "Cerrada/Resuelta" e introduce la resolución del problema.
8. El sistema actualiza el estado de la incidencia, registra la resolución y reactiva las comunicaciones automáticas si estaban pausadas.

**Extensiones:**
*   **3a. El usuario indica que la incidencia afecta al cobro:** El sistema suspende temporalmente el flujo de envíos y recordatorios para la factura asociada a esa incidencia al registrarla en el paso 4.
*   **5a. El usuario adjunta un archivo al comentario:** El sistema valida que el formato y tamaño sean correctos, clasifica el archivo por tipo (justificante, orden, etc.) y lo asocia a la incidencia junto con el comentario en el paso 6.
*   **5b. El usuario (Administrador) añade una nota restringida:** El sistema guarda el comentario en el paso 6 asegurando que solo sea visible para usuarios con rol de Administrador o Superadministrador.
*   **7a. Falta motivo de resolución:** El sistema detecta que no se indicó cómo se resolvió, informa que el registro de resolución es obligatorio e impide el cierre. El flujo vuelve al paso 7.

---

### CU-602: Configurar y simular plantillas de comunicación

*   **ID / Código:** CU-602
*   **Nombre del Caso de Uso:** Configurar y simular plantillas de comunicación.
*   **Descripción:** El administrador edita las plantillas para correo electrónico o WhatsApp y simula su envío para previsualizar los mensajes y el calendario de envíos.
*   **Actor Principal:** Administrador.
*   **Ámbito:** Sistema interno de Turbine.
*   **Nivel:** Objetivo de usuario.
*   **Interesados e Intereses:**
    *   **Administrador:** Quiere asegurarse de que el mensaje que recibirá el cliente es correcto y profesional.
*   **Precondiciones:** El administrador está autenticado en el sistema.
*   **Disparador:** El administrador necesita crear un nuevo aviso de impago o probar cómo se ve un recordatorio.
*   **Garantía de éxito:** La plantilla se guarda como una nueva versión funcional y la simulación muestra el resultado exacto sin generar envíos reales.
*   **Garantía mínima:** El sistema asegura que nunca se enviarán mensajes reales a los clientes durante el uso del modo de prueba.

**Escenario principal de éxito:**
1. El administrador solicita editar una plantilla de comunicación específica.
2. El sistema muestra el contenido actual, los idiomas disponibles y las variables dinámicas permitidas.
3. El administrador modifica el texto insertando variables dinámicas y confirma los cambios.
4. El sistema valida el contenido y guarda una nueva versión de la plantilla en el historial.
5. El administrador solicita simular el envío de la plantilla (Modo de prueba).
6. El sistema solicita seleccionar un cliente y una factura de prueba.
7. El administrador selecciona los datos de prueba requeridos.
8. El sistema reemplaza las variables dinámicas con los datos reales del cliente seleccionado y renderiza el mensaje final.
9. El sistema muestra la previsualización del mensaje y el calendario proyectado de recordatorios.

**Extensiones:**
*   **4a. Falta una variable obligatoria en el texto:** El sistema detecta la ausencia de un dato esencial (ej. el importe o la fecha de vencimiento), aborta el guardado e informa del error. El flujo vuelve al paso 3.

---

### CU-603: Gestionar notificaciones internas y alertas

*   **ID / Código:** CU-603
*   **Nombre del Caso de Uso:** Gestionar notificaciones internas y alertas.
*   **Descripción:** El usuario consulta su bandeja de notificaciones sobre eventos relevantes, actualiza su estado y configura sus preferencias de recepción.
*   **Actor Principal:** Usuario (Operativo o Administrador).
*   **Ámbito:** Sistema interno de Turbine.
*   **Nivel:** Objetivo de usuario.
*   **Interesados e Intereses:**
    *   **Usuario:** Quiere estar al tanto de los eventos críticos sin sentirse abrumado por el ruido interno.
*   **Precondiciones:** El usuario está autenticado en el sistema.
*   **Disparador:** El sistema genera un evento relevante (ej. factura vencida) o el usuario decide revisar sus avisos.
*   **Garantía de éxito:** El usuario visualiza la información actualizada y el sistema registra correctamente sus preferencias y el estado de cada aviso.
*   **Garantía mínima:** El sistema conserva el historial de alertas generadas aunque el usuario no las lea.

**Escenario principal de éxito:**
1. El sistema notifica al usuario la llegada de una nueva alerta relevante.
2. El usuario accede a su bandeja de notificaciones.
3. El sistema muestra la lista de alertas clasificadas por fecha y estado.
4. El usuario marca una notificación concreta como "Atendida/Resuelta".
5. El sistema actualiza el estado de la notificación y la archiva visualmente.
6. El usuario accede a la configuración de sus preferencias de notificación.
7. El sistema muestra las opciones disponibles (alertas en plataforma, email inmediato o resumen diario).
8. El usuario modifica sus preferencias y guarda los cambios.
9. El sistema confirma y aplica la nueva configuración de avisos.

**Extensiones:**
*   **3a. El usuario es un Administrador:** El sistema muestra, además de las alertas personales, una opción de acceso a la bandeja global de notificaciones para supervisar avisos de todo el equipo.