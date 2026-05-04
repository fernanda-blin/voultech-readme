---
title: Órdenes de Renta Variable
excerpt: >-
  Ingresa órdenes de compra/venta de instrumentos de renta variable y anula
  órdenes existentes.
deprecated: false
hidden: false
metadata:
  robots: index
---
Ingresa órdenes de compra/venta de instrumentos de renta variable y anula órdenes existentes. La orden viaja al motor de Voultech y al mercado para su ejecución.

<Callout icon="📚" theme="info">
  En el API reference este endpoint aparece tanto en **Órdenes — Renta Variable** (mercado nacional) como en **Órdenes Internacionales** (Alpaca). Es el mismo endpoint: el destino lo determinan `tipoSeguridad`, `codBolsa` y la cuenta utilizada.
</Callout>

## Operaciones disponibles

<Cards columns={2}>
  <Card title="Ingresar orden" href="#ingresar-orden-de-mercado" icon="fa-chart-line">
    Ingresa una orden de compra o venta de un instrumento de renta variable.
  </Card>
  <Card title="Anular orden" href="#anular-orden" icon="fa-ban">
    Solicita la anulación de una orden previamente ingresada.
  </Card>
</Cards>

<br />

## Ingresar orden de mercado

**→ POST** `/api/publicapi/creasys/Ordenes/IngresarOrdenesMercado`

Ingresa una orden de compra o venta de un instrumento de renta variable.

<Accordion title="Ver parámetros principales" icon="fa-file-lines">

| Parámetro | Descripción |
|---|---|
| `uuid` | Identificador único de la orden (idempotencia) |
| `numCuenta` | Número de cuenta del cliente |
| `tipoOperacion` | `C` (Compra) o `V` (Venta) |
| `cantidad` | Cantidad a transar |
| `precio` | Precio unitario |
| `tipoPrecio` | `LIMIT` o `MARKET` |
| `nemotecnico` | Código bolsa del instrumento |
| `tipoSeguridad` | Según [FIX Dictionary 4.2](https://www.onixs.biz/fix-dictionary/4.2/tagnum_167.html) (ej: `CS`) |
| `codBolsa` | Bolsa de Santiago: `XSGO` |
| `tipoLiquidacion` | `CASH` (PH), `NEXT_DAY` (PM) o `T2` (CN) |
| `comision` | Comisión porcentual (opcional, máx. 2 decimales, entre `0` y `1`) |

</Accordion>

```json title="Request Body"
[
  {
    "uuid": "abcdefgh-12kl-3456-mnop789qrstu",
    "numCuenta": "11931044/80",
    "nemotecnico": "COPEC",
    "cantidad": 100,
    "precio": 1000,
    "tipoPrecio": "LIMIT",
    "tipoOperacion": "C",
    "tipoSeguridad": "CS",
    "tipoLiquidacion": "T2",
    "codBolsa": "XSGO",
    "comision": 0
  }
]
```

<Callout icon="💡" theme="info">
  Valores especiales en `cantidad` de la respuesta: `98` = Cancelado, `99` = Rechazado, `50` = Parcialmente asignado, `100` = Asignado.
</Callout>

**Resultado esperado:** la orden queda ingresada para ejecución en mercado según los parámetros enviados.

<br />

## Anular orden

**→ POST** `/api/publicapi/creasys/Ordenes/AnularOrden`

<Accordion title="Ver parámetros" icon="fa-file-lines">

| Parámetro | Descripción |
|---|---|
| `uuid` | Identificador de la orden a anular |
| `numCuenta` | Cuenta sobre la que se ingresó la orden |

</Accordion>

```json title="Request Body"
[
  {
    "uuid": "abcdefgh-12kl-3456-mnop789qrstu",
    "numCuenta": "12345678/0"
  }
]
```

**Resultado esperado:** la orden indicada queda solicitada para anulación.

<br />

## Próximos pasos

<Cards columns={2}>
  <Card title="Movimientos de Caja" href="/docs/movimientos" icon="fa-duotone fa-money-bill-transfer">Aportes, retiros y operaciones sobre la caja del cliente.</Card>
  <Card title="Órdenes FX (Spot)" href="/docs/ordenes-fx" icon="fa-duotone fa-money-bill-trend-up">Compra y venta de divisas con liquidación spot.</Card>
  <Card title="Sistema de Eventos" href="/docs/eventos" icon="fa-duotone fa-bell">Recibe notificaciones cuando se ejecutan órdenes en tiempo real.</Card>
  <Card title="Operaciones internacionales" href="/docs/introduccion-alpaca" icon="fa-duotone fa-globe">Opera instrumentos en mercados internacionales con Alpaca.</Card>
</Cards>
