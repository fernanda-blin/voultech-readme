---
title: Movimientos Internacionales
excerpt: >-
  Registra y consulta aportes y retiros patrimoniales entre cuentas locales y
  cuentas Alpaca.
deprecated: false
hidden: false
metadata:
  robots: index
---
Registra y consulta movimientos patrimoniales internacionales entre cuentas locales y cuentas Alpaca.

## Operaciones disponibles

<Cards columns={2}>
  <Card title="Registrar movimiento" href="#registrar-movimiento-internacional" icon="fa-money-bill-transfer">
    Aporte o retiro patrimonial entre cuenta local y Alpaca.
  </Card>
  <Card title="Consultar movimientos" href="#consultar-movimientos-patrimoniales-por-cuenta" icon="fa-list">
    Listado por cuenta con filtros opcionales.
  </Card>
</Cards>

<br />

## Registrar movimiento internacional

**→ POST** `/api/publicapi/creasys/MovimientosAlpaca/MovimientoInternacionalAlpaca`

Registra un movimiento internacional de tipo **retiro** o **aporte patrimonial** entre cuentas locales y cuentas Alpaca.

<Accordion title="Tipos de movimiento" icon="fa-tag">

| Código | Descripción |
|---|---|
| `APO_PAT_IT` | Aporte patrimonial (Voultech → Alpaca) |
| `RET_PAT_IT` | Retiro patrimonial (Alpaca → Voultech) |

</Accordion>

<Accordion title="Ver parámetros" icon="fa-file-lines">

| Parámetro | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `codTipoMovimiento` | string | **Sí** | `APO_PAT_IT` o `RET_PAT_IT` |
| `numCuenta` | string | **Sí** | Número de cuenta (máx. 15 caracteres) |
| `monto` | decimal | **Sí** | Monto del movimiento (> 0) |
| `codMoneda` | string | **Sí** | Código de moneda (máx. 3 caracteres, ej: `USD`) |
| `obsMovimiento` | string | No | Observaciones (máx. 100 caracteres) |
| `dscMedioPagoCobro` | string | No | Medio de pago/cobro (máx. 20 caracteres) |
| `id` | int | No | ID asociado |
| `uuid` | string | No | UUID para trazabilidad e idempotencia |

</Accordion>

```json title="Request Body"
{
  "codMoneda": "USD",
  "monto": 5,
  "numCuenta": "18784154/0",
  "id": 864857,
  "codTipoMovimiento": "RET_PAT_IT",
  "obsMovimiento": "Retiro a caja USD",
  "dscMedioPagoCobro": "TRANSFERENCIA"
}
```

```json title="Respuesta (200 OK)"
{
  "id": 14380256,
  "codTipoMovimiento": "RET_PAT_IT",
  "numCuenta": "18784154/0",
  "fechaMovimiento": "2025-12-01T00:00:00-03:00",
  "monto": 5,
  "codMoneda": "USD",
  "uuidJournal": "85de80c1-5389-4e0e-a9a9-3b5cab378121"
}
```

<Callout icon="💡" theme="info">
  Usá `uuid` para mantener trazabilidad e idempotencia sobre cada movimiento internacional registrado.
</Callout>

<br />

## Consultar movimientos patrimoniales por cuenta

**→ GET** `/api/publicapi/creasys/MovimientosAlpaca/patrimoniales/{numCuenta}`

Listado de movimientos patrimoniales para una cuenta específica, con filtros opcionales.

<Accordion title="Ver parámetros" icon="fa-file-lines">

| Parámetro | Tipo | Descripción |
|---|---|---|
| `numCuenta` (path) | string | Número de cuenta Voultech |
| `idMovimiento` | int | ID específico del movimiento |
| `fechaDesde` | date | Fecha inicial del rango (YYYY-MM-DD) |
| `fechaHasta` | date | Fecha final del rango (YYYY-MM-DD) |
| `CuentaOrigen` | string | UUID de cuenta origen en Alpaca |
| `CuentaDestino` | string | UUID de cuenta destino en Alpaca |
| `estadoActual` | string | Estado del journal: `executed`, `pending`, `canceled` |

</Accordion>

```json title="Respuesta (200 OK)"
[
  {
    "idMovimiento": 14380227,
    "fechaMovimiento": "2025-12-01T00:00:00",
    "codOrigen": "RET_PAT_IT",
    "monto": 5.0,
    "descripcion": "RETIRO PATRIMONIAL ALPACA",
    "entryType": "JNLC",
    "cuentaOrigen": "57c57391-426b-3474-99d7-d94830d0447e",
    "cuentaDestino": "5d2b13eb-0765-4364-8115-a65c7695303d",
    "journalAmount": 5.0,
    "estadoActual": "executed",
    "numeroCuenta": "18784154/0"
  }
]
```

<Accordion title="Campos destacados" icon="fa-list">

| Campo | Descripción |
|---|---|
| `idMovimiento` | ID del movimiento |
| `codOrigen` | Tipo de movimiento (`APO_PAT_IT`, `RET_PAT_IT`) |
| `entryType` | Tipo de entry de Alpaca (ej: `JNLC`) |
| `cuentaOrigen` | UUID de cuenta Alpaca origen |
| `cuentaDestino` | UUID de cuenta Alpaca destino |
| `estadoActual` | `executed`, `pending`, `canceled` |

</Accordion>

<br />

## Flujo recomendado

### Aporte (Voultech → Alpaca)

1. El cliente deposita CLP/USD en su cuenta Voultech
2. Registrá el aporte con `POST /MovimientosAlpaca/MovimientoInternacionalAlpaca` con `codTipoMovimiento: APO_PAT_IT`
3. Guardá el `uuid` y `id` retornados
4. Esperá confirmación asíncrona

### Retiro (Alpaca → Voultech)

1. El cliente solicita un retiro
2. Registrá el retiro con `codTipoMovimiento: RET_PAT_IT`
3. Verificá el estado consultando `GET /MovimientosAlpaca/patrimoniales/{numCuenta}` con `estadoActual=executed`

<br />

## Relación con otros componentes

- Usá **[Cuentas Internacionales](/docs/cuentas-internacionales)** para validar el saldo antes de procesar un retiro
- Consultá **[Actividad y Custodias](/docs/actividad-y-custodias)** para ver el detalle del JNLC asociado
