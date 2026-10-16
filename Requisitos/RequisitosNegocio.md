# BORRADOR

# Requisitos de Negocio (RN)

* **RN-01. Automatización de la facturación y los cobros:** Sustituir el proceso actual, en el que cada factura se hace a mano y cada recordatorio de pago se escribe uno a uno, por un sistema que facture y persiga los cobros de forma automática.
  * *Origen:* entrevista (p. 5); anexo de la entrevista (p. 18, «Problema actual» y «Reto»).
  * *Se concreta en:* FR14.2, FR21, FR23.

* **RN-02. Liberar al equipo de las tareas administrativas:** Turbine es una startup en fase *seed* con siete personas dedicadas al producto y al crecimiento. Hoy el CEO lleva solo toda la administración, y la empresa no quiere desviar a su equipo hacia ella.
  * *Origen:* entrevista (p. 5).
  * *Se concreta en:* RN-01; FR14.2, FR23.2, FR23.3.

* **RN-03. Gestión diaria rápida y sencilla:** Las operaciones habituales (dar de alta un cliente, emitir una factura, comprobar un cobro) deben hacerse en menos de un minuto, con los mínimos clics y sin la curva de aprendizaje de un software contable como Holded ni de un ERP. Si no, a Turbine le compensaría contratar uno ya existente.
  * *Origen:* entrevista (pp. 5–6, criterios de éxito); anexo (p. 18); caso ampliado (p. 1).
  * *Se concreta en:* FR28; RNF-01, RNF-14.

* **RN-04. Gestión desde cualquier lugar:** El CEO viaja continuamente y necesita emitir, enviar y consultar facturas sin depender de un ordenador de sobremesa.
  * *Origen:* entrevista (p. 6); anexo (p. 18).
  * *Se concreta en:* RNF-02.

* **RN-05. Información a la persona adecuada:** Cada cliente tiene un responsable interno que debe enterarse a tiempo de los cobros, los vencimientos y los impagos, sin que ningún dato se quede por el camino.
  * *Origen:* entrevista (pp. 6 y 17); caso ampliado (p. 6).
  * *Se concreta en:* FR2, FR26, FR28.1.

* **RN-06. Visibilidad de la situación económica:** Saber en todo momento cuánto se ha facturado, cobrado, queda pendiente o está vencido, sin abrir cliente por cliente, y poder explicar de dónde sale cada cifra.
  * *Origen:* entrevista (pp. 10–11); caso ampliado (pp. 1, 6 y 10).
  * *Se concreta en:* FR29.

* **RN-07. Confianza en la automatización:** La automatización debe ser fiable y explicable: si una factura no se envió o se marcó como pagada, tiene que poder saberse por qué. Para el cliente, automatizar errores a escala sería peor que hacerlo a mano.
  * *Origen:* caso ampliado (pp. 5 y 14).
  * *Se concreta en:* FR21.4, FR32, FR33; RNF-05, RNF-08, RNF-15.

* **RN-08. Preparación para crecer y evolucionar:** Pasar de 10 clientes beta y una sola persona encargada a miles de clientes y un departamento administrativo, sin rehacer la herramienta. Debe admitir la vocación internacional de la empresa y cambios futuros, como otros planes, otra sociedad emisora o nuevos canales.
  * *Origen:* entrevista (pp. 5 y 16); caso ampliado (pp. 2, 8, 11 y 15).
  * *Se concreta en:* FR4.2, FR30.5; RNF-04, RNF-11, RNF-16.

* **RN-09. Cumplimiento legal de la facturación y protección de datos:** Las facturas deben ser válidas (numeración correlativa, datos fiscales e IVA) y no modificarse una vez cerrado el trimestre. Los datos fiscales y personales de los clientes deben estar protegidos y conservarse el tiempo que permita la ley.
  * *Origen:* `TurbineH.pdf` (apartados 1.2.2 y 1.3.2); entrevista (pp. 8–10 y 12); anexo (p. 18); caso ampliado (p. 7).
  * *Se concreta en:* FR18, FR19, FR33; RNF-07, RNF-09.

* **RN-10. Separación entre la administración y el producto:** La herramienta debe ser interna e independiente de Brain, el producto que Turbine vende a sus clientes, de modo que siga funcionando aunque Brain cambie. A la vez, debe poder comunicarse con Brain u otros sistemas en el futuro mediante una API.
  * *Origen:* entrevista (pp. 9, 11 y 18); caso ampliado (p. 1).
  * *Se concreta en:* FR31.