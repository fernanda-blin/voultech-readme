---
title: Enrolamiento de Clientes
excerpt: >-
  Registra clientes en el sistema con sus datos y documentos KYC, y consulta su
  información para validar el alta.
deprecated: false
hidden: false
metadata:
  robots: index
---
Registra un cliente en Voultech con sus datos personales y documentación KYC, y consulta sus datos para confirmar el alta.

<Callout icon="🧭" theme="info">
  Flujo típico: **Crear cliente (con documentos) → Consultar cliente**. La validación KYC/Compliance se ejecuta automáticamente cuando los documentos quedan asociados al cliente.
</Callout>

## Operaciones disponibles

<Cards columns={3}>
  <Card title="Crear cliente" href="#crear-cliente" icon="fa-user-plus">
    Registra el cliente con sus datos personales y de domicilio en una sola llamada.
  </Card>
  <Card title="Consultar cliente" href="#consultar-cliente" icon="fa-magnifying-glass">
    Recupera datos completos del cliente, incluyendo contactos asociados.
  </Card>
  <Card title="Subir documentos" href="#subir-documentos-caso-avanzado" icon="fa-file-arrow-up">
    Sólo si enrolaste con datos mínimos y necesitas cargar documentos después.
  </Card>
</Cards>

<br />

## Crear cliente

**→ POST** `/api/publicapi/creasys/Clientes`

Crea un cliente en el sistema. El body incluye los datos de la **persona** (natural o jurídica) y el **asesor** (tu fintech). Esta llamada crea simultáneamente la persona y el cliente — no necesitas llamar a `POST /Personas` por separado.

<Callout icon="📎" theme="info">
  En el flujo típico de enrolamiento, la documentación KYC del cliente (cédula, contrato, etc.) se carga **junto con la creación del cliente**. Sólo si enrolás con datos mínimos y posponés la carga, usa `POST /Documentos` después (ver [Subir documentos](#subir-documentos-caso-avanzado)).
</Callout>

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Tipo | Descripción |
|---|---|---|
| `persona` | object | Datos de la persona (ver schema `PersonaMantencionDTO`) |
| `pep` | string | `S` si es Persona Expuesta Políticamente, `N` en caso contrario |
| `fatca` | string | `S` si aplica FATCA, `N` en caso contrario |
| `codIdentificacion` | string | Código del proveedor KYC que validó la identidad (Rillis o Identyz). Lo entrega el proveedor cuando completa la verificación; lo guardas y lo envías al crear el cliente. |
| `relacionado` | object | Datos del banco relacionado al cliente (opcional) |
| `asesor` | array | Lista con el `abrNombre` (código de asesor) que vincula al cliente con tu fintech |

**Campos clave de `persona`:**

| Campo | Descripción |
|---|---|
| `identificador` | RUT del cliente (formato `12345678-9`) |
| `tipoIdentificador` | `R` para RUT, otros valores según tipo de identificación |
| `tipoEntidad` | `N` para natural, `J` para jurídica |
| `nombre`, `paterno`, `materno` | Nombres y apellidos |
| `email`, `telefono`, `celular` | Datos de contacto principales |
| `direccionPersona` | Array con direcciones del cliente (al menos una requerida) |

</Accordion>

```json title="Request Body"
{
  "persona": {
    "identificador": "11111111-1",
    "tipoIdentificador": "R",
    "tipoEntidad": "N",
    "nombre": "Ana",
    "paterno": "Prueba",
    "email": "ana.prueba@email.com",
    "direccionPersona": [
      {
        "direccion": "Av. Siempre Viva 123",
        "idComunaCiudadNavigation": {
          "dscComunaCiudad": "Santiago",
          "idRegionNavigation": {
            "dscRegion": "Metropolitana",
            "codPaisNavigation": {
              "dscPais": "CHILE"
            }
          }
        }
      }
    ]
  },
  "asesor": [
    { "abrNombre": "TU_CODIGO_ASESOR" }
  ]
}
```

<Callout icon="💡" theme="info">
  Antes de enviar el cliente, consulta los catálogos válidos: `GET /Comuna`, `GET /Pais`, `GET /EstadoCivil`, `GET /TipoIdentificacion`, `GET /TipoEntidad`. Ver [Listados del Sistema](/docs/datos-del-sistema).
</Callout>

<Callout icon="🆔" theme="info">
  El `codIdentificacion` identifica al proveedor KYC que validó la identidad del cliente. Voultech soporta **Rillis** e **Identyz**. Cuando integras uno de los dos, el proveedor te entrega un código por verificación que debes guardar y enviar acá. Si dejas el campo vacío, el sistema asume que la validación se ejecutará por el flujo interno de Voultech.
</Callout>

**Respuesta exitosa:** `201 Created`. El cliente queda registrado y la documentación KYC asociada queda lista para validación automática.

<br />

## Consultar cliente

**→ GET** `/api/publicapi/creasys/Clientes?identificador={RUT}`

Devuelve los datos completos del cliente, incluyendo persona, dirección, teléfonos, emails y asesor asociado. Útil para validar que el alta quedó correctamente registrada.

<Callout icon="💡" theme="info">
  Esta consulta ya devuelve los datos de contacto. **No es necesario** llamar a endpoints separados de teléfono, dirección o email para consultarlos.
</Callout>

**Resultado esperado:** obtienes la información completa del cliente y sus contactos en una sola respuesta.

<br />

## Consultar persona (sin cliente)

**→ GET** `/api/publicapi/creasys/Personas?identificador={RUT}`

Si necesitas los datos de una **persona** que aún no es cliente (por ejemplo, un representante legal o relacionado), usa este endpoint.

**Resultado esperado:** datos de la persona registrada, sin el contexto de cliente/asesor.

<br />

## Subir documentos (caso avanzado)

<Callout icon="📌" theme="info">
  En el flujo típico **no necesitas este endpoint** — la documentación KYC se carga junto con `POST /Clientes`. Usa `POST /Documentos` sólo si enrolaste con datos mínimos y quieres adjuntar los documentos en una llamada posterior.
</Callout>

**→ POST** `/api/publicapi/creasys/Documentos`

Carga documentos KYC del cliente codificados en **Base64**. La validación de identidad se ejecuta automáticamente cuando los documentos requeridos están cargados.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Descripción |
|---|---|
| `identificador` | RUT del cliente (debe coincidir con un cliente existente) |
| `tipoDocumento` | Extensión del archivo: `.pdf`, `.jpg`, `.png` |
| `nombreDocumento` | Nombre lógico del archivo |
| `contenidoBase64` | Contenido del archivo codificado en Base64 |
| `codTipo` | Tipo del documento (ver tabla abajo) |
| `observacion` | Observación opcional |

</Accordion>

```json title="Request Body"
{
  "identificador": "11111111-1",
  "tipoDocumento": ".pdf",
  "nombreDocumento": "TuFintech_ciFrontal",
  "contenidoBase64": "JVBERi0xLjQKJ...",
  "codTipo": "ciFrontal"
}
```

<Accordion title="Tipos de documento (`codTipo`)" icon="fa-file-lines">

| `codTipo` | Descripción |
|---|---|
| `ciFrontal` | Cédula de identidad (frente) |
| `ciReverso` | Cédula de identidad (reverso) |
| `contrato` | Contrato firmado |

</Accordion>

**Resultado esperado:** el documento queda asociado al cliente. Repetí la llamada por cada documento que necesites cargar.

<br />

## Crear persona asociada (caso avanzado)

<Callout icon="📌" theme="info">
  **Persona ≠ Cliente.** Una persona es la entidad natural o jurídica registrada en el sistema; un cliente es una persona enrolada con tu fintech. En el flujo base **no necesitas este endpoint** — `POST /Clientes` ya crea la persona internamente.
</Callout>

**→ POST** `/api/publicapi/creasys/Personas`

Crea una **persona que no es cliente** pero que está asociada a un cliente con un tipo de relación. Casos típicos:

- **Cónyuge** del cliente (relevante para régimen patrimonial conyugal)
- **Representante legal** (en clientes jurídicos)
- **Beneficiario**, **apoderado** o **relacionado** según norma CMF

La persona queda registrada y luego se vincula al cliente principal con un `codTipoRelacion` (cónyuge, representante, etc.).

**Resultado esperado:** la persona queda registrada en el sistema, disponible para vincularse a un cliente existente con su tipo de relación.
