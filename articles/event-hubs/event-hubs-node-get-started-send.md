---
title: Inviare e ricevere eventi usando Node. js - hub eventi di Azure | Microsoft Docs
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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677902"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Inviare eventi a o ricevere eventi da hub eventi di Azure con Node. js

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come creare applicazioni Node. js per inviare eventi a o ricevere eventi da un hub eventi.

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato
- **Creare uno spazio dei nomi di hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi continuare con i passaggi seguenti di questa esercitazione. Quindi, è possibile ottenere la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.
- Clona il [repository GitHub di esempio](https://github.com/Azure/azure-event-hubs-node) nel computer. 


## <a name="send-events"></a>Inviare eventi
Questa sezione illustra come creare un'applicazione Node. js che invia eventi a un hub eventi. 

### <a name="install-nodejs-package"></a>Installare il pacchetto Node.js
Installare il pacchetto Node.js per Hub eventi di Azure nel computer. 

```shell
npm install @azure/event-hubs
```

Se è ancora stato clonato il repository Git come indicato nella sezione dei prerequisiti, scaricare il [esempio](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) da GitHub. 

L'SDK clonato contiene vari esempi che illustrano come inviare eventi a un hub eventi usando Node.js. In questo avvio rapido viene usato l'esempio **simpleSender.js**. Per osservare gli eventi che si ricevono aprire un altro terminale e ricevere gli eventi usando l'[esempio di ricezione](event-hubs-node-get-started-receive.md).

1. Aprire il progetto in Visual Studio Code. 
2. Creare un file denominato **.env** nella cartella del **client**. Copiare e incollare le variabili di ambiente di esempio da **sample.env** nella cartella radice.
3. Configurare la stringa di connessione dell'hub eventi, il nome dell'hub eventi e l'endpoint di archiviazione. Per istruzioni su come ottenere una stringa di connessione per un hub eventi, vedere [Ottenere una stringa di connessione](event-hubs-create.md#create-an-event-hubs-namespace).
4. Nell'interfaccia della riga di comando di Azure andare al percorso della cartella del **client**. Installare i pacchetti di nodo e creare il progetto eseguendo questi comandi:

    ```shell
    npm i
    npm run build
    ```
5. Iniziare a inviare eventi eseguendo il comando seguente: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Esaminare il codice di esempio 
Esaminare il codice di esempio nel file simpleSender.js per inviare eventi a un hub eventi.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Ricordare di impostare le variabili di ambiente prima di eseguire lo script. È possibile configurare le variabili nella riga di comando come illustrato nell'esempio seguente o usare il [pacchetto dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Ricevere eventi
Questa esercitazione illustra come ricevere eventi da un hub eventi usando [EventProcessorHost](event-hubs-event-processor-host.md) di Azure in un'applicazione Node.js. EventProcessorHost (EPH) consente di ricevere in modo efficiente gli eventi da un hub eventi tramite la creazione di ricevitori tra tutte le partizioni nel gruppo di consumer di un hub eventi. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

Il codice per questo avvio rapido è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Clonare il repository Git
Scaricare o clonare l'[esempio](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) da GitHub. 

### <a name="install-the-eventprocessorhost"></a>Installare EventProcessorHost
Installare EventProcessorHost per il modulo Hub eventi. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Ricevere eventi usando EventProcessorHost
L'SDK clonato contiene vari esempi che illustrano come ricevere eventi da un hub eventi usando Node.js. In questo avvio rapido viene usato l'esempio **singleEPH.js**. Per osservare gli eventi che si ricevono aprire un altro terminale e inviare gli eventi usando l'[esempio di invio](event-hubs-node-get-started-send.md).

1. Aprire il progetto in Visual Studio Code. 
2. Creare un file denominato **.env** nella cartella del **processore**. Copiare e incollare le variabili di ambiente di esempio da **sample.env** nella cartella radice.
3. Configurare la stringa di connessione dell'hub eventi, il nome dell'hub eventi e l'endpoint di archiviazione. È possibile copiare la stringa di connessione per l'hub eventi dalla **Stringa di connessione - chiave primaria** in **RootManageSharedAccessKey** nella pagina dell'Hub eventi nel portale di Azure. Per informazioni dettagliate, vedere [Get connection string](event-hubs-create.md#create-an-event-hubs-namespace) (Ottenere la stringa di connessione).
4. Nell'interfaccia della riga di comando di Azure andare al percorso della cartella del **processore**. Installare i pacchetti di nodo e creare il progetto eseguendo questi comandi:

    ```shell
    npm i
    npm run build
    ```
5. Ricevere eventi con l'host processore di eventi eseguendo il comando seguente:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Esaminare il codice di esempio 
Ecco il codice di esempio per ricevere eventi da un hub eventi con Node.js. È possibile creare manualmente un file sampleEph.js ed eseguirlo per ricevere gli eventi in un hub eventi. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
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

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Per altri esempi, vedere [qui](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)
- Controllare gli altri esempi di Node.js per Hub eventi in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
