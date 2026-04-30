---
title: "Assets e Instrumentos Disponibles"
excerpt: "Busca instrumentos, consulta cotizaciones, market data histórica y horario de mercado."
---

Busca instrumentos disponibles para operar en bolsa internacional, consulta cotizaciones en tiempo real, market data histórica y el estado del mercado.

## Qué cubre esta página

- Buscar instrumentos por símbolo, nombre o bolsa
- Consultar la última cotización de un activo
- Saber si el mercado americano está abierto
- Consultar market data histórica (barras, cotizaciones, trades, snapshots)
- Obtener el logo de un activo

> 💡 Estos endpoints son la base para construir buscadores, gráficos de precios y feeds de cotizaciones antes de enviar órdenes con `codBolsa = "ALPACA"`.

---

## Buscar assets

`GET /api/publicapi/creasys/Asset`

Servicio para obtener los instrumentos disponibles para operar en bolsa internacional, con opciones de búsqueda y filtrado.

### Parámetros

| Parámetro | Tipo | Obligatorio | Descripción |
|-----------|------|-------------|-------------|
| `exchangeName` | string | No | Nombre del mercado o bolsa (`NASDAQ`, `NYSE`). Si se omite, devuelve instrumentos de todos los mercados |
| `search` | string | No | Búsqueda amplia por símbolo o nombre. Ej: `Apple` |
| `nemo` | string | No | Búsqueda exacta por símbolo. Ej: `AAPL` |
| `id_correlativo` | int | No | Identificador correlativo interno |
| `page` | int | No | Número de página (default: `1`) |
| `pageSize` | int | No | Cantidad de registros por página (default: `20`) |

### Respuesta (200 OK)

```json
{
  "items": [
    {
      "asset_id": "b0b6dd9d-8b9b-48a9-ba46-b9d54906e415",
      "symbol": "AAPL",
      "exchange": "NASDAQ",
      "asset_class": "us_equity",
      "name": "Apple Inc. Common Stock",
      "tradable": true,
      "marginable": true
    }
  ],
  "totalCount": 250
}
```

---

## Última cotización de un activo

`GET /api/publicapi/creasys/Asset/LastQuote`

Obtiene la última cotización disponible para un activo (precio compra/venta, tamaños, timestamp).

| Parámetro | Tipo | Obligatorio | Descripción |
|-----------|------|-------------|-------------|
| `nemo` | string | **Sí** | Símbolo del activo (ej: `AAPL`, `TSLA`) |
| `feed` | string | No | Fuente de datos (default: `sip`) |

### Tipos de feed disponibles

| Valor | Descripción |
|-------|-------------|
| `sip` | Securities Information Processor — datos consolidados en tiempo real (**default**) |
| `iex` | Investors Exchange — datos exclusivos de IEX |
| `delayed_sip` | SIP con delay de 15 minutos |
| `boats` | Blue Ocean ATS — operaciones fuera de horario regular |
| `overnight` | Cotizaciones derivadas para operaciones overnight |
| `otc` | Over-The-Counter — mercados OTC |

### Respuesta (200 OK)

```json
{
  "symbol": "AMZN",
  "lastPrice": 233,
  "bidPrice": 232.95,
  "askPrice": 233,
  "bidSize": 700,
  "askSize": 200,
  "timestamp": "2025-11-28T18:45:39.437Z",
  "feed": "delayed_sip",
  "currency": "USD"
}
```

---

## Estado del mercado

`GET /api/publicapi/creasys/ClockAlpaca`

Obtiene el estado actual del reloj de mercado de Alpaca: si está abierto, próxima apertura y próximo cierre.

### Respuesta (200 OK)

```json
{
  "timestamp": "2025-11-28T19:11:33.788Z",
  "is_open": true,
  "next_open": "2025-11-29T14:30:00Z",
  "next_close": "2025-11-28T21:00:00Z"
}
```

> 💡 Usa este endpoint antes de enviar órdenes para validar que el mercado esté operativo.

---

## Logo de un activo

`GET /api/publicapi/creasys/Asset/LogoParquet/{nemotecnico}`

Obtiene el logo (imagen) de un activo financiero por su nemotécnico. Útil para mostrar logos en UI.

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| `nemotecnico` (path) | string | Símbolo del activo (ej: `AAPL`) |

---

# Market Data — Stocks

Endpoints proxy al micro de Alpaca para consultar market data histórica y en tiempo real. **Todos requieren autenticación Bearer.**

## Resumen de endpoints disponibles

| Endpoint | Descripción |
|----------|-------------|
| `GET /Asset/stocks/bars` | Barras OHLCV para varios símbolos |
| `GET /Asset/stocks/bars/latest` | Última barra por símbolo (batch) |
| `GET /Asset/stocks/quotes` | Cotizaciones históricas para varios símbolos |
| `GET /Asset/stocks/quotes/latest` | Última cotización por símbolo (batch) |
| `GET /Asset/stocks/snapshots` | Snapshot actual por símbolo (batch) |
| `GET /Asset/stocks/trades` | Trades históricos para varios símbolos |
| `GET /Asset/stocks/trades/latest` | Último trade por símbolo (batch) |
| `GET /Asset/stocks/auctions` | Subastas para uno o varios símbolos |
| `GET /Asset/stocks/meta/conditions/{tickType}` | Condiciones de tick por tipo |
| `GET /Asset/stocks/meta/exchanges` | Listado de exchanges disponibles |
| `GET /Asset/stocks/{symbol}/bars` | Barras OHLCV para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/bars/latest` | Última barra para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/quotes` | Cotizaciones históricas para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/quotes/latest` | Última cotización para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/trades` | Trades históricos para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/trades/latest` | Último trade para un símbolo en ruta |
| `GET /Asset/stocks/{symbol}/auctions` | Subastas para un símbolo en ruta |

## Parámetros comunes de Market Data

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| `symbols` | string | Tickers separados por coma. Tiene prioridad sobre `nemo` |
| `nemo` | string | Un solo ticker si no se envía `symbols` |
| `symbol` (path) | string | Ticker en la ruta para endpoints `/{symbol}/...` |
| `start` | string | Inicio del rango (YYYY-MM-DD o ISO 8601) |
| `end` | string | Fin del rango |
| `feed` | string | Fuente: `sip`, `iex`, `delayed_sip`, etc. |
| `currency` | string | Moneda (ej: `USD`) |
| `limit` | int | Máximo de filas por página |
| `page_token` | string | Token de paginación devuelto por Alpaca |
| `sort` | string | Orden: `asc` o `desc` |

> 💡 Para endpoints batch usar `symbols=AAPL,MSFT`. Para un solo símbolo, usar el endpoint `/{symbol}/...` o el batch con un solo ticker.

### Bars (OHLCV)

`GET /Asset/stocks/bars` — Open, High, Low, Close, Volume agregado por timeframe.

Parámetros adicionales:

| Parámetro | Descripción |
|-----------|-------------|
| `timeframe` | Agregación temporal: `1Min`, `15Min`, `1Hour`, `1Day` |
| `adjustment` | Ajuste corporativo: `raw`, `split`, `dividend`, `all` |

### Quotes

`GET /Asset/stocks/quotes` — cotizaciones bid/ask históricas para uno o varios símbolos.

### Snapshots

`GET /Asset/stocks/snapshots` — fotografía completa actual: última cotización, último trade, barra del día y barra del minuto previo.

### Trades

`GET /Asset/stocks/trades` — histórico de trades ejecutados.

### Auctions

`GET /Asset/stocks/auctions` — datos de subastas (apertura y cierre).

| Parámetro adicional | Descripción |
|---------------------|-------------|
| `asof` | Fecha as-of (YYYY-MM-DD) |

### Meta / Conditions

`GET /Asset/stocks/meta/conditions/{tickType}` — condiciones de tick disponibles por tipo.

| Parámetro | Descripción |
|-----------|-------------|
| `tickType` (path) | Tipo de tick: `trade`, `quote`, `bar` |
| `tape` | Tape NYSE: `A`, `B`, `C` |

### Meta / Exchanges

`GET /Asset/stocks/meta/exchanges` — listado completo de exchanges disponibles. Sin parámetros.

---

## Errores generales

- `401 Unauthorized` — token expiró o inválido
- `403 Forbidden` — sin acceso al recurso
- `500 Server Error` — fallo interno

## Siguiente paso

Continúa con **[Órdenes Internacionales](/docs/ordenes-internacionales)** para enviar una orden con `codBolsa = "ALPACA"` usando el símbolo encontrado.
