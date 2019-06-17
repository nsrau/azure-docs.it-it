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
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539336"
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


### <a name="install-npm-package"></a>Installare il pacchetto npm
Per installare il [pacchetto npm per hub eventi](https://www.npmjs.com/package/@azure/event-hubs), aprire un prompt dei comandi con `npm` nel relativo percorso, cambiare la directory alla cartella in cui si desidera avere esempi e quindi eseguire questo comando

```shell
npm install @azure/event-hubs
```

Per installare il [pacchetto npm per l'Host processore di eventi](https://www.npmjs.com/package/@azure/event-processor-host), eseguire il comando seguente invece

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Inviare eventi

Questa sezione illustra come creare un'applicazione Node. js che invia eventi a un hub eventi. 

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `send.js` e incollare il seguente codice al suo interno.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. Immettere la stringa di connessione e il nome dell'Hub eventi nel codice precedente
4. Quindi eseguire il comando `node send.js` in un prompt dei comandi eseguire questo file. 100 eventi verrà inviato all'hub eventi

Congratulazioni! È stato appena inviato gli eventi a un hub eventi.


## <a name="receive-events"></a>Ricevere eventi

Questa sezione illustra come creare un'applicazione Node. js che riceve gli eventi da una singola partizione del gruppo di consumer predefinito in un hub eventi. 

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receive.js` e incollare il seguente codice al suo interno.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. Immettere la stringa di connessione e il nome dell'Hub eventi nel codice precedente.
4. Quindi eseguire il comando `node receive.js` in un prompt dei comandi eseguire questo file. Si riceveranno gli eventi da una delle partizioni del gruppo di consumer predefinito nell'Hub eventi

Congratulazioni! Sono stati appena ricevuti gli eventi da hub eventi.

## <a name="receive-events-using-event-processor-host"></a>Ricevere eventi usando Eventprocessorhost

In questa sezione illustra come ricevere eventi da un hub eventi usando Azure [EventProcessorHost](event-hubs-event-processor-host.md) in un'applicazione Node. js. EventProcessorHost (EPH) consente di ricevere in modo efficiente gli eventi da un hub eventi tramite la creazione di ricevitori tra tutte le partizioni nel gruppo di consumer di un hub eventi. Esegue il checkpoint dei metadati sui messaggi ricevuti a intervalli regolari in un BLOB del servizio di archiviazione di Azure. Questo approccio semplifica la possibilità di continuare a ricevere messaggi dal punto in cui si era interrotto in un momento successivo.

1. Aprire l'editor preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com). 
2. Creare un file denominato `receiveAll.js` e incollare il seguente codice al suo interno.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Immettere la stringa di connessione e il nome dell'Hub eventi nel codice precedente con stringa di connessione per un'archiviazione Blob di Azure
4. Quindi eseguire il comando `node receiveAll.js` in un prompt dei comandi eseguire questo file.

Congratulazioni! Sono stati appena ricevuti gli eventi da hub eventi usando Eventprocessorhost. Si riceveranno gli eventi da tutte le partizioni del gruppo di consumer predefinito nell'Hub eventi

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)
- Scopri altri esempi di Node. js per [hub eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e [Host processore di eventi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) su GitHub
