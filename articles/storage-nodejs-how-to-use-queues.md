<properties 
	pageTitle="Come usare l'archiviazione di accodamento da Node.js | Microsoft Azure" 
	description="Informazioni su come usare il servizio di accodamento di Azure per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi. Gli esempi sono scritti in Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>


# Come usare l'archiviazione di accodamento da Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## Informazioni generali

Questa guida illustra come eseguire diversi scenari comuni con il
servizio di accodamento di Azure. Gli esempi sono scritti usando
l'API Python. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il
**recupero** e l'**eliminazione** dei messaggi in coda, nonché la **creazione ed
eliminazione di code**.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Creare un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Servizio cloud Node.js] (usando Windows PowerShell) o [Sito Web con WebMatrix].

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che
comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione di esempio.

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
    **node\_modules**. All'interno di questa cartella
    si trova il pacchetto **azure-storage**, che contiene le librerie necessarie per
    accedere all'archiviazione.

### Importare il pacchetto

Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file
**server.js** dell'applicazione dove si intende usare l'archiviazione:

    var azure = require('azure-storage');

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createQueueService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con il servizio tabelle di Azure].

## Procedura: Creare una coda

Il codice seguente crea un oggetto **QueueService**, che consente di
usare le code.

    var queueSvc = azure.createQueueService();

Usare il metodo **createQueueIfNotExists**, che restituisce la coda
specificata se esiste già o, in caso contrario, crea una nuova coda
con il nome specificato.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Se la coda viene creata, `result` è true. Se la coda esiste già, `result` è false.

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite con **QueueService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la seguente firma:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **QueueService** che usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Procedura: Inserire un messaggio in una coda

Per inserire un messaggio in una coda, usare il metodo **createMessage** per
creare un nuovo messaggio e aggiungerlo alla coda.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Procedura: Visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo
dalla coda chiamando il metodo **peekMessages**. Per impostazione predefinita,
**peekMessages** visualizza un singolo messaggio.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

 `result` contiene il messaggio.

> [AZURE.NOTE] Se si usa **peekMessages** in assenza di messaggi nella coda, non viene visualizzato un errore, ma non vengono restituiti messaggi.

## Procedura: Rimuovere il messaggio successivo dalla coda

L'elaborazione di un messaggio prevede un processo a due fasi:

1. Rimozione del messaggio dalla coda.

2. Eliminazione del messaggio.

Per rimuovere un messaggio dalla coda, usare **getMessage**. Il messaggio viene reso invisibile nella coda, in modo che gli altri client non possano elaborarlo. Dopo che l'applicazione ha elaborato il messaggio, chiamare **deleteMessage** per eliminarlo dalla coda. Nell'esempio seguente il messaggio viene prima richiamato, quindi eliminato:

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

> [AZURE.NOTE] Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi, dopo i quali torna visibile agli altri client. È possibile specificare un valore diverso usando `options.visibilityTimeout` con **getMessages**.

> [AZURE.NOTE]
> Se si usa <b>getMessages</b> in assenza di messaggi nella coda, non viene visualizzato un errore, ma non vengono restituiti messaggi.

## Procedura: Cambiare il contenuto di un messaggio in coda

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

## Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi:

* `options.numOfMessages`: consente di recuperare un batch di messaggi (massimo 32.)
* `options.visibilityTimeout`: consente di impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il metodo **getMessages** per recuperare 15 messaggi con una sola chiamata. Quindi,
ogni messaggio viene elaborato con un ciclo for. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

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

## Procedura: Recuperare la lunghezza della coda

**getQueueMetadata** restituisce metadati relativi alla coda, includendo il numero approssimativo di messaggi in attesa nella coda.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## Procedura: Elenco delle code

Per recuperare un elenco di code, usare **listQueuesSegmented**. Per recuperare un elenco filtrato in base a uno specifico prefisso, usare **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Se non possono essere restituite tutte le code, è possibile usare `result.continuationToken` come primo parametro di **listQueuesSegmented** o secondo parametro di **listQueuesSegmentedWithPrefix** per recuperare più risultati.

## Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene chiamare il metodo
**deleteQueue** sull'oggetto coda.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Per deselezionare tutti i messaggi da una coda senza eliminarla, usare **clearMessages**.

## Procedura: Usare le firme di accesso condiviso di Azure

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

Per impostare i criteri di accesso per una firma di accesso condiviso, è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere alla coda, impostando tuttavia criteri di accesso diversi per ogni client.

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

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso basata sull'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, usare i collegamenti seguenti
per informazioni sulle attività di archiviazione più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Archiviazione][]
-   [Blog del team di Archiviazione di Azure][]
-   Repository [Azure Storage SDK per Node.js][] su GitHub

  [Azure Storage SDK per Node.js]: https://github.com/Azure/azure-storage-node
  [Uso dell'API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [Creare e distribuire un'applicazione Node.js in un Sito Web di Azure]: web-sites-nodejs-develop-deploy-mac.md
  [Servizio cloud Node.js con Archiviazione]: storage-nodejs-use-table-storage-cloud-service-app.md
  [Applicazione Web Node.js con archiviazione]: storage-nodejs-use-table-storage-web-site.md

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Servizio cloud Node.js]: cloud-services-nodejs-develop-deploy-app.md
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 [Sito Web con WebMatrix]: web-sites-nodejs-use-webmatrix.md

<!--HONumber=49-->