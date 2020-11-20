---
title: Esportare i dati da IoT Central di Azure (legacy) | Microsoft Docs
description: Come esportare i dati dall'applicazione IoT Central di Azure in hub eventi di Azure, nel bus di servizio di Azure e nell'archiviazione BLOB di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 9e5f4fd14f56f0a2dff45dd2650ea552b07fecd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987355"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Esportare i dati delle cose nelle destinazioni cloud usando l'esportazione dei dati (legacy)

> [!Note]
> Questo articolo descrive le funzionalità legacy di esportazione dei dati in IoT Central.
>
> - Per informazioni sulle nuove funzionalità di anteprima per l'esportazione dei dati, vedere [esportare i dati delle cose nelle destinazioni cloud usando l'esportazione dei dati](./howto-export-data.md).
> - Per informazioni sulle differenze tra le funzionalità di esportazione dei dati di anteprima e di esportazione dei dati legacy, vedere la [tabella di confronto](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

Questo articolo descrive come usare la funzionalità di esportazione dei dati in Azure IoT Central. Questa funzionalità consente di esportare i dati in modo continuo in **Hub eventi** di Azure, nel **bus di servizio di Azure** o nelle istanze di **archiviazione BLOB di Azure** . L'esportazione dei dati usa il formato JSON e può includere dati di telemetria, informazioni sul dispositivo e informazioni sul modello di dispositivo. Usare i dati esportati per:

- Approfondimenti e analisi a caldo del percorso. Questa opzione include l'attivazione di regole personalizzate in analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati in app per la logica di Azure o il passaggio tramite funzioni di Azure da trasformare.
- Analisi del percorso a freddo, ad esempio i modelli di training in Azure Machine Learning o l'analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione dei dati, si ottengono solo i dati da quel momento in poi. Attualmente non è possibile recuperare i dati per un periodo di tempo in cui l'esportazione dei dati è disattivata. Per conservare dati più cronologici, attivare prima l'esportazione dei dati.

## <a name="prerequisites"></a>Prerequisiti

È necessario essere un amministratore dell'applicazione IoT Central o disporre delle autorizzazioni di esportazione dei dati.

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione dei dati.

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Se non si dispone di uno spazio dei nomi di hub eventi esistente da esportare in, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui eseguire l'esportazione, attenersi alla procedura seguente:

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Per creare una coda o un argomento in cui eseguire l'esportazione, passare allo spazio dei nomi del bus di servizio e selezionare **+ coda** o **+ argomento**.

Quando si sceglie bus di servizio come destinazione di esportazione, per le code e gli argomenti non devono essere abilitate le sessioni o il rilevamento dei duplicati. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="create-storage-account"></a>Crea account di archiviazione

Se non si ha un account di archiviazione di Azure esistente da esportare, seguire questa procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) o [Azure Data Lake storage V2](../../storage/common/storage-account-create.md). L'esportazione dei dati può scrivere solo i dati negli account di archiviazione che supportano i BLOB in blocchi. L'elenco seguente illustra i tipi di account di archiviazione compatibili noti:

    |Livello di prestazioni|Tipo di account|
    |-|-|
    |Standard|per utilizzo generico V2|
    |Standard|per utilizzo generico V1|
    |Standard|Archiviazione BLOB|
    |Premium|Archiviazione BLOB in blocchi|

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

## <a name="set-up-data-export"></a>Configurare l'esportazione dei dati

Ora che si dispone di una destinazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Tip]
    > Se non viene visualizzata l' **esportazione dei dati** nel riquadro sinistro, non si dispone delle autorizzazioni per configurare l'esportazione dei dati nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+New**. Scegliere una risorsa di **archiviazione BLOB** di Azure, **Hub eventi** di Azure, **coda del bus di servizio** di Azure o un argomento del bus di servizio di **Azure** come destinazione dell'esportazione. Il numero massimo di esportazioni per ogni applicazione è cinque.

4. Immettere un nome per l'esportazione. Nella casella di riepilogo a discesa selezionare lo **spazio dei nomi** oppure **immettere una stringa di connessione**.

    - Nella stessa sottoscrizione dell'applicazione IoT Central vengono visualizzati solo gli account di archiviazione, gli spazi dei nomi di hub eventi e gli spazi dei nomi del bus di servizio. Se si desidera esportare in una destinazione esterna a questa sottoscrizione, scegliere **immettere una stringa di connessione** e vedere il passaggio 6.
    - Per le app create con il piano tariffario gratuito, l'unico modo per configurare l'esportazione dei dati consiste nell'usare una stringa di connessione. Alle app del piano tariffario gratuito non è associata una sottoscrizione di Azure.

    ![Crea nuovo hub eventi](media/howto-export-data-legacy/export-event-hub.png)

5. Nella casella di riepilogo a discesa scegliere un hub eventi, una coda, un argomento o un contenitore.

6. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:

    - Hub eventi o bus di servizio, passare allo spazio dei nomi nel portale di Azure:
        - Per utilizzare una stringa di connessione per l'intero spazio dei nomi:
            1. In **Impostazioni** selezionare **criteri di accesso condiviso**
            2. Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** .
            3. Copiare la stringa di connessione primaria o secondaria.
        - Per usare la stringa di connessione per un'istanza specifica di hub eventi o per una coda o un argomento del bus di servizio, passare a **entità > Hub eventi** o **entità > code** o **entità > argomenti**. Scegliere un'istanza specifica e seguire la stessa procedura descritta in precedenza per ottenere una stringa di connessione.
    - Account di archiviazione, passare all'account di archiviazione nel portale di Azure:
        - Sono supportate solo le stringhe di connessione per l'intero account di archiviazione. Non sono supportate le stringhe di connessione con ambito a un singolo contenitore.
          1. In **Impostazioni** selezionare **chiavi di accesso**
          2. Copiare la stringa di connessione Key1 o la stringa di connessione Key2

    Incollare la stringa di connessione. Digitare l'istanza o il **nome del contenitore** con distinzione tra maiuscole e minuscole.

7. In **dati da esportare** scegliere i tipi di dati da esportare impostando il tipo **su on**.

8. Per attivare l'esportazione dei dati, assicurarsi che l'interruttore abilitato sia **On** **attivo** . Selezionare **Salva**.

9. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.

## <a name="export-contents-and-format"></a>Esporta contenuto e formato

I dati di telemetria esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori di telemetria stessi. I dati dei dispositivi esportati contengono modifiche alle proprietà e ai metadati di tutti i dispositivi e i modelli di dispositivi esportati contengono modifiche a tutti i modelli di dispositivo.

Per gli hub eventi e il bus di servizio, i dati vengono esportati in tempo quasi reale. I dati si trova nella `body` proprietà ed è in formato JSON. Per esempi, vedere di seguito.

Per l'archiviazione BLOB, i dati vengono esportati una volta al minuto, con ogni file contenente il batch di modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:

- Telemetria: _{container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Dispositivi: _{container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modelli di dispositivo: _{container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Per esplorare i file esportati nella portale di Azure, passare al file e selezionare la scheda **modifica BLOB** .

## <a name="telemetry"></a>Telemetria

Per gli hub eventi e il bus di servizio, IoT Central Esporta rapidamente un nuovo messaggio dopo la ricezione del messaggio da un dispositivo. Ogni messaggio esportato contiene il messaggio completo del dispositivo inviato nella proprietà Body in formato JSON.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto. I file esportati utilizzano lo stesso formato dei file di messaggio esportati dal [routing dei messaggi dell'hub](../../iot-hub/tutorial-routing.md) Internet all'archiviazione BLOB.

> [!NOTE]
> Per l'archiviazione BLOB, assicurarsi che i dispositivi inviino messaggi `contentType: application/JSON` con e `contentEncoding:utf-8` (o `utf-16` `utf-32` ). Per un esempio, vedere la [documentazione sull'hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Il dispositivo che ha inviato i dati di telemetria è rappresentato dall'ID del dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati del dispositivo e correlare ogni messaggio usando il **connectionDeviceId** corrispondente alla **DeviceID** del messaggio del dispositivo.

L'esempio seguente illustra un messaggio ricevuto da un hub eventi o da una coda o un argomento del bus di servizio:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Questo messaggio non include l'ID del dispositivo mittente.

Per recuperare l'ID del dispositivo dai dati del messaggio in una query di analisi di flusso di Azure, usare la funzione [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Per un esempio, vedere la query in [estendere Azure IOT Central con regole personalizzate usando analisi di flusso, funzioni di Azure e SendGrid](./howto-create-custom-rules.md).

Per recuperare l'ID del dispositivo in un'area di lavoro Azure Databricks o Apache Spark, usare [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Per un esempio, vedere l'area di lavoro databricks in [extend Azure IOT Central con l'analisi personalizzata usando Azure Databricks](./howto-create-custom-analytics.md).

L'esempio seguente mostra un record esportato nell'archivio BLOB:

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

## <a name="devices"></a>Dispositivi

Ogni messaggio o record in uno snapshot rappresenta una o più modifiche a un dispositivo e alle relative proprietà del dispositivo e del cloud dall'ultimo messaggio esportato. Il messaggio include:

- `id` del dispositivo in IoT Central
- `displayName` ID del dispositivo
- ID modello di dispositivo in `instanceOf`
- `simulated` flag, true se il dispositivo è un dispositivo simulato
- `provisioned` flag, true se è stato eseguito il provisioning del dispositivo
- `approved` flag, true se il dispositivo è stato approvato per l'invio di dati
- Valori delle proprietà
- `properties` inclusione dei valori delle proprietà del dispositivo e del cloud

I dispositivi eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.

Per gli hub eventi e il bus di servizio, IoT Central invia messaggi contenenti dati del dispositivo all'hub eventi o alla coda o all'argomento del bus di servizio quasi in tempo reale.

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche apportate dall'ultima scrittura viene esportato una volta al minuto.

Il messaggio di esempio seguente mostra le informazioni sui dati dei dispositivi e delle proprietà in un hub eventi o in una coda o un argomento del bus di servizio:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
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
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Questo snapshot è un messaggio di esempio che mostra i dati dei dispositivi e delle proprietà nell'archivio BLOB. I file esportati contengono una sola riga per ogni record.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Modelli di dispositivo

Ogni record di messaggio o snapshot rappresenta una o più modifiche a un modello di dispositivo pubblicato dopo l'ultimo messaggio esportato. Le informazioni inviate in ogni messaggio o record includono:

- `id` del modello di dispositivo che corrisponde `instanceOf` a del flusso di dispositivi sopra
- `displayName` del modello del dispositivo
- Il dispositivo include le definizioni di dati di `capabilityModel` `interfaces` telemetria, proprietà e comandi
- `cloudProperties` definizioni
- Esegue l'override di e i valori iniziali, inline con `capabilityModel`

I modelli di dispositivo eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

Per gli hub eventi e il bus di servizio, IoT Central invia i messaggi contenenti i dati del modello di dispositivo all'hub eventi o alla coda o all'argomento del bus di servizio quasi in tempo reale.

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche apportate dall'ultima scrittura viene esportato una volta al minuto.

Questo esempio mostra un messaggio sui dati dei modelli di dispositivo nell'hub eventi o nella coda o nell'argomento del bus di servizio:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
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

Questo snapshot di esempio mostra un messaggio che contiene i dati del dispositivo e delle proprietà nell'archivio BLOB. I file esportati contengono una sola riga per ogni record.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Avviso di modifica del formato dati

> [!Note]
> Il formato dei dati del flusso di telemetria non è influenzato da questa modifica. Sono interessati solo i flussi di dati dei dispositivi e dei modelli di dispositivo.

Se si ha un'esportazione dei dati esistente nell'applicazione di anteprima con i *dispositivi* e i flussi di *modelli di dispositivo* accesi, aggiornare l'esportazione entro il **30 giugno 2020**. Questo requisito si applica alle esportazioni nell'archiviazione BLOB di Azure, negli hub eventi di Azure e nel bus di servizio di Azure.

A partire dal 3 febbraio 2020, tutte le nuove esportazioni nelle applicazioni con i dispositivi e i modelli di dispositivo abilitati avranno il formato dati descritto in precedenza. Tutte le esportazioni create prima di tale data rimarranno nel formato dati precedente fino al 30 giugno 2020, a quel punto verrà eseguita la migrazione automatica di queste esportazioni al nuovo formato dati. Il nuovo formato dati corrisponde al [dispositivo](/rest/api/iotcentral/devices/get), alla proprietà del [dispositivo, alla](/rest/api/iotcentral/devices/getproperties)proprietà del [cloud del dispositivo](/rest/api/iotcentral/devices/getcloudproperties)e agli oggetti [modello di dispositivo](/rest/api/iotcentral/devicetemplates/get) nell'API IOT Central Public.

Per i **dispositivi**, le differenze rilevanti tra il formato dati precedente e il nuovo formato dati includono:
- `@id` per il dispositivo è stato rimosso, `deviceId` viene rinominato in `id` 
- `provisioned` viene aggiunto il flag per descrivere lo stato del provisioning del dispositivo
- `approved` viene aggiunto il flag per descrivere lo stato di approvazione del dispositivo
- `properties` incluse le proprietà del dispositivo e del cloud, corrisponde alle entità nell'API pubblica

Per i **modelli di dispositivo**, le differenze rilevanti tra il formato dati precedente e il nuovo formato dati includono:

- `@id` per il modello di dispositivo è stato rinominato in `id`
- `@type` per il modello di dispositivo viene rinominato in `types` e ora è una matrice

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivi (formato deprecato a partire da 3 febbraio 2020)

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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelli di dispositivo (formato deprecato a partire da 3 febbraio 2020)

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

Ora che si è appreso come esportare i dati in hub eventi di Azure, nel bus di servizio di Azure e nell'archivio BLOB di Azure, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come eseguire analisi personalizzate con databricks](./howto-create-custom-analytics.md)