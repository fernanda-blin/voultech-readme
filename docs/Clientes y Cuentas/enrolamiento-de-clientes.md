---
title: Enrolamiento de Clientes
excerpt: >-
  Registra clientes en el sistema, sube su documentación KYC y consulta su
  información para validar el alta.
deprecated: false
hidden: false
metadata:
  robots: index
---
Registra un cliente en Voultech, sube los documentos requeridos por KYC/Compliance y consulta sus datos para confirmar el alta.

<Callout icon="🧭" theme="info">
  Flujo base: **Crear cliente → Subir documentos → Consultar cliente**. La validación KYC/Compliance se ejecuta automáticamente tras la carga de documentos.
</Callout>

## Operaciones disponibles

<Cards columns={3}>
  <Card title="Crear cliente" href="#crear-cliente" icon="fa-user-plus">
    Registra el cliente con sus datos personales y de domicilio en una sola llamada.
  </Card>
  <Card title="Subir documentos" href="#subir-documentos" icon="fa-file-arrow-up">
    Carga la documentación requerida (cédula, contrato) en Base64.
  </Card>
  <Card title="Consultar cliente" href="#consultar-cliente" icon="fa-magnifying-glass">
    Recupera datos completos del cliente, incluyendo contactos asociados.
  </Card>
</Cards>

<br />

## Crear cliente

**→ POST** `/api/publicapi/creasys/Clientes`

Crea un cliente en el sistema. El body incluye los datos de la **persona** (natural o jurídica) y el **asesor** (tu fintech). Esta llamada crea simultáneamente la persona y el cliente — no necesitas llamar a `POST /Personas` por separado.

<Accordion title="Ver campos del body" icon="fa-file-lines">

| Campo | Tipo | Descripción |
|---|---|---|
| `persona` | object | Datos de la persona (ver schema `PersonaMantencionDTO`) |
| `pep` | string | `S` si es Persona Expuesta Políticamente, `N` en caso contrario |
| `fatca` | string | `S` si aplica FATCA, `N` en caso contrario |
| `codIdentificacion` | string | Código adicional de identificación (opcional) |
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

**Respuesta exitosa:** `201 Created`. El cliente queda registrado en el sistema y disponible para los siguientes pasos.

<br />

## Subir documentos

**→ POST** `/api/publicapi/creasys/Documentos`

Carga los documentos KYC del cliente codificados en **Base64**. La validación de identidad se ejecuta automáticamente cuando los documentos requeridos están cargados.

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

<Accordion title="Ver tipos de documento (`codTipo`)" icon="fa-file-lines">

| `codTipo` | Descripción |
|---|---|
| `ciFrontal` | Cédula de identidad (frente) |
| `ciReverso` | Cédula de identidad (reverso) |
| `contrato` | Contrato firmado |

</Accordion>

**Resultado esperado:** el documento queda asociado al cliente. Repite la llamada por cada documento que necesites cargar.

<br />

## Consultar cliente

**→ GET** `/api/publicapi/creasys/Clientes?identificador={RUT}`

Devuelve los datos completos del cliente, incluyendo persona, dirección, teléfonos, emails y asesor asociado. Útil para validar que el alta quedó correctamente registrada.

<Callout icon="💡" theme="info">
  Esta consulta ya devuelve los datos de contacto. **No es necesario** llamar a endpoints separados de teléfono, dirección o email para consultarlos.
</Callout>

**Resultado esperado:** obtendrás la información completa del cliente y sus contactos en una sola respuesta.

<br />

## Consultar persona (sin cliente)

**→ GET** `/api/publicapi/creasys/Personas?identificador={RUT}`

Si necesitás los datos de una **persona** que aún no es cliente (por ejemplo, un representante legal o relacionado), usá este endpoint.

**Resultado esperado:** datos de la persona registrada, sin el contexto de cliente/asesor.

<br />

## Crear persona sin cliente (caso avanzado)

<Callout icon="📌" theme="info">
  Para el flujo base de enrolamiento, **no necesitas** este endpoint — `POST /Clientes` ya crea la persona internamente.
</Callout>

**→ POST** `/api/publicapi/creasys/Personas`

Crea una persona en el sistema sin asociarla a un cliente. Útil cuando registrás representantes legales, beneficiarios o personas relacionadas que después se vinculan a un cliente existente.

**Resultado esperado:** la persona quedará registrada en el sistema, disponible para vincularse posteriormente con un cliente.
