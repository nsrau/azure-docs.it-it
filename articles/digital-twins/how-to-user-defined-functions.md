---
title: Come usare le funzioni definite dall'utente in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come creare funzioni definite dall'utente, matcher e assegnazioni di ruolo con Gemelli digitali di Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 8094965da5fb0a5fad0313fd96e2878f86d78aa7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215498"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Come usare le funzioni definite dall'utente in Gemelli digitali di Azure

Le [funzioni definite dall'utente](./concepts-user-defined-functions.md) consentono all'utente eseguire una logica personalizzata sui messaggi di telemetria in ingresso e sui metadati del grafico spaziale, permettendo all'utente di inviare eventi agli endpoint predefiniti. In questa guida verrà illustrato un esempio correlato agli eventi relativi alla temperatura, per rilevare le letture che superano una determinata temperatura e inviare un avviso.

Negli esempi seguenti, `https://yourManagementApiUrl` fa riferimento all'URI delle API di Gemelli digitali:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourInstanceName* | Nome dell'istanza di Gemelli digitali di Azure |
| *yourLocation* | Area del server in cui è ospitata l'istanza |

## <a name="client-library-reference"></a>Informazioni di riferimento sulla libreria client

Le funzioni disponibili come metodi helper nel runtime delle funzioni definite dall'utente sono enumerate nelle [informazioni di riferimento client](#Client-Reference) seguenti.

## <a name="create-a-matcher"></a>Creare un matcher

I matcher sono oggetti del grafico che determinano quali funzioni definite dall'utente verranno eseguite per un messaggio di telemetria specifico.

Confronti di condizione validi per i matcher:

- `Equals`
- `NotEquals`
- `Contains`

Destinazioni delle condizioni valide per i matcher:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

Il matcher di esempio seguente restituirà true per ogni evento di telemetria dei sensori con tipo di dati `"Temperature"`. È possibile creare più matcher in una funzione definita dall'utente.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourManagementApiUrl* | Percorso URL completo per l'API Gestione  |
| *yourSpaceIdentifier* | Area del server in cui è ospitata l'istanza |

## <a name="create-a-user-defined-function-udf"></a>Creare una funzione definita dall'utente

Dopo aver creato i matcher, caricare il frammento di codice della funzione con la chiamata POST seguente:

> [!IMPORTANT]
> - Nelle intestazioni impostare `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Il corpo è costituito da più parti:
>   - La prima parte riguarda i metadati necessari per la funzione definita dall'utente.
>   - La seconda parte è la logica di calcolo JavaScript.
> - Sostituire nella sezione `userDefinedBoundary` i GUID `SpaceId` e `Machers`.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourManagementApiUrl* | Percorso URL completo per l'API Gestione  |

Corpo:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourSpaceIdentifier* | Identificatore dello spazio  |
| *yourMatcherIdentifier* | ID del matcher da usare |

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

Il parametro *Telemetria* espone gli attributi **SensorId** e **Messaggio** (corrispondenti a un messaggio inviato da un sensore). Il parametro *executionContext* espone i seguenti attributi:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Nel prossimo esempio, registreremo un messaggio se la lettura dei dati di telemetria del sensore supera una soglia predefinita. Se le impostazioni di diagnostica sono abilitate nell'istanza di Gemelli digitali, verranno inoltrati anche i log delle funzioni definite dall'utente:

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

Il codice seguente attiva una notifica se il livello di temperatura sale sopra il valore costante predefinito.

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

Per un esempio di codice di funzione definita dall'utente più complesso, vedere la [funzione definita dall'utente per il controllo degli spazi disponibili con aria fresca](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

È necessario creare un'assegnazione di ruolo per l'esecuzione della funzione definita dall'utente. In caso contrario, non saranno disponibili le autorizzazioni appropriate per interagire con l'API Gestione per eseguire azioni sugli oggetti del grafico. Le azioni eseguite dalla funzione definita dall'utente non sono esenti dal controllo degli accessi in base al ruolo delle API Gestione di Gemelli digitali. È possibile limitarne l'ambito specificando determinati ruoli o percorsi di controllo di accesso. Per altre informazioni, vedere la documentazione relativa a [Controllo degli accessi in base al ruolo](./security-role-based-access-control.md).

1. Eseguire una query per i ruoli e ottenere l'ID del ruolo da assegnare alla funzione definita dall'utente. Passare il valore a **RoleId**, come illustrato di seguito.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourManagementApiUrl* | Percorso URL completo per l'API Gestione  |

2. **ObjectId** corrisponderà all'ID della funzione definita dall'utente creata in precedenza.
3. Trovare il valore di **Path** eseguendo una query degli spazi con `fullpath`.
4. Copiare il valore `spacePaths` restituito. Usare quello indicato di seguito.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourManagementApiUrl* | Percorso URL completo per l'API Gestione  |
| *yourSpaceName* | Nome dello spazio da usare |

4. Ora, incollare il valore `spacePaths` restituito in **Path** per creare un'assegnazione di ruolo alla funzione definita dall'utente.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| *yourManagementApiUrl* | Percorso URL completo per l'API Gestione  |
| *yourDesiredRoleIdentifier* | Identificatore del ruolo desiderato |
| *yourUserDefinedFunctionId* | ID della funzione definita dall'utente da usare |
| *yourAccessControlPath* | Percorso di controllo di accesso |

## <a name="send-telemetry-to-be-processed"></a>Inviare i dati di telemetria da elaborare

I dati di telemetria generati dal sensore descritto nel grafico devono attivare l'esecuzione della funzione definita dall'utente che è stata caricata. Quando i dati di telemetria vengono prelevati dall'elaboratore dei dati, viene creato un piano di esecuzione per chiamare la funzione definita dall'utente.

1. Recuperare i matcher per il sensore da cui è stata generata la lettura.
1. A seconda dei matcher valutati correttamente, recuperare le funzioni definite dall'utente associate.
1. Eseguire ogni funzione definita dall'utente.

## <a name="client-reference"></a>Informazioni di riferimento client

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Dato un identificatore di spazio, recupera lo spazio dal grafico.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di spazio |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Dato un identificatore di sensore, recupera il sensore dal grafico.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di sensore |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Dato un identificatore di dispositivo, recupera il dispositivo dal grafico.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identificatore di dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Dato un identificatore di sensore e il relativo tipo di dati, recupera il valore corrente per il sensore.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Dato un identificatore di spazio e il nome del valore, recupera il valore corrente per tale proprietà dello spazio.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Dato un identificatore di sensore e il relativo tipo di dati, recupera i valori cronologici per il sensore.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType* | `string` | Tipo di dati del sensore |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Dato un identificatore di spazio e il nome del valore, recupera i valori cronologici per tale proprietà nello spazio.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Dato un identificatore di spazio, recupera gli spazi figlio per tale spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Dato un identificatore di spazio, recupera i sensori figlio per tale spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Dato un identificatore di spazio, recupera i dispositivi figlio per tale spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Dato un identificatore di dispositivo, recupera i sensori figlio per tale dispositivo padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Dato un identificatore di spazio, recupera lo spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificatore di spazio |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Dato un identificatore di sensore, recupera lo spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Dato un identificatore di dispositivo, recupera lo spazio padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificatore di dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Dato un identificatore di sensore, recupera il dispositivo padre.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di spazio, recupera la proprietà e il relativo valore dallo spazio.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *propertyName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di sensore, recupera la proprietà e il relativo valore dal sensore.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *propertyName* | `string` | Nome della proprietà del sensore |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di dispositivo, recupera la proprietà e il relativo valore dal dispositivo.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |
| *propertyName* | `string` | Nome della proprietà del dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Imposta un valore sull'oggetto sensore con il tipo di dati specificato.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |
| *value*  | `string` | value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Imposta un valore sull'oggetto spazio con il tipo di dati specificato.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *dataType* | `string` | tipo di dati |
| *value* | `string` | value |

### <a name="logmessage"></a>log(message)

Registra il messaggio seguente all'interno della funzione definita dall'utente.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Messaggio da registrare |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Invia una notifica personalizzata da consegnare.

**Tipo**: funzione globale

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificatore dell'oggetto del grafico (ad esempio, spazio/sensore/ID dispositivo)|
| *topologyObjectType*  | `string` | Ad esempio spazio/sensore/dispositivo)|
| *payload*  | `string` | Payload JSON da inviare con la notifica |

## <a name="return-types"></a>Tipi restituiti

Di seguito sono elencati i modelli che descrivono gli oggetti restituiti da quanto indicato nelle informazioni di riferimento client precedenti.

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

Restituisce lo spazio padre dello spazio corrente.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Restituisce i sensori figlio dello spazio corrente.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Restituisce i dispositivi figlio dello spazio corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Restituisce la proprietà estesa e il relativo valore per lo spazio corrente.

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Restituisce il valore dello spazio corrente.

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Restituisce i valori cronologici dello spazio corrente.

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="notifypayload"></a>Notify(payload)

Invia una notifica con il payload specificato.

| Param  | type                | DESCRIZIONE  |
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

### <a name="device-methods"></a>Metodi relativi al dispositivo

#### <a name="parent--space"></a>Parent() ⇒ `space`

Restituisce lo spazio padre del dispositivo corrente.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Restituisce i sensori figlio del dispositivo corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Restituisce la proprietà estesa e il relativo valore per il dispositivo corrente.

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="notifypayload"></a>Notify(payload)

Invia una notifica con il payload specificato.

| Param  | type                | DESCRIZIONE  |
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

Restituisce lo spazio padre del sensore corrente.

#### <a name="device--device"></a>Device() ⇒ `device`

Restituisce il dispositivo padre del sensore corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Restituisce la proprietà estesa e il relativo valore per il sensore corrente.

| Param  | type                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="value--value"></a>Value() ⇒ `value`

Restituisce il valore del sensore corrente.

#### <a name="history--value"></a>History() ⇒ `value[]`

Restituisce i valori cronologici del sensore corrente.

#### <a name="notifypayload"></a>Notify(payload)

Invia una notifica con il payload specificato.

| Param  | type                | DESCRIZIONE  |
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

### <a name="extended-property"></a>Property esteso

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare endpoint di Gemelli digitali per l'invio di eventi, vedere [Creare endpoint di Gemelli digitali](how-to-egress-endpoints.md).

Per altre informazioni sugli endpoint di Gemelli digitali, vedere [Altre informazioni sugli endpoint](concepts-events-routing.md).
