---
title: Guida per gli sviluppatori-anteprima Plug and Play Microsoft Docs
description: Descrizione della modellazione dei dispositivi per gli sviluppatori Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6d5247454fe65e5539a2401330192f1db9a65114
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880566"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guida per gli sviluppatori di modelli di anteprima Plug and Play

Il Plug and Play anteprima consente di creare dispositivi che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play. IoT Central è un esempio di un'applicazione con funzionalità Plug and Play.

Per creare un dispositivo Plug and Play, è necessario creare una descrizione del dispositivo. La descrizione viene eseguita con un linguaggio di definizione semplice denominato Digital Gemini Definition Language (DTDL).

## <a name="device-capability-model"></a>Modello di funzionalità del dispositivo

Con DTDL è possibile creare un _modello di funzionalità del dispositivo_ per descrivere le parti del dispositivo. Un dispositivo molto comune è costituito da:

- Parti personalizzate, ovvero le operazioni che rendono univoco il dispositivo.
- Parti standard, ovvero cose comuni a tutti i dispositivi.

Queste parti sono denominate _interfacce_ in un modello di funzionalità del dispositivo. Le interfacce definiscono i dettagli di ogni parte implementata dal dispositivo.

L'esempio seguente mostra il modello di funzionalità del dispositivo per un dispositivo termostato:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Un modello di funzionalità include alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un semplice nome di risorsa uniforme.
- `@type`: dichiara che questo oggetto è un modello di funzionalità.
- `@context`: specifica la versione di DTDL usata per il modello di funzionalità.
- `implements`: elenca le interfacce implementate dal dispositivo.

Ogni voce dell'elenco di interfacce nella sezione Implements include:

- `name`: nome di programmazione dell'interfaccia.
- `schema`: interfaccia implementata dal modello di funzionalità.

Sono disponibili ulteriori campi facoltativi che è possibile utilizzare per aggiungere ulteriori dettagli al modello di funzionalità, ad esempio il nome visualizzato e la descrizione. Le interfacce dichiarate all'interno di un modello di funzionalità possono essere considerate come componenti del dispositivo. Per l'anteprima pubblica, è possibile che l'elenco di interfacce includa una sola voce per schema.

## <a name="interface"></a>Interfaccia

Con DTDL è possibile descrivere le funzionalità del dispositivo usando le interfacce. Le interfacce descrivono le _Proprietà_, la telemetria e i _comandi_ che una parte del dispositivo implementa:

- [https://login.microsoftonline.com/common/](`Properties`). Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Usare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato di spegnimento di un pompa di raffreddamento. Le proprietà possono anche rappresentare le proprietà di base dei dispositivi, ad esempio la versione del firmware del dispositivo. È possibile dichiarare le proprietà in sola lettura o in scrittura.
- `Telemetry`. I campi di telemetria rappresentano le misurazioni dai sensori. Ogni volta che il dispositivo acquisisce una misura del sensore, deve inviare un evento di telemetria contenente i dati del sensore.
- [https://login.microsoftonline.com/consumers/](`Commands`). I comandi rappresentano i metodi che gli utenti del dispositivo possono eseguire sul dispositivo. Ad esempio, un comando reset o un comando per attivare o disattivare un ventilatore.

Nell'esempio seguente viene illustrata l'interfaccia per un dispositivo termostato:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/Interface.json"
}
```

In un'interfaccia sono presenti alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un semplice nome di risorsa uniforme.
- `@type`: dichiara che questo oggetto è un'interfaccia.
- `@context`: specifica la versione di DTDL usata per l'interfaccia.
- `contents`: elenca le proprietà, la telemetria e i comandi che compongono il dispositivo.

In questo semplice esempio esiste un solo campo di telemetria. Una descrizione del campo minima presenta:

- `@type`: specifica il tipo di funzionalità: `Telemetry`, `Property`o `Command`.
- `name`: fornisce il nome del valore di telemetria.
- `schema`: specifica il tipo di dati per i dati di telemetria. Questo valore può essere un tipo primitivo, ad esempio Double, Integer, Boolean o String. Sono supportati anche tipi di oggetti complessi, matrici e mappe.

Altri campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

### <a name="properties"></a>Properties

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura indicano che il dispositivo segnala gli aggiornamenti del valore della proprietà all'hub. L'hub Internet delle cose non può impostare il valore di una proprietà di sola lettura.

È anche possibile contrassegnare una proprietà come scrivibile in un'interfaccia. Un dispositivo può ricevere un aggiornamento a una proprietà scrivibile dall'hub Internet e segnalare gli aggiornamenti del valore della proprietà all'hub.

Non è necessario che i dispositivi siano connessi per impostare i valori delle proprietà. I valori aggiornati vengono trasferiti quando il dispositivo si connette successivamente all'hub. Questo comportamento si applica sia alle proprietà di sola lettura che a quelle scrivibili.

Non usare le proprietà per inviare dati di telemetria dal dispositivo. Una proprietà di sola lettura, ad `temperatureSetting=80` esempio, deve indicare che la temperatura del dispositivo è stata impostata su 80 e che il dispositivo sta provando a raggiungere o rimanere in corrispondenza della temperatura.

Per le proprietà scrivibili, l'applicazione del dispositivo restituisce un codice di stato desiderato, una versione e una descrizione per indicare se ha ricevuto e applicato il valore della proprietà.

### <a name="telemetry"></a>Telemetria

Per impostazione predefinita, l'hub Internet instrada tutti i messaggi di telemetria dai dispositivi al relativo endpoint per il [servizio incorporato (**messaggi/eventi**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) compatibile con [Hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/).

È possibile usare [endpoint personalizzati e regole di routing dell'hub](../iot-hub/iot-hub-devguide-messages-d2c.md) Internet per inviare dati di telemetria ad altre destinazioni, ad esempio l'archiviazione BLOB o altri hub eventi. Le regole di routing utilizzano le proprietà del messaggio per selezionare i messaggi.

### <a name="commands"></a>Comandi:

I comandi sono sincroni o asincroni. Per impostazione predefinita, un comando sincrono deve essere eseguito entro 30 secondi e il dispositivo deve essere connesso all'arrivo del comando. Se il dispositivo risponde nel tempo o il dispositivo non è connesso, il comando ha esito negativo.

Usare i comandi asincroni per le operazioni a esecuzione prolungata. Il dispositivo invia le informazioni di stato usando i messaggi di telemetria. Questi messaggi di stato hanno le proprietà di intestazione seguenti:

- `iothub-command-name`: nome del comando, ad esempio `UpdateFirmware`.
- `iothub-command-request-id`: ID richiesta generato sul lato server e inviato al dispositivo nella chiamata iniziale.
- `iothub-interface-id`:  ID dell'interfaccia su cui è definito questo comando, ad esempio `urn:example:AssetTracker:1`.
 `iothub-interface-name`: nome dell'istanza di questa interfaccia, ad esempio `myAssetTracker`.
- `iothub-command-statuscode`: codice di stato restituito dal dispositivo, ad esempio `202`.

## <a name="register-a-device"></a>Registrare un dispositivo

Il Plug and Play di Internet delle cose rende più semplice pubblicizzare le funzionalità del dispositivo. Con il Plug and Play Internet delle cose, dopo che il dispositivo si connette all'hub Internet, è necessario registrare il modello di funzionalità del dispositivo. La registrazione consente ai clienti di usare le funzionalità Plug and Play del dispositivo.

Questa guida illustra come registrare un dispositivo usando l'SDK per dispositivi Azure per dispositivi per C.

Per ogni interfaccia implementata dal dispositivo, è necessario creare un'interfaccia e connetterla alla relativa implementazione.

Per l'interfaccia del termostato illustrata in precedenza, l'uso di C SDK consente di creare e connettere l'interfaccia del termostato alla relativa implementazione:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Ripetere questo codice per ogni interfaccia implementata dal dispositivo.

Dopo aver creato un'interfaccia, registrare il modello di funzionalità e le interfacce del dispositivo con l'hub Internet delle cose:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Usare un dispositivo

Internet delle cose Plug and Play consente di usare i dispositivi che hanno registrato le proprie funzionalità con l'hub Internet delle cose. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un Plug and Play dispositivo connesso all'hub Internet delle cose, usare l'API REST dell'hub Internet delle cose o uno degli SDK del linguaggio Internet delle cose. Gli esempi seguenti usano l'API REST dell'hub Internet.

Per ottenere il valore di una proprietà del dispositivo, ad esempio la versione del`fwVersion`firmware () `DeviceInformation` nell'interfaccia del termostato, si usa l'API REST di Digital gemelli.

Se il dispositivo termostato viene `t-123`chiamato, si ottengono tutte le proprietà implementate dal dispositivo con una chiamata Get dell'API REST:

```REST
GET /digitalTwins/t-123/interfaces
```

Più in generale, si accede a tutte le proprietà con questo modello `{device-id}` API REST in cui è l'identificatore del dispositivo:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se si conosce il nome dell'interfaccia e si desidera ottenere proprietà per l'interfaccia specifica, definire l'ambito della richiesta a un'interfaccia specifica in base al nome:

```REST
GET /digitalTwins/t-123/interfaces/info
```

Più in generale, è possibile accedere alle proprietà di un'interfaccia specifica tramite questo modello API `device-id` Rest, dove è l'identificatore per `{interface-name}` il dispositivo e è il nome dell'interfaccia:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

È possibile chiamare direttamente i comandi del dispositivo Plug and Play. Se l' `Thermostat` interfaccia `t-123` nel dispositivo ha un `restart` comando, è possibile chiamarla con una chiamata all'API REST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Più in generale, i comandi possono essere chiamati tramite questo modello API REST:

- `device-id`: identificatore per il dispositivo.
- `interface-name`: nome dell'interfaccia dalla sezione Implements nel modello di funzionalità del dispositivo.
- `command-name`: nome del comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [SDK per dispositivi C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
