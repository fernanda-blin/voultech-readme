---
title: Ingresar Orden de Mercado
slug: ingresar-ordenes-mercado-nacional
api:
  file: public-api.json
  operationId: post_api-publicapi-creasys-ordenes-ingresarordenesmercado
hidden: false
---

Ingresa una orden de compra o venta de instrumentos de **renta variable**. Este endpoint sirve tanto para mercado nacional (Bolsa de Santiago, `XSGO`) como para el módulo internacional Alpaca — la categoría depende del `tipoSeguridad`, `codBolsa` y la cuenta utilizada.
