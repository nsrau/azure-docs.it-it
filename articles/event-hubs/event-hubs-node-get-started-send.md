---
title: Inviare eventi ad Hub eventi di Azure tramite Node.js | Microsoft Docs
description: Guida introduttiva all'invio di eventi ad Hub eventi tramite Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 14ea98b9d31bee08b962e8b3801ed507472ba692
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455794"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Inviare eventi a Hub eventi di Azure usando Node.js

Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come inviare eventi a un hub eventi da un'applicazione scritta in Node.js.

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi
Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi procedere con i passaggi seguenti di questa esercitazione.

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
3. Configurare la stringa di connessione dell'hub eventi, il nome dell'hub eventi e l'endpoint di archiviazione. È possibile copiare la stringa di connessione per l'hub eventi dalla **Stringa di connessione - chiave primaria** in **RootManageSharedAccessKey** nella pagina dell'Hub eventi nel portale di Azure. Per informazioni dettagliate, vedere [Get connection string](event-hubs-create.md#create-an-event-hubs-namespace) (Ottenere la stringa di connessione).
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
In questa guida introduttiva si sono inviati messaggi a un hub eventi usando Node.js. Per informazioni su come ricevere eventi da un hub eventi usando Node.js, vedere [Ricevere eventi da Hub eventi di Azure usando Node.js](event-hubs-node-get-started-receive.md).

Controllare gli altri esempi di Node.js per Hub eventi in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
