---
title: Gestión de Cuentas
excerpt: >-
  Crea cuentas de inversión, asocia cuentas bancarias y habilita cajas por
  moneda para operar.
deprecated: false
hidden: false
metadata:
  robots: index
---
Una vez registrado el cliente, gestiona su operativa creando una cuenta de inversión, asociando su cuenta bancaria y habilitando cajas por moneda.

<Callout icon="🧭" theme="info">
  Flujo base: **Crear cuenta de inversión → Asociar cuenta bancaria → Crear cajas por moneda**.
</Callout>

## Operaciones disponibles

<Cards columns={3}>
  <Card title="Cuentas de inversión" href="#crear-cuenta-de-inversión" icon="fa-folder-open">
    Crea y consulta cuentas asociadas a un cliente.
  </Card>
  <Card title="Cuentas bancarias" href="#asociar-cuenta-bancaria" icon="fa-building-columns">
    Vincula la cuenta bancaria utilizada para abonos y retiros.
  </Card>
  <Card title="Cajas por moneda" href="#crear-caja-por-moneda" icon="fa-wallet">
    Habilita saldos separados por divisa dentro de una cuenta.
  </Card>
</Cards>

<br />

## Crear cuenta de inversión

**→ POST** `/api/publicapi/creasys/Cuentas`

Crea una cuenta individual de inversión para un cliente existente, asociada a tu fintech como asesor.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `numCuenta` | Identificador único de la cuenta (ej. `12345678/17`) |
| `dscCuenta` | Nombre descriptivo de la cuenta |
| `abrCuenta` | Abreviatura, usualmente igual a `numCuenta` |
| `identificador` | RUT del cliente (debe existir) |
| `codMoneda` | Moneda base: `CLP`, `USD`, `EUR` |
| `codTipoAdministracion` | Tipo de administración (`NF` por defecto) |
| `dscPerfilRiesgo` | Perfil de riesgo: `CONSERVADOR`, `MODERADO`, `ARRIESGADO`, `AGRESIVO`, `CALIFICADO` |
| `dscTipoCuenta` | Tipo: `NACIONAL`, `FIP`, `EXTRANJERA`, `PERSHING` |
| `abrAsesor` | Código de asesor vinculado a tu fintech |

</Accordion>

```json title="Request Body"
{
  "numCuenta": "12345678/17",
  "dscCuenta": "Javiera Río Casanova",
  "abrCuenta": "12345678/17",
  "identificador": "12345678-K",
  "codMoneda": "CLP",
  "codTipoAdministracion": "NF",
  "dscPerfilRiesgo": "AGRESIVO",
  "dscTipoCuenta": "NACIONAL",
  "abrAsesor": "TU_CODIGO_ASESOR"
}
```

<Callout icon="💡" theme="info">
  Consulta valores válidos con `GET /Moneda`, `GET /PerfilRiesgo`, `GET /TipoCuenta`. Ver [Listados del Sistema](/docs/datos-del-sistema).
</Callout>

**Respuesta exitosa:** `201 Created`. La cuenta queda creada y asociada al cliente.

<br />

## Consultar cuentas

**→ GET** `/api/publicapi/creasys/Cuentas?identificador={RUT}`

Retorna todas las cuentas asociadas al cliente identificado.

```json title="Respuesta"
[
  {
    "numCuenta": "12345678/17",
    "dscCuenta": "Javiera Río Casanova",
    "abrCuenta": "12345678/17",
    "identificador": "12345678-K",
    "codMoneda": "CLP",
    "codTipoAdministracion": "NF",
    "dscPerfilRiesgo": "AGRESIVO",
    "dscTipoCuenta": "NACIONAL",
    "abrAsesor": "TU_CODIGO_ASESOR"
  }
]
```

**Resultado esperado:** detalle de cuentas asociadas al cliente.

<br />

## Asociar cuenta bancaria

**→ POST** `/api/publicapi/creasys/CuentaCorriente`

Vincula la cuenta bancaria del cliente para recibir abonos y ejecutar retiros.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificador` | RUT del cliente |
| `numeroCuentaCte` | Número de cuenta bancaria |
| `codMoneda` | Moneda: `CLP`, `USD`, `EUR` |
| `dscBanco` | Nombre del banco (ej. `BANCO BICE`) |
| `tipoCuenta` | `Cuenta Corriente`, `Cuenta Vista`, `Cuenta de Ahorro`, `Chequera`, `Electrónica` |
| `codEstado` | Estado de la cuenta (opcional) |

</Accordion>

```json title="Request Body"
{
  "identificador": "18737322-0",
  "numeroCuentaCte": "11111111112",
  "codMoneda": "CLP",
  "dscBanco": "BANCO BICE",
  "tipoCuenta": "Cuenta Corriente"
}
```

<Callout icon="💡" theme="info">
  Consulta los bancos válidos con `GET /Banco` y los tipos de cuenta bancaria con `GET /TipoCuentaBanco`.
</Callout>

**Resultado esperado:** la cuenta bancaria queda asociada al cliente y disponible para abonos/retiros.

<br />

## Consultar cuenta bancaria

**→ GET** `/api/publicapi/creasys/CuentaCorriente?numCuenta={numCuenta}`

Devuelve la cuenta bancaria asociada a la cuenta de inversión consultada.

**Resultado esperado:** datos de la cuenta bancaria vinculada.

<br />

## Crear caja por moneda

**→ POST** `/api/publicapi/creasys/Cajas`

Habilita una caja en una moneda específica dentro de la cuenta de inversión. Las cajas representan los saldos disponibles por divisa.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `numCuenta` | Cuenta de inversión a la que se asocia la caja |
| `codMoneda` | Moneda de la caja (`CLP`, `USD`, `EUR`) |
| `codMercado` | Mercado asociado a la caja |

</Accordion>

```json title="Request Body"
{
  "numCuenta": "12345678/17",
  "codMoneda": "USD",
  "codMercado": "EXT"
}
```

<Callout icon="💡" theme="info">
  Una cuenta puede tener múltiples cajas en distintas monedas. Las cajas en CLP suelen crearse automáticamente al crear la cuenta; el resto se crean según necesidad operativa.
</Callout>

**Resultado esperado:** la cuenta dispondrá de una caja adicional para operar en la moneda indicada.

<br />

## Consultar cajas

Para revisar saldos de las cajas, ver la sección [Consultas Operativas](/docs/consultas-operativas) que cubre `GET /Cajas`, `GET /Cajas/ConSaldo` y `GET /Cajas/ConSaldoOnline`.
