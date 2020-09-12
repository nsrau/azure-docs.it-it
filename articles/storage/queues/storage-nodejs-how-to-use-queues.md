---
title: Come usare l'archiviazione di Accodamento di Azure da Node.js-archiviazione di Azure
description: Informazioni su come usare il Servizio di accodamento di Azure per creare ed eliminare code. Informazioni su come inserire, ottenere ed eliminare i messaggi utilizzando Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-javascript
ms.openlocfilehash: 18e184ed126aab8d03867db7b6b7d28c88644366
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89288815"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Come usare l'archiviazione di Accodamento di Azure da Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Panoramica

In questa guida viene illustrato come eseguire scenari comuni utilizzando il Microsoft Azure Servizio di accodamento. Gli esempi sono scritti usando l'API Node.js. Gli scenari descritti includono inserimento, visualizzazione, recupero ed eliminazione dei messaggi in coda. Viene inoltre illustrato come creare ed eliminare code.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Per creare un'applicazione Node.js vuota, vedere [creare un'app Web di Node.js nel servizio app Azure][Create a Node.js web app in Azure App Service], [compilare e distribuire un'applicazione Node.js a un servizio cloud di Azure][Build and deploy a Node.js application to an Azure Cloud Service] con Windows PowerShell o [Visual Studio Code][Visual Studio Code].

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione

La [libreria client di archiviazione di Azure per JavaScript][Azure Storage client library for JavaScript] include un set di pratici librerie che comunicano con i servizi REST di archiviazione.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio PowerShell (Windows), Terminal (Mac) o bash (Unix), per passare alla cartella in cui è stata creata l'applicazione di esempio.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Digitare **NPM install \@ Azure/Storage-Queue** nella finestra di comando.

1. Verificare che sia stata creata una cartella **Node \_ modules** . All'interno di tale cartella troverai il pacchetto ** \@ Azure/Storage-Queue** , che contiene la libreria client che ti serve per accedere all'archiviazione.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

1. Digitare **npm install azure-storage** nella finestra di comando.

1. Verificare che sia stata creata una cartella **Node \_ modules** . All'interno di tale cartella è disponibile il pacchetto **Azure-Storage** , che contiene le librerie necessarie per accedere all'archiviazione.

---

### <a name="import-the-package"></a>Importare il pacchetto

Usando l'editor di codice, aggiungere quanto segue alla parte superiore del file JavaScript in cui si intende usare le code.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Come creare una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Il codice seguente consente di ottenere il valore di una variabile di ambiente denominata `AZURE_STORAGE_CONNECTION_STRING` e di utilizzarlo per creare un oggetto [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) . L'oggetto **QueueServiceClient** viene quindi utilizzato per creare un oggetto [QueueClient](/javascript/api/@azure/storage-queue/queueclient) . L'oggetto **QueueClient** consente di usare una coda specifica.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Se la coda esiste già, viene generata un'eccezione.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` o le `AZURE_STORAGE_CONNECTION_STRING` informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni sull'account quando si chiama **createQueueService**.

La coda seguente crea un oggetto **QueueService** che consente di utilizzare le code.

```javascript
var queueSvc = azure.createQueueService();
```

Chiamare il metodo **createQueueIfNotExists** per creare una nuova coda con il nome specificato o restituire la coda, se esiste già.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se la coda viene creata, `result.created` è true. Se la coda esiste già, `result.created` è false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Come inserire un messaggio in una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per aggiungere un messaggio a una coda, chiamare il metodo [SendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per inserire un messaggio in una coda, chiamare il metodo **CreateMessage** per creare un nuovo messaggio e aggiungerlo alla coda.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Come leggere il messaggio successivo

È possibile leggere i messaggi nella coda senza rimuoverli dalla coda chiamando il metodo **peekMessages** .

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per impostazione predefinita, [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) visualizza un singolo messaggio. Nell'esempio seguente vengono visualizzati i primi cinque messaggi nella coda. Se sono visibili meno di cinque messaggi, vengono restituiti solo i messaggi visibili.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per impostazione predefinita, **peekMessages** visualizza un singolo messaggio.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` contiene il messaggio.

---

Se si chiama **peekMessages** in assenza di messaggi nella coda, non verrà restituito alcun errore. Tuttavia, non viene restituito alcun messaggio.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Come modificare il contenuto di un messaggio in coda

Nell'esempio seguente viene aggiornato il testo di un messaggio.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Modificare il contenuto di un messaggio sul posto nella coda chiamando [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-). 

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Modificare il contenuto di un messaggio sul posto nella coda chiamando **updateMessage**. 

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Come rimuovere dalla coda un messaggio

La rimozione dalla coda di un messaggio è un processo in due fasi:

1. Ottenere il messaggio.

1. Eliminazione del messaggio.

Nell'esempio seguente viene ottenuto un messaggio, che viene quindi eliminato.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per ottenere un messaggio, chiamare il metodo [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) . Questa chiamata rende i messaggi invisibile nella coda, pertanto nessun altro client può elaborarli. Dopo che l'applicazione ha elaborato il messaggio, chiamare [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) per eliminarlo dalla coda.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi. Dopo 30 secondi è visibile agli altri client. È possibile specificare un valore diverso impostando [options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) quando si chiama **receiveMessages**.

Se si chiama **receiveMessages** in assenza di messaggi nella coda, non verrà restituito alcun errore. Tuttavia, non verrà restituito alcun messaggio.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per ottenere un messaggio, chiamare il metodo **GetMessages** . Questa chiamata rende i messaggi invisibile nella coda, pertanto nessun altro client può elaborarli. Dopo che l'applicazione ha elaborato il messaggio, chiamare **deleteMessage** per eliminarlo dalla coda.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi. Dopo 30 secondi è visibile agli altri client. È possibile specificare un valore diverso usando `options.visibilityTimeout` con **getMessages**.

Se **non** sono presenti messaggi nella coda, viene restituito un errore. Tuttavia, non verrà restituito alcun messaggio.

---

## <a name="additional-options-for-dequeuing-messages"></a>Opzioni aggiuntive per rimuovere i messaggi dalla coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

È possibile personalizzare il recupero di messaggi da una coda in due modi:

* [options. numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) : Recupera un batch di messaggi (fino a 32).
* [options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) : impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il metodo **receiveMessages** per ottenere cinque messaggi in un'unica chiamata. Ogni messaggio viene poi elaborato con un ciclo `for`. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

È possibile personalizzare il recupero di messaggi da una coda in due modi:

* `options.numOfMessages` - consente di recuperare un batch di messaggi (massimo 32).
* `options.visibilityTimeout` - consente di impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il metodo **getMessages** per recuperare 15 messaggi con una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `for`. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Come ottenere la lunghezza della coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Il metodo [GetProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) restituisce i metadati relativi alla coda, incluso il numero approssimativo di messaggi in attesa nella coda.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Il metodo **getQueueMetadata** restituisce i metadati relativi alla coda, incluso il numero approssimativo di messaggi in attesa nella coda.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Come elencare le code

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per recuperare un elenco di code, chiamare [QueueServiceClient. listQueues](). Per recuperare un elenco filtrato in base a un prefisso specifico, impostare [options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) nella chiamata a **listQueues**.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per recuperare un elenco di code, usare **listQueuesSegmented**. Per recuperare un elenco filtrato in base a uno specifico prefisso, usare **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se non è possibile restituire tutte le code, passare `result.continuationToken` come primo parametro di **listQueuesSegmented** o secondo parametro di **listQueuesSegmentedWithPrefix** per recuperare più risultati.

---

## <a name="how-to-delete-a-queue"></a>Come eliminare una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) sull'oggetto **QueueClient** .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Per cancellare tutti i messaggi da una coda senza eliminarli, chiamare [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-).

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **deleteQueue** sull'oggetto Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Per cancellare tutti i messaggi da una coda senza eliminarli, chiamare **clearMessages**.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* Per informazioni sulle novità, visitare il [Blog del team di archiviazione di Azure][Azure Storage Team Blog]
* Visitare il repository della [libreria client di archiviazione di Azure per JavaScript][Azure Storage client library for JavaScript] su GitHub

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
