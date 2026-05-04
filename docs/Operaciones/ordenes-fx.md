---
title: Órdenes FX (Spot)
excerpt: >-
  Registra operaciones spot de compra y venta de divisas con liquidación
  inmediata.
deprecated: false
hidden: false
metadata:
  robots: index
---
Registra operaciones spot de compra y venta de divisas. Las órdenes FX viven separadas de las órdenes de renta variable en el API reference, bajo la categoría **Órdenes — FX y Spot**.

<Callout icon="⚠️" theme="warning">
  Para comprar efectivamente las divisas debes conectarte a la **API FX de Voultech** y obtener el precio de mesa. Solicita acceso a [hey@voultech.com](mailto:hey@voultech.com).
</Callout>

## Compra/venta de divisas (Spot)

**→ POST** `/api/publicapi/creasys/Operaciones/IngresoOperacionSpot`

Ingresa una operación spot de compra o venta de divisas.

<Accordion title="Ver parámetros principales" icon="fa-file-lines">

| Parámetro | Descripción |
|---|---|
| `numCuenta` | Cuenta del cliente |
| `codTipoOperacion` | `COMPRA` o `VENTA` |
| `contraparte` | Usar `M/X` como contraparte default |
| `codMonedaOperacion` | `CLP`, `USD`, `EUR` |
| `codMonedaPagoCobro` | `CLP`, `USD`, `EUR` |
| `cantidad` | Cantidad de la divisa operada |
| `precio` | Precio unitario final entregado al cliente |
| `precioMesa` | Precio de mesa (obtenido vía API FX) |
| `precioTransferencia` | Debe ser igual a `precioMesa` |
| `monto` | `cantidad × precio`. Siempre en CLP, redondeado sin decimales |
| `fechaOperacion` | Fecha de la operación |
| `fechaLiquidacion` | Fecha de liquidación |
| `obsOperacion` | Observación opcional |

</Accordion>

```json title="Request Body"
[
  {
    "idOperacion": 0,
    "numCuenta": "17931004/80",
    "codTipoOperacion": "VENTA",
    "obsOperacion": "Prueba",
    "fechaOperacion": "2023-05-26",
    "fechaLiquidacion": "2023-05-26",
    "contraparte": "M/X",
    "codMonedaOperacion": "USD",
    "codMonedaPagoCobro": "CLP",
    "cantidad": 1,
    "precio": 500,
    "precioMesa": 500,
    "precioTransferencia": 500,
    "monto": 500
  }
]
```

**Resultado esperado:** la operación spot queda registrada con su moneda operada, moneda de pago/cobro y monto calculado.

<Accordion title="Catálogo de errores — Operaciones Spot" icon="fa-duotone fa-circle-exclamation">

| Código | Descripción |
|---|---|
| SPT-001 | No existe cuenta disponible con `numCuenta` |
| SPT-002 | No se encontró caja vigente `codMonedaOperacion` para `numCuenta` |
| SPT-003 | No existe caja vigente `codMonedaPagoCobro` para `numCuenta` |
| SPT-004 | No se encuentra tipo de operación `codTipoOperacion` para Spot |
| SPT-005 | No existe instrumento con nemotécnico `codMonedaOperacion` |
| SPT-006 | No existe la `contraparte` |
| SPT-007 | Tipo origen mov caja operación no encontrado |
| SPT-008 | Tipo origen mov caja pago cobro no se encuentra |
| SPT-009 | La fecha operación debe ser igual a la fecha máxima de operaciones ingresadas |
| SPT-010 | El `monto` ingresado no corresponde a `precio × cantidad` |
| SPT-011 | Saldo disponible insuficiente para ejecutar esta operación |
| SPT-012 | UUID duplicado en la misma transacción |
| SPT-013 | UUID ya utilizado en operación anterior |
| SPT-014 | Error del sistema |
| SPT-015 | Excepción del sistema |

</Accordion>

<br />

## Próximos pasos

<Cards columns={2}>
  <Card title="Movimientos de Caja" href="/docs/movimientos" icon="fa-duotone fa-money-bill-transfer">Aportes, retiros y operaciones sobre la caja del cliente.</Card>
  <Card title="Órdenes de Renta Variable" href="/docs/ordenes-renta-variable" icon="fa-duotone fa-chart-line">Ingresa y anula órdenes sobre instrumentos.</Card>
  <Card title="Sistema de Eventos" href="/docs/eventos" icon="fa-duotone fa-bell">Recibe notificaciones cuando se ejecutan operaciones en tiempo real.</Card>
</Cards>
