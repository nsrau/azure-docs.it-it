---
title: Ricevere eventi da Hub eventi di Azure usando Node.js | Microsoft Docs
description: Informazioni su come ricevere eventi da Hub eventi usando Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 6d5b52c8a5dd0306a349cac5e67eecc809005c6f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429185"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Ricevere eventi da Hub eventi di Azure usando Node.js

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi. È anche possibile acquisire dati degli eventi in Archiviazione di Azure o Azure Data Lake Store prima che vengano elaborati.  

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md).

Questa esercitazione illustra come ricevere eventi da un hub eventi usando [EventProcessorHost](event-hubs-event-processor-host.md) di Azure in un'applicazione Node.js. EventProcessorHost (EPH) consente di ricevere in modo efficiente gli eventi da un hub eventi tramite la creazione di ricevitori tra tutte le partizioni nel gruppo di consumer di un hub eventi. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

Il codice per questo avvio rapido è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Per inviare eventi a Hub eventi usando Node.js, vedere questo articolo: [Send events to Azure Event Hubs using Node.js](event-hubs-node-get-started-send.md) (Inviare eventi a Hub eventi di Azure usando Node.js). 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org). Non usare la versione LTS meno recente di Node.js. 
- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.

## <a name="create-a-namespace-and-event-hub"></a>Creare uno spazio dei nomi e un hub eventi
Il primo passaggio è usare il portale di Azure per creare uno spazio dei nomi di Hub eventi con un hub eventi. Se non esistono, è possibile creare queste entità seguendo le istruzioni disponibili in [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Microsoft Azure](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore
Per usare EventProcessorHost, è necessario un account di archiviazione di Azure. Le informazioni sullo stato, come i lease sulle partizioni e i checkpoint nel flusso di eventi, vengono condivise tra i ricevitori tramite un contenitore di Archiviazione di Azure. È possibile creare un account di archiviazione di Azure seguendo le istruzioni di [questo articolo](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Clonare il repository Git
Scaricare o clonare l'[esempio](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) da GitHub. 

## <a name="install-the-eventprocessorhost"></a>Installare EventProcessorHost
Installare EventProcessorHost per il modulo Hub eventi. 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Ricevere eventi usando EventProcessorHost
L'SDK clonato contiene vari esempi che illustrano come ricevere eventi da un hub eventi usando Node.js. In questo avvio rapido viene usato l'esempio **singleEPH.js**. Per osservare gli eventi che si ricevono aprire un altro terminale e inviare gli eventi usando l'[esempio di invio](event-hubs-node-get-started-send.md).

1. Aprire il progetto in Visual Studio Code. 
2. Creare un file denominato **.env** nella cartella del **processore**. Copiare e incollare le variabili di ambiente di esempio da **sample.env** nella cartella radice.
3. Configurare la stringa di connessione dell'hub eventi, il nome dell'hub eventi e l'endpoint di archiviazione. È possibile copiare la stringa di connessione per l'hub eventi dalla **Stringa di connessione - chiave primaria** in **RootManageSharedAccessKey** nella pagina dell'Hub eventi nel portale di Azure. Per informazioni dettagliate, vedere [Get connection string](event-hubs-create.md#create-an-event-hubs-namespace) (Ottenere la stringa di connessione).
4. Nell'interfaccia della riga di comando di Azure andare al percorso della cartella del **processore**. Installare i pacchetti di nodo e creare il progetto eseguendo questi comandi:

    ```nodejs
    npm i
    npm run build
    ```
5. Ricevere eventi con l'host processore di eventi eseguendo il comando seguente:

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>Esaminare il codice di esempio 
Ecco il codice di esempio per ricevere eventi da un hub eventi con Node.js. È possibile creare manualmente un file sampleEph.js ed eseguirlo per ricevere gli eventi in un hub eventi. 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Ricordare di impostare le variabili di ambiente prima di eseguire lo script. È possibile configurare questa impostazione nella riga di comando come illustrato nell'esempio seguente o usare il [pacchetto dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Per altri esempi, vedere [qui](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, visitare le pagine seguenti:

* [Inviare eventi con Node.js](event-hubs-go-get-started-send.md)
* [Esempi di Hub eventi](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)
* [Acquisire gli eventi in Archiviazione di Azure o Data Lake Store](event-hubs-capture-overview.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
