---
title: Implementare l'individuazione del modello Plug and Play di anteprima | Microsoft Docs
description: In qualità di generatore di soluzioni, informazioni su come implementare l'individuazione dei modelli Plug and Play nella soluzione.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337382"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementare l'individuazione del modello di Plug and Play di anteprima in una soluzione Internet delle cose

Questo articolo descrive come un generatore di soluzioni può implementare l'individuazione del modello di Plug and Play di anteprima in una soluzione Internet delle cose. L'individuazione del modello descrive come:

- Dispositivi Plug and Play i dispositivi registrano l'ID del modello.
- Una soluzione Internet delle cose recupera le interfacce implementate dal dispositivo.

Esistono due categorie generali di soluzioni Internet:

- Una *soluzione* di Internet delle cose basata su uno scopo funziona con un set noto di tipi di dispositivi Plug and Play.

- Una *soluzione* di gestione delle cose basata su modelli può funzionare con qualsiasi plug and Play dispositivo. La creazione di una soluzione basata su modelli è più complessa, ma il vantaggio è che la soluzione funziona con tutti i dispositivi aggiunti in futuro.

    Per creare una soluzione di Internet delle cose basata su modelli, è necessario compilare la logica rispetto alle primitive dell'interfaccia Plug and Play: telemetria, proprietà e comandi. La logica della soluzione rappresenta un dispositivo combinando più funzionalità di telemetria, proprietà e comando.

Questo articolo descrive come implementare l'individuazione del modello in entrambi i tipi di soluzione.

## <a name="model-discovery"></a>Individuazione dei modelli

Per individuare il modello implementato da un dispositivo, una soluzione può ottenere l'ID modello usando l'individuazione basata su eventi o l'individuazione basata su gemelli:

### <a name="event-based-discovery"></a>Individuazione basata su eventi

Quando un Plug and Play dispositivo si connette all'hub Internet, registra il modello che implementa. Questa registrazione comporta una notifica degli [eventi di modifica digitale del dispositivo gemello](concepts-digital-twin.md#digital-twin-change-events) . Per informazioni su come abilitare il routing per gli eventi gemelli digitali, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

La soluzione può usare l'evento illustrato nel frammento di codice seguente per ottenere informazioni sugli elementi Plug and Play dispositivo connesso e ottenere il relativo ID modello:

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

Questo evento viene generato quando viene aggiunto o aggiornato l'ID del modello di dispositivo.

### <a name="twin-based-discovery"></a>Individuazione basata su gemelli

Se la soluzione vuole conoscere le funzionalità di un determinato dispositivo, può usare l'API di [Get Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) per recuperare le informazioni.

Nel frammento di codice gemello digitale seguente `$metadata.$model` contiene l'ID del modello di un dispositivo Plug and Play:

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

La soluzione può anche usare **Get gemelle** per recuperare l'ID modello dal dispositivo gemello, come illustrato nel frammento di codice seguente:

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

## <a name="model-resolution"></a>Risoluzione del modello

Una soluzione USA la risoluzione del modello per ottenere l'accesso alle interfacce che compongono un modello dall'ID del modello. 

- Le soluzioni possono scegliere di archiviare queste interfacce come file in una cartella locale. 
- Le soluzioni possono usare il [repository del modello](concepts-model-repository.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come individuare la soluzione di individuazione dei modelli, è possibile ottenere altre informazioni sulla [piattaforma Azure](overview-iot-plug-and-play.md) per usare altre funzionalità per la soluzione.

- [Interagire con un dispositivo dalla soluzione](quickstart-service-node.md)
- [API REST Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Esplora risorse di Azure](howto-use-iot-explorer.md)
