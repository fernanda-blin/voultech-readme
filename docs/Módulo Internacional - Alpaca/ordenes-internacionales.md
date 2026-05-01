---
title: Órdenes Internacionales
excerpt: >-
  Envía órdenes de compra y venta de acciones internacionales a través de
  Alpaca.
deprecated: false
hidden: false
metadata:
  robots: index
---
Envía órdenes de compra/venta para activos internacionales (ALPACA) usando el mismo endpoint que las órdenes nacionales, cambiando solo `codBolsa` y algunos parámetros.

<Callout icon="⚠️" theme="warning">
  La ejecución **no es inmediata**. La confirmación llega de forma **asíncrona vía Service Bus**.
</Callout>

## Ingresar orden

**→ POST** `/api/publicapi/creasys/Ordenes/IngresarOrdenesMercado`

Ingresa órdenes para cuentas **nacionales (XSGO)** o **internacionales (ALPACA)** en el mismo endpoint. Solo cambia `codBolsa` y algunos parámetros específicos.

<Accordion title="Ver parámetros" icon="fa-file-lines">

| Parámetro | Obligatorio | Descripción |
|---|---|---|
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

</Accordion>

<Tabs>
  <Tab title="Orden LIMIT">

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

  </Tab>
  <Tab title="Orden MARKET">

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

  </Tab>
</Tabs>

<Callout icon="📌" theme="info">
  En `MARKET` no se envía `precio`. La orden se ejecuta al mejor precio disponible.
</Callout>

```json title="Respuesta (200 OK)"
[
  {
    "uuid": "550e8400-e29b-41d4-a716-446655440021",
    "mensaje": "Orden internacional procesada correctamente.",
    "exitoso": true,
    "uuidBolsa": null
  }
]
```

<Callout icon="⚠️" theme="warning">
  La respuesta exitosa significa que la orden fue **aceptada** para enviarse a Alpaca, **no que ya se ejecutó**. La confirmación de ejecución llega vía Service Bus de forma asíncrona.
</Callout>

<br />

## Diferencias clave: nacional vs. internacional

| Campo | Nacional (XSGO) | Internacional (ALPACA) |
|---|---|---|
| `codBolsa` | `XSGO` | `ALPACA` |
| `tipoSeguridad` | varía según instrumento | `CS` |
| `tipoLiquidacion` | varía | `T2` |
| Horario | bolsa local | mercado USA (lun-vie 14:30–21:00 UTC) |
| Confirmación | Service Bus | Service Bus |

<br />

## Flujo recomendado

1. Verificá que el mercado esté abierto con `GET /ClockAlpaca`
2. Buscá el ticker disponible con `GET /Asset?search=...` o `GET /Asset?nemo=...`
3. Consultá la cotización actual con `GET /Asset/LastQuote?nemo=...`
4. Generá un `uuid` único (idempotencia)
5. Enviá la orden con `POST /Ordenes/IngresarOrdenesMercado`
6. Esperá la confirmación de ejecución vía Service Bus
7. Consultá la posición resultante con `GET /CuentaAlpaca/Custodias/{accountNumber}`

<br />

## Errores comunes

| Código | Descripción |
|---|---|
| `401 Unauthorized` | Token expiró |
| `403 Forbidden` | Sin acceso a la cuenta |
| `500 Server Error` | Fallo al enviar a Alpaca |
