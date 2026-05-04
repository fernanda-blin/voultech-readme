---
title: Sistema de Eventos
excerpt: >-
  Recibe notificaciones en tiempo real vía Azure Service Bus sobre movimientos
  bancarios, Shinkansen y validaciones KYC.
deprecated: false
hidden: false
metadata:
  robots: index
---
Recibe notificaciones automáticas de movimientos bancarios, ejecuciones de pagos y validaciones KYC mediante una cola de Azure Service Bus.

## Eventos disponibles

<Cards columns={4}>
  <Card title="Movimiento bancario" href="#movimiento-bancario" icon="fa-building-columns">
    Recibe abonos y cargos detectados sobre movimientos bancarios.
  </Card>
  <Card title="Reversa bancaria" href="#reversa-de-movimiento-bancario" icon="fa-arrow-rotate-left">
    Recibe reversas asociadas a movimientos bancarios previamente informados.
  </Card>
  <Card title="Movimiento Shinkansen" href="#movimiento-shinkansen" icon="fa-money-bill-transfer">
    Recibe el estado de ejecuciones de pagos procesados por Shinkansen.
  </Card>
  <Card title="Validación KYC" href="#validacion-de-kyc" icon="fa-user-check">
    Recibe el resultado de validaciones KYC realizadas sobre un usuario.
  </Card>
</Cards>

<br />

## Conexión

La conexión se realiza mediante una cadena de conexión a Service Bus:

```
sb://voultech.servicebus.windows.net/{NombreCola}
```

<Callout icon="⚠️" theme="warning">
  El nombre de la cola es entregado por Voultech a cada fintech. Solicítalo a [hey@voultech.com](mailto:hey@voultech.com).
</Callout>

<Accordion title="Ver horarios de procesamiento" icon="fa-clock">

**Horarios de procesamiento:**
- Los mensajes de **aportes automáticos** se reciben entre **09:00 y 14:55 hrs**
- Los aportes realizados fuera de este horario se encolan para el día siguiente

</Accordion>

**Resultado esperado:** contarás con la cadena de conexión y el nombre de la cola necesarios para consumir los eventos asignados a tu integración.

<br />

## Suscripción a eventos

Ejemplo en **C#** para suscribirte a la cola y procesar mensajes:

```csharp title="Ejemplo de suscripción — C#"
using Azure.Messaging.ServiceBus;
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        string connectionString = "<cadena_de_conexión>";
        string queueName = "<nombre_de_cola>";

        ServiceBusClient client = new ServiceBusClient(connectionString);
        ServiceBusProcessor processor = client.CreateProcessor(queueName);

        processor.ProcessMessageAsync += MessageHandler;
        processor.ProcessErrorAsync += ErrorHandler;

        await processor.StartProcessingAsync();
        Console.WriteLine("Presiona ENTER para detener...");
        Console.ReadLine();

        await processor.StopProcessingAsync();
        await processor.DisposeAsync();
        await client.DisposeAsync();
    }

    static async Task MessageHandler(ProcessMessageEventArgs args)
    {
        string body = args.Message.Body.ToString();
        Console.WriteLine($"Recibido: {body}");
        await args.CompleteMessageAsync(args.Message);
    }

    static Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine($"Error: {args.Exception.Message}");
        return Task.CompletedTask;
    }
}
```

**Resultado esperado:** tu integración queda suscrita a la cola y procesa los mensajes entrantes en formato JSON.

<br />

## Mensajes

Los mensajes se envían en formato **JSON** con una frecuencia aproximada de **1 minuto**. Cada mensaje contiene un campo `Topic` que identifica el tipo de evento.

<Accordion title="Ver estructura general de los mensajes" icon="fa-envelope-open-text">

- Todos los mensajes se entregan en formato **JSON**.
- El campo `Topic` identifica el tipo de evento recibido.
- Debes procesar cada mensaje según su `Topic` y su estructura específica.

</Accordion>

### Movimiento bancario

**Topic:** `MOVBANCO`

```json title="Ejemplo"
{
  "Topic": "MOVBANCO",
  "TransactionId": "236",
  "Banco": "",
  "BookingDate": "2024-01-16T00:00:00",
  "TransactionTime": "2024-01-15T14:15:39.527",
  "AbonoCargo": "A",
  "DebtorUserId": "151837115",
  "TransactionInformation": "TRANSFERENCIA DESDE Security DE RAMIRO JOSE EXEQUIEL GAR | Fondo de liquidez",
  "Amount": 250000.0,
  "IdMovCaja": "0",
  "TransactionIdCartola": "123",
  "Result": ""
}
```

**Resultado esperado:** identificas el movimiento bancario informado y lo asocias a tu proceso de conciliación o trazabilidad.

### Reversa de movimiento bancario

**Topic:** `REVERSAMOVBANCO`

```json title="Ejemplo"
{
  "Topic": "REVERSAMOVBANCO",
  "TransactionId": "236",
  "Banco": "",
  "BookingDate": "2024-01-16T00:00:00",
  "TransactionTime": "2024-01-15T14:15:39.527",
  "AbonoCargo": "A",
  "DebtorUserId": "151837115",
  "TransactionInformation": "TRANSFERENCIA DESDE Security DE RAMIRO JOSE EXEQUIEL GAR | Fondo de liquidez",
  "Amount": 250000.0,
  "IdMovCaja": "0",
  "TransactionIdCartola": "123",
  "Result": ""
}
```

**Resultado esperado:** detectas que un movimiento bancario previamente informado fue revertido.

### Movimiento Shinkansen

**Topic:** `MOVSHINKANSEN`

```json title="Ejemplo"
{
  "Topic": "MOVSHINKANSEN",
  "TransactionId": "4",
  "MovId": 10811975,
  "Date": "2024-09-09T12:04:30.757",
  "State": "OK",
  "Information": ""
}
```

**Resultado esperado:** consultás el estado del movimiento procesado por Shinkansen mediante su identificador y el estado devuelto.

### Validación de KYC

**Topic:** `KYCVALIDATION`

<Tabs>
  <Tab title="Aprobada">

```json
{
  "Topic": "KYCVALIDATION",
  "Date": "{fecha}",
  "State": "OK",
  "Code": "001",
  "UserId": "{identificador}",
  "Information": "AML OK",
  "Detail": {}
}
```

  </Tab>
  <Tab title="Rechazada">

```json
{
  "Topic": "KYCVALIDATION",
  "Date": "{fecha}",
  "State": "Error",
  "Code": "{código error}",
  "UserId": "{identificador}",
  "Information": "{tipo error}",
  "Detail": {}
}
```

  </Tab>
</Tabs>

**Códigos de error KYC:**

| Code | Information | Descripción |
|---|---|---|
| 002 | AML High Risk | Cliente de riesgo alto en listas de vigilancia |
| 003 | Cannot get document data | No se puede extraer la información del documento de identidad |
| 004 | Missing data | Falta información del cliente (RUT, codDocumento, etc.) |
| 005 | Document rejected | Documento rechazado por proveedor |
| 006 | Code not found | No se encuentra codIdentificación |

**Resultado esperado:** determinas si la validación KYC fue aprobada o rechazada y actúas según el código informado.

<Callout icon="💡" theme="info">
  El sistema de eventos está diseñado para notificaciones **en tiempo real** (pub/sub). Si pierdes un mensaje, puedes consultar el estado manualmente vía API (por ejemplo: saldos o estado KYC directamente).
</Callout>
