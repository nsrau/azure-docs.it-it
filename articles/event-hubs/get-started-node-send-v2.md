---
title: Inviare o ricevere eventi da Hub eventi di Azure con JavaScript (ultima versione)
description: Questo articolo illustra come creare un'applicazione JavaScript che invia o riceve eventi da Hub eventi di Azure usando il pacchetto azure/event-hubs versione 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 71c50e8efdf26f2a7d3f270a774b08e49c92faa7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159421"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Inviare o ricevere eventi da Hub eventi con JavaScript (azure/event-hubs versione 5)
Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto JavaScript **azure/event-hubs versione 5**. 

> [!IMPORTANT]
> In questa guida di avvio rapido si usa il pacchetto azure/event-hubs versione 5. Per una guida di avvio rapido che usa la versione 2 del pacchetto azure/event-hubs, vedere [Inviare e ricevere eventi con azure/eventhubs versione 2](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Node.js 8.x o versione successiva. Scaricare l'ultima [versione con supporto a lungo termine (LTS)](https://nodejs.org).  
- Visual Studio Code (scelta consigliata) o qualsiasi altro IDE (Integrated Development Environment).  
- Uno spazio dei nomi di Hub eventi e un hub eventi attivi. Per crearli, procedere come segue: 

   1. Nel [portale di Azure](https://portal.azure.com) creare uno spazio dei nomi di *Hub eventi* e quindi ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. 
   1. Per creare lo spazio dei nomi e l'hub eventi, seguire le istruzioni disponibili in [Avvio rapido: Creare un hub eventi con il portale di Azure](event-hubs-create.md).
   1. Continuare seguendo le istruzioni riportate in questo argomento di avvio rapido. 
   1. Per ottenere la stringa di connessione allo spazio dei nomi di Hub eventi, seguire le istruzioni riportate in [Ottenere la stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Prendere nota della stringa di connessione per usarla più avanti in questo argomento di avvio rapido.
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi la **stringa di connessione allo spazio dei nomi di Hub eventi** seguendo le istruzioni disponibili nell'articolo [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti in questa guida di avvio rapido.

### <a name="install-the-npm-package"></a>Installare il pacchetto npm
Per installare il [pacchetto npm (Node Package Manager) per Hub eventi](https://www.npmjs.com/package/@azure/event-hubs), aprire un prompt dei comandi il cui percorso comprenda *npm*, passare alla directory in cui si vogliono archiviare gli esempi e quindi eseguire questo comando:

```shell
npm install @azure/event-hubs
```

Per il lato ricevente, è necessario installare altri due pacchetti. In questo argomento di avvio rapido si usa archiviazione BLOB di Azure per rendere persistenti i checkpoint, in modo che il programma non legga gli eventi già letti. Vengono eseguiti a intervalli regolari i checkpoint dei metadati sui messaggi ricevuti in un BLOB. Con questo approccio risulta facile continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

Eseguire i comandi seguenti:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Inviare eventi

In questa sezione viene creata un'applicazione JavaScript che invia eventi a un hub eventi.

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com).
1. Creare un file denominato *send.js* e incollare il codice seguente all'interno:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. Nel codice usare valori reali per sostituire quanto segue:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Eseguire `node send.js` per eseguire questo file. Questo comando invia un batch di tre eventi all'hub eventi.
1. Nel portale di Azure verificare se l'hub eventi ha ricevuto i messaggi. Passare alla visualizzazione **Messaggi** nella sezione **Metriche**. Aggiornare la pagina per aggiornare il grafico. La conferma che i messaggi sono stati ricevuti potrebbe comparire dopo alcuni secondi.

    [![Verificare se l'hub eventi ha ricevuto i messaggi](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Per il codice sorgente completo, inclusi i commenti informativi aggiuntivi, passare alla [pagina sendEvents.js di GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Congratulazioni! Sono stati inviati eventi a un hub eventi.


## <a name="receive-events"></a>Ricevere eventi
In questa sezione si ricevono eventi da un hub eventi usando un archivio di checkpoint di archiviazione BLOB di Azure in un'applicazione JavaScript. Vengono eseguiti a intervalli regolari i checkpoint dei metadati sui messaggi ricevuti in un BLOB di archiviazione di Azure. Con questo approccio risulta facile continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

> [!NOTE]
> Se il codice viene eseguito in Hub di Azure Stack, questa piattaforma potrebbe supportare una versione diversa dell'SDK di archiviazione BLOB rispetto a quelle disponibili in Azure. Se ad esempio l'esecuzione avviene nell'[hub di Azure Stack versione 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è 2017-11-09. In questo caso, oltre ai passaggi descritti in questa sezione, sarà anche necessario aggiungere codice destinato alla versione 2017-11-09 dell'API del servizio di archiviazione. Per informazioni su come definire come destinazione una versione specifica dell'API di archiviazione, vedere gli esempi di [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) in GitHub. Per altre informazioni sulle versioni del servizio di archiviazione di Azure supportate in Hub di Azure Stack, vedere [Archiviazione nell'hub di Azure Stack: differenze e considerazioni](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Creare un account di archiviazione di Azure e un contenitore BLOB
Per creare un account di archiviazione di Azure e un contenitore BLOB, eseguire queste operazioni:

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Creare un contenitore BLOB nell'account di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md)

Assicurarsi di prendere nota della stringa di connessione e del nome del contenitore per un uso successivo nel codice di ricezione.

### <a name="write-code-to-receive-events"></a>Scrivere il codice per la ricezione di eventi

1. Aprire l'editor preferito, ad esempio[Visual Studio Code](https://code.visualstudio.com).
1. Creare un file denominato *receive.js* e incollare il codice seguente all'interno:

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. Nel codice usare valori reali per sostituire quanto segue:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Eseguire `node receive.js` al prompt dei comandi per eseguire questo file. Nella finestra dovrebbero essere visualizzati i messaggi sugli eventi ricevuti.

    > [!NOTE]
    > Per il codice sorgente completo, inclusi i commenti informativi aggiuntivi, passare alla [pagina receiveEventsUsingCheckpointStore.js di GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Congratulazioni! Sono stati ricevuti eventi dall'hub eventi. Il programma ricevente riceverà eventi da tutte le partizioni del gruppo di consumer predefinito nell'hub eventi.

## <a name="next-steps"></a>Passaggi successivi
Controllare questi esempi in GitHub:

- [Esempi JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Esempi di TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
