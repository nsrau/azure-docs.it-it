---
title: Guida per gli sviluppatori di servizi-Plug and Play Internet | Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori di servizi
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7728af831b26bff19f347e5b85db6420e7966ed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580482"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guida per gli sviluppatori del servizio Plug and Play

Internet delle cose Plug and Play consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

Internet delle cose Plug and Play consente di usare i dispositivi che hanno annunciato l'ID modello con l'hub Internet delle cose. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un Plug and Play dispositivo connesso all'hub Internet delle cose, usare uno degli SDK del servizio Internet delle cose o l'API REST dell'hub Internet:

## <a name="service-sdks"></a>SDK per servizi

Per interagire con i dispositivi e i moduli, usare gli SDK dei servizi di Azure. Ad esempio, è possibile usare gli SDK del servizio per leggere e aggiornare le proprietà dei dispositivi gemelli e richiamare i comandi. Le lingue supportate includono C#, Java, Node.js e Python.

Gli SDK dei servizi consentono di accedere alle informazioni sul dispositivo da una soluzione, ad esempio un'applicazione desktop o Web. Gli SDK dei servizi includono due spazi dei nomi e modelli a oggetti che è possibile usare per recuperare l'ID modello:

- Client del servizio hub Internet.
- Client del servizio digitale gemelli.

| Linguaggio | Client del servizio hub Internet | Client Servizi gemelli digitali |
| -------- | ---------------------- | ---------------------------- |
| C#       | [Documentazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Esempio](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples/Thermostat/Program.cs)| [Esempio](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentazione](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample/thermostat-service-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/service/Thermostat.java)| [Esempio](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentazione](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentazione](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) |
| Python   | [Documentazione](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentazione](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) | 

## <a name="rest-api"></a>API REST

Gli esempi seguenti usano l'API REST dell'hub Internet per interagire con un dispositivo Plug and Play. La versione corrente dell'API è `2020-09-30` . Accodare `?api-version=2020-05-31` le chiamate a Rest pi.

> [!NOTE]
> I moduli gemelli non sono attualmente supportati dall' `digitalTwins` API.

Se il dispositivo termostato viene chiamato `t-123` , si ottengono tutte le proprietà di tutte le interfacce implementate dal dispositivo con una chiamata Get dell'API REST:

```REST
GET /digitalTwins/t-123
```

Questa chiamata includerà la proprietà JSON `$metadata.$model` con l'ID modello annunciato dal dispositivo.

Tutte le proprietà di tutte le interfacce sono accessibili con il `GET /DigitalTwin/{device-id}` modello API REST in cui `{device-id}` è l'identificatore del dispositivo:

```REST
GET /digitalTwins/{device-id}
```

È possibile chiamare direttamente i comandi del dispositivo Plug and Play. Se il `Thermostat` componente nel `t-123` dispositivo ha un `restart` comando, è possibile chiamarlo con una chiamata all'API REST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Più in generale, i comandi possono essere chiamati tramite questo modello API REST:

- `device-id`: identificatore per il dispositivo.
- `component-name`: nome dell'interfaccia dalla sezione Implements nel modello di funzionalità del dispositivo.
- `command-name`: nome del comando.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)
- [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md)
