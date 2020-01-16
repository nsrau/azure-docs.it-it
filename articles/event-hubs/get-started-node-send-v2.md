---
title: Inviare e ricevere eventi tramite Node.js - Hub eventi di Azure
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Node.js in grado di inviare eventi da Hub eventi di Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981622"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Inviare o ricevere eventi da Hub eventi di Azure tramite Node.js

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come creare applicazioni Node.js per inviare o ricevere eventi da un hub eventi.

> [!IMPORTANT]
> Questa Guida introduttiva usa la versione 5 dell'SDK di script Java per hub eventi di Azure. Per una guida introduttiva che usa la versione 2 precedente di Java script SDK, vedere [questo articolo](event-hubs-node-get-started-send.md). Se si usa la versione 2 dell'SDK, è consigliabile eseguire la migrazione del codice alla versione più recente. Per informazioni dettagliate, vedere la [Guida alla migrazione](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi continuare con i passaggi seguenti di questa esercitazione. Ottenere quindi la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni dell'articolo: [ottenere la stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.


### <a name="install-npm-packages"></a>Installa nuovi pacchetti npm
Per installare il [pacchetto npm per Hub eventi](https://www.npmjs.com/package/@azure/event-hubs), aprire un prompt dei comandi il cui percorso comprenda `npm`, passare alla cartella in cui si vogliono archiviare gli esempi e quindi eseguire questo comando

```shell
npm install @azure/event-hubs
```

Per il lato ricevente, è necessario installare altri due pacchetti. In questa Guida introduttiva si userà l'archiviazione BLOB di Azure per rendere persistenti i checkpoint in modo che il programma non legga gli eventi già letti. Il Checkpoint imposta i metadati sui messaggi ricevuti a intervalli regolari in un BLOB. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Inviare eventi

Questa sezione illustra come creare un'applicazione Node.js che invia eventi a un hub eventi.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com).
2. Creare un file denominato `send.js` e incollarvi il codice seguente.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Non dimenticare di sostituire la **stringa di connessione** e i valori del **nome dell'hub eventi** nel codice.
5. Eseguire il comando `node send.js` per eseguire il file. Verrà inviato un batch di tre eventi all'hub eventi
6. Nella portale di Azure è possibile verificare che l'hub eventi abbia ricevuto i messaggi. Passare alla visualizzazione **messaggi** nella sezione **metrica** . Aggiornare la pagina per aggiornare il grafico. Potrebbero essere necessari alcuni secondi per indicare che i messaggi sono stati ricevuti.

    [![verificare che l'hub eventi abbia ricevuto i messaggi](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Per il codice sorgente completo con commenti più informativi, vedere [questo file in GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Congratulazioni! Sono stati inviati eventi a un hub eventi.


## <a name="receive-events"></a>Ricevere eventi
Questa sezione illustra come ricevere eventi da un hub eventi usando un archivio del checkpoint BLOB di Azure in un'applicazione Node. js. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Creare un'archiviazione di Azure e un contenitore BLOB
Seguire questa procedura per creare un account di archiviazione di Azure in un contenitore BLOB.

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Annotare la stringa di connessione e il nome del contenitore. Che vengono usati nel codice di ricezione.

### <a name="write-code-to-receive-events"></a>Scrivere codice per ricevere eventi

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com).
2. Creare un file denominato `receive.js` e incollarvi il codice seguente. Per informazioni dettagliate, vedere i commenti del codice.
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Non dimenticare di specificare i **valori seguenti** nel codice:
    - Stringa di connessione allo spazio dei nomi di hub eventi
    - Nome dell'hub eventi
    - Stringa di connessione all'account di archiviazione di Azure
    - Nome del contenitore BLOB
5. Eseguire quindi questo file tramite il comando `node receive.js` in un prompt dei comandi. Verranno visualizzati i messaggi relativi agli eventi ricevuti nella finestra di.

    > [!NOTE]
    > Per il codice sorgente completo con commenti più informativi, vedere [questo file su GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Congratulazioni! Sono stati ricevuti eventi dall'hub eventi. Il programma Receiver riceverà gli eventi da tutte le partizioni del gruppo di consumer predefinito nell'hub eventi

## <a name="next-steps"></a>Passaggi successivi
Vedere questi esempi su GitHub:

- [Esempi JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Esempi di TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
