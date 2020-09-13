---
title: Usare i modelli Plug and Play in una soluzione | Microsoft Docs
description: In qualità di generatore di soluzioni, informazioni su come usare i modelli Plug and Play nella soluzione Internet delle cose.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9752589c8863cc911369225d268035d9f61c0273
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032028"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Usare i modelli Plug and Play in una soluzione Internet delle cose

Questo articolo descrive come, in una soluzione Internet delle cose, è possibile identificare l'ID modello di un dispositivo Plug and Play e quindi recuperare la definizione del modello.

Esistono due categorie generali di una soluzione Internet delle cose:

- Una *soluzione compilata appositamente* funziona con un set di modelli noto per i dispositivi Plug and Play che si connetteranno alla soluzione. Questi modelli vengono usati quando si sviluppa la soluzione.

- Una soluzione *basata su modelli* può funzionare con il modello di qualsiasi plug and Play dispositivo. La creazione di una soluzione basata su modelli è più complessa, ma il vantaggio è che la soluzione funziona con tutti i dispositivi che possono essere aggiunti in futuro. Una soluzione di Internet delle cose basata su modelli recupera un modello e lo usa per determinare i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Per usare un modello Plug and Play, una soluzione Internet delle cose:

1. Identifica l'ID modello del modello implementato dalla Plug and Play dispositivo connesso alla soluzione.

1. Usa l'ID modello per recuperare la definizione del modello del dispositivo connesso da un repository di modelli o da un archivio personalizzato.

## <a name="identify-model-id"></a>Identificazione ID modello

Quando un Plug and Play dispositivo si connette all'hub Internet, registra l'ID del modello implementato con l'hub Internet.

L'hub Internet delle cose informa la soluzione con l'ID del modello di dispositivo come parte del flusso di connessione del dispositivo.

Una soluzione può ottenere l'ID del modello del Plug and Play dispositivo usando uno dei tre metodi seguenti:

### <a name="get-device-twin-api"></a>Ottenere l'API del dispositivo gemello

La soluzione può usare l'API [get device Twin](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) per recuperare l'ID modello del dispositivo Plug and Play.

Nel frammento di risposta del dispositivo gemello seguente `modelId` contiene l'ID del modello di un dispositivo Plug and Play:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Ottenere l'API del dispositivo gemello digitale

La soluzione può usare l'API di [Get Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) per recuperare l'ID modello del modello implementato dal dispositivo Plug and Play.

Nel seguente frammento di risposta del dispositivo gemello digitale `$metadata.$model` contiene l'ID del modello di un dispositivo Plug and Play:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Notifica degli eventi di modifica del dispositivo gemello digitale

Una connessione del dispositivo comporta una notifica degli [eventi di modifica digitale del dispositivo gemello](concepts-digital-twin.md#digital-twin-change-events) . Una soluzione deve sottoscrivere questa notifica degli eventi. Per informazioni su come abilitare il routing per gli eventi gemelli digitali, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

La soluzione può usare l'evento illustrato nel frammento di codice seguente per ottenere informazioni sul dispositivo Plug and Play che si connette e ottenere il relativo ID modello:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Recuperare una definizione di modello

Una soluzione USA l'ID modello identificato sopra per recuperare la definizione del modello corrispondente.

Una soluzione può ottenere la definizione del modello usando una delle opzioni seguenti:

### <a name="model-repository"></a>Repository modelli

Le soluzioni possono usare il [repository del modello](concepts-model-repository.md) per recuperare i modelli. I generatori di dispositivi o i generatori di soluzioni devono caricare i propri modelli nel repository in anticipo, in modo che la soluzione possa recuperarli.

Dopo aver identificato l'ID modello per una nuova connessione del dispositivo, attenersi alla procedura seguente:

1. Recuperare la definizione del modello usando l'ID modello del repository del modello. Per altre informazioni, vedere [ottenere i modelli](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync).

1. Utilizzando la definizione del modello del dispositivo connesso, è possibile enumerare le funzionalità del dispositivo.

1. Usando le funzionalità enumerate del dispositivo, è possibile consentire agli utenti di [interagire con il dispositivo](quickstart-service-node.md).

### <a name="custom-store"></a>Archivio personalizzato

Le soluzioni possono archiviare queste definizioni di modello in un file system locale, in un archivio file pubblico o usare un'implementazione personalizzata.

Dopo aver identificato l'ID modello per una nuova connessione del dispositivo, attenersi alla procedura seguente:

1. Recuperare la definizione del modello usando l'ID modello dall'archivio personalizzato.

1. Utilizzando la definizione del modello del dispositivo connesso, è possibile enumerare le funzionalità del dispositivo. 

1. Usando le funzionalità enumerate del dispositivo, è possibile consentire agli utenti di [interagire con il dispositivo](quickstart-service-node.md).  

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come integrare i modelli Plug and Play in una soluzione Internet delle cose, alcuni passaggi successivi suggeriti sono:

- [Interagire con un dispositivo dalla soluzione](quickstart-service-node.md)
- [API REST Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
