---
title: Introducción a Alpaca
excerpt: >-
  Visión general del módulo internacional para operar acciones en EE.UU. a
  través de Alpaca Markets.
deprecated: false
hidden: false
metadata:
  robots: index
---
El **módulo internacional** de Voultech permite a tus clientes operar acciones en mercados de EE.UU. a través de **Alpaca Markets**, manteniendo la cuenta local en Voultech como punto de control.

<Callout icon="💡" theme="info">
  En este módulo conviven dos identificadores: `numCuenta` (cuenta local en GPI) y `accountNumber` (cuenta en Alpaca).
</Callout>

## Qué cubre el módulo

<Cards columns={3}>
  <Card title="Cuentas internacionales" href="/docs/cuentas-internacionales" icon="fa-id-card">
    Crear, consultar y gestionar cuentas Alpaca asociadas a cuentas locales.
  </Card>
  <Card title="Assets e instrumentos" href="/docs/assets-e-instrumentos-disponibles" icon="fa-magnifying-glass-chart">
    Buscar instrumentos, cotizaciones, market data y horario de mercado.
  </Card>
  <Card title="Órdenes internacionales" href="/docs/ordenes-internacionales" icon="fa-chart-line">
    Enviar órdenes de compra/venta usando `codBolsa = ALPACA`.
  </Card>
  <Card title="Actividad y custodias" href="/docs/actividad-y-custodias" icon="fa-list-check">
    Consultar movimientos históricos y posiciones vigentes.
  </Card>
  <Card title="Movimientos internacionales" href="/docs/movimientos-internacionales" icon="fa-money-bill-transfer">
    Registrar aportes y retiros patrimoniales entre cuenta local y Alpaca.
  </Card>
</Cards>

<br />

## Flujo de integración recomendado

```
1. Crear cuenta Alpaca       → POST /CuentaAlpaca/CrearClienteAlpaca
2. Aportar fondos            → POST /MovimientosAlpaca/MovimientoInternacionalAlpaca (APO_PAT_IT)
3. Buscar instrumentos       → GET  /Asset?search=...
4. Consultar cotización      → GET  /Asset/LastQuote?nemo=AAPL
5. Enviar orden              → POST /Ordenes/IngresarOrdenesMercado (codBolsa: ALPACA)
6. Consultar custodias       → GET  /CuentaAlpaca/Custodias/{accountNumber}
7. Revisar saldo             → GET  /CuentaAlpaca/SaldoAlpaca/{accountNumber}
```

<br />

## Conceptos clave

### Identificadores

| Campo | Descripción |
|---|---|
| `numCuenta` | Cuenta local Voultech (formato `xxxxx/x`, ej: `19130340/0`) |
| `accountNumber` | Cuenta en Alpaca (string numérico) |
| `identificador` | RUT del cliente con dígito verificador (ej: `12345678-9`) |

### Bolsas y horario

- Mercado americano abierto de **lunes a viernes 14:30–21:00 UTC** (ajustado por DST)
- Consultá el estado en tiempo real con `GET /ClockAlpaca`
- Las órdenes ingresadas con mercado cerrado quedan **pendientes** hasta la apertura

<Callout icon="⚠️" theme="warning">
  Las confirmaciones de ejecución de órdenes y movimientos llegan de forma **asíncrona vía Service Bus**, no en la respuesta HTTP del POST.
</Callout>

### Tipo de orden por bolsa

| Campo | Nacional (XSGO) | Internacional (ALPACA) |
|---|---|---|
| `codBolsa` | `XSGO` | `ALPACA` |
| `tipoSeguridad` | varía | `CS` (common stock) |
| `tipoLiquidacion` | varía | `T2` |

<br />

## Próximos pasos

- Comenzá con **[Cuentas Internacionales](/docs/cuentas-internacionales)** para crear tu primera cuenta Alpaca
- Revisá **[Assets e Instrumentos](/docs/assets-e-instrumentos-disponibles)** para encontrar tickers disponibles
- Continuá con **[Órdenes Internacionales](/docs/ordenes-internacionales)** para enviar tu primera orden
