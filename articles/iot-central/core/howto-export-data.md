---
title: Esportare i dati di Azure IoT Central Documenti Microsoft
description: Come esportare dati dall'applicazione Azure IoT Central in Hub eventi di Azure, Bus di servizio di Azure e Archiviazione BLOB di AzureHow to export data from your Azure IoT Central application to Azure Event Hubs, Azure Service Bus, and Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157909"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Esportare i dati IoT nelle destinazioni in AzureExport IoT data to destinations in Azure

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati in Hub eventi di Azure, Bus di servizio di Azure o istanze di archiviazione BLOB di Azure.This article describes how to use the continuous data export feature in Azure IoT Central to export your data to **Azure Event Hubs**, **Azure Service Bus,** or **Azure Blob storage** instances. I dati vengono esportati in formato JSON e possono includere dati di telemetria, informazioni sul dispositivo e informazioni sul modello di dispositivo. Utilizzare i dati esportati per:

- Approfondimenti e analisi dei percorsi caldi. Questa opzione include l'attivazione di regole personalizzate in Analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati nelle app per la logica di Azure o il passaggio a Funzioni di Azure per la trasformazione.
- Analisi dei percorsi freddi, ad esempio modelli di training in Azure Machine Learning o analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisiti

È necessario essere un amministratore nell'applicazione IoT Central o disporre delle autorizzazioni di esportazione dei dati.

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione continua dei dati.

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Se non si dispone di uno spazio dei nomi Hub eventi esistente in cui esportare, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

2. Scegliere una sottoscrizione. È possibile esportare dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central.You can export data to other subscriptions that are not in the same subscription as your IoT Central application. In questo caso, ci si connette utilizzando una stringa di connessione.

3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui esportare, attenersi alla seguente procedura:

1. Creare un nuovo spazio dei nomi del bus di servizio nel portale di Azure.Create a [new Service Bus namespace in the Azure portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Scegliere una sottoscrizione. È possibile esportare dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central.You can export data to other subscriptions that are not in the same subscription as your IoT Central application. In questo caso, ci si connette utilizzando una stringa di connessione.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.

Quando si sceglie Il bus di servizio come destinazione di esportazione, le code e gli argomenti non devono avere sessioni o rilevamento duplicati abilitato. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="create-storage-account"></a>Crea account di archiviazione

Se non si dispone di un account di archiviazione di Azure esistente in cui esportare, eseguire la procedura seguente:If you don't have an existing Azure Storage account to export to, follow these steps:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Per altre informazioni sulla creazione di nuovi account di [Archiviazione BLOB](https://aka.ms/blobdocscreatestorageaccount) di Azure o account di archiviazione di Azure Data Lake [Storage v2.](../../storage/blobs/data-lake-storage-quickstart-create-account.md) L'esportazione dei dati può scrivere dati solo in account di archiviazione che supportano BLOB di blocchi. Di seguito è riportato un elenco di tipi di account di archiviazione compatibili noti:The following is a list of known compatible types of storage accounts: 

    |Livello di prestazioni|Tipo di account|
    |-|-|
    |Standard|Scopo generale V2|
    |Standard|Scopo generale V1|
    |Standard|Archiviazione BLOB|
    |Premium|Archiviazione BLOB a blocchiBlock Blob Storage|

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione in cui esportare i dati, attenersi alla seguente procedura per impostare l'esportazione continua dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **Esportazione dati**.

    > [!Note]
    > Se l'esportazione dei dati non è visualizzata nel riquadro sinistro, non si dispone delle autorizzazioni necessarie per configurare l'esportazione dei dati nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Seleziona il pulsante **" Nuovo"** in alto a destra. Scegliere uno degli hub eventi di **Azure,** il **bus**di servizio di Azure o l'archiviazione BLOB di **Azure** come destinazione dell'esportazione. Il numero massimo di esportazioni per applicazione è cinque.

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data/new-export-definition.png)

4. Nella casella di riepilogo a discesa selezionare lo spazio dei nomi **Hub eventi**, spazio dei nomi del bus di **servizio**, spazio dei **nomi dell'account**di archiviazione o **Immettere una stringa**di connessione.

    - Nella stessa sottoscrizione dell'applicazione IoT Central vengono visualizzati solo account di archiviazione, spazi dei nomi Hub eventi e Spazi dei nomi del bus di servizio. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.
    - Per le app create usando il piano tariffario gratuito, l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. Le app nel piano tariffario gratuito non hanno una sottoscrizione di Azure associata.

    ![Crea nuovo hub eventi](media/howto-export-data/export-event-hub.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:
    - Hub eventi o Bus di servizio, passare allo spazio dei nomi nel portale di Azure.Event Hubs or Service Bus, go to the namespace in the Azure portal.
        - In **Impostazioni**selezionare **Criteri di accesso condiviso**
        - Scegliere il valore predefinito **RootManageSharedAccessKey** oppure crearne uno nuovo
        - Copiare la stringa di connessione primaria o secondaria.
    - Account di archiviazione, passare all'account di archiviazione nel portale di Azure:Storage account, go to the Storage account in the Azure portal:
        - In **Impostazioni**selezionare **Chiavi di accesso**
        - Copiare la stringa di connessione key1 o key2Copy either the key1 connection string or the key2 connection string

6. Scegliere un hub, una coda, un argomento o un contenitore eventi dalla casella di riepilogo a discesa.

7. In **Dati da esportare**scegliere i tipi di dati da esportare impostando il tipo su **Attivato**.

8. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore **Attivato** sia **Attivato**. Selezionare **Salva**.

9. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.

## <a name="export-contents-and-format"></a>Esportare contenuti e formato

I dati di telemetria esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori di telemetria stessi. I dati dei dispositivi esportati contengono modifiche alle proprietà e ai metadati di tutti i dispositivi e i modelli di dispositivo esportati contengono modifiche a tutti i modelli di dispositivo.

Per gli hub eventi e il bus di servizio, i dati vengono esportati quasi in tempo reale. I dati si trova nella proprietà body e sono in formato JSON (vedere di seguito per esempi).

Per l'archiviazione BLOB, i dati vengono esportati una volta al minuto, con ogni file contenente il batch di modifiche dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:

- Telemetria: _scontainer / id-app / telemetry /_
- Dispositivi: _"contenitore" / id-app / dispositivi / AAAA / MM / / dd / ._ . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
- Modelli di dispositivo: _"contenitore" / "id-app" / deviceTemplates / YYYY / / MM / dd / ._

È possibile esplorare i file esportati nel portale di Azure passando al file e scegliendo la scheda **Modifica BLOB.**


## <a name="telemetry"></a>Telemetria

Per hub eventi e bus di servizio, un nuovo messaggio viene esportato rapidamente dopo che IoT Central riceve il messaggio da un dispositivo e ogni messaggio esportato contiene il messaggio completo inviato nel dispositivo nella proprietà body in formato JSON.

Per l'archiviazione BLOB, i messaggi vengono raggruppati in batch ed esportati una volta al minuto. I file esportati usano lo stesso formato dei file dei messaggi esportati dal routing dei [messaggi dell'hub IoT](../../iot-hub/tutorial-routing.md) nell'archiviazione BLOB. 

> [!NOTE]
> Per l'archiviazione BLOB, verificare che `contentType: application/JSON` `contentEncoding:utf-8` i `utf-16`dispositivi `utf-32`inviino messaggi con e (o , ). Per un esempio, vedere la [documentazione dell'hub IoT.See the IoT Hub documentation](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) for an example.

Il dispositivo che ha inviato i dati di telemetria è rappresentato dall'ID del dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati del dispositivo e correlare ogni messaggio usando il **connectionDeviceId** che corrisponde al **deviceId** del messaggio del dispositivo.

Si tratta di un messaggio di esempio ricevuto in un hub eventi o in una coda o in un argomento del bus di servizio.

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

Questo è un record di esempio esportato nell'archiviazione BLOB:This is an example record exported to blob storage:

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

Ogni messaggio o record in uno snapshot rappresenta una o più modifiche a un dispositivo e alle relative proprietà del dispositivo e del cloud dall'ultimo messaggio esportato. ad esempio:

- `id` del dispositivo in IoT Central
- `displayName` ID del dispositivo
- ID modello dispositivo in`instanceOf`
- `simulated`flag, true se il dispositivo è un dispositivo simulato
- `provisioned`flag, true se il dispositivo è stato sottoposto a provisioning
- `approved`flag, true se il dispositivo è stato approvato per l'invio di dati
- Valori delle proprietà
- `properties`inclusi i valori delle proprietà del dispositivo e del cloud

I dispositivi eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.

Per gli hub eventi e il bus di servizio, i messaggi contenenti i dati del dispositivo vengono inviati all'hub eventi o alla coda del bus di servizio o all'argomento quasi in tempo reale, come viene visualizzato in IoT Central.For Event Hubs and Service Bus, messages containing device data are sent to your event hub or Service Bus queue or topic in near real-time, as it appears in IoT Central. 

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche dall'ultimo scritto viene esportato una volta al minuto.

Questo è un messaggio di esempio sui dispositivi e i dati delle proprietà nell'hub eventi o nella coda o nell'argomento del bus di servizio:This is an example message about devices and properties data in event hub or Service Bus queue or topic:

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

Si tratta di uno snapshot di esempio contenente i dati relativi a dispositivi e proprietà nell'archivio BLOB. I file esportati contengono una singola riga per record.

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

Ogni messaggio o record snapshot rappresenta una o più modifiche a un modello di dispositivo pubblicato dall'ultimo messaggio esportato. Le informazioni inviate in ogni messaggio o record includono:

- `id`del modello di dispositivo `instanceOf` che corrisponde al flusso di dispositivi sopra
- `displayName` del modello del dispositivo
- Il `capabilityModel` dispositivo, `interfaces`inclusi i relativi , e i dati di telemetria, le proprietà e le definizioni dei comandi
- `cloudProperties`Definizioni
- Sostituzioni e valori iniziali, in linea con il`capabilityModel`

I modelli di dispositivo eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

Per gli hub eventi e il bus di servizio, i messaggi contenenti i dati del modello di dispositivo vengono inviati all'hub eventi o alla coda del bus di servizio o all'argomento quasi in tempo reale, come viene visualizzato in IoT Central.For Event Hubs and Service Bus, messages containing device template data are sent to your event hub or Service Bus queue or topic in near real-time, as it appears in IoT Central. 

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche dall'ultimo scritto viene esportato una volta al minuto.

Questo è un messaggio di esempio sui dati dei modelli di dispositivo nell'hub eventi o nella coda o nell'argomento del bus di servizio:This is an example message about device templates data in event hub or Service Bus queue or topic:

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
                              "commandType": "synchronous",
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

Si tratta di uno snapshot di esempio contenente i dati relativi a dispositivi e proprietà nell'archivio BLOB. I file esportati contengono una singola riga per record.

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
                              "commandType": "synchronous",
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
## <a name="data-format-change-notice"></a>Avviso di modifica del formato dei dati

> [!Note]
> Il formato dei dati del flusso di telemetria non è interessato da questa modifica. Sono interessati solo i flussi di dati dei dispositivi e dei modelli di dispositivo.

Se nell'applicazione di anteprima è presente un'esportazione dati con i flussi *Dispositivi* e *Modelli di dispositivo* attivati, sarà necessario aggiornare l'esportazione entro il **30 giugno 2020.** Questo vale per le esportazioni in Archiviazione BLOB di Azure, Hub eventi di Azure e bus di servizio di Azure.This applies to exports to Azure Blob Storage, Azure Event Hubs, and Azure Service Bus.

A partire dal 3 febbraio 2020, tutte le nuove esportazioni nelle applicazioni con dispositivi e modelli di dispositivo abilitati avranno il formato dati descritto in precedenza. Tutte le esportazioni create prima di tale testo rimarranno nel vecchio formato dati fino al 30 giugno 2020, dopo di che queste esportazioni verranno automaticamente migrate al nuovo formato dati. Il nuovo formato dati corrisponde agli oggetti del modello di [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/get) [proprietà del dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [proprietà cloud del dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) e modello di [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) nell'API pubblica di IoT Central. 
 
Per **i dispositivi**, notevoli differenze tra il vecchio formato dati e il nuovo formato dati includono:
- `@id`per il dispositivo `deviceId` viene rimosso, viene rinominato in`id` 
- `provisioned`viene aggiunto il flag per descrivere lo stato di provisioning del dispositivo
- `approved`viene aggiunto un flag per descrivere lo stato di approvazione del dispositivo
- `properties`incluse le proprietà del dispositivo e del cloud, corrisponde alle entità nell'API pubblica

Per **i modelli di dispositivo,** differenze notevoli tra il formato dati precedente e il nuovo formato dati includono:

- `@id`per il modello di dispositivo viene rinominato in`id`
- `@type`per il modello di `types`dispositivo viene rinominato in , ed è ora un array

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivi (formato deprecato al 3 febbraio 2020)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelli di dispositivo (formato deprecato a partire dal 3 febbraio 2020)
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

Ora che si sa come esportare i dati negli hub eventi di Azure, nel bus di servizio di Azure e nell'archiviazione BLOB di Azure, continuare con il passaggio successivo:Now that you know how to export your data to Azure Event Hubs, Azure Service Bus, and Azure Blob Storage, continue to the next step:

> [!div class="nextstepaction"]
> [Come creare webhook](./howto-create-webhooks.md)
