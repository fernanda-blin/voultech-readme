---
title: Listados del Sistema
excerpt: >-
  Catálogos del sistema: bancos, comunas, monedas, perfiles de riesgo, tipos de
  cuenta, documentos y más.
deprecated: false
hidden: false
metadata:
  robots: index
---
Consulta los catálogos del sistema para obtener valores válidos que te permitan poblar menús, validar información y construir solicitudes a otros endpoints.

## Categorías disponibles

<Cards columns={4}>
  <Card title="Datos geográficos" href="#datos-geograficos" icon="fa-earth-americas">
    Consulta países, regiones y comunas para datos de residencia, nacionalidad y domicilio.
  </Card>
  <Card title="Datos financieros" href="#datos-financieros" icon="fa-coins">
    Consulta monedas, bancos, perfiles de riesgo, contrapartes y precios publicados.
  </Card>
  <Card title="Cuentas y entidades" href="#tipos-de-cuenta" icon="fa-folder-open">
    Revisa tipos de cuenta, entidad, identificación y sociedad.
  </Card>
  <Card title="Operación y control" href="#operaciones-y-movimientos" icon="fa-list-check">
    Consulta medios de pago, movimientos, contratos y documentos.
  </Card>
</Cards>

**Formato general de las URLs:**

```
GET /api/publicapi/creasys/{NombreLista}
```

`{NombreLista}` es un patrón: reemplázalo por el nombre del catálogo que necesites (`Pais`, `Banco`, `Moneda`, `PerfilRiesgo`, `TipoCuenta`, etc.). Los catálogos disponibles están listados más abajo.

**Resultado esperado:** obtienes los valores de referencia necesarios para construir requests válidos en el resto de la integración.

<br />

## Datos geográficos

<Accordion title="Ver listados geográficos" icon="fa-earth-americas">

**→ GET** `/api/publicapi/creasys/Pais` — Países disponibles para residencia o nacionalidad

**→ GET** `/api/publicapi/creasys/Region` — Regiones del país

**→ GET** `/api/publicapi/creasys/Comuna` — Comunas, utilizadas en la definición de domicilios

</Accordion>

<br />

## Datos financieros

<Accordion title="Ver listados financieros" icon="fa-coins">

**→ GET** `/api/publicapi/creasys/Moneda` — Monedas admitidas (`CLP`, `USD`, `EUR`, etc.)

**→ GET** `/api/publicapi/creasys/Banco` — Bancos disponibles para operaciones de pago o cobro

**→ GET** `/api/publicapi/creasys/PerfilRiesgo` — Perfiles de riesgo configurados en la corredora

<Callout icon="💡" theme="info">
  El perfil de riesgo se utiliza al crear una cuenta, ya que se debe asignar un perfil al titular.
</Callout>

**→ GET** `/api/publicapi/creasys/Contraparte` — Contrapartes registradas en operaciones

**→ GET** `/api/publicapi/creasys/PublicadorPrecio/GetPreciosInstrumento` — Precios publicados de instrumentos financieros

</Accordion>

<br />

## Tipos de cuenta

<Accordion title="Ver listados de cuentas" icon="fa-folder-open">

**→ GET** `/api/publicapi/creasys/TipoCuenta` — Tipos de cuenta disponibles (corriente, inversión, custodia, etc.)

**→ GET** `/api/publicapi/creasys/TipoCuentaBanco` — Tipos de cuenta bancaria (vista, corriente, ahorro, etc.)

</Accordion>

<br />

## Tipos de entidad y persona

<Accordion title="Ver listados de entidad y persona" icon="fa-users">

**→ GET** `/api/publicapi/creasys/TipoEntidad` — Categorías de entidad jurídica o natural

**→ GET** `/api/publicapi/creasys/TipoIdentificacion` — Tipos de documento de identidad (RUT, pasaporte, DNI extranjero, etc.)

**→ GET** `/api/publicapi/creasys/TipoSociedad` — Tipos societarios aplicables a personas jurídicas

**→ GET** `/api/publicapi/creasys/EstadoCivil` — Estados civiles disponibles para personas naturales

**→ GET** `/api/publicapi/creasys/ContratoMatrimonial` — Regímenes matrimoniales (sociedad conyugal, separación, participación, etc.)

</Accordion>

<br />

## Operaciones y movimientos

<Accordion title="Ver listados operativos" icon="fa-money-bill-transfer">

**→ GET** `/api/publicapi/creasys/TipoMedioPagoCobro` — Medios de pago o cobro habilitados (`P` = Pago/abonos, `C` = Cobro/retiros)

**→ GET** `/api/publicapi/creasys/TipoMovCaja` — Tipos de movimientos de caja (ingresos, retiros, transferencias, etc.)

**→ GET** `/api/publicapi/creasys/FormaOperacion` — Formas de operación habilitadas

**→ GET** `/api/publicapi/creasys/RelacionClienteConBanco` — Tipos de relación entre cliente y corredora según norma NCG 69

</Accordion>

<br />

## Contratos y documentos

<Accordion title="Ver listados de contratos y documentos" icon="fa-file-signature">

**→ GET** `/api/publicapi/creasys/ContratoOperacion/GetTipoContrato` — Tipos de contrato para operaciones financieras

**→ GET** `/api/publicapi/creasys/ContratoOperacion/GetTipoDocumento` — Tipos de documentos asociados a contratos

**→ GET** `/api/publicapi/creasys/CodigoTipoDocumento` — Tipos de documento requeridos para enrolamiento o identificación

</Accordion>
