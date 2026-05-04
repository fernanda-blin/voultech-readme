---
title: 02. Clientes y Personas
hidden: false
---

> ⚠️ **Persona ≠ Cliente.** Una *persona* es la entidad natural o jurídica registrada en el sistema (con RUT/pasaporte, nombre, etc.). Un *cliente* es una persona que ha sido enrolada con tu fintech (vinculada a un asesor, con perfil de riesgo y documentación KYC).
>
> En el flujo típico no necesitas crear personas por separado: `POST /Clientes` crea internamente la persona y la vincula como cliente. Sólo usa `POST /Personas` cuando registres representantes legales, cónyuges, beneficiarios u otras personas relacionadas que después se vincularán a un cliente existente con un tipo de relación.
