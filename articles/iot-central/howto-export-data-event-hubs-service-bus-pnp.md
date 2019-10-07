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
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973198"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Esportare i dati in IoT Central di Azure (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati in **Hub eventi di Azure**e nelle istanze del **bus di servizio di Azure** . È possibile esportare i dati di **telemetria**, i **dispositivi**e i **modelli di dispositivo** nella propria destinazione per informazioni dettagliate sul percorso e analisi. Questo include l'attivazione di regole personalizzate in analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati in app per la logica di Azure o il passaggio tramite funzioni di Azure da trasformare.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere amministratore nell'applicazione IoT Central

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

Se non è presente un hub eventi o un bus di servizio per l'esportazione, attenersi alla procedura seguente per crearne uno:

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Scegliere una sottoscrizione. 

    > [!Note] 
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.
3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Scegliere una sottoscrizione. 

    > [!Note] 
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.


## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di un hub eventi/destinazione del bus di servizio in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati. 

1. Accedere all'applicazione IoT Central.

2. Nel menu a sinistra selezionare **esportazione dati**.

    > [!Note]
    > Se l'esportazione dei dati non viene visualizzata nel menu a sinistra, l'utente non è un amministratore nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere uno degli **Hub eventi di Azure** o il **bus di servizio di Azure** come destinazione dell'esportazione. 

    > [!NOTE] 
    > Il numero massimo di esportazioni per app è cinque. 

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data-pnp/export-new2.png)

4. Nella casella di riepilogo a discesa selezionare lo spazio dei nomi di **Hub eventi/spazio dei nomi del bus di servizio**. È anche possibile selezionare l'ultima opzione dell'elenco, ossia **Immettere una stringa di connessione**. 

    > [!NOTE] 
    > Saranno visibili solo gli account di archiviazione, gli spazi dei nomi di hub eventi o gli spazi dei nomi del bus di servizio nella **stessa sottoscrizione dell'app IoT Central**. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.

    > [!NOTE] 
    > Per le app in versione di valutazione per 7 giorni l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. A queste app non è infatti associata una sottoscrizione di Azure.

    ![Creare un nuovo hub eventi per l'esportazione continua dei dati](media/howto-export-data-pnp/export-eh.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:
    - Hub eventi o bus di servizio, passare allo spazio dei nomi nel portale di Azure.
        - In **Impostazioni**selezionare **criteri di accesso condiviso**
        - Scegliere il valore predefinito **RootManageSharedAccessKey** oppure crearne uno nuovo
        - Copiare la stringa di connessione primaria o secondaria.

6. Nella casella di riepilogo a discesa scegliere un hub eventi, una coda o un argomento.

7. In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).

8. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore **Esporta dati** sia **attivato**. Selezionare **Salva**.

9. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.


## <a name="data-format"></a>Formato dati

I dati di telemetria, dispositivi e modelli di dispositivo vengono esportati nell'hub eventi o nella coda o nell'argomento del bus di servizio in tempo quasi reale. I dati di telemetria esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori di telemetria stessi. I dati dei dispositivi esportati contengono modifiche alle proprietà e ai metadati di tutti i dispositivi e i modelli di dispositivi esportati contengono modifiche a tutti i modelli di dispositivo. I dati esportati sono contenuti nella proprietà "body" in formato JSON.

> [!NOTE]
> Quando si sceglie un bus di servizio come destinazione dell'esportazione, per le code e gli argomenti **non devono essere abilitati le sessioni e il rilevamento dei duplicati**. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="telemetry"></a>Telemetria

Un nuovo messaggio viene esportato poco dopo che IoT Central lo ha ricevuto da un dispositivo. Ogni messaggio esportato in Hub eventi e nel bus di servizio contiene il messaggio completo inviato dal dispositivo nella proprietà "body" in formato JSON. 

> [!NOTE]
> I dispositivi che inviano i dati di telemetria sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati dei dispositivi e correlare ogni messaggio usando il **connectionDeviceId** che corrisponde al **deviceId** del messaggio del dispositivo.

L'esempio seguente mostra un messaggio sui dati di telemetria ricevuti nell'hub eventi o nella coda o nell'argomento del bus di servizio.

```json
{ 
  "body":{ 
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{ 
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{ 
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

### <a name="devices"></a>Dispositivi

I messaggi contenenti i dati dei dispositivi vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Questo significa che ogni pochi minuti viene recapitato un batch di messaggi con dati relativi a
- I nuovi dispositivi aggiunti
- Dispositivi con valori di proprietà modificati

Ogni messaggio rappresenta una o più modifiche a un dispositivo dopo l'esportazione dell'ultimo messaggio. Le informazioni inviate in ogni messaggio includono:
- `@id` del dispositivo in IoT Central
- `name` ID del dispositivo
- `deviceId` dal [Servizio di provisioning di dispositivi](https://aka.ms/iotcentraldocsdps)
- Informazioni sul modello di dispositivo
- Valori delle proprietà

> [!NOTE]
> I dispositivi eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.
>
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, assicurarsi di esportare anche i dati del modello.

L'esempio seguente mostra un messaggio relativo ai dati dei dispositivi nell'hub eventi o in una coda o un argomento del bus di servizio:


```json
{ 
  "body":{ 
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{ 
      "$cloudProperties":{ 
        "Color":"blue"
      },
      "EnvironmentalSensor":{ 
        "thsensormodel":{ 
          "reported":{ 
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{ 
          "reported":{ 
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{ 
        "totalStorage":{ 
          "reported":{ 
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{ 
          "reported":{ 
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{ 
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

### <a name="device-templates"></a>Modelli di dispositivo

I messaggi contenenti i dati dei modelli di dispositivo vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Questo significa che ogni pochi minuti viene recapitato un batch di messaggi con dati relativi a
- Nuovi modelli di dispositivo aggiunti o con versione
- Modelli di dispositivo con capabilityModels, cloudProperties, sostituzioni e valori iniziali modificati

Ogni messaggio rappresenta una o più modifiche a un modello di dispositivo dopo l'esportazione dell'ultimo messaggio. Le informazioni inviate in ogni messaggio includono:
- `@id` del modello del dispositivo
- `name` del modello del dispositivo
- `version` del modello del dispositivo
- Il dispositivo `capabilityModel` inclusi il relativo `interfaces` e le definizioni di telemetria, proprietà e comandi
- definizioni `cloudProperties`
- Esegue l'override di e i valori iniziali, inline con il `capabilityModel`

> [!NOTE]
> I modelli di dispositivo eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

L'esempio seguente illustra un messaggio di modello di dispositivo in un hub eventi o in una coda o un argomento del bus di servizio:

```json
{ 
  "body":{ 
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{ 
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[ 
        { 
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{ 
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[ 
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{ 
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{ 
                    "@value":"50"
                  }
                },
                "visualizationDetail":{ 
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        { 
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{ 
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[ 
              { 
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{ 
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[ 
        { 
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{ 
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{ 
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{ 
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è imparato a esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come attivare Funzioni di Azure](howto-trigger-azure-functions.md)
