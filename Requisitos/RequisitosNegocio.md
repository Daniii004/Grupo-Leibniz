# Requisitos de Negocio (RN)

## 1. Automatización y eficiencia operativa

* **RN-01. Automatización de la facturación y los cobros:** Sustituir el proceso actual, en el que cada factura se hace a mano y cada recordatorio de pago se escribe uno a uno, por un sistema que facture y persiga los cobros de forma automática.
  * *Se concreta en:* FR14.2, FR21, FR23.

* **RN-02. Reducir la carga administrativa:** Quitar al CEO, que hoy lleva solo toda la administración, la mayor parte del trabajo de facturación y cobros, y poder delegarlo más adelante en un departamento administrativo sin desviar al equipo de producto.
  * *Se concreta en:* FR2, FR14.2, FR23.2, FR23.3.

* **RN-03. Gestión diaria rápida y sencilla:** Las operaciones habituales (dar de alta un cliente, emitir una factura, comprobar un cobro) deben hacerse en menos de un minuto, con los mínimos clics y sin la curva de aprendizaje de un software contable como Holded ni de un ERP.
  * *Se concreta en:* FR28; RNF-01, RNF-14.

* **RN-04. Gestión desde cualquier lugar:** El CEO viaja continuamente y necesita emitir, enviar y consultar facturas sin depender de un ordenador de sobremesa.
  * *Se concreta en:* RNF-02.

## 2. Información, control y confianza

* **RN-05. Información a la persona adecuada:** Cada cliente tiene un responsable interno que debe enterarse a tiempo de los cobros, los vencimientos y los impagos, sin que ningún dato se quede por el camino.
  * *Se concreta en:* FR2, FR26, FR28.1.

* **RN-06. Visibilidad de la situación económica:** Saber en todo momento cuánto se ha facturado, cobrado, queda pendiente o está vencido, sin abrir cliente por cliente, y poder explicar de dónde sale cada cifra.
  * *Se concreta en:* FR29.

* **RN-07. Confianza en la automatización:** La automatización debe ser fiable y explicable: si una factura no se envió o se marcó como pagada, tiene que poder saberse por qué.
  * *Se concreta en:* FR21.4, FR32, FR33; RNF-05, RNF-08, RNF-15.

## 3. Crecimiento, cumplimiento legal e independencia del producto

* **RN-08. Preparación para crecer y evolucionar:** Pasar de 10 clientes beta y una sola persona encargada a miles de clientes y un departamento administrativo, sin rehacer la herramienta. Debe admitir la vocación internacional de la empresa y cambios futuros, como otros planes, otra sociedad emisora o nuevos canales.
  * *Se concreta en:* FR4.2, FR30.5; RNF-04, RNF-11, RNF-16.

* **RN-09. Cumplimiento legal de la facturación y protección de datos:** Las facturas deben ser válidas (numeración correlativa, datos fiscales e IVA) y, una vez emitidas, no pueden alterarse sin dejar rastro: cualquier corrección se hace con una factura rectificativa, y los periodos cerrados no admiten cambios ordinarios. Los datos fiscales y personales de los clientes deben estar protegidos y conservarse el tiempo que permita la ley.
* *Se concreta en:* FR18, FR19, FR33; RNF-07, RNF-09.

* **RN-10. Separación entre la administración y el producto:** La herramienta debe ser interna e independiente de Brain, el producto que Turbine vende a sus clientes, de modo que siga funcionando aunque Brain cambie. A la vez, debe poder comunicarse con Brain u otros sistemas en el futuro mediante una API.
  * *Se concreta en:* FR31.