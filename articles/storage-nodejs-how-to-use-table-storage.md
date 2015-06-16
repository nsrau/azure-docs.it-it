<properties 
	pageTitle="Come usare l'archiviazione tabelle da Node.js | Microsoft Azure" 
	description="Informazioni su come usare il servizio di archiviazione tabelle in Azure. Gli esempi di codice sono scritti usando l'API Node.js." 
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


# Come usare l'archiviazione tabelle da Node.js

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]


## Informazioni generali

Questo argomento descrive come eseguire diversi scenari comuni con il 
servizio tabelle di Azure in un'applicazione Node.js. 

Negli esempi di codice illustrati in questo argomento si suppone che sia già stata ottenuta un'applicazione Node.js. Per istruzioni su come creare un'applicazione Node.js in Azure, vedere uno degli argomenti seguenti:

- [Creare e distribuire un sito Web Node.js in Azure][Creare e distribuire un'applicazione Node.js in un Sito Web di Azure]
- [Creare e distribuire un sito Web Node.js in Azure con WebMatrix][Sito Web con WebMatrix].
- [Creare e distribuire un'applicazione Node.js in un servizio cloud di Azure][Servizio cloud Node.js] (usando Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]


## Configurare l'applicazione per l'accesso all'archiviazione di Azure

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che
comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per installare il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione.

2.  Digitare **npm install azure-storage** nella finestra di comando, che dovrebbe restituire questo output:

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
    **node_modules**. All'interno di questa cartella
    si trova il pacchetto **azure-storage**, che contiene le librerie necessarie per
    accedere all'archiviazione.

### Importare il pacchetto

Aggiungere il codice seguente all'inizio del file **server.js** nell'applicazione:

    var azure = require('azure-storage');

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente AZURE_STORAGE_ACCOUNT e AZURE_STORAGE_ACCESS_KEY o AZURE_STORAGE_CONNECTION_STRING per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni relative all'account quando si chiama **TableService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con il servizio tabelle di Azure].

## Creare una tabella

Il codice seguente crea un oggetto **TableService** e lo usa per
creare una nuova tabella. Aggiungere il codice seguente nella parte superiore di **server.js**.

    var tableSvc = azure.createTableService();

La chiamata a **createTableIfNotExists** crea una nuova tabella con il nome specificato, se non
esiste ancora. Nell'esempio seguente viene creata una nuova tabella denominata  'mytable' se questa non esiste ancora:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

 `result` sarà `true` se viene creata una nuova tabella e sarà `false` se la tabella è già presente. `response` conterrà le informazioni sulla richiesta.

### Filtri

È possibile applicare operazioni di filtro facoltative alle operazioni eseguite usando **TableService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la seguente firma:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Questo codice crea un oggetto **TablebService** che usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare prima un oggetto che definisca le proprietà
dell'entità. Tutte le entità devono contenere **PartitionKey** e **RowKey**, che sono gli identificatori univoci per l'entità.

* **PartitionKey**: determina la partizione in cui è archiviata l'entità.

* **RowKey**: identifica in modo univoco l'entità all'interno della partizione.

**PartitionKey** e **RowKey** devono essere entrambi valori stringa. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Nell'esempio seguente viene definita un'entità. Si noti che **dueDate** è definito come tipo di **Edm.DateTime**. La specifica del tipo è facoltativa e si presuppone che i tipi non siano specificati.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] Per ogni record è anche presente un campo **Timestamp**, impostato da Azure quando viene inserita o aggiornata un'entità.

È anche possibile usare **entityGenerator** per creare entità. Nell'esempio seguente viene creata la stessa entità task usando **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Per aggiungere un'entità alla tabella, passare l'oggetto entità al metodo
**insertEntity**.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

Se l'operazione ha esito positivo, `result` conterrà l'[ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del record inserito e `response` conterrà le informazioni sull'operazione.

Esempio di risposta:

	{ '.metadata': { etag: 'W/"datetime'2015-02-25T01%3A22%3A22.5Z'"' } }

> [AZURE.NOTE] Per impostazione predefinita, **insertEntity** non restituisce l'entità inserita come parte delle informazioni di `response`. Se si prevede di eseguire altre operazioni su questa entità o si intende memorizzare le informazioni nella cache, è opportuno che l'entità venga restituita insieme a `result`. A questo scopo, è possibile abilitare **echoContent** nel modo seguente:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## Aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

* **updateEntity**: aggiorna un'entità esistente sostituendola.

* **mergeEntity**: aggiorna un'entità esistente unendovi i nuovi valori di proprietà.

* **insertOrReplaceEntity**: aggiorna un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova.

* **insertOrMergeEntity**: aggiorna un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Ecco un esempio che mostra l'aggiornamento di un'entità usando **updateEntity**:

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Per impostazione predefinita, l'aggiornamento di un'entità non comporta la verifica dei dati per controllare se siano stati modificati da altri processi. Per supportare gli aggiornamenti simultanei:
> 
> 1. Recuperare il valore ETag dell'oggetto da aggiornare. Questo valore viene restituito insieme a `response` per qualsiasi operazione associata all'entità e può essere recuperato tramite `response['.metadata'].etag`.
> 
> 2. Quando si esegue un'operazione di aggiornamento su un'entità, aggiungere le informazioni ETag precedentemente recuperate alla nuova entità. Ad esempio:
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. Eseguire l'operazione di aggiornamento. Se l'entità è stata modificata dall'ultimo recupero del valore di ETag, ad esempio da un'altra istanza dell'applicazione, viene restituito un oggetto `error` che indica che la condizione di aggiornamento specificata nella richiesta non è stata soddisfatta.
    
Con **updateEntity** e **mergeEntity**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riesce. Di conseguenza, se si vuole archiviare un'entità indipendentemente dalla sua esistenza o meno, è necessario usare **insertOrReplaceEntity** o **insertOrMergeEntity**.

In `result` per le operazioni di aggiornamento riuscite correttamente sarà incluso l'**Etag** dell'entità aggiornata.

## Usare i gruppi di entità

È talvolta consigliabile inviare più operazioni in un
batch per garantire l'elaborazione atomica da parte del server. A questo scopo, usare la classe **TableBatch** per creare un batch e quindi usare il metodo **executeBatch** di **TableService** per eseguire le operazioni in batch.

 Nell'esempio seguente viene dimostrato l'invio di due entità in un batch:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

Per le operazioni in batch riuscite, `result` conterrà le informazioni relative a ogni operazione del batch.

### Uso delle operazioni in batch

Le operazioni aggiunte a un batch possono essere esaminate visualizzando la proprietà `operations`. Per usare le operazioni sono disponibili anche i seguenti metodi.

* **clear**: cancella tutte le operazioni da un batch.

* **getOperations**: ottiene un'operazione dal batch.

* **hasOperations**: restituisce true se il batch contiene operazioni.

* **removeOperations**: rimuove un'operazione.

* **size**: restituisce il numero di operazioni nel batch.

## Recuperare un'entità in base alla chiave

Per restituire un'entità specifica in base a **PartitionKey** e **RowKey**, usare il metodo **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

Al termine di questa operazione, `result` conterrà l'entità.

## Eseguire query su un set di entità

Per eseguire una query su una tabella, usare l'oggetto **TableQuery** per creare un'espressione di query
con queste clausole:

* **select**: campi che la query deve restituire.

* **where**: clausola where.

	* **and**: condizione where `and`.

	* **or**: condizione where `or`.

* **top**: numero di elementi da recuperare.


Nell'esempio seguente viene creata una query che restituisce i primi cinque elementi con PartitionKey  'hometasks'

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Poiché la clausola **select** non viene usata, vengono restituiti tutti i campi. Per eseguire la query su una tabella, usare **queryEntities**. Nell'esempio seguente viene usata questa query per restituire entità da  'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

Se la query ha esito positivo, `result.entries` conterrà una matrice delle entità che corrispondono alla query. Se la query non è in grado di restituire tutte le entità, `result.continuationToken` sarà un valore diverso da *null* e potrà essere usato come terzo parametro di **queryEntities** per recuperare più risultati. Per la query iniziale, il terzo parametro deve essere  *null*.

### Come eseguire query su un subset di proprietà di entità

Una query su una tabella può recuperare solo alcuni campi da un'entità.
Questa tecnica permette di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Usare la clausola **select** e passare i nomi dei campi da restituire. Ad esempio, la query seguente restituirà solo i campi **description** e **dueDate**.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## Come eliminare un'entità

È possibile eliminare un'entità usando le relative chiavi di riga e di partizione. In questo
esempio l'oggetto **task1** contiene i valori **RowKey** e
**PartitionKey** dell'entità da eliminare. L'oggetto viene quindi
passato al metodo **deleteEntity**.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [AZURE.NOTE] Quando si eliminano elementi, è bene valutare l'uso di ETag per assicurarsi che l'elemento non sia stato modificato da un altro processo. Vedere [Procedura: Aggiornare un'entità][] per informazioni sull'uso di ETag.

## Come eliminare una tabella

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

In caso di dubbi sull'esistenza o meno della tabella, usare **deleteTableIfExists**.

## Procedura: Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare alle tabelle senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app mobile di eseguire query sui record.

Un'applicazione attendibile, come un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **TableService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente vengono generati nuovi criteri di accesso condiviso che permetteranno al proprietario della firma di accesso condiviso di eseguire query ('r') sulla tabella e che scadranno 100 minuti dopo la data di creazione.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il proprietario della firma di accesso condiviso tenta di accedere alla tabella.

L'applicazione client usa quindi la firma di accesso condiviso con **TableServiceWithSAS** per eseguire operazioni sulla tabella. Nell'esempio seguente viene eseguita la connessione alla tabella e viene eseguita una query.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

Poiché la firma di accesso condiviso è stata generata con accesso solo query, se si tentasse di inserire, aggiornare o eliminare le entità verrebbe restituito un errore.

### Elenchi di controllo di accesso

Per impostare i criteri di accesso per una firma di accesso condiviso, è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere alla tabella, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. Nell'esempio seguente vengono definiti due criteri, uno per 'user1' e uno per 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

Nell'esempio seguente viene recuperato l'elenco di controllo di accesso corrente per **hometasks** e vengono aggiunti i nuovi criteri tramite **setTableAcl**. Risultato:

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso basata sull'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, seguire questi collegamenti
per informazioni su come eseguire attività di archiviazione più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][]
-   [Blog del team di Archiviazione di Azure][]
-   Repository [Azure Storage SDK per Node.js][] su GitHub

  [Azure Storage SDK per Node.js]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Uso dell'API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Portale di gestione di Azure]: http://manage.windowsazure.com

  [Servizio cloud Node.js]: cloud-services-nodejs-develop-deploy-app.md
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Sito Web con WebMatrix]: app-service-web/web-sites-nodejs-use-webmatrix.md
  [Servizio cloud Node.js con Archiviazione]: storage-nodejs-use-table-storage-cloud-service-app.md
  [Applicazione Web Node.js con il servizio tabelle di Azure]: app-service-web/storage-nodejs-use-table-storage-web-site.md
  [Creare e distribuire un'applicazione Node.js in un Sito Web di Azure]: app-service-web/web-sites-nodejs-develop-deploy-mac.md

<!--HONumber=49-->