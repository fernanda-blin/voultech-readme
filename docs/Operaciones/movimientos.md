---
title: Movimientos de Caja
excerpt: >-
  Registra aportes, retiros y operaciones sobre la caja del cliente: aportes
  puntuales, retiros vía Shinkansen y cuenta remunerada.
deprecated: false
hidden: false
metadata:
  robots: index
---
Registra movimientos sobre la caja del cliente: aportes y retiros patrimoniales, retiros automáticos vía Shinkansen y operaciones sobre cuenta remunerada.

<Callout icon="🧭" theme="info">
  Esta página cubre **movimientos de caja**. Para órdenes sobre instrumentos ver [Órdenes de Renta Variable](/docs/ordenes-renta-variable). Para compra/venta de divisas ver [Órdenes FX (Spot)](/docs/ordenes-fx).
</Callout>

## Flujo estándar

El flujo de movimientos depende del modelo de negocio de cada fintech. Un ejemplo típico:

1. El cliente realiza un **aporte** sobre su caja
2. La fintech recibe la notificación a través del **Sistema de Eventos**
3. El cliente realiza una **compra de divisas** (ver [Órdenes FX](/docs/ordenes-fx))
4. Ejecuta una **orden de instrumento financiero** (ver [Órdenes de Renta Variable](/docs/ordenes-renta-variable))
5. Al cerrar la inversión, **vende el instrumento** y **reconvierte la divisa**
6. Se realiza el **retiro de fondos** desde la caja

## Operaciones de caja disponibles

<Cards columns={3}>
  <Card title="Aportes y retiros" href="#aportes-y-retiros" icon="fa-money-bill-transfer">
    Registra movimientos puntuales o masivos de ingreso y salida de fondos.
  </Card>
  <Card title="Retiros Shinkansen" href="#retiros-via-shinkansen" icon="fa-building-columns">
    Ejecuta retiros bancarios automáticos a cuentas del mismo cliente.
  </Card>
  <Card title="Cuenta remunerada" href="#aporteretiro-con-cuenta-remunerada" icon="fa-piggy-bank">
    Registra inversiones o rescates sobre cuenta remunerada.
  </Card>
</Cards>

<br />

## Aportes y retiros

Permite registrar aportes o retiros sobre la caja de un cliente.

**→ POST** `/api/publicapi/creasys/Movimientos/IngresoAporteRetiro` — Aporte o retiro puntual

**→ POST** `/api/publicapi/creasys/Movimientos/IngresoAporteRetiroMasivo` — Aportes o retiros masivos (acepta un array)

<Accordion title="Ver parámetros principales" icon="fa-file-lines">

| Parámetro | Tipo | Descripción |
|---|---|---|
| `uuid` | string | Identificador único de idempotencia del movimiento |
| `codTipoMovimiento` | string | `APO_PAT` (aporte) o `RET_PAT` (retiro) |
| `numCuenta` | string | Número de la cuenta donde se aplica el movimiento |
| `dscMedioPagoCobro` | string | Medio de pago: `TRANSFERENCIA`, `EFECTIVO`, `CHEQUE`, etc. |
| `codMoneda` | string | Moneda del movimiento: `CLP`, `USD`, `EUR` |
| `monto` | decimal | Monto del aporte o retiro |
| `fechaMovimiento` | date | Fecha del movimiento (ISO 8601) |
| `fechaLiquidacion` | date | Fecha de liquidación (ISO 8601) |
| `obsMovimiento` | string | Observación o comentario opcional |
| `banco` | string | Banco origen/destino (cuando aplica) |
| `numeroCuenta` | string | Número de cuenta bancaria (cuando aplica) |
| `tipoCuenta` | string | Tipo de cuenta bancaria (cuando aplica) |

</Accordion>

### Tipos de movimientos y trazabilidad

El campo `codTipoMovimiento` identifica el tipo de movimiento registrado sobre una cuenta.

**Movimientos no liquidados**

Corresponden a movimientos que **no nacen liquidados** y pueden requerir validación o procesamiento posterior antes de quedar en estado final.

| Tipo de movimiento | Código |
|---|---|
| Aporte patrimonial | `APO_PAT` |
| Retiro patrimonial | `RET_PAT` |

**Movimientos liquidados**

Corresponden a movimientos que **nacen automáticamente en estado liquidado**, por lo que no requieren validación adicional.

En estos casos, el código puede incorporar un identificador adicional de origen para efectos de trazabilidad.

**Formato general:**

```
{TIPO}_{ORIGEN}
```

**Ejemplos genéricos:**

- `APO_PAT_XX`
- `RET_PAT_XX`

Donde `ORIGEN` corresponde a un identificador interno del sistema, canal o integración que genera el movimiento.

**Otros tipos de movimiento**

| Tipo de movimiento | Código |
|---|---|
| Aporte ajuste contable | `APO_AJUST` |
| Retiro ajuste contable | `RET_AJUST` |
| Aporte regalo | `APO_GIFT` |
| Aporte referidos | `APO_REF` |

<Callout icon="💡" theme="info">
  Los identificadores de origen utilizados en movimientos liquidados son de uso interno y no forman parte de la documentación pública de la API. No todos los movimientos utilizan sufijo de trazabilidad, ya que su uso depende de la configuración aplicable en cada caso.
</Callout>

```json title="Request Body (masivo)"
[
  {
    "uuid": "123-123-123",
    "codTipoMovimiento": "APO_PAT",
    "numCuenta": "XXXXXXX/X",
    "obsMovimiento": "Aporte Patrimonial",
    "fechaMovimiento": "2024-06-11",
    "fechaLiquidacion": "2024-06-11",
    "monto": 1000000,
    "codMoneda": "CLP",
    "dscMedioPagoCobro": "TRANSFERENCIA",
    "banco": "Banco Itaú",
    "numeroCuenta": "XXXXXXXX",
    "tipoCuenta": "Cuenta Corriente"
  }
]
```

**Resultado esperado:** el movimiento queda registrado sobre la cuenta con el tipo y trazabilidad correspondiente.

<Accordion title="Catálogo de errores — Aportes/Retiros" icon="fa-duotone fa-circle-exclamation">

| Código | Descripción |
|---|---|
| ARP-001 | No se encontró la cuenta `{numCuenta}` |
| ARP-002 | Falta ingresar `numeroCuenta` de banco |
| ARP-003 | No se encontró caja vigente `{codMoneda}` para la cuenta `{numCuenta}` |
| ARP-004 | Tipo origen mov caja `{codTipoMovimiento}` no existe |
| ARP-005 | Falta ingresar `tipoCuenta` de banco |
| ARP-006 | Falta ingresar `banco` |
| ARP-007 | `fechaMovimiento` o `fechaLiquidacion` no es igual a la fecha actual |
| ARP-008 | Cuando `codMoneda` es CLP el `monto` no puede contener decimales |
| ARP-009 | La fecha operación debe ser igual a la fecha máxima de las operaciones ingresadas |
| ARP-010 | Monto máximo permitido para `RET_PAT_BA`: 7.000.000 |
| ARP-011 | Saldo disponible insuficiente para ejecutar este movimiento |
| ARP-012 | UUID duplicado en la misma transacción |
| ARP-013 | UUID ya utilizado con anterioridad en otro movimiento de caja |
| ARP-014 | La hora actual está fuera del horario permitido |
| ARP-015 | Excepción del sistema |

</Accordion>

<br />

## Retiros vía Shinkansen

**→ POST** `/api/publicapi/creasys/Movimientos/IngresoAporteRetiroMasivo`

Los retiros vía Shinkansen permiten transferencias bancarias automáticas a cuentas del mismo cliente:

- **Montos ≤ 5.000.000 CLP** → automáticos e instantáneos
- **Montos hasta 7.000.000 CLP** → procesamiento aproximado a las 16:00 hrs, requieren firma de apoderado

<Callout icon="🚨" theme="danger">
  Solo se permiten transferencias **a cuentas bancarias del mismo cliente**, nunca a terceros. El monto total no puede superar las 1.000 UF.
</Callout>

<Accordion title="Ver parámetros principales" icon="fa-file-lines">

| Parámetro | Descripción |
|---|---|
| `uuid` | Identificador único de idempotencia |
| `codTipoMovimiento` | Retiro patrimonial banco: `RET_PAT_BA` |
| `numCuenta` | Cuenta del cliente |
| `codMoneda` | Por el momento solo `CLP` |
| `banco` | Banco del cliente |
| `numeroCuenta` | Número de cuenta bancaria del cliente |
| `tipoCuenta` | Tipo de cuenta bancaria |

</Accordion>

```json title="Request Body"
[
  {
    "uuid": "xxx-xxxx-xxxx-xxxx-xxxx-xxxx",
    "codTipoMovimiento": "RET_PAT_BA",
    "numCuenta": "17931004/60",
    "obsMovimiento": "RETIRO PATRIMONIAL SHINKANSEN",
    "fechaMovimiento": "2024-02-06",
    "fechaLiquidacion": "2024-02-06",
    "monto": 1000,
    "codMoneda": "CLP",
    "dscMedioPagoCobro": "TRANSFERENCIA",
    "banco": "Banco BICE",
    "numeroCuenta": "37684701",
    "tipoCuenta": "Cuenta Corriente"
  }
]
```

**Resultado esperado:** el retiro queda ingresado para procesamiento bancario y puedes consultar su estado posteriormente.

<br />

## Aporte/Retiro con cuenta remunerada

**→ POST** `/api/publicapi/creasys/Operaciones/IngresoOperacionCuentaRemunerada`

Ingresa una operación de inversión o rescate sobre una cuenta remunerada. El endpoint genera automáticamente:

1. Un **movimiento de caja**
2. Una **orden de compra/venta del instrumento**
3. El impacto correspondiente en **cartera y caja del cliente**

<Accordion title="Ver parámetros principales" icon="fa-file-lines">

| Parámetro | Descripción |
|---|---|
| `uuid` | Identificador único para la operación (idempotencia) |
| `numCuenta` | Número de cuenta del cliente |
| `codTipoOperacion` | `INVERSION` o `RESCATE` |
| `nemotecnico` | Código bolsa del instrumento |
| `fechaOperacion` | Fecha de la operación (ISO 8601) |
| `monto` | Monto total de la operación |

</Accordion>

```json title="Request Body"
[
  {
    "idOperacion": 0,
    "uuid": "xxx-xxxx-xxxx-xxxx",
    "numCuenta": "12345678/80",
    "codTipoOperacion": "INVERSION",
    "nemotecnico": "VECTOR-A",
    "fechaOperacion": "2024-08-27T20:55:41.260Z",
    "monto": 1000
  }
]
```

**Resultado esperado:** se genera el movimiento de caja y la orden financiera asociada en la cuenta del cliente.

<Accordion title="Catálogo de errores — Cuenta Remunerada" icon="fa-duotone fa-circle-exclamation">

| Código | Descripción |
|---|---|
| OCR-001 | No existe cuenta disponible con `numCuenta` |
| OCR-002 | No se pudo obtener el precio para la operación |
| OCR-003 | No existe operación concepto |
| OCR-004 | No existe instrumento con `nemotecnico` |
| OCR-005 | No se encontró caja vigente para `numCuenta` |
| OCR-006 | El `uuid` ya ha sido procesado previamente |
| OCR-007 | Error en la operación |

</Accordion>

<br />

## Próximos pasos

<Cards columns={2}>
  <Card title="Órdenes de Renta Variable" href="/docs/ordenes-renta-variable" icon="fa-duotone fa-chart-line">Ingresa y anula órdenes de compra/venta de instrumentos.</Card>
  <Card title="Órdenes FX (Spot)" href="/docs/ordenes-fx" icon="fa-duotone fa-money-bill-trend-up">Compra y venta de divisas con liquidación spot.</Card>
  <Card title="Sistema de Eventos" href="/docs/eventos" icon="fa-duotone fa-bell">Recibe notificaciones automáticas cuando se ejecutan movimientos en tiempo real.</Card>
  <Card title="Operaciones internacionales" href="/docs/introduccion-alpaca" icon="fa-duotone fa-globe">¿Necesitas operar en mercados internacionales? Conoce el módulo Alpaca.</Card>
</Cards>
