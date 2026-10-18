# Requisitos de Usuario (RU)

* **RU-01. Acceso y alta:** El acceso será con email y contraseña, y el email debe pertenecer al dominio corporativo autorizado. No hay autorregistro: el superadministrador, o un administrador con permiso, crea o invita usuarios.
  * *Se concreta en:* FR1, FR1.1, FR1.6.

* **RU-02. Desactivación:** El superadministrador, o un administrador con permiso, puede desactivar la cuenta de un usuario. La desactivación no borra el rastro de las acciones que hizo y, al desactivar un usuario, se invalidan sus sesiones activas.
  * *Se concreta en:* FR1.3, FR1.4.

* **RU-03. Recuperación de contraseña:** El usuario podrá recuperar su contraseña mediante enlaces de recuperación con caducidad razonable.
  * *Se concreta en:* FR1.2.

* **RU-04. Tarifas:** Puede cambiar la tabla de tarifas para nuevas revisiones sin que eso reescriba las facturas históricas.
  * *Se concreta en:* FR11.2, FR31.2.

* **RU-05. Aprobación de descuentos altos:** Aprueba los descuentos que superen el umbral configurado.
  * *Se concreta en:* FR16.5.

* **RU-06. Series de facturación:** Configura las series de facturación.
  * *Se concreta en:* FR18.2, FR31.2.

* **RU-07. Tiempos de recordatorio:** Configura los tiempos de los recordatorios de impago.
  * *Se concreta en:* FR23.2, FR23.3.

* **RU-08. Configuración global:** Gestiona el plazo de pago, la cadencia de recordatorios, el dominio de correo, los datos fiscales de Turbine, el logo y la plantilla, las series, las monedas, los impuestos, los umbrales, los canales y las políticas de automatización.
  * *Se concreta en:* FR31.2.

* **RU-09. Reglas corporativas:** Define las reglas corporativas, junto con el administrador según el impacto.
  * *Se concreta en:* FR3, FR31.2.

* **RU-10. Reapertura de periodos cerrados:** Puede reabrir un periodo cerrado, con permiso superior y motivo.
  * *Se concreta en:* FR19.5.

* **RU-11. Auditoría:** Puede consultar y filtrar la auditoría.
  * *Se concreta en:* RNF-08.

* **RU-12. Notificaciones propias:** Puede elegir qué notificaciones recibe dentro de la aplicación, cuáles por email y si quiere un resumen diario.
  * *Se concreta en:* FR26.3.

* **RU-13. Integridad de la auditoría:** No puede reescribir el pasado: la auditoría no es editable.
  * *Se concreta en:* RNF-08.

* **RU-14. Intangibilidad de las facturas emitidas:** Los cambios de tarifa no afectan a las facturas históricas.
  * *Se concreta en:* FR11.1, FR11.2.

* **RU-15. Aprobaciones económicas:** Aprueba determinados cambios económicos y, según importe, las facturas rectificativas, que además requieren permiso administrativo.
  * *Se concreta en:* FR16.2, FR19.6.

* **RU-16. Asignación de clientes:** Asigna y desasigna clientes a los usuarios.
  * *Se concreta en:* FR2.

* **RU-17. Delegación:** Puede delegar sus clientes en otra persona durante un intervalo, sin perder la asignación principal.
  * *Se concreta en:* FR2.2.

* **RU-18. Aprobaciones en ausencia:** Puede reasignar una aprobación o designar un suplente.
  * *Se concreta en:* FR16.4.

* **RU-19. Bandeja global:** Ve una bandeja global de notificaciones.
  * *Se concreta en:* FR26.3.

* **RU-20. Dashboard financiero:** Accede al dashboard financiero.
  * *Se concreta en:* FR30.

* **RU-21. Objetivos:** Gestiona los objetivos; es una función exclusiva de administradores.
  * *Se concreta en:* FR30.3.

* **RU-22. Notas restringidas:** Puede crear y ver notas restringidas administrativas.
  * *Se concreta en:* FR27.2.

* **RU-23. Cierre de periodos:** El cierre de periodos lo realiza un administrador autorizado.
  * *Se concreta en:* FR19.4.

* **RU-24. Informes programados:** Puede configurar un resumen semanal o mensual.
  * *Se concreta en:* FR30.9.

* **RU-25. Control de exportaciones:** Puede detectar exportaciones masivas (por ejemplo, de 2.000 clientes).
  * *Se concreta en:* FR29.5.

* **RU-26. Operaciones administrativas controladas:** Fusión de fichas de cliente, condonación de deuda con motivo y aprobación e importación administrativa de histórico.
  * *Se concreta en:* FR6.2, FR19.2, FR20.

* **RU-27. Usuarios y reglas:** Con permiso, crea o invita y desactiva usuarios (RU-01, RU-02) y participa en las reglas corporativas según el impacto.
  * *Se concreta en:* FR1.3, FR1.6, FR3.

* **RU-28. Trazabilidad de las operaciones controladas:** Las operaciones de fusión, condonación e importación son acciones controladas y completamente auditadas.
  * *Se concreta en:* RNF-08.

* **RU-29. Separación de funciones:** No puede aprobar su propia solicitud cuando la regla exige separación de funciones.
  * *Se concreta en:* FR16.2.

* **RU-30. Permisos granulares:** Sus permisos varían según lo concedido, por lo que cada capacidad debe poder concederse o retirarse de forma individual.
  * *Se concreta en:* FR3.

* **RU-31. Alcance:** Ve y modifica la información operativa de los clientes que tiene asignados.
  * *Se concreta en:* FR3.1.

* **RU-32. Facturación:** Crea facturas si su permiso lo permite.
  * *Se concreta en:* FR3.1, FR14.

* **RU-33. Cobros y documentos:** Sigue los cobros y descarga los documentos de sus clientes.
  * *Se concreta en:* FR3.1, FR21.

* **RU-34. Descuentos:** Puede preparar una propuesta o solicitar un descuento para el cliente que gestiona.
  * *Se concreta en:* FR15.3, FR16.1.

* **RU-35. Disputas:** El responsable puede poner una factura en disputa.
  * *Se concreta en:* FR24.1.

* **RU-36. Notificaciones:** Ve únicamente sus notificaciones.
  * *Se concreta en:* FR26.3.

* **RU-37. Preferencias personales:** Puede configurar sus preferencias personales, como recibir resumen por email.
  * *Se concreta en:* FR26.3.

* **RU-38. Pausa de recordatorios:** Solo puede pausar un caso concreto con permiso explícito y motivo.
  * *Se concreta en:* FR24.1.

* **RU-39. Sin autoasignación de clientes:** No puede darse a sí mismo acceso a otro cliente.
  * *Se concreta en:* FR3.2.

* **RU-40. Sin cambios globales de tarifas ni series:** No puede cambiar tarifas globales ni crear series de facturación.
  * *Se concreta en:* FR3.2.

* **RU-41. Sin acceso al panel financiero global:** No puede ver el dashboard financiero global.
  * *Se concreta en:* FR3.2.

* **RU-42. Sin supresión de recordatorios:** No puede decidir que sus clientes no tengan recordatorios de impago, salvo lo indicado en RU-39.
  * *Se concreta en:* FR24.1.

* **RU-43. Sin cambios en las reglas corporativas:** No puede cambiar reglas corporativas.
  * *Se concreta en:* FR3.2.

* **RU-44. Exportación limitada a sus clientes:** Solo puede exportar datos de los clientes que ve; no puede exportar el conjunto completo mediante una pantalla escondida.
  * *Se concreta en:* FR29.2, FR29.5.

* **RU-45. Notas restringidas fuera de su alcance:** No ve la nota restringida cuando no le corresponde.
  * *Se concreta en:* FR27.2.

* **RU-46. Aislamiento por URL:** El servidor rechaza el acceso por URL a facturas o documentos que no le corresponden.
  * *Se concreta en:* RNF-07.

* **RU-47. Responsable interno del cliente:** Cada cliente tiene un responsable interno al que deben llegarle los eventos relevantes: pago confirmado, factura vencida, rebote de email, disputa abierta, revisión anual pendiente, aprobación rechazada y aviso de fin de pausa.
  * *Se concreta en:* FR26.1.

* **RU-48. Solicitante de una aprobación:** Ve el estado (pendiente, aprobada, rechazada) y el comentario del aprobador.
  * *Se concreta en:* FR16.3.

* **RU-49. Aprobador:** No puede aprobar su propia solicitud cuando la regla exige separación de funciones; puede tener un suplente en su ausencia.
  * *Se concreta en:* FR16.2, FR16.4.

* **RU-50. Sustituto o delegado temporal:** Recibe clientes durante un intervalo sin que el titular pierda la asignación principal; al acabar, la delegación caduca.
  * *Se concreta en:* FR2.2.

* **RU-51. Nuevo responsable tras reasignación:** Hereda las tareas pendientes del cliente.
  * *Se concreta en:* FR2.1.

* **RU-52. Usuario mencionado:** Un comentario con mención genera una notificación al usuario mencionado.
  * *Se concreta en:* FR27.2.

* **RU-53. Ejecutor de acciones masivas, importaciones y exportaciones:** Estas acciones quedan identificadas y auditadas.
  * *Se concreta en:* FR29.5.

* **RU-54. Desarrollador o mantenimiento:** Necesita errores con identificador y traza de pila en el servidor.
  * *Se concreta en:* RNF-12.