---
title: "Introducción a Alpaca"
excerpt: "Visión general del módulo internacional para operar acciones en EE.UU. a través de Alpaca Markets."
---

El **módulo internacional** de Voultech permite a tus clientes operar acciones en mercados de EE.UU. a través de **Alpaca Markets**, manteniendo la cuenta local en Voultech como punto de control.

> 💡 En este módulo conviven dos identificadores: `numCuenta` (cuenta local en GPI) y `accountNumber` (cuenta en Alpaca).

## Qué cubre el módulo

| Sección | Para qué sirve |
|---------|----------------|
| **Cuentas Internacionales** | Crear, consultar y gestionar cuentas Alpaca asociadas a cuentas locales |
| **Assets e Instrumentos** | Buscar instrumentos disponibles, cotizaciones, market data y horario de mercado |
| **Órdenes Internacionales** | Enviar órdenes de compra/venta usando `codBolsa = "ALPACA"` |
| **Actividad y Custodias** | Consultar movimientos históricos y posiciones vigentes |
| **Movimientos Internacionales** | Registrar aportes y retiros patrimoniales entre cuenta local y Alpaca |

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

## Conceptos clave

### Identificadores
- **`numCuenta`**: cuenta local Voultech (formato `xxxxx/x`, ej: `19130340/0`)
- **`accountNumber`**: cuenta en Alpaca (string numérico)
- **`identificador`**: RUT del cliente con dígito verificador (ej: `12345678-9`)

### Bolsas y horario
- Mercado americano abierto de **lunes a viernes 14:30–21:00 UTC** (ajustado por DST)
- Consulta el estado en tiempo real con `GET /ClockAlpaca`
- Las órdenes ingresadas con mercado cerrado quedan **pendientes** hasta la apertura

### Confirmación asíncrona
> ⚠️ Las confirmaciones de ejecución de órdenes y movimientos llegan de forma **asíncrona vía Service Bus**, no en la respuesta HTTP del POST.

### Tipo de orden por bolsa
| Campo | Nacional (XSGO) | Internacional (ALPACA) |
|-------|-----------------|------------------------|
| `codBolsa` | `XSGO` | `ALPACA` |
| `tipoSeguridad` | varía | `CS` (common stock) |
| `tipoLiquidacion` | varía | `T2` |

## Próximos pasos

- Comienza con **[Cuentas Internacionales](/docs/cuentas-internacionales)** para crear tu primera cuenta Alpaca
- Revisa **[Assets e Instrumentos](/docs/assets-e-instrumentos-disponibles)** para encontrar tickers disponibles
- Continúa con **[Órdenes Internacionales](/docs/ordenes-internacionales)** para enviar tu primera orden
