---
title: Come usare le funzioni definite dall'utente in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come creare funzioni definite dall'utente, matcher e assegnazioni di ruolo con Gemelli digitali di Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636833"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Come usare le funzioni definite dall'utente in Gemelli digitali di Azure

Le [funzioni definite dall'utente](./concepts-user-defined-functions.md) (UDF) consentono all'utente di eseguire una logica personalizzata sui messaggi di telemetria in ingresso e sui metadati del grafico spaziale. L'utente può quindi inviare gli eventi a endpoint predefiniti. Questa guida illustra un esempio correlato agli eventi relativi alla temperatura, per rilevare le letture che superano una determinata temperatura e inviare un avviso.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Informazioni di riferimento sulla libreria client

Le funzioni disponibili come metodi helper nel runtime delle funzioni definite dall'utente sono elencate nella sezione [informazioni di riferimento client](#Client-Reference).

## <a name="create-a-matcher"></a>Creare un matcher

I matcher sono oggetti del grafico che determinano quali funzioni definite dall'utente eseguire per un messaggio di telemetria specifico.

- Confronti di condizione validi per i matcher:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinazioni delle condizioni valide per i matcher:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Il matcher di esempio seguente restituisce true per ogni evento di telemetria dei sensori con tipo di dati `"Temperature"`. È possibile creare più matcher in una funzione definita dall'utente:

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Area del server in cui è ospitata l'istanza |

## <a name="create-a-user-defined-function-udf"></a>Creare una funzione definita dall'utente

Dopo aver creato i matcher, caricare il frammento di codice della funzione con la chiamata **POST** seguente:

> [!IMPORTANT]
> - Nelle intestazioni, impostare `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Il corpo è costituito da più parti:
>   - La prima parte riguarda i metadati necessari per la funzione definita dall'utente.
>   - La seconda parte è la logica di calcolo JavaScript.
> - Nella sezione **USER_DEFINED_BOUNDARY**, sostituire i valori **SpaceId** e **Matchers**.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Valore del parametro | Sostituire con |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Un nome di limite del contenuto costituito da più parti |

### <a name="body"></a>Corpo

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Identificatore dello spazio  |
| YOUR_MATCHER_IDENTIFIER | ID del matcher da usare |

### <a name="example-functions"></a>Funzioni di esempio

Impostare la lettura dei dati di telemetria del sensore direttamente per un sensore con tipo di dati **Temperatura**, ovvero `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Il parametro **telemetry** espone gli attributi **SensorId** e **Message**, corrispondenti a un messaggio inviato da un sensore. Il parametro **executionContext** espone i seguenti attributi:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Nel prossimo esempio, registriamo un messaggio se la lettura dei dati di telemetria del sensore supera una soglia predefinita. Se le impostazioni di diagnostica sono abilitate nell'istanza di Gemelli digitali di Azure, vengono inoltrati anche i log delle funzioni definite dall'utente:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Il codice seguente attiva una notifica se il livello di temperatura sale sopra il valore costante predefinito:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Per un esempio di codice di funzione definita dall'utente più complesso, consultare la [funzione definita dall'utente per il controllo degli spazi disponibili con aria fresca](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

È necessario creare un'assegnazione di ruolo per l'esecuzione della funzione definita dall'utente. In caso contrario, non saranno disponibili le autorizzazioni appropriate per interagire con l'API Gestione per eseguire azioni sugli oggetti del grafico. Le azioni eseguite dalla funzione definita dall'utente non sono esenti dal controllo degli accessi in base al ruolo delle API Gestione di Gemelli digitali di Azure. È possibile limitarne l'ambito specificando determinati ruoli o percorsi di controllo di accesso. Per altre informazioni, vedere la documentazione relativa al [controllo degli accessi in base al ruolo](./security-role-based-access-control.md).

1. Eseguire una query per i ruoli e ottenere l'ID del ruolo da assegnare alla funzione definita dall'utente. Passare il valore a **RoleId**:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** corrisponderà all'ID della funzione definita dall'utente creata in precedenza.
1. Trovare il valore di **Path** eseguendo una query degli spazi con `fullpath`.
1. Copiare il valore `spacePaths` restituito. Questo verrà usato nel codice seguente:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valore del parametro | Sostituire con |
    | --- | --- |
    | *YOUR_SPACE_NAME* | Nome dello spazio da usare |

1. Incollare il valore `spacePaths` restituito in **Path** per creare un'assegnazione di ruolo alla funzione definita dall'utente:

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Valore | Sostituire con |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identificatore del ruolo desiderato |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID della funzione definita dall'utente da usare |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | L'ID che specifica il tipo di funzione definita dall'utente |
    | YOUR_ACCESS_CONTROL_PATH | Percorso di controllo di accesso |

## <a name="send-telemetry-to-be-processed"></a>Inviare i dati di telemetria da elaborare

I dati di telemetria generati dal sensore descritto nel grafico attivano l'esecuzione della funzione definita dall'utente che è stata caricata. L'elaboratore di dati seleziona i dati di telemetria. Viene quindi creato un piano di esecuzione per la chiamata della funzione definita dall'utente.

1. Recuperare i matcher per il sensore da cui è stata generata la lettura.
1. A seconda dei matcher valutati correttamente, recuperare le funzioni definite dall'utente associate.
1. Eseguire ogni funzione definita dall'utente.

## <a name="client-reference"></a>Informazioni di riferimento client

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Dato un identificatore di spazio, questa funzione recupera lo spazio dal grafico.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di spazio |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Dato un identificatore di sensore, questa funzione recupera il sensore dal grafico.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di sensore |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Dato un identificatore di dispositivo, questa funzione recupera il dispositivo dal grafico.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identificatore di dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera il valore corrente per il sensore.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera il valore corrente per il sensore.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera i valori storici per il sensore.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType* | `string` | Tipo di dati del sensore |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Dato un identificatore di spazio e il nome del valore, questa funzione recupera i valori cronologici per tale proprietà nello spazio.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Dato un identificatore di spazio, questa funzione recupera gli spazi figlio per tale spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Dato un identificatore di spazio, questa funzione recupera i sensori figlio per tale spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Dato un identificatore di spazio, questa funzione recupera i dispositivi figlio per tale spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Dato un identificatore di dispositivo, questa funzione recupera i sensori figlio per tale dispositivo padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Dato un identificatore di spazio, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificatore di spazio |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Dato un identificatore di sensore, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Dato un identificatore di dispositivo, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificatore di dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Dato un identificatore di sensore, questa funzione recupera il proprio dispositivo padre.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di spazio, questa funzione recupera la proprietà e il relativo valore dallo spazio.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *propertyName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di sensore, questa funzione recupera la proprietà e il relativo valore dal sensore.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *propertyName* | `string` | Nome della proprietà del sensore |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di dispositivo, questa funzione recupera la proprietà e il relativo valore dal dispositivo.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |
| *propertyName* | `string` | Nome della proprietà del dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Questa funzione imposta un valore sull'oggetto sensore con il tipo di dati specificato.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |
| *value*  | `string` | Valore |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Questa funzione imposta un valore sull'oggetto spazio con il tipo di dati specificato.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *dataType* | `string` | Tipo di dati |
| *value* | `string` | Valore |

### <a name="logmessage"></a>log(message)

Questa funzione registra il messaggio seguente all'interno della funzione definita dall'utente.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Messaggio da registrare |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Questa funzione invia una notifica personalizzata da consegnare.

**Tipo**: funzione globale

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificatore dell'oggetto del grafico. Spazi, sensore e ID del dispositivo sono esempi.|
| *topologyObjectType*  | `string` | Sensore e dispositivo sono esempi.|
| *payload*  | `string` | Payload JSON da inviare con la notifica. |

## <a name="return-types"></a>Tipi restituiti

I modelli seguenti descrivono gli oggetti restituiti da quanto indicato nelle informazioni di riferimento client precedenti.

### <a name="space"></a>Spazio

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Metodi relativi allo spazio

#### <a name="parent--space"></a>Parent() ⇒ `space`

Questa funzione restituisce lo spazio padre dello spazio corrente.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Questa funzione restituisce i sensori figlio dello spazio corrente.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Questa funzione restituisce i dispositivi figlio dello spazio corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Questa funzione restituisce la proprietà estesa e il relativo valore per lo spazio corrente.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Questa funzione restituisce il valore dello spazio corrente.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Questa funzione restituisce i valori storici dello spazio corrente.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="device"></a>Dispositivo

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Metodi per il dispositivo

#### <a name="parent--space"></a>Parent() ⇒ `space`

Questa funzione restituisce lo spazio padre del dispositivo corrente.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Questa funzione restituisce i sensori figlio del dispositivo corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Questa funzione restituisce la proprietà estesa e il relativo valore per il dispositivo corrente.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="sensor"></a>Sensore

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Metodi relativi al sensore

#### <a name="space--space"></a>Space() ⇒ `space`

Questa funzione restituisce lo spazio padre del sensore corrente.

#### <a name="device--device"></a>Device() ⇒ `device`

Questa funzione restituisce il dispositivo padre del sensore corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Questa funzione restituisce la proprietà estesa e il relativo valore per il sensore corrente.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="value--value"></a>Value() ⇒ `value`

Questa funzione restituisce il valore del sensore corrente.

#### <a name="history--value"></a>History() ⇒ `value[]`

Questa funzione restituisce i valori storici del sensore corrente.

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| Parametro  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="value"></a>Valore

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Proprietà estesa

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare endpoint di Gemelli digitali di Azure](how-to-egress-endpoints.md) a cui inviare eventi.

- Per altre informazioni sugli endpoint di Gemelli digitali di Azure, vedere [altre informazioni sugli endpoint](concepts-events-routing.md).
