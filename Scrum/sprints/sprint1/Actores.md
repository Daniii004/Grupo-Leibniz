## **ACT-01: Super administrador**
- **Descripción:** Es el rol con máximo poder sobre la plataforma.
- **Funciones:**
  - Puede gestionar todo lo que desee dentro de la aplicación sin ninguna restricción.
  - Es el único que puede hacer una configuración profunda del sistema.
  - Puede crear y dar de baja a otros administradores o usuarios.
  - Puede ver los registros de auditoría para saber quién ha tocado qué cosa.
- **Restricciones:** Debe existir de forma obligatoria al menos una persona con este rol.

## **ACT-02: Administrador**
- **Descripción:** Es el rol que lleva la gestión de la empresa en el día a día y supervisa a los demás.
- **Funciones:**
  - Puede gestionar cualquier aspecto de la aplicación, dar permisos y asignar o reasignar clientes a los usuarios que desee.
  - Tiene acceso al panel de métricas semanales, mensuales, trimestrales y anuales de la empresa.
  - Puede comprobar y marcar cobros a mano si Stripe o el banco fallan al detectarlos.
  - Puede sacar los informes y los archivos de facturas para mandárselos a la gestoría.

## **ACT-03: Usuario / Lector**
- **Descripción:** 
  - Empleado básico del departamento administrativo o comercial interno de Turbine.
- **Función:**
    - Consultar fechas de emisión, vencimiento y estado de pago o impago de facturas.
    - Gestionar las fichas y personas de contacto de sus clientes asignados.
    - Generar, editar y tramitar facturas asociadas a dichos clientes.
    - Realizar seguimiento de cobros, registrar incidencias y descargar documentos PDF.
- **Restricciones:**
    - Acceso limitado exclusivamente a los clientes que un Administrador le haya asignado (o delegado temporalmente).
    - Sin acceso al dashboard financiero global, configuración general ni modificación de tarifas globales.
