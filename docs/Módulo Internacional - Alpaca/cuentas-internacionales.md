---
title: "Cuentas Internacionales"
excerpt: "Crea cuentas Alpaca, consulta su detalle, lista las cuentas del asesor y revisa saldos."
---

Crea y gestiona cuentas Alpaca para que tus clientes operen en mercados internacionales.

## Qué cubre esta página

- crear una cuenta Alpaca asociada a una cuenta local
- consultar el detalle de una cuenta internacional
- listar las cuentas Alpaca del asesor autenticado
- revisar el saldo disponible de una cuenta

> 💡 En este módulo conviven dos identificadores: `numCuenta` (cuenta local en GPI) y `accountNumber` (cuenta en Alpaca).

---

## Crear cuenta Alpaca

`POST /api/publicapi/creasys/CuentaAlpaca/CrearClienteAlpaca`

Crea una cuenta Alpaca para el cliente especificado usando el asesor autenticado.

### Parámetros

| Parámetro | Tipo | Obligatorio | Descripción |
|-----------|------|-------------|-------------|
| `numCuenta` | string | **Sí** | Número de cuenta Voultech con la que se quiere operar |
| `identificador` | string | **Sí** | RUT del cliente con validación |
| `fundingSource` | array[string] | **Sí** | Origen de los fondos del cliente |

### Valores válidos para `fundingSource`

| Valor | Descripción |
|-------|-------------|
| `employment_income` | Ingresos por empleo |
| `savings` | Ahorros personales |
| `investments` | Inversiones |
| `inheritance` | Herencia |
| `business_income` | Ingresos por negocios |
| `family` | Familia |

### Request

```json
{
  "numCuenta": "19130340/0",
  "identificador": "19130340-7",
  "fundingSource": ["employment_income"]
}
```

### Respuesta (200 OK)

```json
{
  "id": "b1fd6038-6b34-4fdd-8164-1c2162f678e7",
  "accountNumber": "901988645",
  "status": "SUBMITTED",
  "currency": "USD",
  "createdAt": "2025-08-04T16:03:34.43686Z",
  "lastEquity": "0",
  "cryptoStatus": "INACTIVE",
  "identity": {
    "givenName": "SERGIO",
    "familyName": "CONSTERLA DIAZ",
    "dateOfBirth": "1995-09-03",
    "taxIdType": "CHL_RUT",
    "countryOfTaxResidence": "CHL",
    "fundingSource": ["employment_income"]
  },
  "enabledAssets": ["us_equity"]
}
```

> 📘 La respuesta entrega el `accountNumber` de Alpaca, que necesitarás para consultar saldo, custodias, actividad y órdenes.

---

## Obtener una cuenta Alpaca

`GET /api/publicapi/creasys/CuentaAlpaca/ObtenerCuentaAlpaca/{accountNumber}`

Obtiene el detalle de una cuenta Alpaca específica.

| Parámetro | Descripción | Obligatorio |
|-----------|-------------|-------------|
| `accountNumber` | Número de cuenta Alpaca (URI encoded) | Sí |

### Respuesta (200 OK)

```json
{
  "account_id": "6f1d3a2b-3c4d-4b8f-9a1e-112233445566",
  "account_number": "19837710/0",
  "status": "ACTIVE",
  "currency": "USD",
  "cash": "2500.00",
  "portfolio_value": "2575.25",
  "pattern_day_trader": false,
  "trading_blocked": false,
  "idCliente": 14521,
  "numCuenta": "12345678"
}
```

### Campos destacados

| Campo | Descripción |
|-------|-------------|
| `status` | Estado actual de la cuenta Alpaca |
| `currency` | Moneda de operación de la cuenta |
| `cash` | Efectivo disponible en la cuenta |
| `portfolio_value` | Valor total del portafolio |
| `pattern_day_trader` | Indica si la cuenta está marcada como day trader |
| `trading_blocked` | Indica si el trading está bloqueado |
| `numCuenta` | Relación con la cuenta local en GPI |

---

## Obtener todas las cuentas Alpaca

`GET /api/publicapi/creasys/CuentaAlpaca/ObtenerCuentas`

Obtiene todas las cuentas Alpaca asociadas al asesor autenticado.

### Respuesta (200 OK)

```json
[
  {
    "account_id": "6f1d3a2b-3c4d-4b8f-9a1e-112233445566",
    "account_number": "19837710/0",
    "status": "ACTIVE",
    "currency": "USD"
  },
  {
    "account_id": "4a2f9b10-8c39-4b8e-8b2e-778899001122",
    "account_number": "19837710/1",
    "status": "SUBMITTED",
    "currency": "USD"
  }
]
```

---

## Obtener saldo de una cuenta Alpaca

`GET /api/publicapi/creasys/CuentaAlpaca/SaldoAlpaca/{accountNumber}`

Obtiene el saldo completo de una cuenta Alpaca: equity, cash, buying power y valor del portafolio.

### Respuesta (200 OK)

```json
{
  "account_number": "870221079",
  "status": "ACTIVE",
  "currency": "USD",
  "equity": 2129.75,
  "last_equity": 2135.66,
  "cash": 113.09,
  "buying_power": 113.09,
  "portfolio_value": 2129.75,
  "long_market_value": 2016.66,
  "pattern_day_trader": false,
  "daytrade_count": 1
}
```

### Campos destacados

| Campo | Descripción |
|-------|-------------|
| `equity` | Patrimonio total actualizado (posiciones + cash) |
| `last_equity` | Equity del cierre del día hábil anterior |
| `cash` | Efectivo disponible |
| `buying_power` | Poder de compra disponible |
| `portfolio_value` | Valor total del portafolio |
| `long_market_value` | Valor de mercado de posiciones largas |
| `pattern_day_trader` | Indica si la cuenta está marcada como day trader |
| `daytrade_count` | Cantidad de operaciones day trade en los últimos 5 días |

> ⚠️ **No existe WebSocket para el equity total.** Implementar **polling periódico** a este endpoint.
> Para saldo de caja (`cash`) sí está disponible WebSocket.

---

## Flujo recomendado

1. Crea la cuenta con `POST /CuentaAlpaca/CrearClienteAlpaca` incluyendo `fundingSource`
2. Guarda el `accountNumber` retornado
3. Consulta el detalle con `GET /CuentaAlpaca/ObtenerCuentaAlpaca/{accountNumber}`
4. Verifica el saldo con `GET /CuentaAlpaca/SaldoAlpaca/{accountNumber}`
5. Usa ese `accountNumber` en las páginas de actividad, custodias y órdenes

## Siguiente paso

Continúa con **[Assets e Instrumentos](/docs/assets-e-instrumentos-disponibles)** para buscar símbolos disponibles, o con **[Órdenes Internacionales](/docs/ordenes-internacionales)** para enviar tu primera orden.
