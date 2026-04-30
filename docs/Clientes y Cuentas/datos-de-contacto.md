---
title: Datos de Contacto
excerpt: >-
  Gestiona teléfonos, direcciones y correos electrónicos asociados a personas
  registradas en el sistema.
deprecated: false
hidden: false
metadata:
  robots: index
---
Gestiona los datos de contacto de personas registradas en el sistema, incluyendo teléfonos, direcciones y correos electrónicos.

<Callout icon="📌" theme="info">
  **¿Necesitas consultar los datos de contacto de una persona o cliente?**
  No es necesario llamar a endpoints específicos de teléfono, dirección o email. Toda la información de contacto ya viene incluida en la respuesta de:

  - `GET /api/publicapi/creasys/Personas/{identificador}` — datos completos de la persona, incluyendo sus contactos.
  - `GET /api/publicapi/creasys/Clientes/{identificador}` — datos del cliente, incluyendo sus contactos.

  Esta sección se enfoca únicamente en **crear** y **actualizar** datos de contacto.
</Callout>

## Operaciones disponibles

<Cards columns={3}>
  <Card title="Teléfonos" href="#teléfonos" icon="fa-phone">
    Registra y actualiza teléfonos asociados a una persona.
  </Card>
  <Card title="Direcciones" href="#direcciones" icon="fa-location-dot">
    Crea y actualiza direcciones registradas en el sistema.
  </Card>
  <Card title="Correos electrónicos" href="#correos-electrónicos" icon="fa-envelope">
    Crea y actualiza correos electrónicos asociados a una persona.
  </Card>
</Cards>

<br />

## Teléfonos

<Accordion title="Ver operaciones disponibles para teléfonos" icon="fa-phone">

- `POST /api/publicapi/creasys/TelefonoPersona`: crea un nuevo teléfono asociado a una persona.
- `PUT /api/publicapi/creasys/TelefonoPersona`: actualiza un teléfono existente por identificador y número.

</Accordion>

**→ POST** `/api/publicapi/creasys/TelefonoPersona`

Crea un nuevo teléfono asociado a una persona.

**→ PUT** `/api/publicapi/creasys/TelefonoPersona`

Actualiza un teléfono existente por identificador y número.

<Callout icon="⚠️" theme="warning">
  El acceso a la actualización (PUT) requiere **autorización previa** del equipo de Voultech.
</Callout>

<Callout icon="💡" theme="info">
  Consulta los tipos de teléfono disponibles (celular, fijo, laboral) con `GET /TipoDireccion/GetTipoTelefono`.
</Callout>

**Resultado esperado:** podrás registrar y mantener teléfonos asociados a una persona utilizando los tipos válidos del sistema.

<br />

## Direcciones

<Accordion title="Ver operaciones disponibles para direcciones" icon="fa-location-dot">

- `POST /api/publicapi/creasys/DireccionPersona`: crea una nueva dirección asociada a una persona.
- `PUT /api/publicapi/creasys/DireccionPersona`: actualiza una dirección existente por identificador.

</Accordion>

**→ POST** `/api/publicapi/creasys/DireccionPersona`

Crea una nueva dirección asociada a una persona.

**→ PUT** `/api/publicapi/creasys/DireccionPersona`

Actualiza una dirección por su identificador.

<Callout icon="⚠️" theme="warning">
  El acceso a la actualización (PUT) requiere **autorización previa** del equipo de Voultech.
</Callout>

**Resultado esperado:** podrás registrar y actualizar direcciones de personas en el sistema.

<br />

## Correos electrónicos

<Accordion title="Ver operaciones disponibles para correos electrónicos" icon="fa-envelope">

- `POST /api/publicapi/creasys/EmailPersona`: crea un nuevo correo electrónico asociado a una persona.
- `PUT /api/publicapi/creasys/EmailPersona`: actualiza un correo existente por identificador.

</Accordion>

**→ POST** `/api/publicapi/creasys/EmailPersona`

Crea un nuevo correo electrónico asociado a una persona.

**→ PUT** `/api/publicapi/creasys/EmailPersona`

Actualiza un correo existente por su identificador.

<Callout icon="⚠️" theme="warning">
  El acceso a la actualización (PUT) requiere **autorización previa** del equipo de Voultech.
</Callout>

<Callout icon="💡" theme="info">
  Consulta los tipos de email disponibles (personal, corporativo, principal) con `GET /TipoDireccion/GetTipoMail`.
</Callout>

**Resultado esperado:** podrás registrar y actualizar correos electrónicos con tipos válidos para la integración.
