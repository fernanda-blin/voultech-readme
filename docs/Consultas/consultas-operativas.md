---
title: Consultas Operativas
excerpt: >-
  Consulta cajas, saldos, cartera, movimientos, órdenes, asignaciones y precios
  del sistema.
deprecated: false
hidden: false
metadata:
  robots: index
---
Consulta información operativa del sistema para revisar cajas, saldos, cartera, movimientos, órdenes, asignaciones y precios.

## Consultas disponibles

<Cards columns={4}>
  <Card title="Cajas y saldos" href="#cajas-con-saldo-online" icon="fa-wallet">
    Consulta cajas registradas y saldos asociados a cuentas operativas.
  </Card>
  <Card title="Operaciones y cartera" href="#operaciones" icon="fa-briefcase">
    Revisa operaciones ejecutadas y la cartera vigente de un cliente.
  </Card>
  <Card title="Movimientos" href="#movimientos" icon="fa-money-bill-transfer">
    Consulta movimientos operativos, bancarios y trazabilidad de retiros.
  </Card>
  <Card title="Órdenes y asignaciones" href="#ordenes" icon="fa-chart-line">
    Revisa órdenes registradas y sus asignaciones asociadas.
  </Card>
</Cards>

<br />

## Cajas con saldo online

**→ GET** `/api/publicapi/creasys/Cajas/ConSaldoOnline`

Devuelve las cajas registradas de una cuenta a la fecha consultada con saldo en línea. Incluye únicamente **parámetros operativos** (sin información del cliente).

**Resultado esperado:** obtienes el detalle operativo de las cajas vigentes para la cuenta consultada.

<br />

## Cajas

**→ GET** `/api/publicapi/creasys/Cajas`

Devuelve **todas las cajas registradas** por fecha consultada. Incluye tanto parámetros operativos como los **datos del cliente propietario**.

<Callout icon="💡" theme="info">
  Variante disponible: `GET /Cajas/ConSaldo` entrega cajas con saldo (no en línea), útil para consultas a cierre de día.
</Callout>

**Resultado esperado:** obtienes las cajas registradas junto con la información del cliente asociado.

<br />

## Operaciones

**→ GET** `/api/publicapi/creasys/Operaciones`

Entrega información detallada de las operaciones de un cliente dentro de un **periodo determinado**.

**Resultado esperado:** obtienes el historial de operaciones del cliente dentro del rango consultado.

<br />

## Cartera

**→ GET** `/api/publicapi/creasys/Cartera`

Entrega la cartera de inversiones de un cliente, incluyendo posiciones, instrumentos y valorizaciones vigentes.

<Accordion title="Ver variantes de consulta de cartera" icon="fa-briefcase">

**→ GET** `/api/publicapi/creasys/Cartera/CarteraResumida`

Versión **resumida** de la cartera, con menor nivel de detalle que la consulta principal.

**→ GET** `/api/publicapi/creasys/Cartera/CarteraDetallada`

Versión **detallada** con desglose completo por instrumento.

**→ GET** `/api/publicapi/creasys/Cartera/CarteraActual`

Snapshot vigente de cartera (a la fecha actual).

**→ GET** `/api/publicapi/creasys/Cartera/CarteraActualOptimizada`

Versión **optimizada** del snapshot, ideal para consultas masivas o vistas de alto rendimiento.

</Accordion>

<Callout icon="💡" theme="info">
  Usá `CarteraActualOptimizada` para vistas rápidas y frecuentes. Usá `Cartera` o `CarteraDetallada` cuando necesites el detalle completo.
</Callout>

**Resultado esperado:** obtienes la posición vigente del cliente con el nivel de detalle que mejor se ajuste a tu caso de uso.

<br />

## Movimientos

**→ GET** `/api/publicapi/creasys/Movimientos`

Entrega los movimientos de una cuenta o cliente, filtrables por rango de fechas o tipo de operación.

**Resultado esperado:** obtienes los movimientos registrados para la cuenta o cliente según los filtros aplicados.

<br />

## Órdenes

**→ GET** `/api/publicapi/creasys/Ordenes`

Consulta las órdenes registradas en el sistema dentro de un rango de fechas.

<Callout icon="💡" theme="info">
  Variante disponible: `GET /Ordenes/SaldoCustodiaIntraDay` entrega el saldo de custodia intra-día asociado a las órdenes en curso.
</Callout>

**Resultado esperado:** obtienes el listado de órdenes registradas en el periodo consultado.

<br />

## Asignaciones

**→ GET** `/api/publicapi/creasys/Asignaciones`

Devuelve las asignaciones de órdenes o instrumentos asociadas a un cliente o portafolio específico.

**Resultado esperado:** obtienes el detalle de las asignaciones vinculadas al cliente o portafolio consultado.

<br />

## Precios publicados

**→ GET** `/api/publicapi/creasys/PublicadorPrecio/GetPreciosInstrumento`

Entrega los precios publicados de instrumentos financieros disponibles en la corredora.

**Resultado esperado:** obtienes los precios publicados para los instrumentos financieros disponibles.

<br />

## Movimientos Shinkansen

**→ GET** `/api/publicapi/creasys/Movimientos/MovimientosShinkansen`

Consulta los movimientos procesados a través del sistema Shinkansen, utilizados para conciliaciones o trazabilidad de aportes y retiros automáticos.

**Resultado esperado:** obtienes la trazabilidad de los movimientos procesados mediante Shinkansen.

<br />

## Movimientos Banco BICE

**→ GET** `/api/publicapi/creasys/Movimientos/MovimientosBancoBice`

Devuelve los movimientos bancarios sincronizados con **Banco BICE**, en formato estándar de conciliación.

**Resultado esperado:** obtienes los movimientos bancarios sincronizados desde Banco BICE.
