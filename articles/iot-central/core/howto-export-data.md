---
title: Esportare i dati di Azure IoT Central | Microsoft Docs
description: Come esportare i dati dall'applicazione IoT Central di Azure in hub eventi di Azure, nel bus di servizio di Azure e nell'archiviazione BLOB di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: a3d60bf38c4a9dad13dacf8ba9798c4078c1df1a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049724"
---
# <a name="export-your-azure-iot-central-data"></a>Esporta i dati di Azure IoT Central



*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati in **Hub eventi**di Azure, nel **bus di servizio di Azure**o nelle istanze di **archiviazione BLOB di Azure** . I dati vengono esportati in formato JSON e possono includere dati di telemetria, informazioni sul dispositivo e informazioni sul modello di dispositivo. Usare i dati esportati per:

- Approfondimenti e analisi a caldo del percorso. Questa opzione include l'attivazione di regole personalizzate in analisi di flusso di Azure, l'attivazione di flussi di lavoro personalizzati in app per la logica di Azure o il passaggio tramite funzioni di Azure da trasformare.
- Analisi del percorso a freddo, ad esempio i modelli di training in Azure Machine Learning o l'analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisites

È necessario essere un amministratore dell'applicazione IoT Central o disporre delle autorizzazioni di esportazione dei dati.

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione continua dei dati.

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Se non si dispone di uno spazio dei nomi di hub eventi esistente da esportare in, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui eseguire l'esportazione, attenersi alla procedura seguente:

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Scegliere una sottoscrizione. È possibile esportare i dati in altre sottoscrizioni che non si trovano nella stessa sottoscrizione dell'applicazione IoT Central. In questo caso, si esegue la connessione utilizzando una stringa di connessione.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.

Quando si sceglie bus di servizio come destinazione di esportazione, per le code e gli argomenti non devono essere abilitate le sessioni o il rilevamento dei duplicati. Se una di queste due opzioni è abilitata, alcuni messaggi non verranno recapitati alla coda o all'argomento.

### <a name="create-storage-account"></a>Crea account di archiviazione

Se non si ha un account di archiviazione di Azure esistente da esportare, seguire questa procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](https://aka.ms/blobdocscreatestorageaccount) o [Azure Data Lake storage V2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). L'esportazione dei dati può scrivere solo i dati negli account di archiviazione che supportano i BLOB in blocchi. Di seguito è riportato un elenco di tipi compatibili noti di account di archiviazione: 

    |Livello di prestazioni|Tipo di account|
    |-|-|
    |Standard|per utilizzo generico V2|
    |Standard|per utilizzo generico V1|
    |Standard|Archiviazione BLOB|
    |Premium|Archiviazione BLOB in blocchi|

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Note]
    > Se non viene visualizzata l'esportazione dei dati nel riquadro sinistro, non si dispone delle autorizzazioni per configurare l'esportazione dei dati nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere uno degli **Hub eventi**di Azure, il **bus di servizio**di Azure o l' **Archivio BLOB di Azure** come destinazione dell'esportazione. Il numero massimo di esportazioni per ogni applicazione è cinque.

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data/new-export-definition.png)

4. Nella casella di riepilogo a discesa selezionare lo spazio dei nomi di **Hub eventi**, lo spazio dei nomi del **bus di servizio**, **lo spazio dei nomi dell'account di archiviazione**oppure **immettere una stringa di connessione**.

    - Nella stessa sottoscrizione dell'applicazione IoT Central vengono visualizzati solo gli account di archiviazione, gli spazi dei nomi di hub eventi e gli spazi dei nomi del bus di servizio. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.
    - Per le app create usando il piano tariffario gratuito, l'unico modo per configurare l'esportazione continua dei dati consiste nell'usare una stringa di connessione. Alle app del piano tariffario gratuito non è associata una sottoscrizione di Azure.

    ![Crea nuovo hub eventi](media/howto-export-data/export-event-hub.png)

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

8. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore abilitato sia **attivo** . Selezionare **Salva**.

9. Dopo alcuni minuti, i dati vengono visualizzati nella destinazione scelta.

## <a name="export-contents-and-format"></a>Esporta contenuto e formato

I dati di telemetria esportati contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori di telemetria stessi. I dati dei dispositivi esportati contengono modifiche alle proprietà e ai metadati di tutti i dispositivi e i modelli di dispositivi esportati contengono modifiche a tutti i modelli di dispositivo.

Per gli hub eventi e il bus di servizio, i dati vengono esportati in tempo quasi reale. I dati si trova nella proprietà Body ed è in formato JSON (vedere di seguito per esempi).

Per l'archiviazione BLOB, i dati vengono esportati una volta al minuto, con ogni file contenente il batch di modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:

- Telemetria: _{container}/{App-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Dispositivi: _{container}/{App-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modelli di dispositivo: _{container}/{App-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

È possibile esplorare i file esportati nel portale di Azure passando al file e scegliendo la scheda **modifica BLOB** .


## <a name="telemetry"></a>Telemetria

Per gli hub eventi e il bus di servizio, un nuovo messaggio viene esportato rapidamente dopo che IoT Central riceve il messaggio da un dispositivo e ogni messaggio esportato contiene il messaggio completo del dispositivo inviato nella proprietà Body in formato JSON.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto. I file esportati utilizzano lo stesso formato dei file di messaggio esportati dal [routing dei messaggi dell'hub](../../iot-hub/tutorial-routing.md) Internet all'archiviazione BLOB. 

> [!NOTE]
> Per l'archiviazione BLOB, assicurarsi che i dispositivi inviino messaggi con `contentType: application/JSON` e `contentEncoding:utf-8` (o `utf-16``utf-32`). Per un esempio, vedere la [documentazione sull'hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Il dispositivo che ha inviato i dati di telemetria è rappresentato dall'ID del dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i dati del dispositivo e correlare ogni messaggio usando il **connectionDeviceId** corrispondente alla **DeviceID** del messaggio del dispositivo.

Questo è un messaggio di esempio ricevuto in un hub eventi o in una coda o un argomento del bus di servizio.

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

Si tratta di un record di esempio esportato nell'archivio BLOB:

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
- ID modello di dispositivo in `instanceOf`
- flag di `simulated`, true se il dispositivo è un dispositivo simulato
- flag di `provisioned`, true se è stato effettuato il provisioning del dispositivo
- flag di `approved`, true se il dispositivo è stato approvato per l'invio di dati
- Valori delle proprietà
- `properties` inclusi i valori delle proprietà del dispositivo e del cloud

I dispositivi eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei dispositivi eliminati.

Per gli hub eventi e il bus di servizio, i messaggi contenenti i dati del dispositivo vengono inviati all'hub eventi o alla coda o all'argomento del bus di servizio quasi in tempo reale, come visualizzato in IoT Central. 

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche apportate dall'ultima scrittura viene esportato una volta al minuto.

Questo è un messaggio di esempio sui dati dei dispositivi e delle proprietà nell'hub eventi o nella coda o nell'argomento del bus di servizio:

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

Si tratta di uno snapshot di esempio che contiene i dati dei dispositivi e delle proprietà nell'archivio BLOB. I file esportati contengono una sola riga per ogni record.

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

- `id` del modello di dispositivo che corrisponde alla `instanceOf` del flusso di dispositivi sopra
- `displayName` del modello del dispositivo
- Il dispositivo `capabilityModel` inclusi i `interfaces`e le definizioni di telemetria, proprietà e comandi
- definizioni `cloudProperties`
- Esegue l'override di e i valori iniziali, inline con il `capabilityModel`

I modelli di dispositivo eliminati non vengono esportati. Attualmente nei messaggi esportati non è presente alcun indicatore dei modelli di dispositivo eliminati.

Per gli hub eventi e il bus di servizio, i messaggi contenenti i dati del modello di dispositivo vengono inviati all'hub eventi o alla coda o all'argomento del bus di servizio quasi in tempo reale, come visualizzato in IoT Central. 

Per l'archiviazione BLOB, un nuovo snapshot contenente tutte le modifiche apportate dall'ultima scrittura viene esportato una volta al minuto.

Questo è un messaggio di esempio sui dati dei modelli di dispositivo nell'hub eventi o nella coda o nell'argomento del bus di servizio:

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

Si tratta di uno snapshot di esempio che contiene i dati dei dispositivi e delle proprietà nell'archivio BLOB. I file esportati contengono una sola riga per ogni record.

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
## <a name="data-format-change-notice"></a>Avviso di modifica del formato dati

> [!Note]
> Il formato dei dati del flusso di telemetria non è influenzato da questa modifica. Sono interessati solo i flussi di dati dei dispositivi e dei modelli di dispositivo.

Se si ha un'esportazione dei dati esistente nell'applicazione di anteprima con i *dispositivi* e i flussi di *modelli di dispositivo* accesi, sarà necessario aggiornare l'esportazione entro il **30 giugno 2020**. Questo vale per le esportazioni in archiviazione BLOB di Azure, Hub eventi di Azure e il bus di servizio di Azure.

A partire dal 3 febbraio 2020, tutte le nuove esportazioni nelle applicazioni con i dispositivi e i modelli di dispositivo abilitati avranno il formato dati descritto in precedenza. Tutte le esportazioni create prima di questo rimarranno nel formato dati precedente fino al 30 giugno 2020, dopo il quale verranno automaticamente migrate le esportazioni nel nuovo formato dati. Il nuovo formato dati corrisponde al [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devices/get), alla [Proprietà](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)del dispositivo, alla [proprietà del cloud del dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) e agli oggetti modello di [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) nell'API IOT Central Public. 
 
Per i **dispositivi**, le differenze rilevanti tra il formato dati precedente e il nuovo formato dati includono:
- `@id` per il dispositivo viene rimosso, `deviceId` viene rinominato `id` 
- viene aggiunto il flag di `provisioned` per descrivere lo stato del provisioning del dispositivo
- viene aggiunto il flag di `approved` per descrivere lo stato di approvazione del dispositivo
- `properties` incluse le proprietà del dispositivo e del cloud, corrisponde alle entità nell'API pubblica

Per i **modelli di dispositivo**, le differenze rilevanti tra il formato dati precedente e il nuovo formato dati includono:

- `@id` per il modello di dispositivo è stato rinominato in `id`
- `@type` per il modello di dispositivo viene rinominato `types`ed è ora una matrice

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
> [Come creare webhook](./howto-create-webhooks.md)
