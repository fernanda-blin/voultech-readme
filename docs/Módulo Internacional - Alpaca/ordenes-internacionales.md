---
title: "Órdenes Internacionales"
excerpt: "Envía órdenes de compra y venta de acciones internacionales a través de Alpaca."
---

Envía órdenes de compra/venta para activos internacionales (ALPACA) usando el mismo endpoint que las órdenes nacionales, cambiando solo `codBolsa` y algunos parámetros.

## Qué cubre esta página

- Cómo enviar una orden de compra o venta a Alpaca
- Diferencias entre órdenes `MARKET` y `LIMIT`
- Cómo manejar la confirmación asíncrona

---

## Ingresar orden

`POST /api/publicapi/creasys/Ordenes/IngresarOrdenesMercado`

Ingresa órdenes para cuentas **nacionales (XSGO)** o **internacionales (ALPACA)** en el mismo endpoint. Solo cambia `codBolsa` y algunos parámetros específicos.

> ⚠️ La ejecución **no es inmediata**. La confirmación llega de forma **asíncrona vía Service Bus**.

### Parámetros

| Parámetro | Obligatorio | Descripción |
|-----------|-------------|-------------|
| `uuid` | **Sí** | Identificador único de la orden (idempotencia) |
| `numCuenta` | **Sí** | Número de cuenta del cliente (`xxxxx/x`) |
| `nemotecnico` | **Sí** | Símbolo o ticker del activo |
| `tipoSeguridad` | **Sí** | Para ALPACA usar `"CS"` (Common Stock) |
| `tipoOperacion` | **Sí** | `C` = Compra, `V` = Venta |
| `cantidad` | **Sí** | Cantidad de títulos (mayor a 0) |
| `precio` | Condicional | Requerido si `tipoPrecio = LIMIT`, debe ser > 0 |
| `tipoPrecio` | **Sí** | `MARKET` o `LIMIT`. Con `MARKET` no enviar `precio` |
| `codBolsa` | **Sí** | `XSGO` para nacional, `ALPACA` para internacional |
| `tipoLiquidacion` | **Sí** | Para ALPACA usar `"T2"` |

### Request — orden LIMIT internacional

```json
[
  {
    "uuid": "550e8400-e29b-41d4-a716-446655440021",
    "numCuenta": "18784154/0",
    "nemotecnico": "AMZN",
    "cantidad": 1,
    "precio": 180,
    "tipoPrecio": "LIMIT",
    "tipoOperacion": "C",
    "tipoSeguridad": "CS",
    "tipoLiquidacion": "T2",
    "codBolsa": "ALPACA"
  }
]
```

### Request — orden MARKET internacional

```json
[
  {
    "uuid": "550e8400-e29b-41d4-a716-446655440022",
    "numCuenta": "18784154/0",
    "nemotecnico": "AAPL",
    "cantidad": 2,
    "tipoPrecio": "MARKET",
    "tipoOperacion": "C",
    "tipoSeguridad": "CS",
    "tipoLiquidacion": "T2",
    "codBolsa": "ALPACA"
  }
]
```

> 📌 En `MARKET` no se envía `precio`. La orden se ejecuta al mejor precio disponible.

### Respuesta (200 OK)

```json
[
  {
    "uuid": "550e8400-e29b-41d4-a716-446655440021",
    "mensaje": "Orden internacional procesada correctamente.",
    "exitoso": true,
    "uuidBolsa": null
  }
]
```

> ⚠️ La respuesta exitosa significa que la orden fue **aceptada** para enviarse a Alpaca, **no que ya se ejecutó**. La confirmación de ejecución llega vía Service Bus de forma asíncrona.

---

## Diferencias clave: nacional vs. internacional

| Campo | Nacional (XSGO) | Internacional (ALPACA) |
|-------|-----------------|------------------------|
| `codBolsa` | `XSGO` | `ALPACA` |
| `tipoSeguridad` | varía según instrumento | `CS` |
| `tipoLiquidacion` | varía | `T2` |
| Horario | bolsa local | mercado USA (lun-vie 14:30–21:00 UTC) |
| Confirmación | Service Bus | Service Bus |

## Flujo recomendado

1. Verifica que el mercado esté abierto con `GET /ClockAlpaca`
2. Busca el ticker disponible con `GET /Asset?search=...` o `GET /Asset?nemo=...`
3. Consulta la cotización actual con `GET /Asset/LastQuote?nemo=...`
4. Genera un `uuid` único (idempotencia)
5. Envía la orden con `POST /Ordenes/IngresarOrdenesMercado`
6. Espera la confirmación de ejecución vía Service Bus
7. Consulta la posición resultante con `GET /CuentaAlpaca/Custodias/{accountNumber}`

## Errores comunes

- `401 Unauthorized` — token expiró
- `403 Forbidden` — sin acceso a la cuenta
- `500 Server Error` — fallo al enviar a Alpaca

## Siguiente paso

Continúa con **[Actividad y Custodias](/docs/actividad-y-custodias)** para revisar tus posiciones y movimientos de cuenta.
