---
title: Guida per gli sviluppatori - IoT Plug and Play Preview Documenti Microsoft
description: Descrizione della modellazione dei dispositivi per gli sviluppatori IoT Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605214"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guida per gli sviluppatori di modelli IoT Plug and Play Preview

IoT Plug and Play Preview consente di creare dispositivi che annunciano le loro funzionalità alle applicazioni IoT di Azure.IoT Plug and Play Preview lets you build devices that advertise their capabilities to Azure IoT applications. I dispositivi Plug and Play IoT non richiedono la configurazione manuale quando un cliente li connette alle applicazioni abilitate per IoT Plug e Play. IoT Central è un esempio di un'applicazione IoT Plug and Play-enabled.

Per creare un dispositivo IoT Plug and Play, devi creare una descrizione del dispositivo. La descrizione viene eseguita con un linguaggio di definizione semplice denominato Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Modello di funzionalità di dispositivo

Con DTDL, si crea un modello di _funzionalità del dispositivo_ per descrivere le parti del dispositivo. Un tipico dispositivo IoT è costituito da:

- Parti personalizzate, che sono le cose che rendono il vostro dispositivo unico.
- Parti standard, che sono cose comuni a tutti i dispositivi.

Queste parti sono chiamate _interfacce_ in un modello di funzionalità del dispositivo. Le interfacce definiscono i dettagli di ogni parte implementata dal dispositivo.

L'esempio seguente mostra il modello di funzionalità del dispositivo per un dispositivo termostato:The following example shows the device capability model for a thermostat device:

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
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Un modello di funzionalità ha alcuni campi obbligatori:A capability model has some required fields:

- `@id`: ID univoco sotto forma di un nome di risorsa uniforme semplice.
- `@type`: dichiara che questo oggetto è un modello di funzionalità.
- `@context`: specifica la versione DTDL utilizzata per il modello di funzionalità.
- `implements`: elenca le interfacce implementate dal dispositivo.

Ogni voce nell'elenco di interfacce nella sezione implements ha un:

- `name`: il nome di programmazione dell'interfaccia.
- `schema`: l'interfaccia implementata dal modello di funzionalità.

Sono disponibili campi facoltativi aggiuntivi che è possibile utilizzare per aggiungere ulteriori dettagli al modello di funzionalità, ad esempio il nome visualizzato e la descrizione. Le interfacce dichiarate all'interno di un modello di funzionalità possono essere considerate come componenti del dispositivo. Per l'anteprima pubblica, l'elenco di interfacce può avere una sola voce per schema.

## <a name="interface"></a>Interfaccia

Con DTDL, si descrivono le funzionalità del dispositivo tramite interfacce. Le interfacce descrivono le _proprietà_, i _dati di telemetria_e i _comandi_ che una parte del dispositivo implementa:

- `Properties`. Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Utilizzare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato di on-off di una pompa di refrigerante. Le proprietà possono anche rappresentare le proprietà di base del dispositivo, ad esempio la versione firmware del dispositivo. È possibile dichiarare le proprietà come di sola lettura o scrivibili.
- `Telemetry`. I campi di telemetria rappresentano le misurazioni dei sensori. Ogni volta che il dispositivo esegue una misurazione del sensore, deve inviare un evento di telemetria contenente i dati del sensore.
- `Commands`. I comandi rappresentano i metodi che gli utenti del dispositivo possono eseguire sul dispositivo. Ad esempio, un comando di reset o un comando per attivare o disattivare una ventola.

L'esempio seguente mostra l'interfaccia per un dispositivo termostato:The following example shows the interface for a thermostat device:

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
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Un'interfaccia ha alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un nome di risorsa uniforme semplice.
- `@type`: dichiara che questo oggetto è un'interfaccia.
- `@context`: specifica la versione DTDL utilizzata per l'interfaccia.
- `contents`: elenca le proprietà, i dati di telemetria e i comandi che costituiscono il dispositivo.

In questo semplice esempio è disponibile un solo campo di telemetria. Una descrizione minima del campo ha:

- `@type`: specifica il tipo di `Telemetry` `Property`funzionalità: `Command`, , o .
- `name`: fornisce il nome del valore di telemetria.
- `schema`: specifica il tipo di dati per i dati di telemetria. Questo valore può essere un tipo primitivo, ad esempio double, integer, boolean o string. Sono supportati anche tipi di oggetti complessi, matrici e mappe.

Altri campi facoltativi, ad esempio il nome visualizzato e la descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

### <a name="properties"></a>Proprietà

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura indicano che il dispositivo segnala gli aggiornamenti del valore della proprietà all'hub IoT.Read-only properties mean that the device reports property value updates to your IoT hub. L'hub IoT non può impostare il valore di una proprietà di sola lettura.

È anche possibile contrassegnare una proprietà come scrivibile in un'interfaccia. Un dispositivo può ricevere un aggiornamento a una proprietà scrivibile dall'hub IoT e segnalare gli aggiornamenti del valore della proprietà all'hub.

I dispositivi non devono essere connessi per impostare i valori delle proprietà. I valori aggiornati vengono trasferiti quando il dispositivo si connette successivamente all'hub. Questo comportamento si applica alle proprietà di sola lettura e scrivibili.

Non usare le proprietà per inviare dati di telemetria dal dispositivo. Ad esempio, una proprietà `temperatureSetting=80` readonly come dovrebbe significare che la temperatura del dispositivo è stata impostata su 80 e il dispositivo sta tentando di raggiungere o rimanere a questa temperatura o rimanere a questa temperatura.

Per le proprietà scrivibili, l'applicazione del dispositivo restituisce un codice di stato, una versione e una descrizione desiderati per indicare se ha ricevuto e applicato il valore della proprietà.

### <a name="telemetry"></a>Telemetria

Per impostazione predefinita, l'hub IoT instrada tutti i messaggi di telemetria dai dispositivi all'endpoint predefinito rivolto [al servizio (**messaggi/eventi**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) compatibile con Hub [eventi.](https://azure.microsoft.com/documentation/services/event-hubs/)

È possibile usare [gli endpoint personalizzati dell'hub IoT e le regole](../iot-hub/iot-hub-devguide-messages-d2c.md) di routing per inviare dati di telemetria ad altre destinazioni, ad esempio l'archiviazione BLOB o altri hub eventi. Le regole di routing utilizzano le proprietà dei messaggi per selezionare i messaggi.

### <a name="commands"></a>Comandi:

I comandi sono sincroni o asincroni. Un comando sincrono deve essere eseguito entro 30 secondi per impostazione predefinita e il dispositivo deve essere connesso all'arrivo del comando. Se il dispositivo risponde in tempo o il dispositivo non è connesso, il comando non riesce.

Utilizzare comandi asincroni per le operazioni a esecuzione prolungata. Il dispositivo invia informazioni sullo stato di avanzamento tramite messaggi di telemetria. Questi messaggi di stato hanno le seguenti proprietà di intestazione:

- `iothub-command-name`: il nome del `UpdateFirmware`comando, ad esempio .
- `iothub-command-request-id`: l'ID richiesta generato sul lato server e inviato al dispositivo nella chiamata iniziale.
- `iothub-interface-id`: l'ID dell'interfaccia su cui `urn:example:AssetTracker:1`è definito questo comando, ad esempio .
 `iothub-interface-name`: il nome dell'istanza `myAssetTracker`di questa interfaccia, ad esempio .
- `iothub-command-statuscode`: il codice di stato restituito `202`dal dispositivo, ad esempio .

## <a name="register-a-device"></a>Registrare un dispositivo

IoT Plug and Play semplifica la pubblicità delle funzionalità del dispositivo. Con IoT Plug and Play, dopo che il dispositivo si è connesso all'hub IoT, devi registrare il modello di funzionalità del dispositivo. La registrazione consente ai clienti di utilizzare le funzionalità Plug and Play IoT del dispositivo.

Questa guida illustra come registrare un dispositivo usando l'SDK del dispositivo Azure IoT per C.This guide shows you how to register a device using the Azure IoT Device SDK for C.

Per ogni interfaccia implementata dal dispositivo, è necessario creare un'interfaccia e connetterla alla relativa implementazione.

Per l'interfaccia del termostato illustrata in precedenza, utilizzando l'SDK di C, creare e connettere l'interfaccia del termostato alla relativa implementazione:

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

Dopo aver creato un'interfaccia, registrare il modello di funzionalità del dispositivo e le interfacce con l'hub IoT:After you create an interface, register your device capability model and interfaces with your IoT hub:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Utilizzare un dispositivo

IoT Plug and Play ti consente di usare dispositivi che hanno registrato le loro funzionalità con il tuo hub IoT. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un dispositivo Plug and Play IoT connesso all'hub IoT, usa l'API REST dell'hub IoT o uno degli SDK del linguaggio IoT. The following examples use the IoT Hub REST API. La versione corrente dell'API è `2019-07-01-preview`. Aggiungi `?api-version=2019-07-01-preview` alle chiamate REST PI.

Per ottenere il valore di una proprietà del`fwVersion`dispositivo, `DeviceInformation` ad esempio la versione del firmware ( ) nell'interfaccia nel termostato, si utilizza l'API REST dei gemelli digitali.

Se il dispositivo termostato `t-123`è chiamato , si ottengono tutte le proprietà su tutte le interfacce implementate dal dispositivo con una chiamata GET all'API REST:

```REST
GET /digitalTwins/t-123/interfaces
```

Più in generale, tutte le proprietà su tutte `{device-id}` le interfacce sono accessibili con questo modello di API REST, dove è l'identificatore per il dispositivo:More generally, all properties on all interfaces are accessed with this REST API template where is the identifier for the device:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se si conosce il nome dell'interfaccia, ad `deviceInformation`esempio , e si desidera ottenere le proprietà per l'interfaccia specifica, definire l'ambito della richiesta per un'interfaccia specifica in base al nome:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Più in generale, è possibile accedere alle proprietà `device-id` per un'interfaccia specifica `{interface-name}` tramite questo modello di API REST, dove è l'identificatore del dispositivo ed è il nome dell'interfaccia:More generally, properties for a specific interface can be accessed through this REST API template where is the identifier for the device and is the name of the interface:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

È possibile chiamare direttamente i comandi del dispositivo IoT Plug and Play. Se `Thermostat` l'interfaccia `t-123` nel `restart` dispositivo dispone di un comando, è possibile chiamarla con una chiamata POST all'API REST:If the interface in the device has a command, you can call it with a REST API POST call:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Più in generale, i comandi possono essere chiamati tramite questo modello di API REST:More generally, commands can be called through this REST API template:

- `device-id`: l'identificatore del dispositivo.
- `interface-name`: il nome dell'interfaccia dalla sezione implements nel modello di funzionalità del dispositivo.
- `command-name`: il nome del comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a conoscere la modellazione dei dispositivi, ecco alcune risorse aggiuntive:Now that you've learned about device modeling, here are some additional resources:

- [DTDL (Digital Twin Definition Language)](https://aka.ms/DTDL)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
