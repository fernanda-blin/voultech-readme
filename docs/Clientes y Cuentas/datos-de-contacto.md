---
title: Datos de Contacto
excerpt: >-
  Crea teléfonos, direcciones, correos y vincula contactos relacionados a una
  persona o cliente.
deprecated: false
hidden: false
metadata:
  robots: index
---
Crea teléfonos, direcciones, correos electrónicos y contactos relacionados asociados a una persona o cliente.

<Callout icon="📌" theme="info">
  **¿Necesitas consultar los datos de contacto de una persona o cliente?**
  No es necesario llamar a endpoints específicos de teléfono, dirección o email. Toda la información de contacto ya viene incluida en la respuesta de:

  - `GET /api/publicapi/creasys/Personas?identificador={RUT}` — datos completos de la persona, incluyendo sus contactos.
  - `GET /api/publicapi/creasys/Clientes?identificador={RUT}` — datos del cliente, incluyendo sus contactos.

  Esta sección se enfoca únicamente en **crear** datos de contacto.
</Callout>

## Operaciones disponibles

<Cards columns={4}>
  <Card title="Teléfonos" href="#teléfonos" icon="fa-phone">
    Registra un teléfono asociado a una persona.
  </Card>
  <Card title="Direcciones" href="#direcciones" icon="fa-location-dot">
    Crea una dirección asociada a una persona.
  </Card>
  <Card title="Correos" href="#correos-electrónicos" icon="fa-envelope">
    Registra un email asociado a una persona.
  </Card>
  <Card title="Contactos relacionados" href="#contactos-relacionados" icon="fa-people-arrows">
    Vincula otra persona o cliente como contacto del cliente.
  </Card>
</Cards>

<br />

## Teléfonos

**→ POST** `/api/publicapi/creasys/TelefonoPersona`

Registra un teléfono asociado a una persona existente.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificadorPersona` | RUT de la persona dueña del teléfono |
| `telefono` | Número de teléfono |
| `dscTipoTelefono` | Tipo: `CELULAR`, `FIJO`, `LABORAL` |
| `observacionTelefono` | Observación opcional |

</Accordion>

```json title="Request Body"
{
  "identificadorPersona": "11111111-1",
  "telefono": "+56912345678",
  "dscTipoTelefono": "CELULAR"
}
```

**Resultado esperado:** el teléfono quedará asociado a la persona indicada.

<br />

## Direcciones

**→ POST** `/api/publicapi/creasys/DireccionPersona`

Registra una dirección asociada a una persona existente.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificadorPersona` | RUT de la persona |
| `direccion` | Calle (sin número) |
| `numero` | Número de la dirección |
| `dscComuna` | Nombre de la comuna |
| `dscTipoDireccion` | Tipo: `PARTICULAR`, `LABORAL`, `COMERCIAL` |
| `adicional` | Información adicional opcional (ej. depto, oficina) |

</Accordion>

```json title="Request Body"
{
  "identificadorPersona": "11111111-1",
  "direccion": "Av. Siempre Viva",
  "numero": "742",
  "dscComuna": "Santiago",
  "dscTipoDireccion": "PARTICULAR"
}
```

<Callout icon="💡" theme="info">
  Consulta las comunas válidas con `GET /Comuna`. Ver [Listados del Sistema](/docs/datos-del-sistema).
</Callout>

**Resultado esperado:** la dirección quedará asociada a la persona indicada.

<br />

## Correos electrónicos

**→ POST** `/api/publicapi/creasys/EmailPersona`

Registra un correo electrónico asociado a una persona existente.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificadorPersona` | RUT de la persona |
| `email` | Dirección de correo |
| `dscTipoEmail` | Tipo: `PERSONAL`, `CORPORATIVO`, `PRINCIPAL` |

</Accordion>

```json title="Request Body"
{
  "identificadorPersona": "11111111-1",
  "email": "ana.prueba@email.com",
  "dscTipoEmail": "PERSONAL"
}
```

**Resultado esperado:** el correo quedará asociado a la persona indicada.

<br />

## Contactos relacionados

**→ POST** `/api/publicapi/creasys/Contacto`

Vincula a otra persona o cliente como **contacto relacionado** del cliente (por ejemplo, representante legal, contacto de emergencia, beneficiario).

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificadorCliente` | RUT del cliente principal |
| `tipoContacto` | Tipo de relación (ej. `REPRESENTANTE`, `BENEFICIARIO`, `EMERGENCIA`) |
| `identificadorContacto` | RUT de la persona/cliente vinculado como contacto |

</Accordion>

```json title="Request Body"
{
  "identificadorCliente": "11111111-1",
  "tipoContacto": "REPRESENTANTE",
  "identificadorContacto": "22222222-2"
}
```

<Callout icon="💡" theme="info">
  La persona/cliente que se vincula como contacto debe existir previamente en el sistema. Si no está creada, primero registrala con `POST /Personas` o `POST /Clientes`.
</Callout>

**Resultado esperado:** el contacto quedará vinculado al cliente con el tipo de relación indicado.
