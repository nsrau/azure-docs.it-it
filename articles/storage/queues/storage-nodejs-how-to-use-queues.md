---
title: Come usare l'archiviazione code da Node.js | Microsoft Docs
description: Informazioni su come usare il servizio di accodamento di Azure per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi. Gli esempi sono scritti in Node.js.
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 97522abd05d60eeaa2cc8dd07d3ab81d7f1d5fb9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Come usare l'archiviazione di accodamento da Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Panoramica
In questa guida viene illustrato come eseguire scenari comuni del Servizio di accodamento di Microsoft Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari illustrati includono **inserimento**, **visualizzazione**, **recupero** ed **eliminazione** dei messaggi in coda, oltre alla **creazione ed eliminazione di code**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creazione di un'applicazione Node.js
Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creare un'app Web Node.js nel servizio app di Azure](../../app-service/app-service-web-get-started-nodejs.md), [Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) con Windows PowerShell, o [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione
Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminal** (Mac,) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.
2. Digitare **npm install azure-storage** nella finestra di comando. L'output da questo comando sarà simile al seguente esempio:
 
    ```
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di questa cartella si trova il pacchetto **azure-storage** , che contiene le librerie necessarie per accedere all'archiviazione.

### <a name="import-the-package"></a>Importare il pacchetto
Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

```
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
I modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY, o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createQueueService**.

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure](https://portal.azure.com) per un sito Web di Azure, vedere [App Web Node.js con il servizio tabelle di Azure].

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda
La coda seguente crea un oggetto **QueueService** che consente di utilizzare le code.

```
var queueSvc = azure.createQueueService();
```

Utilizzare il metodo **createQueueIfNotExists** che restituisce la coda specificata se esiste già o, in caso contrario, crea una nuova coda con il nome specificato.

```
queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se la coda viene creata, `result.created` è true. Se la coda esiste già, `result.created` è false.

### <a name="filters"></a>Filtri
Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite usando **QueueService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

```
function handle (requestOptions, next)
```

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

```
function (returnObject, finalCallback, next)
```

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi. Sono **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **QueueService** che usa **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: inserire un messaggio in una coda
Per inserire un messaggio in una coda, utilizzare il metodo **createMessage** per creare un nuovo messaggio e aggiungerlo alla coda.

```
queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Procedura: visualizzare il messaggio successivo
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peekMessages** . Per impostazione predefinita, **peekMessages** visualizza un singolo messaggio.

```
queueSvc.peekMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
  }
});
```

`result` contiene il messaggio.

> [!NOTE]
> Se si usa **peekMessages** in assenza di messaggi nella coda, non viene visualizzato un errore, ma non vengono restituiti messaggi.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Procedura: rimuovere il messaggio successivo dalla coda
L'elaborazione di un messaggio prevede un processo a due fasi:

1. Rimozione del messaggio dalla coda.
2. Eliminazione del messaggio.

Per rimuovere un messaggio dalla coda usare **getMessages**. Il messaggio viene reso invisibile nella coda, quindi nessun altro client può elaborarlo. Dopo che l'applicazione ha elaborato il messaggio, chiamare **deleteMessage** per eliminarlo dalla coda. Nell'esempio seguente il messaggio viene prima richiamato, quindi eliminato:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
    var message = result[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi, dopo i quali torna visibile agli altri client. È possibile specificare un valore diverso usando `options.visibilityTimeout` con **getMessages**.
> 
> [!NOTE]
> Se si usa **getMessages** in assenza di messaggi nella coda, non viene visualizzato un errore, ma non vengono restituiti messaggi.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: cambiare il contenuto di un messaggio accodato
È possibile cambiare il contenuto di un messaggio inserito nella coda con **updateMessage**. Nell'esempio seguente viene aggiornato il testo di un messaggio:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Got the message
    var message = result[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedura: opzioni aggiuntive per rimuovere i messaggi dalla coda
È possibile personalizzare il recupero di messaggi da una coda in due modi:

* `options.numOfMessages` - consente di recuperare un batch di messaggi (massimo 32).
* `options.visibilityTimeout` - consente di impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il metodo **getMessages** per recuperare 15 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = result[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Procedura: recuperare la lunghezza delle code
**getQueueMetadata** restituisce metadati relativi alla coda, includendo il numero approssimativo di messaggi in attesa nella coda.

```
queueSvc.getQueueMetadata('myqueue', function(error, result, response){
  if(!error){
    // Queue length is available in result.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Procedura: Elenco di code
Per recuperare un elenco di code, usare **listQueuesSegmented**. Per recuperare un elenco filtrato in base a uno specifico prefisso, usare **listQueuesSegmentedWithPrefix**.

```
queueSvc.listQueuesSegmented(null, function(error, result, response){
  if(!error){
    // result.entries contains the list of queues
  }
});
```

Se non possono essere restituite tutte le code, è possibile usare `result.continuationToken` come primo parametro di **listQueuesSegmented** o secondo parametro di **listQueuesSegmentedWithPrefix** per recuperare più risultati.

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **deleteQueue** sull'oggetto coda.

```
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Per deselezionare tutti i messaggi da una coda senza eliminarla, usare **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Procedura: usare le firme di accesso condiviso di Azure
Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare alle code senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato alle code, ad esempio per consentire a un'app per dispositivi mobili di inviare messaggi.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **QueueService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà al titolare della firma di accesso condiviso di aggiungere i messaggi alla coda e che scadrà 100 minuti dopo la data di creazione.

```
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere alla coda.

L'applicazione client usa quindi la firma di accesso condiviso con il metodo **QueueServiceWithSAS** per eseguire operazioni sulla coda. Nell'esempio seguente viene eseguita la connessione alla coda e viene creato un messaggio.

```
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Poiché la firma di accesso condiviso è stata generata con accesso di aggiunta, se si tentasse di leggere, aggiornare o eliminare i messaggi verrebbe restituito un errore.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso
Per impostare i criteri di accesso per una firma di accesso condiviso è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere alla coda, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. L'esempio seguente definisce due criteri, uno per 'user1' e uno per 'user2':

```
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Nell'esempio seguente viene recuperato l'elenco di controllo di accesso corrente per **myqueue** e vengono aggiunti i nuovi criteri tramite **setQueueAcl**. Risultato:

```
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

```
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* Vedere il [Blog del team di Archiviazione di Azure][Azure Storage Team Blog].
* Vedere il repository [Azure Storage SDK per Node][Azure Storage SDK for Node] su GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Creare un'app Web Node.js nel servizio app di Azure](../../app-service/app-service-web-get-started-nodejs.md)

[Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
