---
title: Inviare eventi ad Hub eventi di Azure tramite Node.js | Microsoft Docs
description: Guida introduttiva all'invio di eventi ad Hub eventi tramite Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801064"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Inviare eventi a Hub eventi di Azure usando Node.js

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi.

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md).

Questa esercitazione descrive come inviare eventi a un hub eventi da un'applicazione scritta in Node.js. Per ricevere eventi usando il pacchetto host processore di eventi di Node.js, vedere l'[articolo corrispondente relativo alla ricezione](event-hubs-node-get-started-receive.md).

Il codice per questo avvio rapido è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato

## <a name="create-a-namespace-and-event-hub"></a>Creare uno spazio dei nomi e un hub eventi
Il primo passaggio è usare il portale di Azure per creare uno spazio dei nomi di Hub eventi con un hub eventi. Se non esistono, è possibile creare queste entità seguendo le istruzioni disponibili in [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Microsoft Azure](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Clonare il repository Git di esempio
Clonare il repository Git di esempio da [Github](https://github.com/Azure/azure-event-hubs-node) nel computer. 

## <a name="install-nodejs-package"></a>Installare il pacchetto Node.js
Installare il pacchetto Node.js per Hub eventi di Azure nel computer. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Clonare il repository Git
Scaricare o clonare l'[esempio](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) da GitHub. 

## <a name="send-events"></a>Inviare eventi
L'SDK clonato contiene vari esempi che illustrano come inviare eventi a un hub eventi usando Node.js. In questo avvio rapido viene usato l'esempio **simpleSender.js**. Per osservare gli eventi che si ricevono aprire un altro terminale e ricevere gli eventi usando l'[esempio di ricezione](event-hubs-node-get-started-receive.md).

1. Aprire il progetto in Visual Studio Code. 
2. Creare un file denominato **.env** nella cartella del **client**. Copiare e incollare le variabili di ambiente di esempio da **sample.env** nella cartella radice.
3. Configurare la stringa di connessione dell'hub eventi, il nome dell'hub eventi e l'endpoint di archiviazione. È possibile copiare la stringa di connessione per l'hub eventi dalla **Stringa di connessione - chiave primaria** in **RootManageSharedAccessKey** nella pagina dell'Hub eventi nel portale di Azure. Per informazioni dettagliate, vedere [Get connection string](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace) (Ottenere la stringa di connessione).
4. Nell'interfaccia della riga di comando di Azure andare al percorso della cartella del **client**. Installare i pacchetti di nodo e creare il progetto eseguendo questi comandi:

    ```nodejs
    npm i
    npm run build
    ```
5. Iniziare a inviare eventi eseguendo il comando seguente: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Esaminare il codice di esempio 
Ecco il codice di esempio per inviare eventi da un hub eventi con Node.js. È possibile creare manualmente un file sampleSender.js ed eseguirlo per inviare gli eventi in un hub eventi. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere gli articoli seguenti:

* [Ricevere eventi con Node.js](event-hubs-node-get-started-receive.md)
* [Esempi in GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
