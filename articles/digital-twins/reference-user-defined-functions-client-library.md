---
title: Informazioni di riferimento sulla libreria client di funzioni definite dall'utente-dispositivi gemelli digitali di Azure | Microsoft Docs
description: Informazioni di riferimento sulla libreria client delle funzioni definite dall'utente di Gemelli digitali di Azure
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0bbf247adbeab89cf8b16ed089eb13e53cf501f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009227"
---
# <a name="user-defined-functions-client-library-reference"></a>Informazioni di riferimento sulla libreria client delle funzioni definite dall'utente

Questo documento fornisce informazioni di riferimento sulla libreria client delle funzioni definite dall'utente di Gemelli digitali di Azure.

## <a name="helper-methods"></a>Metodi di supporto

La libreria client definisce metodi di supporto per le operazioni più comuni.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Dato un identificatore di spazio, questa funzione recupera lo spazio dal grafico.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di spazio |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Dato un identificatore di sensore, questa funzione recupera il sensore dal grafico.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificatore di sensore |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Dato un identificatore di dispositivo, questa funzione recupera il dispositivo dal grafico.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identificatore di dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera il valore corrente per il sensore.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera il valore corrente per il sensore.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Dato un identificatore di sensore e il relativo tipo di dati, questa funzione recupera i valori storici per il sensore.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType* | `string` | Tipo di dati del sensore |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Dato un identificatore di spazio e il nome del valore, questa funzione recupera i valori cronologici per tale proprietà nello spazio.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *valueName* | `string` | Nome della proprietà dello spazio |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Dato un identificatore di spazio, questa funzione recupera gli spazi figlio per tale spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Dato un identificatore di spazio, questa funzione recupera i sensori figlio per tale spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Dato un identificatore di spazio, questa funzione recupera i dispositivi figlio per tale spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Dato un identificatore di dispositivo, questa funzione recupera i sensori figlio per tale dispositivo padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Dato un identificatore di spazio, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificatore di spazio |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Dato un identificatore di sensore, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Dato un identificatore di dispositivo, questa funzione recupera il proprio spazio padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificatore di dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Dato un identificatore di sensore, questa funzione recupera il proprio dispositivo padre.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificatore di sensore |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di spazio, questa funzione recupera la proprietà e il relativo valore dallo spazio.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *propertyName* | `string` | Nome della proprietà dello spazio |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di sensore, questa funzione recupera la proprietà e il relativo valore dal sensore.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *propertyName* | `string` | Nome della proprietà del sensore |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Dato un identificatore di dispositivo, questa funzione recupera la proprietà e il relativo valore dal dispositivo.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificatore di dispositivo |
| *propertyName* | `string` | Nome della proprietà del dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Questa funzione imposta un valore sull'oggetto sensore con il tipo di dati specificato.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificatore di sensore |
| *dataType*  | `string` | Tipo di dati del sensore |
| *value*  | `string` | Valore |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Questa funzione imposta un valore sull'oggetto spazio con il tipo di dati specificato.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificatore di spazio |
| *dataType* | `string` | Tipo di dati |
| *value* | `string` | Valore |

### <a name="logmessage"></a>log(message)

Questa funzione registra il messaggio seguente all'interno della funzione definita dall'utente.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Messaggio da registrare |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Questa funzione invia una notifica personalizzata da consegnare.

**Tipo**: funzione globale

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificatore dell'oggetto del grafico. Spazi, sensore e ID del dispositivo sono esempi.|
| *topologyObjectType*  | `string` | Sensore e dispositivo sono esempi.|
| *payload*  | `string` | Payload JSON da inviare con la notifica. |

## <a name="return-types"></a>Tipi restituiti

Di seguito sono descritti i modelli di risposta restituiti dai metodi di supporto di riferimento client.

### <a name="space"></a>Spazio

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Questa funzione restituisce il valore dello spazio corrente.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Questa funzione restituisce i valori storici dello spazio corrente.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome del valore |

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="device"></a>Dispositivo

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Metodi per il dispositivo

#### <a name="parent--space"></a>Parent() ⇒ `space`

Questa funzione restituisce lo spazio padre del dispositivo corrente.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Questa funzione restituisce i sensori figlio del dispositivo corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Questa funzione restituisce la proprietà estesa e il relativo valore per il dispositivo corrente.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="sensor"></a>Sensore

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Metodi relativi al sensore

#### <a name="space--space"></a>Space() ⇒ `space`

Questa funzione restituisce lo spazio padre del sensore corrente.

#### <a name="device--device"></a>Device() ⇒ `device`

Questa funzione restituisce il dispositivo padre del sensore corrente.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Questa funzione restituisce la proprietà estesa e il relativo valore per il sensore corrente.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome della proprietà estesa |

#### <a name="value--value"></a>Value() ⇒ `value`

Questa funzione restituisce il valore del sensore corrente.

#### <a name="history--value"></a>History() ⇒ `value[]`

Questa funzione restituisce i valori storici del sensore corrente.

#### <a name="notifypayload"></a>Notify(payload)

Questa funzione invia una notifica con il payload specificato.

| .  | digitare                | DESCRIZIONE  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Payload JSON da includere nella notifica |

### <a name="value"></a>Valore

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Proprietà estesa

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [funzioni definite dall'utente di Gemelli digitali di Azure](./concepts-user-defined-functions.md).

- Informazioni su come [creare funzioni definite dall'utente](./how-to-user-defined-functions.md).

- Informazioni su come [eseguire il debug di funzioni definite dall'utente](./how-to-diagnose-user-defined-functions.md).
