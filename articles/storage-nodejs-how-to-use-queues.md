<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Come utilizzare il Servizio di accodamento da Node.js

In questa guida viene illustrato come eseguire scenari comuni con il servizio di accodamento di Windows
Azure. Gli esempi sono scritti usando l'API Node.js
. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il
**recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed
eliminazione delle code**. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sul Servizio di accodamento][Informazioni sul Servizio di accodamento]
-   [Concetti][Concetti]
-   [Creazione di un account di archiviazione di Azure][Creazione di un account di archiviazione di Azure]
-   [Creazione di un'applicazione Node.js][Creazione di un'applicazione Node.js]
-   [Configurazione dell'applicazione per l'accesso all'archiviazione][Configurazione dell'applicazione per l'accesso all'archiviazione]
-   [Configurazione di una stringa di connessione di archiviazione di Azure][Configurazione di una stringa di connessione di archiviazione di Azure]
-   [Procedura: Creare una coda][Procedura: Creare una coda]
-   [Procedura: Inserire un messaggio in una coda][Procedura: Inserire un messaggio in una coda]
-   [Procedura: Visualizzare il messaggio successivo][Procedura: Visualizzare il messaggio successivo]
-   [Procedura: Rimuovere il messaggio successivo dalla coda][Procedura: Rimuovere il messaggio successivo dalla coda]
-   [Procedura: Cambiare il contenuto di un messaggio in coda][Procedura: Cambiare il contenuto di un messaggio in coda]
-   [Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda][Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda]
-   [Procedura: Recuperare la lunghezza della coda][Procedura: Recuperare la lunghezza della coda]
-   [Procedura: Eliminare una coda][Procedura: Eliminare una coda]
-   [Procedura: Utilizzo delle firme di accesso condiviso][Procedura: Utilizzo delle firme di accesso condiviso]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"></a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Servizio cloud Node.js] (utilizzando Windows PowerShell) o [Sito Web con WebMatrix][Sito Web con WebMatrix].

## <a name="configure-access"> </a>Configurazione dell'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di librerie che
comunicano con i servizi di archiviazione REST.

### Utilizzare Node Package Manager (NPM) per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure-storage** nella finestra di comando, che dovrebbe
    restituire il seguente output:

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella
    **node\_modules**. Nella cartella si
    trova il pacchetto **azure-storage** che contiene le librerie necessarie per
    accedere all'archiviazione.

### Importare il pacchetto

Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file
**server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Configurazione di una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createQueueService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione][Applicazione Web Node.js con archiviazione]

## <a name="create-queue"> </a>Procedura: Creare una coda

La seguente coda crea un oggetto **QueueService** che consente di usare le
code.

    var queueSvc = azure.createQueueService();

Usare il metodo **createQueueIfNotExists** che restituisce la coda
specificata se esiste già o, in caso contrario, crea una nuova coda con il nome
specificato.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Se la coda viene creata, `result` è true. Se la coda esiste già, `result` è false.

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite con **QueueService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

        function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

        function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **QueueService** che usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>Procedura: Inserire un messaggio in una coda

Per inserire un messaggio in una coda, usare il metodo **createMessage** per
creare un nuovo messaggio e aggiungerlo alla coda.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="peek-message"> </a>Procedura: Visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo
dalla coda chiamando il metodo **peekMessages**. Per impostazione predefinita,
**peekMessages** visualizza un singolo messaggio.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Messages peeked
      }
    });

`result` contiene il messaggio.

> [WACOM.NOTE] Se si usa **peekMessages** in assenza di messaggi nella coda non viene visualizzato un errore, ma non vengono restituiti messaggi.

## <a name="get-message"> </a>Procedura: Rimuovere il messaggio successivo dalla coda

L'elaborazione di un messaggio prevede un processo a due fasi:

1.  Rimozione del messaggio dalla coda.

2.  Eliminazione del messaggio.

Per rimuovere un messaggio dalla coda usare **getMessage**. Il messaggio viene reso invisibile nella coda, in modo che gli altri client non possano elaborarlo. Dopo che l'applicazione ha elaborato il messaggio, chiamare **deleteMessage** per eliminarlo dalla coda. Nell'esempio seguente il messaggio viene prima richiamato, quindi eliminato:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [WACOM.NOTE] Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi, dopo i quali torna visibile agli altri client. È possibile specificare un valore diverso usando `options.visibilityTimeout` con **getMessages**.

> [WACOM.NOTE]
> Se si usa **getMessages** in assenza di messaggi nella coda non viene visualizzato un errore, ma non vengono restituiti messaggi.

## <a name="change-contents"> </a>Procedura: Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda con **updateMessage**. Nell'esempio seguente viene aggiornato il testo di un messaggio:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="advanced-get"> </a>Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi:

-   `options.numOfMessages`: recuperare un batch di messaggi (massimo 32.)
-   `options.visibilityTimeout`: impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il metodo **getMessages** per recuperare 15 messaggi con una sola chiamata. Quindi, ogni messaggio
viene elaborato con un ciclo for. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="get-queue-length"> </a>Procedura: Recuperare la lunghezza della coda

**getQueueMetadata** restituisce metadati relativi alla coda, includendo il numero approssimativo di messaggi in attesa nella coda.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximatemessagecount
      }
    });

## <a name="list-queue"> </a>Procedura: Elenco delle code

Per recuperare un elenco di code, usare **listQueuesSegmented**. Per recuperare un elenco filtrato in base a uno specifico prefisso, usare **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Se non possono essere restituite tutte le code, è possibile usare `result.continuationToken` come primo parametro di **listQueuesSegmented** o secondo parametro di **listQueuesSegmentedWithPrefix** per recuperare più risultati.

## <a name="delete-queue"> </a>Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo
**deleteQueue** sull'oggetto coda.

    queueSvc.deleteQueue(queueName, function(error, response){
        if(!error){
            // Queue has been deleted
        }
    });

Per deselezionare tutti i messaggi da una coda senza eliminarla, usare **clearMessages**.

## <a name="sas"></a>Procedura: Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare alle code senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato alle code, ad esempio per consentire a un'app per dispositivi mobili di inviare messaggi.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **QueueService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà al titolare della firma di accesso condiviso di aggiungere i messaggi alla coda e che scadrà 100 minuti dopo la data di creazione.

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

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere alla coda.

L'applicazione client usa quindi la firma di accesso condiviso con il metodo **QueueServiceWithSAS** per eseguire operazioni sulla coda. Nell'esempio seguente viene eseguita la connessione alla coda e viene creato un messaggio.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Poiché la firma di accesso condiviso è stata generata con accesso di aggiunta, se si tentasse di leggere, aggiornare o eliminare i messaggi verrebbe restituito un errore.

### Elenchi di controllo di accesso

Per impostare i criteri di accesso per una firma di accesso condiviso è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere alla coda, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. Nell'esempio seguente vengono definiti due criteri, uno per 'user1' e uno per 'user2':

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

Nell'esempio seguente viene recuperato l'elenco di controllo di accesso corrente per **myqueue** e vengono aggiunti i nuovi criteri tramite **setQueueAcl**. Risultato:

    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre
informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][Archiviazione e accesso ai dati in Azure].
-   [Blog del team di Archiviazione di Azure][Blog del team di Archiviazione di Azure].
-   Archivio [Azure SDK for Node][Azure SDK for Node] su GitHub.

  [Passaggi successivi]: #next-steps
  [Informazioni sul Servizio di accodamento]: #what-is
  [Concetti]: #concepts
  [Creazione di un account di archiviazione di Azure]: #create-account
  [Creazione di un'applicazione Node.js]: #create-app
  [Configurazione dell'applicazione per l'accesso all'archiviazione]: #configure-access
  [Configurazione di una stringa di connessione di archiviazione di Azure]: #setup-connection-string
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inserire un messaggio in una coda]: #insert-message
  [Procedura: Visualizzare il messaggio successivo]: #peek-message
  [Procedura: Rimuovere il messaggio successivo dalla coda]: #get-message
  [Procedura: Cambiare il contenuto di un messaggio in coda]: #change-contents
  [Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda]: #advanced-get
  [Procedura: Recuperare la lunghezza della coda]: #get-queue-length
  [Procedura: Eliminare una coda]: #delete-queue
  [Procedura: Utilizzo delle firme di accesso condiviso]: #sas
  [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]: /it-it/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Sito Web con WebMatrix]: /it-it/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Applicazione Web Node.js con archiviazione]: /it-it/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Node]: https://github.com/Azure/azure-storage-node
