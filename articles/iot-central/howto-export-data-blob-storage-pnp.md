---
title: Esportare i dati in Archiviazione BLOB di Azure | Microsoft Docs
description: Come esportare dati dall'applicazione Azure IoT Central in Archiviazione BLOB di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973242"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Esportare i dati nell'archivio BLOB di Azure (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare periodicamente i dati nell' **account di archiviazione BLOB di Azure** o **Azure Data Lake storage Gen2 account di archiviazione**. È possibile esportare i dati di **telemetria**, i **dispositivi**e i **modelli di dispositivo** in file in formato JSON. I dati esportati possono essere usati per eseguire analisi di percorsi non critici, ad esempio modelli di training in Azure Machine Learning o analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.


## <a name="prerequisites"></a>Prerequisiti

- È necessario essere amministratore nell'applicazione IoT Central

## <a name="create-storage-account"></a>Crea account di archiviazione
Se non si dispone di una risorsa di archiviazione esistente in cui eseguire l'esportazione, attenersi alla seguente procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](https://aka.ms/blobdocscreatestorageaccount) o [Azure Data Lake storage V2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Se si sceglie di esportare i dati in un account di archiviazione ADLS V2, è necessario scegliere il **tipo di account** come **BlobStorage**. 

    > [!Note] 
    > È possibile esportare i dati negli account di archiviazione in sottoscrizioni diverse da quello per l'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.


## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione di archiviazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati. 

1. Accedere all'applicazione IoT Central.

2. Nel menu a sinistra selezionare **esportazione dati**.

    > [!Note]
    > Se l'esportazione dei dati non viene visualizzata nel menu a sinistra, l'utente non è un amministratore nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere **archiviazione BLOB di Azure** come destinazione dell'esportazione. 

    > [!NOTE] 
    > Il numero massimo di esportazioni per app è cinque. 

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data-pnp/export-new2.png)

4. Nella casella di riepilogo a discesa selezionare lo **spazio dei nomi dell'account di archiviazione**. È anche possibile selezionare l'ultima opzione dell'elenco, ossia **Immettere una stringa di connessione**. 

    > [!NOTE] 
    > Vengono visualizzati solo gli spazi dei nomi degli account di archiviazione nella **stessa sottoscrizione dell'app IOT Central**. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.

    > [!NOTE] 
    > Per le app in versione di valutazione per 7 giorni l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. A queste app non è infatti associata una sottoscrizione di Azure.

    ![Crea nuova esportazione in BLOB](media/howto-export-data-pnp/export-create-blob2.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per l'account di archiviazione, passare all'account di archiviazione nel portale di Azure:
    - In **Impostazioni**selezionare **chiavi di accesso**
    - Copiare la stringa di connessione key1 o la stringa di connessione key2
 
6. Scegliere un contenitore dalla casella di riepilogo a discesa. Se non si dispone di un contenitore, passare all'account di archiviazione nel portale di Azure:
    - In **servizio BLOB**selezionare **BLOB**. Fare clic su **+ contenitore** e assegnare un nome al contenitore. Scegliere un livello di accesso pubblico per i dati (qualsiasi funzionerà con l'esportazione continua dei dati). Altre informazioni sono disponibili nella [documentazione di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).
   
    > [!NOTE] 
    > I dati vengono esportati in formato JSON.

8. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore **Esporta dati** sia attivato. Selezionare **Salva**.

   ![Configurare l'esportazione continua dei dati](media/howto-export-data-pnp/export-list-blob2.png)

9. Dopo alcuni minuti, i dati verranno visualizzati nell'account di archiviazione.


## <a name="path-structure"></a>Struttura Path

I dati di telemetria, i dispositivi e i modelli di dispositivo vengono esportati nell'account di archiviazione una volta al minuto, con ogni file contenente il batch di modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:
- Telemetria: {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Dispositivi: {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Modelli di dispositivo: {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

È possibile esplorare i file esportati nel portale di Azure passando al file e scegliendo la scheda **modifica BLOB** .

## <a name="data-format"></a>Formato dati
### <a name="telemetry"></a>Telemetria

I dati di telemetria esportati hanno tutti i nuovi messaggi ricevuti da IoT Central da tutti i dispositivi durante tale periodo di tempo. I file esportati sono nello stesso formato dei file dei messaggi esportati dal [routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) nell'archiviazione BLOB.

> [!NOTE]
> Assicurarsi che i dispositivi inviino messaggi con `contentType: application/JSON` e `contentEncoding:utf-8` (o `utf-16`, `utf-32`). Per un esempio, vedere la [documentazione sull'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> I dispositivi che inviano i dati di telemetria sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i relativi snapshot. Correlare ogni record di messaggio usando il valore **connectionDeviceId** corrispondente a **deviceId** del dispositivo del record di dispositivo.

L'esempio seguente mostra un record in formato JSON.

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

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i dispositivi. Ogni dispositivo include:
- `@id` del dispositivo in IoT Central
- `name` ID del dispositivo
- `deviceId` dal [Servizio di provisioning di dispositivi](https://aka.ms/iotcentraldocsdps)
- Informazioni sul modello di dispositivo
- Valori delle proprietà

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi dispositivi aggiunti dall'ultimo snapshot
- Dispositivi con valori di proprietà modificati dall'ultimo snapshot.

> [!NOTE]
> I dispositivi eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i dispositivi eliminati.
>
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato dal campo `instanceOf`. Per ottenere il nome del modello di dispositivo, esportare gli snapshot dei modelli di dispositivo.

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

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i modelli di dispositivo. Ogni modello di dispositivo include:
- `@id` del modello del dispositivo
- `name` del modello del dispositivo
- `version` del modello del dispositivo
- Il dispositivo `capabilityModel` inclusi il relativo `interfaces` e le definizioni di telemetria, proprietà e comandi
- definizioni `cloudProperties`
- Esegue l'override di e i valori iniziali, inline con il `capabilityModel`

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi modelli di dispositivo aggiunti dall'ultimo snapshot Sono incluse le nuove versioni dei modelli di dispositivo.
- Modelli di dispositivo con sostituzioni modificate, valori iniziali e definizioni delle proprietà cloud dall'ultimo snapshot.

> [!NOTE]
> I modelli di dispositivo eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i modelli di dispositivo eliminati.

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

Dopo avere appreso come esportare i dati, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come usare il Bridge di dispositivi IoT Central per connettere altri cloud Internet](howto-build-iotc-device-bridge.md)
