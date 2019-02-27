---
title: Inviare eventi tramite Node.js- Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Node.js in grado di inviare eventi da Hub eventi di Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447719"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Inviare eventi a Hub eventi di Azure usando Node.js

Hub eventi di Azure è una piattaforma di streaming per Big Data e un servizio di inserimento eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come inviare eventi a un hub eventi da un'applicazione scritta in Node.js.

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Node.js versione 8.x e successiva. Scaricare la versione LTS più recente da [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (scelta consigliata) o qualsiasi altro ambiente di sviluppo integrato

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi
Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md) e quindi continuare con i passaggi seguenti di questa esercitazione.

Ottenere la stringa di connessione per lo spazio dei nomi dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti nell'esercitazione.

## <a name="clone-the-sample-git-repository"></a>Clonare il repository Git di esempio
Clonare il repository Git di esempio da [GitHub](https://github.com/Azure/azure-event-hubs-node) nel computer. 

## <a name="install-nodejs-package"></a>Installare il pacchetto Node.js
Installare il pacchetto Node.js per Hub eventi di Azure nel computer. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Clonare il repository Git
Scaricare o clonare l'[esempio](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) da GitHub. 

## <a name="send-events"></a>Inviare eventi
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


## <a name="review-the-sample-code"></a>Esaminare il codice di esempio 
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

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si sono inviati messaggi a un hub eventi usando Node.js. Per informazioni su come ricevere eventi da un hub eventi usando Node.js, vedere [Ricevere eventi da Hub eventi di Azure usando Node.js](event-hubs-node-get-started-receive.md).

Controllare gli altri esempi di Node.js per Hub eventi in [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
