<properties
	pageTitle="Come usare l'archiviazione tabelle di Azure da Node.js | Microsoft Azure"
	description="Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="micurd"/>


# Come usare l'archiviazione tabelle di Azure da Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]


## Panoramica

Questo argomento illustra come eseguire scenari comuni usando il servizio tabelle di Azure in un'applicazione Node.js.

Negli esempi di codice illustrati in questo argomento si suppone che sia già stata ottenuta un'applicazione Node.js. Per informazioni su come creare un'applicazione Node.js in Azure, vedere uno qualsiasi degli argomenti seguenti:

- [Creare un'app Web Node.js nel servizio app di Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Creare e distribuire un'applicazione Node.js in un servizio cloud di Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (usando Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Configurare l'applicazione per l'accesso all'archiviazione di Azure

Per usare Archiviazione di Azure, è necessario disporre di Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per installare il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione.

2.  Digitare **npm install azure-storage** nella finestra di comando. L'output da questo comando sarà simile al seguente esempio:

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

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di questa cartella si trova il pacchetto **azure-storage**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Aggiungere il codice seguente all'inizio del file **server.js** nell'applicazione:

	var azure = require('azure-storage');

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **TableService**.

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure](https://portal.azure.com) per un sito Web di Azure, vedere [App Web Node.js con il servizio tabelle di Azure].

## Creare una tabella

Il codice seguente consente di creare un oggetto **TableService** e di utilizzarlo per creare una nuova tabella. Aggiungere il codice seguente nella parte superiore di **server.js**.

	var tableSvc = azure.createTableService();

La chiamata a **createTableIfNotExists** crea una nuova tabella con il nome specificato, se non è già presente. Nell'esempio seguente viene creata una nuova tabella denominata "mytable" se questa non esiste ancora:

	tableSvc.createTableIfNotExists('mytable', function(error, result, response){
	  if(!error){
	    // Table exists or created
	  }
	});

`result.created` sarà `true` se viene creata una nuova tabella e sarà `false` se la tabella è già presente. `response` conterrà le informazioni sulla richiesta.

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **TableService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

	function handle (requestOptions, next)

Dopo aver eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

	function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione di returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **TableService** che utilizza **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare prima un oggetto che definisca le proprietà dell'entità. Tutte le entità devono contenere **PartitionKey** e **RowKey** che sono gli identificatori univoci dell'entità.

* **PartitionKey**: determina la partizione in cui è archiviata l'entità.

* **RowKey**: identifica in modo univoco l'entità all'interno della partizione.

Sia **PartitionKey** che **RowKey** devono essere valori stringa. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Nell'esempio seguente viene definita un'entità. **dueDate** è definito come tipo di **Edm.DateTime**. La specifica del tipo è facoltativa e si presuppone che i tipi non siano specificati.

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] Per ogni record è anche presente un campo **Timestamp**, impostato da Azure quando viene inserita o aggiornata un'entità.

È anche possibile usare **entityGenerator** per creare le entità. Nell'esempio seguente viene creata la stessa entità Task tramite **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
	var task = {
	  PartitionKey: entGen.String('hometasks'),
	  RowKey: entGen.String('1'),
	  description: entGen.String('take out the trash'),
	  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
	};

Per aggiungere un'entità alla tabella, passare l'oggetto entità al metodo **insertEntity**.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
	  if(!error){
	    // Entity inserted
	  }
	});

Se l'operazione ha esito positivo, `result` conterrà l'[ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del record inserito e `response` conterrà le informazioni sull'operazione.

Esempio di risposta:

	{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Per impostazione predefinita, **insertEntity** non restituisce l'entità inserita come parte delle informazioni di `response`. Se si prevede di eseguire altre operazioni su questa entità o si intende memorizzare le informazioni nella cache, è opportuno che l'entità venga restituita insieme a `result`. A tale scopo, abilitare **echoContent** come segue:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## Aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

* **replaceEntity**: aggiorna un'entità esistente sostituendola.

* **mergeEntity**: aggiorna un'entità esistente unendovi i nuovi valori delle proprietà.

* **insertOrReplaceEntity**: aggiorna un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova.

* **insertOrMergeEntity**: aggiorna un'entità esistente unendovi i nuovi valori delle proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene illustrato l'aggiornamento di un'entità mediante **replaceEntity**:

	tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
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
> 3. Eseguire l'operazione di aggiornamento. Se l'entità è stata modificata dall'ultimo recupero del valore di ETag, ad esempio da un'altra istanza dell'applicazione, viene restituito un `error` che indica che la condizione di aggiornamento specificata nella richiesta non è stata soddisfatta.

Con **replaceEntity** e **mergeEntity**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riesce. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, utilizzare **insertOrReplaceEntity** oppure **insertOrMergeEntity**.

In `result` per le operazioni di aggiornamento riuscite correttamente sarà incluso l'**Etag** dell'entità aggiornata.

## Usare i gruppi di entità

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A questo scopo, usare la classe **TableBatch** per creare un batch e quindi usare il metodo **executeBatch** di **TableService** per eseguire le operazioni in batch.

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

Le operazioni aggiunte a un batch possono essere esaminate visualizzando la proprietà `operations`. Per usare le operazioni sono disponibili anche i metodi seguenti:

* **clear**: cancella tutte le operazioni da un batch.

* **getOperations**: recupera un'operazione dal batch.

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

Per eseguire una query su una tabella, usare l'oggetto **TableQuery** per creare un'espressione di query con queste clausole:

* **select**: i campi che la query deve restituire.

* **where**: la clausola where.

	* **and**: una condizione where `and`.

	* **or**: una condizione where `or`.

* **top**: il numero di elementi da recuperare.


L'esempio seguente crea una query che restituisce i primi cinque elementi con PartitionKey 'hometasks'.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Poiché **select** non viene usato, vengono restituiti tutti i campi. Per eseguire la query su una tabella, usare **queryEntities**. Nell'esempio seguente viene usata questa query per restituire entità da 'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

Se la query ha esito positivo, `result.entries` conterrà una matrice delle entità che corrispondono alla query. Se la query non è in grado di restituire tutte le entità, `result.continuationToken` sarà un valore diverso da -*null* e potrà essere usato come terzo parametro di **queryEntities** per recuperare più risultati. Per la query iniziale, utilizzare *null* per il terzo parametro.

### Eseguire query su un subset di proprietà di entità

Una query su una tabella può recuperare solo alcuni campi da un'entità. Questa tecnica permette di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Usare la clausola **select** e passare i nomi dei campi da restituire. Ad esempio, la query seguente restituisce solo i campi **description** e **dueDate**.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## Eliminare un'entità

È possibile eliminare un'entità usando le relative chiavi di riga e di partizione. In questo esempio, l'oggetto **task1** contiene i valori **RowKey** e **PartitionKey** dell'entità da eliminare. L'oggetto viene quindi passato al metodo **deleteEntity**.

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

	tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
	    // Entity deleted
	  }
	});

> [AZURE.NOTE] Quando si eliminano elementi, è bene valutare l'uso di ETag per assicurarsi che l'elemento non sia stato modificato da un altro processo. Vedere [Aggiornare un'entità](#update-an-entity) per informazioni sull'uso di ETag.

## Eliminare una tabella

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

	tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

Se non si è certi dell'esistenza della tabella, usare **deleteTableIfExists**.

## Utilizzare i token di continuazione

Quando si esegue una query di tabelle di grandi quantità di risultati, ricercare i token di continuazione. Potrebbero essere disponibili grandi quantità di dati per la query di cui si potrebbe non essere consapevoli se non si compila il riconoscimento della presenza di un token di continuazione.

L'oggetto results restituito quando si esegue una query sulle entità imposta una proprietà `continuationToken` se è presente un token di questo tipo. È possibile quindi utilizzarlo quando si esegue una query per continuare a spostarsi tra le entità della partizione e della tabella.

Quando si esegue una query, è possibile specificare un parametro continuationToken tra l'istanza dell'oggetto della query e la funzione di richiamata:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se si osserva l'oggetto `continuationToken`, si troveranno proprietà come `nextPartitionKey`, `nextRowKey` e `targetLocation` che possono essere usate per eseguire l'iterazione di tutti i risultati.

È inoltre disponibile un esempio di continuazione all'interno del repository Node.js di Archiviazione di Azure su GitHub. Cercare `examples/samples/continuationsample.js`.

## Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare alle tabelle senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di eseguire query sui record.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **TableService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile, ad esempio a un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà al titolare della firma di accesso condiviso di eseguire una query ('r') per la tabella e che scadrà 100 minuti dopo la data di creazione.

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

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere alla tabella.

L'applicazione client usa quindi la firma di accesso condiviso con il metodo **TableServiceWithSAS** per eseguire operazioni sulla tabella. Nell'esempio seguente viene eseguita la connessione alla tabella e viene eseguita una query.

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

Per impostare i criteri di accesso per una firma di accesso condiviso è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere alla tabella, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. L'esempio seguente definisce due criteri, uno per 'user1' e uno per 'user2':

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

L'esempio seguente recupera l'elenco di controllo di accesso corrente per la tabella **hometasks** e quindi aggiunge i nuovi criteri tramite **setTableAcl**. Risultato:

	var extend = require('extend');
	tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## Passaggi successivi

Per altre informazioni, vedere le risorse seguenti:

-   [Blog del team di Archiviazione di Azure][].
-   Repository [Azure Storage SDK per Node][] su GitHub.
-   [Centro per sviluppatori di Node. js](/develop/nodejs/)

  [Azure Storage SDK per Node]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [App Web Node.js con il servizio tabelle di Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=AcomDC_0511_2016-->