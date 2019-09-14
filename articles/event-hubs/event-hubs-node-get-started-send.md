---
title: Inviare e ricevere eventi usando node. js-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Node.js in grado di inviare eventi da Hub eventi di Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 3bb222d3197ef37d56767300d71cc350d25a37bd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984474"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Inviare eventi o ricevere eventi da Hub eventi di Azure usando node. js

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come creare applicazioni node. js per inviare o ricevere eventi da un hub eventi.

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato
- **Creare uno spazio dei nomi di hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi continuare con i passaggi seguenti di questa esercitazione. Ottenere quindi la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni dell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.


### <a name="install-npm-package"></a>Installare il pacchetto NPM
Per installare il [pacchetto NPM per hub eventi](https://www.npmjs.com/package/@azure/event-hubs), aprire un prompt `npm` dei comandi con il percorso corrispondente, passare alla cartella in cui si desidera ottenere gli esempi, quindi eseguire questo comando.

```shell
npm install @azure/event-hubs
```

Per installare il [pacchetto NPM per l'host del processore di eventi](https://www.npmjs.com/package/@azure/event-processor-host), eseguire il comando seguente

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Inviare eventi

Questa sezione illustra come creare un'applicazione Node. js che invia eventi a un hub eventi. 

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `send.js` e incollarvi il codice riportato di seguito. Ottenere la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice precedente
4. Eseguire quindi il comando `node send.js` in un prompt dei comandi per eseguire il file. Questo invierà 100 eventi all'hub eventi

La procedura è stata completata. A questo punto sono stati inviati eventi a un hub eventi.


## <a name="receive-events"></a>Ricevere eventi

Questa sezione illustra come creare un'applicazione Node. js che riceve gli eventi da una singola partizione del gruppo di consumer predefinito in un hub eventi. 

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receive.js` e incollarvi il codice riportato di seguito.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice riportato sopra.
4. Eseguire quindi il comando `node receive.js` in un prompt dei comandi per eseguire il file. Si riceveranno gli eventi da una delle partizioni del gruppo di consumer predefinito nell'hub eventi

La procedura è stata completata. Sono stati ricevuti eventi dall'hub eventi.

## <a name="receive-events-using-event-processor-host"></a>Ricevere eventi con l'host processore di eventi

Questa sezione illustra come ricevere eventi da un hub eventi usando Azure [EventProcessorHost](event-hubs-event-processor-host.md) in un'applicazione Node. js. EventProcessorHost (EPH) consente di ricevere in modo efficiente gli eventi da un hub eventi tramite la creazione di ricevitori tra tutte le partizioni nel gruppo di consumer di un hub eventi. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receiveAll.js` e incollarvi il codice riportato di seguito.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Immettere la stringa di connessione e il nome dell'hub eventi nel codice precedente insieme alla stringa di connessione per un archivio BLOB di Azure
4. Eseguire quindi il comando `node receiveAll.js` in un prompt dei comandi per eseguire il file.

La procedura è stata completata. Sono stati ricevuti eventi dall'hub eventi usando l'host del processore di eventi. Questa operazione riceverà gli eventi da tutte le partizioni del gruppo di consumer predefinito nell'hub eventi

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)
- Vedere altri esempi node. js per [Hub eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e [host processore di eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) in GitHub
