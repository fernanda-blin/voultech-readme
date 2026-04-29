---
title: "Actividad y Custodias"
excerpt: "Consulta la actividad operativa y posiciones vigentes de una cuenta Alpaca."
---

Consulta la actividad operativa y las posiciones vigentes de una cuenta Alpaca para monitorear ejecuciones, cargos y valorización de instrumentos.

## Qué cubre esta página

- **Actividad de cuenta**: revisar eventos y cargos registrados (FILL, JNLC, DIV, FEE, etc.)
- **Custodias**: consultar las posiciones vigentes y su valorización

---

## Consultar actividad de cuenta

`GET /api/publicapi/creasys/CuentaAlpaca/ObtenerActividadCuenta`

Obtiene las actividades de una cuenta Alpaca. Permite filtrar por rango de fechas, tipo de actividad y categoría.

### Parámetros

| Parámetro | Tipo | Obligatorio | Descripción |
|-----------|------|-------------|-------------|
| `NumCuenta` | string | **Sí** | Número de cuenta Voultech (ej: `"18784154/0"`) |
| `activity_types` | array[string] | No | Tipos de actividad: `FILL`, `JNLC`, `DIV`, `OPCSH`, `FEE`, etc. |
| `category` | string | No | `trade_activity` o `non_trade_activity` |
| `date` | date | No | Fecha específica (YYYY-MM-DD) |
| `after` | date | No | Fecha mínima del rango (YYYY-MM-DD) |
| `until` | date | No | Fecha máxima del rango (YYYY-MM-DD) |
| `direction` | string | No | `asc` o `desc` (default: `desc`) |
| `page_size` | int | No | 1–100 (default: `100`) |
| `page_token` | string | No | Token de paginación de Alpaca |

### Tipos de actividad más comunes

| Tipo | Descripción |
|------|-------------|
| `FILL` | Ejecución de orden (compra/venta) |
| `JNLC` | Journal de cash (movimiento de efectivo) |
| `DIV` | Dividendo recibido |
| `FEE` | Comisión / cargo |
| `OPCSH` | Cash de opening |

### Respuesta (200 OK)

```json
[
  {
    "id": "202512010000000000::ff02076a-c012-4ebf-80b7-5665b9f17dd2",
    "account_id": "5db213eb-0765-4364-8115-a65c7695038d",
    "activity_type": "JNLC",
    "status": "executed",
    "date": "2025-12-01T00:00:00",
    "created_at": "2025-12-01T18:20:24.785Z",
    "net_amount": -5,
    "symbol": null,
    "qty": 0,
    "price": 0
  },
  {
    "id": "20250804000000000::18a9b145-e3d7-4715-a83d-ee69a73d6d6f",
    "account_id": "5d2b13eb-0765-4364-8115-a65c7695303d",
    "activity_type": "FEE",
    "description": "TAF fee for proceed of 2 shares",
    "status": "executed",
    "date": "2025-08-04T00:00:00",
    "net_amount": -0.01,
    "symbol": null,
    "qty": 0,
    "price": 0
  }
]
```

### Campos destacados

| Campo | Descripción |
|-------|-------------|
| `activity_type` | Tipo de actividad registrada |
| `status` | Estado de la actividad |
| `date` | Fecha asociada a la actividad |
| `created_at` | Fecha y hora de creación del registro |
| `net_amount` | Monto neto asociado |
| `symbol` | Instrumento asociado, si aplica |
| `qty` | Cantidad asociada, si aplica |
| `price` | Precio asociado, si aplica |

---

## Consultar custodias

`GET /api/publicapi/creasys/CuentaAlpaca/Custodias/{accountNumber}`

Obtiene las posiciones (custodias) actuales de una cuenta Alpaca con su valorización.

| Parámetro | Descripción | Obligatorio |
|-----------|-------------|-------------|
| `accountNumber` | Número de cuenta Voultech asociada con Alpaca | Sí |

### Respuesta (200 OK)

```json
[
  {
    "asset_id": "b0b6dd9d-8b9b-48a9-ba46-b9d54906e415",
    "symbol": "AAPL",
    "exchange": "NASDAQ",
    "asset_class": "us_equity",
    "qty": 1,
    "avg_entry_price": 209.53,
    "side": "long",
    "market_value": 214.69,
    "cost_basis": 209.53,
    "unrealized_pl": 5.16,
    "unrealized_plpc": 0.0246,
    "current_price": 214.69,
    "lastday_price": 202.92,
    "qty_available": 1,
    "assetName": "Apple Inc. Common Stock",
    "tradable": true,
    "marginable": true
  }
]
```

### Campos destacados

| Campo | Descripción |
|-------|-------------|
| `qty` | Cantidad de acciones en la posición |
| `avg_entry_price` | Precio promedio de entrada |
| `market_value` | Valor de mercado actual |
| `cost_basis` | Costo total de la posición |
| `unrealized_pl` | Ganancia/pérdida no realizada |
| `unrealized_plpc` | Ganancia/pérdida no realizada como % |
| `current_price` | Precio actual del activo |
| `qty_available` | Cantidad disponible para vender |

## Siguiente paso

Continúa con **[Movimientos Internacionales](/docs/movimientos-internacionales)** para registrar aportes y retiros entre tu cuenta local y Alpaca.
