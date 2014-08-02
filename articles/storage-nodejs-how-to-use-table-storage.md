<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

Come utilizzare il servizio tabelle da Node.js
==============================================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio tabelle di Azure. Gli esempi sono scritti utilizzando l'API Node.js. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento di entità ed esecuzione di query sulle entità in una tabella**. Per ulteriori informazioni sulle tabelle, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sul servizio tabelle](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Creazione di un'applicazione Node.js](#create-app)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione](#configure-access)
-   [Configurazione di una connessione di archiviazione di Azure](#setup-connection-string)
-   [Procedura: Creare una tabella](#create-table)
-   [Procedura: Aggiungere un'entità a una tabella](#add-entity)
-   [Procedura: Aggiornare un'entità](#update-entity)
-   [Procedura: Utilizzare i gruppi di entità](#change-entities)
-   [Procedura: Eseguire una query su un'entità](#query-for-entity)
-   [Procedura: Eseguire query su un set di entità](#query-set-entities)
-   [Procedura: Eseguire una query su un subset di proprietà di entità](#query-entity-properties)
-   [Procedura: Eliminare un'entità](#delete-entity)
-   [Procedura: Eliminare una tabella](#delete-table)
-   [Passaggi successivi](#next-steps)

Informazioni sul servizio tabelle
---------------------------------

Il servizio tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Di seguito sono riportati gli utilizzi più comuni per il servizio tabelle.

-   Immagazzinamento di una quantità enorme di dati strutturati (molti TB) che vengono automaticamente ridimensionati per soddisfare le esigenze di velocità effettiva
-   Archiviazione di set di dati che non richiedono join complessi, chiavi esterne o stored procedure e che possono essere denormalizzati per l'accesso rapido
-   Esecuzione rapida di query sui dati, ad esempio profili utente, mediante un indice cluster

È possibile utilizzare il servizio tabelle per archiviare e interrogare grandi set di dati strutturati non relazionali, con tabelle scalabili in base all'aumento del volume.

Concetti
--------

Il servizio tabelle contiene i componenti seguenti:

![Table1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **Formato dell'URL:** il codice fa riferimento alle tabelle in un account utilizzando il formato di indirizzo seguente:

        http://storageaccount.table.core.windows.net/table  

    È possibile fare riferimento direttamente alle tabelle di Azure utilizzando questo indirizzo con il protocollo OData. Per ulteriori informazioni, vedere [OData.org](http://www.odata.org/)

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. La dimensione totale dei contenuti di BLOB, tabelle e code in un account di archiviazione non può superare 100 TB.

-   **Tabella**: una tabella è una raccolta illimitata di entità. Le tabelle non impongono uno schema sulle entità, pertanto una singola tabella può contenere entità che presentano set di proprietà diversi. Un account può contenere molte tabelle.

-   **Entità**: un'entità è un set di proprietà, simile a una riga di database. Un'entità può avere una dimensione massima di 1 MB.

-   **Proprietà**: una proprietà è una coppia nome-valore. Ogni entità può includere fino a 252 proprietà per l'archiviazione dei dati. Ogni entità dispone inoltre di tre proprietà di sistema che specificano una chiave di partizione, una chiave di riga e un timestamp. Le entità con la stessa chiave di partizione possono essere interrogate più rapidamente e inserite o aggiornate in operazioni atomiche. La chiave di riga di un'entità ne rappresenta l'identificatore univoco all'interno di una partizione.

Creazione di un account di archiviazione di Azure
-------------------------------------------------

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito (è possibile creare un account di archiviazione anche [utilizzando l'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx)).

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.  Nella parte inferiore del pannello di navigazione fare clic su **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.

    ![Finestra di dialogo Creazione rapida](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  Nel campo URL, digitare un nome di sottodominio da utilizzare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

6.  Fare clic su **Create Storage Account**.

Creazione di un'applicazione Node.js
------------------------------------

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servizio cloud Node.js]({localLink:2221} "App Web con Express") (utilizzando Windows PowerShell) o [Sito Web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configurazione dell'applicazione per l'accesso all'archiviazione
----------------------------------------------------------------

Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Node.js Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare Node Package Manager (NPM) per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire il seguente output:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella sarà presente il pacchetto **azure**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

    var azure = require('azure');

Configurazione di una connessione di archiviazione di Azure
-----------------------------------------------------------

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **TableService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Servizio cloud Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Come creare una tabella
-----------------------

Il codice seguente consente di creare un oggetto **TableService** e di utilizzarlo per creare una nuova tabella. Aggiungere il codice seguente nella parte superiore di **server.js**.

    var tableService = azure.createTableService();

La chiamata a **createTableIfNotExists** restituirà la tabella specificata se esistente o ne creerà una nuova con il nome specificato, se non esiste ancora. Nell'esempio seguente viene creata una nuova tabella denominata "mytable" se questa non esiste ancora:

    tableService.createTableIfNotExists('mytable', function(error){
        if(!error){
            // Table exists or created
        }
    });

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **TableService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

     function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

     function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **TableService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

Come aggiungere un'entità a una tabella
---------------------------------------

Per aggiungere un'entità, creare innanzitutto un oggetto che definisce le proprietà dell'entità e i relativi tipi di dati. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Il sistema utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. RowKey **è l'ID univoco dell'entità all'interno della partizione cui appartiene.** Per aggiungere un'entità alla tabella, passare l'oggetto entità al metodo **insertEntity**.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Take out the trash'
        , DueDate: new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if(!error){
            // Entity inserted
        }
    });

Come aggiornare un'entità
-------------------------

Esistono vari metodi per aggiornare un'entità esistente:

-   **updateEntity** - Consente di aggiornare un'entità esistente sostituendola.

-   **mergeEntity** - Consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà.

-   **insertOrReplaceEntity** - Consente di aggiornare un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova.

-   **insertOrMergeEntity** - Consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene dimostrato l'aggiornamento di un'entità mediante l'utilizzo di **updateEntity**:

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if(!error){
            // Entity has been updated
        }
    });

Con **updateEntity** e **mergeEntity**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario utilizzare **insertOrReplaceEntity** oppure **insertOrMergeEntity**.

Come utilizzare gruppi di entità
--------------------------------

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. Per realizzare ciò, viene utilizzato il metodo **beginBatch** su **TableService** e quindi viene chiamata normalmente la serie di operazioni. La differenza è che le funzioni di callback di questi operatori indicheranno che l'operazione è stata integrata in batch e non inviata al server. Quando si desidera inviare il batch, si chiamerà **commitBatch**. Il callback fornito a tale metodo indicherà se l'invio dell'intero batch è riuscito. Nell'esempio seguente viene dimostrato l'invio di due entità in un batch:

    var tasks=[
        {
            PartitionKey : 'hometasks'
            , RowKey : '1'
            , Description : 'Take out the trash.'
            , DueDate : new Date(2012, 6, 20)
        }
        , {
            PartitionKey : 'hometasks'
            , RowKey : '2'
            , Description : 'Wash the dishes.'
            , DueDate : new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if(!error){
                    // Entity inserted
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if(!error){
                // All inserts completed
                tableService.commitBatch(function(error){
                    if(!error){
                        // Batch successfully commited
                    }
                });
            }
        });

**Nota**

Nell'esempio precedente viene utilizzato il modulo "async" per garantire che le entità siano state tutte inviate correttamente prima di chiamare **commitBatch**.

Come eseguire query su un'entità
--------------------------------

Per eseguire una query su un'entità in una tabella, utilizzare il metodo **queryEntity** passando i parametri **PartitionKey** e **RowKey**.

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if(!error){
                // entity contains the returned entity
            }
        });

Come eseguire query su un set di entità
---------------------------------------

Per eseguire una query su una tabella, utilizzare l'oggetto **TableQuery** per creare un'espressione di query utilizzando clausole quali **select**, **from**, **where** (comprese clausole di servizio quali **wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey** e **whereNextRowKey**), **and**, **or** e **top**. Passare quindi l'espressione di query al metodo **queryEntities**. È possibile utilizzare i risultati in un ciclo **for** all'interno del callback.

In questo esempio vengono individuate tutte le attività di Seattle in base a **PartitionKey**.

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if(!error){
            //entities contains an array of entities
            }
        });

Come eseguire query su un subset di proprietà di entità
-------------------------------------------------------

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Utilizzare la clausola **select** e passare i nomi delle proprietà da inoltrare al client.

La query nel codice seguente restituisce solo le **descrizioni** delle entità nella tabella. Si noti che nell'output del programma **DueDate** viene mostrato come **undefined** poiché non è stato inviato dal server.

**Nota**

Si noti che il frammento seguente funziona solo su un servizio di archiviazione cloud; la parola chiave **select** non è supportata dall'emulatore di archiviazione.

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if(!error){
            //entities contains an array of entities
            }
        });

Come eliminare un'entità
------------------------

È possibile eliminare un'entità utilizzando le relative chiavi di riga e di partizione. In questo esempio, l'oggetto **task1** contiene i valori **RowKey** e **PartitionKey** dell'entità da eliminare. L'oggetto viene quindi passato al metodo **deleteEntity**.

    tableService.deleteEntity('mytable'
        , {
            PartitionKey : 'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if(!error){
                // Entity deleted
            }
        });

Come eliminare una tabella
--------------------------

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

    tableService.deleteTable('mytable', function(error){
        if(!error){
            // Table deleted
        }
    });

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Archivio [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) su GitHub

