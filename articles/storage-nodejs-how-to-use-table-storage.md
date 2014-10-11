<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Come utilizzare il servizio tabelle da Node.js

In questa guida viene spiegato come eseguire diversi scenari comuni con il servizio tabelle di Windows
Azure. Gli esempi sono scritti con l'API
Node.js. Gli scenari presentati includono la **creazione e l'eliminazione di una
tabella, l'inserimento e l'esecuzione di query sulle entità in una tabella**. Per altre
informazioni sulle tabelle, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni sul Servizio tabelle][]
-   [Concetti][]
-   [Creazione di un account di archiviazione di Azure][]
-   [Creazione di un'applicazione Node.js][]
-   [Configurazione dell'applicazione per l'accesso all'archiviazione][]
-   [Configurazione di una connessione di archiviazione di Azure][]
-   [Procedura: Creare una tabella][]
-   [Procedura: Aggiungere un'entità a una tabella][]
-   [Procedura: Aggiornare un'entità][]
-   [Procedura: Utilizzare i gruppi di entità][]
-   [Procedura: Recuperare un'entità][]
-   [Procedura: Eseguire query su un set di entità][]
-   [Procedura: Eliminare un'entità][]
-   [Procedura: Eliminare una tabella][]
-   [Procedura: Usare le firme di accesso condiviso di Azure][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-table-storage][]]

## <a name="create-account"></a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account][]]

## <a name="create-app"> </a>Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][], [Servizio cloud Node.js][] (utilizzando Windows PowerShell) o [Sito Web con WebMatrix][].

## <a name="configure-access"> </a>Configurazione dell'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di librerie che
comunicano con i servizi di archiviazione REST.

### Utilizzare Node Package Manager (NPM) per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure-storage** nella finestra di comando, che dovrebbe restituire il seguente output:

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

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **TableService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione][]

## <a name="create-table"> </a>Come creare una tabella

Il codice seguente crea un oggetto **TableService** e lo usa per
creare una nuova tabella. Aggiungere il codice seguente nella parte superiore di **server.js**.

    var tableSvc = azure.createTableService();

La chiamata a **createTableIfNotExists** crea una nuova tabella con il nome specificato, se non
esiste ancora. Nell'esempio seguente viene creata una nuova tabella denominata "mytable" se questa non esiste ancora:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
        if(!error){
            // Table exists or created
        }
    });

`result` è `true` se viene creata una nuova tabella e `false` se la tabella esiste già. `response` contiene informazioni sulla richiesta.

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **TableService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

        function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

        function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **TableService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Come aggiungere un'entità a una tabella

Per aggiungere un'entità, creare prima un oggetto che definisca le proprietà dell'
entità. Tutte le entità devono contenere **PartitionKey** e **RowKey** che sono gli identificatori univoci dell'entità.

-   **PartitionKey**: determina la partizione in cui viene archiviata l'entità.

-   **RowKey**: identifica in modo univoco l'entità all'interno della partizione.

Sia **PartitionKey** che **RowKey** devono essere valori stringa. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle][].

Nell'esempio seguente viene definita un'entità. **dueDate** è definito come tipo di **Edm.DateTime**. La specifica del tipo è facoltativa e si presuppone che i tipi non siano specificati.

    var task = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [WACOM.NOTE] Per ciascun record, esiste anche un campo **Timestamp** impostato da Azure quando viene inserita o aggiornata un'entità.

È anche possibile usare **entityGenerator** per creare le entità. Nell'esempio seguente viene creata la stessa entità Task tramite **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Per aggiungere un'entità alla tabella, passare l'oggetto entità al
metodo **insertEntity**.

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
        if(!error){
            // Entity inserted
        }
    });

Se l'operazione riesce, `result` conterrà il valore [ETag][] del record inserito e `response` conterrà le informazioni sull'operazione.

> [WACOM.NOTE] Per impostazione predefinita, **insertEntity** non restituisce l'entità inserita come parte delle informazioni di `response`. Se si prevede di eseguire altre operazioni su questa entità o se si vogliono memorizzare le informazioni nella cache, è opportuno che l'entità venga restituita insieme a `result`. A tale scopo, abilitare **echoContent** come segue:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>Come aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

-   **updateEntity** - Consente di aggiornare un'entità esistente sostituendola.

-   **mergeEntity** - Consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà.

-   **insertOrReplaceEntity** - Consente di aggiornare un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova.

-   **insertOrMergeEntity** - Consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene dimostrato l'aggiornamento di un'entità mediante l'utilizzo di **updateEntity**:

    tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] Per impostazione predefinita, durante l'aggiornamento di un'entità non viene eseguita una verifica dei dati per controllare se siano stati modificati da altri processi. Per supportare gli aggiornamenti simultanei:
>
> 1.  Recuperare il valore ETag dell'oggetto da aggiornare. Questo valore viene restituito insieme a `response` per qualsiasi operazione associata all'entità e può essere recuperato tramite `response['.metadata'].etag`.
>
> 2.  Quando si esegue un'operazione di aggiornamento su un'entità, aggiungere le informazioni ETag precedentemente recuperate alla nuova entità. Ad esempio:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3.  Eseguire l'operazione di aggiornamento. Se l'entità è stata modificata dall'ultimo recupero del valore ETag, ad esempio da un'altra istanza dell'applicazione, viene restituito un `error` che indica che la condizione di aggiornamento specificata nella richiesta non è stata soddisfatta.

Con **updateEntity** e **mergeEntity**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario utilizzare **insertOrReplaceEntity** oppure **insertOrMergeEntity**.

In `result` per le operazioni di aggiornamento riuscite correttamente è incluso il valore **Etag** dell'entità aggiornata.

## <a name="change-entities"> </a>Come utilizzare gruppi di entità

È talvolta consigliabile inviare più operazioni in un
batch per garantire l'elaborazione atomica da parte del server. A questo scopo,
usare la classe **TableBatch** per creare un batch, quindi usare il metodo **executeBatch** di **TableService** per eseguire le operazioni in batch.

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

Per le operazioni batch riuscite correttamente, `result` conterrà le informazioni su ciascuna operazione nel batch.

### Utilizzo delle operazioni in batch

Le operazioni aggiunte a un batch possono essere esaminate visualizzando la proprietà `operations`. Per usare le operazioni sono disponibili anche i seguenti metodi.

-   **clear**: deseleziona tutte le operazioni di un batch.

-   **getOperations**: recupera un'operazione dal batch.

-   **hasOperations**: restituisce true se il batch contiene operazioni.

-   **removeOperations**: rimuove un'operazione.

-   **size**: restituisce il numero di operazioni nel batch.

## <a name="query-for-entity"> </a>Come recuperare un'entità

Per restituire una specifica entità in base a **PartitionKey** e **RowKey**, usare il metodo **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Dopo aver completato l'operazione, `result` conterrà l'entità.

## <a name="query-set-entities"> </a>Come eseguire query su un set di entità

Per eseguire una query su una tabella, usare l'oggetto **TableQuery** per creare un'espressione della query
usando le seguenti clausole:

-   **select**: i campi che la query deve restituire.

-   **where**: la clausola where.

    -   **and**: una condizione where `and`.

    -   **or**: una condizione where `or`.

-   **top**: il numero di elementi da recuperare.

Nell'esempio seguente viene creata una query che restituisce i primi cinque elementi con PartitionKey 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Poiché **select** non viene usato, vengono restituiti tutti i campi. Per eseguire la query su una tabella, usare **queryEntities**. Nell'esempio seguente viene usata questa query per restituire entità da 'mytable'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Se riesce, `result.entries` conterrà una matrice di entità corrispondenti alla query. Se la query non ha restituito tutte le entità, è possibile usare `result.continuationToken` come terzo parametro di **queryEntities** per recuperare più risultati. Per la query iniziale, il secondo parametro deve essere *null*.

### Come eseguire query su un subset di proprietà di entità

Una query su una tabella può restituire solo pochi campi da un'entità.
Ciò riduce la larghezza di banda e può migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Usare la clausola **select** e passare i nomi dei campi da restituire. Ad esempio, la query seguente restituisce solo i campi **description** e **dueDate**.

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>Come eliminare un'entità

È possibile eliminare un'entità utilizzando le relative chiavi di riga e di partizione. In questo
esempio, l'oggetto **task1** contiene i valori **RowKey** e
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

> [WACOM.NOTE] Quando si eliminano gli elementi, considerare l'uso di valori ETag per assicurarsi che l'elemento non sia stato modificato da un altro processo. Vedere [Procedura: Aggiornare un'entità][] per informazioni sull'uso di ETag.

## <a name="delete-table"> </a>Come eliminare una tabella

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Se non si è certi dell'esistenza della tabella, usare **deleteTableIfExists**.

## <a name="sas"></a>Procedura: Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare alle tabelle senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di eseguire query sui record.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **TableService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

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

Nell'esempio seguente viene recuperato l'elenco di controllo di accesso corrente per la tabella **hometasks** e vengono aggiunti i nuovi criteri tramite **setTableAcl**. Risultato:

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

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti
seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][]
-   [Blog del team di Archiviazione di Azure][]
-   Archivio [Azure SDK for Node][] su GitHub.

  [Passaggi successivi]: #next-steps
  [Informazioni sul Servizio tabelle]: #what-is
  [Concetti]: #concepts
  [Creazione di un account di archiviazione di Azure]: #create-account
  [Creazione di un'applicazione Node.js]: #create-app
  [Configurazione dell'applicazione per l'accesso all'archiviazione]: #configure-access
  [Configurazione di una connessione di archiviazione di Azure]: #setup-connection-string
  [Procedura: Creare una tabella]: #create-table
  [Procedura: Aggiungere un'entità a una tabella]: #add-entity
  [Procedura: Aggiornare un'entità]: #update-entity
  [Procedura: Utilizzare i gruppi di entità]: #change-entities
  [Procedura: Recuperare un'entità]: #query-for-entity
  [Procedura: Eseguire query su un set di entità]: #query-set-entities
  [Procedura: Eliminare un'entità]: #delete-entity
  [Procedura: Eliminare una tabella]: #delete-table
  [Procedura: Usare le firme di accesso condiviso di Azure]: #sas
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]: /en-us/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Servizio cloud Node.js]: /en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Sito Web con WebMatrix]: /en-us/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Applicazione Web Node.js con archiviazione]: /en-us/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Informazioni sul modello di dati del servizio tabelle]: http://msdn.microsoft.com/library/azure/dd179338.aspx
  [ETag]: http://en.wikipedia.org/wiki/HTTP_ETag
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Node]: https://github.com/Azure/azure-storage-node
