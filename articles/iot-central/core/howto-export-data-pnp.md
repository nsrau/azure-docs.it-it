---
title: Esportare i dati di Azure IoT Central | Microsoft Docs
description: Come esportare i dati dall'applicazione IoT Central di Azure in hub eventi di Azure, nel bus di servizio di Azure e nell'archiviazione BLOB di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: bc78f11d4f61c46e2ad4f7143fe8b3af6762b0b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950418"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Esportare i dati di Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati in hub eventi di Azure, nel bus di servizio di Azure o nelle istanze di archiviazione BLOB di Azure. I dati vengono esportati in formato JSON e possono includere dati di telemetria, informazioni sul dispositivo e informazioni sul modello di dispositivo. Usare i dati esportati per:

- Approfondimenti e analisi a caldo del percorso. Questa opzione include l'attivazione di regole personalizzate in analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati in app per la logica di Azure o il passaggio tramite funzioni di Azure da trasformare.
- Analisi del percorso a freddo, ad esempio i modelli di training in Azure Machine Learning o l'analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisiti

È necessario essere amministratore nell'applicazione IoT Central

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione continua dei dati.

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Se non si dispone di uno spazio dei nomi di hub eventi esistente da esportare in, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central con pagamento in base al consumo. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui eseguire l'esportazione, attenersi alla procedura seguente:

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central con pagamento in base al consumo. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.

Quando si sceglie bus di servizio come destinazione di esportazione, per le code e gli argomenti non devono essere abilitate le sessioni o il rilevamento dei duplicati. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="create-storage-account"></a>Crea account di archiviazione

Se non si ha un account di archiviazione di Azure esistente da esportare, seguire questa procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](https://aka.ms/blobdocscreatestorageaccount) o [Azure Data Lake storage V2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Se si sceglie di esportare i dati in un account di archiviazione Azure Data Lake Storage V2, è necessario scegliere **BlobStorage** come **tipo di account**.
    - È possibile esportare i dati negli account di archiviazione in sottoscrizioni diverse da quello per l'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Note]
    > Se non viene visualizzata l'esportazione dei dati nel riquadro sinistro, l'utente non è un amministratore nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere uno degli **Hub eventi**di Azure, il **bus di servizio**di Azure o l' **Archivio BLOB di Azure** come destinazione dell'esportazione. Il numero massimo di esportazioni per ogni applicazione è cinque.

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data-pnp/export-new2.png)

4. Nella casella di riepilogo a discesa selezionare lo spazio dei nomi di **Hub eventi**, lo spazio dei nomi del **bus di servizio**, **lo spazio dei nomi dell'account di archiviazione**oppure **immettere una stringa di connessione**.

    - Nella stessa sottoscrizione dell'applicazione IoT Central vengono visualizzati solo gli account di archiviazione, gli spazi dei nomi di hub eventi e gli spazi dei nomi del bus di servizio. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.
    - Per le app di valutazione di sette giorni, l'unico modo per configurare l'esportazione continua dei dati consiste nell'usare una stringa di connessione. Le app di valutazione di sette giorni non hanno una sottoscrizione di Azure associata.

    ![Crea nuovo hub eventi](media/howto-export-data-pnp/export-eh.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:
    - Hub eventi o bus di servizio, passare allo spazio dei nomi nel portale di Azure.
        - In **Impostazioni**selezionare **criteri di accesso condiviso**
        - Scegliere il valore predefinito **RootManageSharedAccessKey** oppure crearne uno nuovo
        - Copiare la stringa di connessione primaria o secondaria.
    - Account di archiviazione, passare all'account di archiviazione nel portale di Azure:
        - In **Impostazioni**selezionare **chiavi di accesso**
        - Copiare la stringa di connessione Key1 o la stringa di connessione Key2

6. Nella casella di riepilogo a discesa scegliere un hub eventi, una coda, un argomento o un contenitore.

7. In **dati da esportare**scegliere i tipi di dati da esportare impostando il tipo **su on**.

8. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore **Esporta dati** sia **attivato**. Selezionare **Salva**.

9. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.

## <a name="data-format"></a>Formato dati

I dati vengono esportati nell'hub eventi o nella coda o nell'argomento del bus di servizio in tempo quasi reale.

I dati vengono esportati nell'account di archiviazione una volta al minuto, con ogni file contenente il batch di modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:

- Telemetria: _{container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Dispositivi: _{container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modelli di dispositivo: _{container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

È possibile esplorare i file esportati nel portale di Azure passando al file e scegliendo la scheda **modifica BLOB** .

I dati di telemetria esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori di telemetria stessi. I dati dei dispositivi esportati contengono modifiche alle proprietà e ai metadati di tutti i dispositivi e i modelli di dispositivi esportati contengono modifiche a tutti i modelli di dispositivo. I dati esportati si trova nella proprietà Body ed è in formato JSON.

### <a name="telemetry"></a>Telemetria

Un nuovo messaggio viene esportato poco dopo che IoT Central lo ha ricevuto da un dispositivo.

- Ogni messaggio esportato in hub eventi e bus di servizio contiene il messaggio completo del dispositivo inviato nella proprietà Body in formato JSON.
- I file esportati nell'archiviazione BLOB usano lo stesso formato dei file di messaggio esportati dal [routing dei messaggi dell'hub](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) Internet all'archiviazione BLOB. Assicurarsi che i dispositivi inviino messaggi con `contentType: application/JSON` e `contentEncoding:utf-8` (o `utf-16``utf-32`). Per un esempio, vedere la [documentazione sull'hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

I dispositivi che inviano i dati di telemetria sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati del dispositivo e correlare ogni messaggio usando il **connectionDeviceId** corrispondente alla **DeviceID** del messaggio del dispositivo.

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

L'esempio seguente mostra un record in formato JSON nell'archivio BLOB:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Dispositivi

I messaggi contenenti i dati dei dispositivi vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Un nuovo snapshot viene scritto nell'archivio BLOB una volta al minuto. Ogni messaggio o snapshot include dati relativi a:

- I nuovi dispositivi aggiunti
- Dispositivi con valori di proprietà modificati

Ogni messaggio o record in uno snapshot rappresenta una o più modifiche a un dispositivo dopo l'ultimo messaggio esportato. Le informazioni includono:

- `@id` del dispositivo in IoT Central
- `name` ID del dispositivo
- `deviceId` dal [Servizio di provisioning di dispositivi](https://aka.ms/iotcentraldocsdps)
- Informazioni sul modello di dispositivo
- Valori delle proprietà

I dispositivi eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.

Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, assicurarsi di esportare anche i dati del modello.

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

I file esportati contengono una sola riga per ogni record. L'esempio seguente mostra un record in formato JSON.

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Modelli di dispositivo

I messaggi contenenti i dati dei modelli di dispositivo vengono inviati all'hub eventi oppure alla coda o all'argomento del bus di servizio ogni pochi minuti. Un nuovo snapshot viene scritto una volta al minuto nell'archivio BLOB. Quindi, a intervalli di pochi minuti, arriva un batch di messaggi con i dati relativi a:

I messaggi contenenti i dati del modello di dispositivo vengono inviati all'hub eventi o alla coda o all'argomento del bus di servizio una volta ogni pochi minuti. Un nuovo snapshot viene scritto nell'archivio BLOB una volta al minuto. Ogni messaggio o snapshot include dati relativi a:

- Nuovi modelli di dispositivo aggiunti o con versione
- Modelli di dispositivo con modelli di funzionalità modificati, proprietà del cloud, sostituzioni e valori iniziali

Ogni record di messaggio o snapshot rappresenta una o più modifiche a un modello di dispositivo dopo l'ultimo messaggio esportato. Le informazioni inviate in ogni messaggio o record includono:

- `@id` del modello del dispositivo
- `name` del modello del dispositivo
- `version` del modello del dispositivo
- Il dispositivo `capabilityModel` inclusi i `interfaces`e le definizioni di telemetria, proprietà e comandi
- definizioni `cloudProperties`
- Esegue l'override di e i valori iniziali, inline con il `capabilityModel`

I modelli di dispositivo eliminati dopo l'ultimo batch non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

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

I file esportati contengono una sola riga per ogni record. L'esempio seguente mostra un record in formato JSON.

```json
{
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
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è imparato a esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come attivare Funzioni di Azure](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
