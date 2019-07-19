---
title: Esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure | Microsoft Docs
description: Come esportare dati dall'applicazione Azure IoT Central in Hub eventi di Azure o nel bus di servizio di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: c6f10352646350152c5aac795885231697e81fe7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850200"
---
# <a name="export-your-data-in-azure-iot-central"></a>Esportare i dati in Azure IoT Central

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati in **Hub eventi di Azure**e nelle istanze del **bus di servizio di Azure** . È possibile esportare **misurazioni**, **dispositivi** e **modelli di dispositivo** in una destinazione per ottenere informazioni dettagliate e analizzare i percorsi ad accesso frequente. La funzionalità include l'attivazione di regole personalizzate in Analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati in App per la logica di Azure o la trasformazione dei dati e il loro passaggio a Funzioni di Azure. 

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.


## <a name="prerequisites"></a>Prerequisiti

- È necessario essere amministratore nell'applicazione IoT Central

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

Se non è presente un hub eventi o un bus di servizio per l'esportazione, attenersi alla procedura seguente:

## <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Scegliere una sottoscrizione. 

    > [!Note] 
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.
3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

## <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Scegliere una sottoscrizione. 

    > [!Note] 
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.


## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di un hub eventi/destinazione del bus di servizio in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati. 

1. Accedere all'applicazione IoT Central.

2. Nel menu a sinistra selezionare **esportazione dati continua**.

    > [!Note]
    > Se questa opzione non è presente nel menu a sinistra, significa che non si è amministratori dell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

    ![Creare un nuovo hub eventi per l'esportazione continua dei dati](media/howto-export-data/export_menu1.png)

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere uno degli **Hub eventi di Azure** o il **bus di servizio di Azure** come destinazione dell'esportazione. 

    > [!NOTE] 
    > Il numero massimo di esportazioni per app è cinque. 

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data/export_new1.png)

4. Nella casella di riepilogo a discesa selezionare lo spazio dei nomi di **Hub eventi/spazio dei nomi del bus di servizio**. È anche possibile selezionare l'ultima opzione dell'elenco, ossia **Immettere una stringa di connessione**. 

    > [!NOTE] 
    > Saranno visibili solo gli account di archiviazione, gli spazi dei nomi di hub eventi o gli spazi dei nomi del bus di servizio nella **stessa sottoscrizione dell'app IoT Central**. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.

    > [!NOTE] 
    > Per le app in versione di valutazione per 7 giorni l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. A queste app non è infatti associata una sottoscrizione di Azure.

    ![Creare un nuovo hub eventi per l'esportazione continua dei dati](media/howto-export-data/export_create1.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:
    - Hub eventi o bus di servizio, passare allo spazio dei nomi nel portale di Azure.
        - In **Impostazioni**selezionare **criteri di accesso condiviso**
        - Scegliere il valore predefinito **RootManageSharedAccessKey** oppure crearne uno nuovo
        - Copiare la stringa di connessione primaria o secondaria.
 
6. Nella casella di riepilogo a discesa scegliere un hub eventi, una coda o un argomento.

7. In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).

6. Per attivare l'esportazione continua dei dati, verificare che l'opzione **Esportazione dati** sia **attivata**. Selezionare **Salva**.

    ![Configurare l'esportazione continua dei dati](media/howto-export-data/export_list1.png)

7. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure

I dati di misurazioni, dispositivi e modelli di dispositivo vengono esportati nell'hub eventi oppure in una coda o un argomento del bus di servizio in tempo quasi reale. I dati delle misurazioni esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori delle misurazioni stesse. I dati dei dispositivi esportati contengono le modifiche alle proprietà e impostazioni di tutti i dispositivi, mentre i dati dei modelli di dispositivo esportati contengono le modifiche a tutti i modelli di dispositivo. I dati esportati sono contenuti nella proprietà "body" in formato JSON.

> [!NOTE]
> Quando si sceglie un bus di servizio come destinazione dell'esportazione, per le code e gli argomenti **non devono essere abilitati le sessioni e il rilevamento dei duplicati**. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="measurements"></a>Misure

Un nuovo messaggio viene esportato poco dopo che IoT Central lo ha ricevuto da un dispositivo. Ogni messaggio esportato in Hub eventi e nel bus di servizio contiene il messaggio completo inviato dal dispositivo nella proprietà "body" in formato JSON. 

> [!NOTE]
> I dispositivi che inviano le misurazioni sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati dei dispositivi e correlare ogni messaggio usando il **connectionDeviceId** che corrisponde al **deviceId** del messaggio del dispositivo.

L'esempio seguente mostra un messaggio relativo ai dati sulle misurazioni ricevuti nell'hub eventi o in una coda o un argomento del bus di servizio.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Dispositivi

I messaggi contenenti i dati dei dispositivi vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Questo significa che ogni pochi minuti viene recapitato un batch di messaggi con dati relativi a
- I nuovi dispositivi aggiunti
- I dispositivi i cui valori di proprietà e impostazioni sono stati modificati

Ogni messaggio rappresenta una o più modifiche a un dispositivo dopo l'esportazione dell'ultimo messaggio. Le informazioni inviate in ogni messaggio includono:
- `id` del dispositivo in IoT Central
- `name` ID del dispositivo
- `deviceId` dal [Servizio di provisioning di dispositivi](https://aka.ms/iotcentraldocsdps)
- Informazioni sul modello di dispositivo
- Valori delle proprietà
- Valori delle impostazioni

> [!NOTE]
> I dispositivi eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.
>
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, assicurarsi di esportare anche i dati del modello.

L'esempio seguente mostra un messaggio relativo ai dati dei dispositivi nell'hub eventi o in una coda o un argomento del bus di servizio:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Modelli di dispositivo

I messaggi contenenti i dati dei modelli di dispositivo vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Questo significa che ogni pochi minuti viene recapitato un batch di messaggi con dati relativi a
- I nuovi modelli di dispositivo aggiunti
- I modelli di dispositivo le cui definizioni di misurazioni, proprietà e impostazioni sono state modificate

Ogni messaggio rappresenta una o più modifiche a un modello di dispositivo dopo l'esportazione dell'ultimo messaggio. Le informazioni inviate in ogni messaggio includono:
- `id` del modello del dispositivo
- `name` del modello del dispositivo
- `version` del modello del dispositivo
- Tipi di dati delle misurazioni e valori min/max
- Tipi di dati delle proprietà e valori predefiniti
- Tipi di dati delle impostazioni e valori predefiniti

> [!NOTE]
> I modelli di dispositivo eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

L'esempio seguente mostra un messaggio relativo ai dati dei modelli di dispositivo nell'hub eventi o in una coda o un argomento del bus di servizio:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è imparato a esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come attivare Funzioni di Azure](howto-trigger-azure-functions.md)
