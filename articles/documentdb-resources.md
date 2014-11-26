<properties title="Interact with DocumentDB resources" pageTitle="Interact with DocumentDB resources | Azure" description="DocumentDB manages resources--uniquely identified by logical URIs--that developers can interact with using HTTP verbs, request/response headers, and status codes." metaKeywords="" services="documentdb" solutions="data-management" documentationCenter="" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Modello di risorse e concetti relativi a DocumentDB

DocumentDB offre un modello di programmazione RESTful su HTTP semplice e aperto. Le entità gestite da DocumentDB sono definite **risorse**, identificate in modo univoco dal rispettivo URI logico. Gli sviluppatori possono interagire con le risorse usando verbi HTTP standard, intestazioni di richiesta/risposta e codici di stato. Come illustrato nel diagramma seguente, il **modello di risorse** di DocumentDB è costituito da un insieme di risorse disponibili in un account di database e indirizzabili singolarmente tramite un URI logico e stabile.

> [AZURE.NOTE] DocumentDB offre anche un protocollo TCP a efficienza elevata, con un modello di comunicazione di tipo RESTful disponibile tramite l'SDK del client .NET.

![][0]
**Modello di risorse gerarchico in un account di database**

I clienti di DocumentDB eseguono prima di tutto il provisioning di un **account di database** di DocumentDB usando la sottoscrizione di Azure. Un account di database può essere costituito da un insieme di **database**, ognuno dei quali include più **raccolte**. Ogni raccolta include a sua volta **stored procedure, trigger, funzioni UDF, documenti** e **allegati** correlati. Un database include anche **utenti** associati, ognuno dei quali ha un insieme di **autorizzazioni** per accedere alle diverse raccolte, stored procedure, trigger, funzioni UDF, documenti o allegati disponibili. Mentre i database, gli utenti, le autorizzazioni e le raccolte sono ricorse definite dal sistema con schemi noti, i documenti e gli allegati includono contenuto JSON arbitrario definito dagli utenti.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>Risorsa</strong></p></td>
<td align="left"><p><strong>Descrizione</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Database</strong></p>
<p><strong>Account</strong></p></td>
<td align="left"><p>Un account di database è associato a una o più unità di capacità, che rappresentano l'archiviazione documenti e la velocità effettiva con provisioning, un insieme di database e l'archiviazione BLOB. È possibile creare uno o più account di database usando la sottoscrizione di Azure. Ogni account di database ha un nome DNS univoco.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>Database</strong></p></td>
<td align="left"><p>Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte. È anche un contenitore per gli utenti.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Utente</strong></p></td>
<td align="left"><p>Spazio dei nomi logico per la definizione dell'ambito delle autorizzazioni.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>Autorizzazione</strong></p></td>
<td align="left"><p>Token di autorizzazione associato a un utente per l'accesso autorizzato a una risorsa specifica.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Raccolta</strong></p></td>
<td align="left"><p>Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. L'ambito di query e transazioni è impostato sulle raccolte.</p></td>
</tr>
</tbody>
</table>

## Confronto tra risorse definite dal sistema e risorse definite dall'utente

Tutte le risorse quali account di database, database, raccolte, utenti, autorizzazioni, stored procedure, trigger e funzioni UDF hanno uno schema fisso e sono definite risorse di sistema. Per le risorse quali documenti e allegati, invece, non sono previste restrizioni a livello di schema. Si tratta di risorse definite dall'utente. In DocumentDB le risorse definite dal sistema e definite dall'utente sono rappresentate e gestite come risorse JSON conformi allo standard.

# Account di database

![][1]

È possibile eseguire il provisioning di uno o più account di database di DocumentDB usando la sottoscrizione di Azure. Per l'account di database è possibile acquistare unità organizzabili in stack di archiviazione documenti e velocità effettiva basate su SSD sotto forma di unità di capacità (CU, Capacity Units). In base alla scala delle applicazioni e alle esigenze a livello di prestazioni, è possibile aggiungere o rimuovere unità di capacità in modo incrementale. Ogni unità di capacità include un insieme di raccolte flessibili, archiviazione documenti con provisioning basata su SSD e velocità effettiva con provisioning. La capacità relativa a risorse di archiviazione e velocità effettiva con provisioning associata a un'unità di capacità è distribuita nelle raccolte di DocumentDB create nei diversi database disponibili per l'account di database. La capacità con provisioning in un account di database è disponibile per tutti i database e tutte le raccolte esistenti o creati nell'account. Non è previsto alcun limite di scala pratico per le dimensioni dell'account di database. È possibile aggiungere nel tempo qualsiasi numero di unità di capacità, in base alle limitazioni specifiche dell'offerta. La scalabilità orizzontale è applicata alle risorse gestite in un'unità di capacità tramite il partizionamento e le risorse sono replicate per assicurare una disponibilità elevata.

È possibile creare e gestire account di database di DocumentDB tramite il portale di Azure all'indirizzo [][]<http://portal.azure.com/></a>. Per la creazione e la gestione di un account di database è necessario l'accesso amministrativo e queste operazioni possono essere eseguite solo con una sottoscrizione di Azure. Come parte del provisioning e della gestione di un account di database, è possibile configurare e leggere le proprietà seguenti:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>Nome proprietà</strong></p></td>
<td align="left"><p><strong>Descrizione</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>Criterio di coerenza</p></td>
<td align="left"><p>Impostare questa proprietà per configurare il livello di coerenza predefinito per tutte le raccolte nell'account di database. È possibile eseguire l'override del livello di coerenza per le singole richieste, usando l'intestazione di richiesta [x-ms-consistency-level]. L'override del livello di coerenza per le singole raccolte potrebbe essere supportato in futuro.</p>
<p>Si noti che questa proprietà è applicabile solo alle <em>risorse definite dall'utente</em>. Tutte le risorse definite dal sistema sono configurate per il supporto di letture/query con coerenza assoluta.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Chiave master primaria e chiave master secondaria</p></td>
<td align="left"><p>Si tratta delle chiavi master primaria e secondaria che forniscono accesso amministrativo a tutte le risorse nell'account di database.</p></td>
</tr>
<tr class="even">
<td align="left"><p>MaxMediaStorageUsageInMB (READ)</p></td>
<td align="left"><p>Quantità massima di risorse di archiviazione multimediali disponibili per l'account di database.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>CurrentMediaStorageUsageInMB (READ)</p></td>
<td align="left"><p>Uso attuale delle risorse di archiviazione multimediali per l'account di database.</p></td>
</tr>
<tr class="even">
<td align="left"><p>CapacityUnitsProvisioned</p></td>
<td align="left"><p>Numero totale di unità di capacità con provisioning nell'account di database.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>CapacityUnitsConsumed (READ)</p></td>
<td align="left"><p>Numero totale di unità di capacità attualmente usate nell'account di database.</p></td>
</tr>
<tr class="even">
<td align="left"><p>ProvisionedDocumentStorageInMB (READ)</p></td>
<td align="left"><p>Quantità totale di archiviazione documenti (in MB) con provisioning in tutte le unità di capacità nell'account di database.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ReservedDocumentStorageInMB (READ)</p></td>
<td align="left"><p>Quantità totale di archiviazione documenti (in MB) riservata in tutte le unità di capacità nell'account di database. L'archiviazione documenti è definita riservata quando una raccolta include documenti.</p></td>
</tr>
<tr class="even">
<td align="left"><p>ConsumedDocumentStorageInMB (READ)</p></td>
<td align="left"><p>Quantità totale di archiviazione documenti (in MB) effettivamente usata in tutte le unità di capacità nell'account di database. Si tratta delle dimensioni combinate dei documenti e degli indici effettivi nelle diverse raccolte dell'account di database.</p></td>
</tr>
</tbody>
</table>

Si noti che, oltre al provisioning, alla configurazione e alla gestione dell'account di database dal portale di gestione di Azure, è anche possibile creare e gestire account di database di DocumentDB a livello di codice tramite le [API REST di Azure DocumentDB][API REST di Azure DocumentDB], oltre che tramite gli SDK dei client.

 

# Database

Un database in DocumentDB è un contenitore logico di una o più raccolte e utenti. È possibile creare qualsiasi numero di database in un account di database di DocumentDB, entro i limiti previsti dall'offerta.

![][2]

**A Database is a logical container of users and collections**

Un database può includere una quantità praticamente illimitata di archiviazione documenti, partizionata in base a raccolte, che costituiscono i domini di transazione per i documenti inclusi nelle raccolte stesse. Un database di DocumentDB è flessibile per impostazione predefinita e può includere da pochi GB a petabyte di archiviazione documenti e velocità effettiva con provisioning basate su SSD. A differenza di un database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a una singola macchina. Con DocumentDB, in caso di crescita delle esigenze di scalabilità dell'applicazione, sarà possibile creare più raccolte o più database o entrambi. Molte applicazioni prodotte direttamente da Microsoft usano DocumentDB su scala consumer, creando database DocumentDB di dimensioni estremamente elevate, ognuno dei quali include migliaia di raccolte con terabyte di archiviazione documenti. È possibile aumentare o ridurre le dimensioni di un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scalabilità dell'applicazione. Il numero di raccolte che è possibile creare in un database dipende dall'offerta e dalla quantità di unità di capacità acquistate. Le risorse di archiviazione e la velocità effettiva basate su SSD di cui è eseguito automaticamente il provisioning tramite l'acquisto di unità di capacità possono essere distribuite nelle diverse raccolte del database nell'account di database.

Un database di DocumentDB è anche un contenitore di utenti. Un utente, a sua volta, è uno spazio dei nomi logico per un insieme di autorizzazioni, che fornisce in modo dettagliato autorizzazioni/accesso a raccolte, documenti e allegati.

Analogamente ad altre risorse nel modello di risorse di DocumentDB, i database possono essere creati, sostituiti, eliminati, letti o enumerati con facilità tramite le [API REST di Azure DocumentDB][API REST di Azure DocumentDB] oppure uno degli SDK dei client. DocumentDB assicura una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di una risorsa del database. Se si elimina un database, non sarà automaticamente più possibile accedere alle raccolte o agli utenti inclusi nel database. Anche se DocumentDB recupera in background le risorse di archiviazione e la velocità effettiva con provisioning come parte del database eliminato, le risorse di archiviazione e la velocità effettiva con provisioning per il database eliminato saranno disponibili immediatamente per l'uso.

# Raccolte

Una raccolta di DocumentDB è un contenitore per i documenti JSON, oltre a essere un'unità di scala, transazioni e query. È possibile applicare la scalabilità orizzontale a un database di DocumentDB tramite l'aggiunta di altre raccolte. Se l'applicazione necessita di maggiore scalabilità, è possibile eseguire il provisioning di ulteriore spazio di archiviazione documenti basato su SSD (e di velocità effettiva) e distribuirlo nelle raccolte di uno o più database disponibili nell'account di database.

## Archiviazione documenti flessibile basata su SSD

Una raccolta è intrinsecamente flessibile, poiché le dimensioni della raccolta aumentano o si riducono in seguito all'aggiunta o alla rimozione di documenti. Anche se nella fase di uso produttore DocumentDB è stato testato con migliaia di raccolte in un database, ognuna delle quali con dimensioni comprese tra pochi gigabyte ad alcuni terabyte, l'offerta in anteprima standard di DocumentDB limita attualmente la flessibilità di una raccolta specifica a 10 GB.

## Indicizzazione automatica

DocumentDB è un sistema di database effettivamente privo di schema. Non si presuppone o richiede alcuno schema per i documenti JSON. DocumentDB indicizza automaticamente i documenti aggiunti a una raccolta e li rende disponibili per l'esecuzione di query. L'indicizzazione automatica di documenti senza la richiesta di schema o di indici secondari è una capacità chiave di DocumentDB ed è resa possibile da tecniche di gestione dell'indice ottimizzate per la scrittura, prive di blocco e strutturate in log. DocumentDB supporta un volume prolungato di scritture estremamente veloci, servendo al tempo stesso query coerenti. L'archiviazione documenti e l'archiviazione dell'indice sono usate per calcolare le risorse di archiviazione usate da ogni raccolta. È possibile controllare i compromessi tra archiviazione e prestazioni associati all'indicizzazione tramite la configurazione di un criterio di indicizzazione per una raccolta.

## Configurazione del criterio di indicizzazione per una raccolta

Il criterio di indicizzazione di ogni raccolta rende possibili i compromessi tra prestazioni e archiviazione associati all'indicizzazione. Le opzioni seguenti sono disponibili come parte della configurazione dell'indicizzazione:

-   Possibilità di scegliere se la raccolta indicizza automaticamente o meno tutti i documenti. Per impostazione predefinita, saranno indicizzati automaticamente tutti i documenti. È possibile scegliere di disattivare l'indicizzazione automatica e aggiungere in modo selettivo solo documenti specifici all'indice. In alternativa, è possibile scegliere di escludere in modo selettivo solo documenti specifici. Per ottenere questo risultato, impostare la proprietà automatica su true o false nella proprietà indexingPolicy di una raccolta e usare l'intestazione di richiesta [x-ms-indexingdirective] durante l'inserimento, la sostituzione o l'eliminazione di un documento.
-   Possibilità di scegliere se includere o escludere percorsi o modelli specifici nei documenti dall'indice. Per ottenere questo risultato, impostare rispettivamente includedPaths e excludedPaths in indexingPolicy di una raccolta. È anche possibile configurare i compromessi relativi ad archiviazione e prestazioni per un intervallo ed eseguire l'hashing di query per modelli di percorso specifici.
-   Possibilità di scegliere tra aggiornamenti sincroni (coerenti) e asincroni (differiti) dell'indice. Per impostazione predefinita, l'indice è aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di documenti. Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, oltre a offrire la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco per raccolte principalmente a uso intensivo di lettura.

Per la versione di anteprima di DocumentDB, il criterio di indicizzazione può essere configurato solo durante la creazione di una raccolta. Dopo la creazione della raccolta, non sarà più possibile aggiornare il criterio.

## Esecuzione di query in una raccolta

I documenti in una raccolta possono avere schemi arbitrari ed è possibile eseguire query sui documenti in una raccolta senza fornire anticipatamente alcuno schema o alcun indice secondario. È possibile eseguire query sulla raccolta usando il linguaggio di query SQL di DocumentDB, che offre operatori avanzati gerarchici e relazionali ed estendibilità tramite funzioni UDF basate su JavaScript. La grammatica JSON permette la modellazione di documenti JSON come alberi con etichette come nodi dell'albero. Questa struttura è usata dalle tecniche di indicizzazione automatica di DocumentDB e dal dialetto di query SQL di DocumentDB. Il linguaggio di query di DocumentDB è costituito da tre aspetti principali:

1.  Un insieme ridotto di operazioni di query mappate in modo naturale alla struttura ad albero, che include query e proiezioni gerarchiche.
2.  Un sottoinsieme di operazioni relazionali, incluse la composizione, l'applicazione di filtri, le proiezioni, le aggregazioni e i self join.
3.  Funzioni UDF pure basate su JavaScript, costituite da (1) e (2).

Il modello di query di DocumentDB tenta di ottenere un equilibrio tra funzionalità, efficienza e semplicità. Il motore di database di DocumentDB compila ed esegue in modalità nativa le istruzioni di query SQL. È possibile eseguire query in una raccolta usando le [API REST di Azure DocumentDB][API REST di Azure DocumentDB] oppure uno degli SDK dei client. In .NET SDK è disponibile un provider LINQ. In una versione futura saranno forniti i mapping nativi con caratteri di sottolineatura, che possono essere usati dagli SDK JavaScript lato client e dalle stored procedure e dai trigger lato server.

## Transazioni in più documenti

Le transazioni di database offrono un modello di programmazione sicuro e prevedibile per la gestione delle modifiche simultanee ai dati. In RDBMS la logica di business è tradizionalmente scritta tramite la scrittura di **stored procedure** e/o **trigger** ed è inviata al server database per l'esecuzione transazionale. In RDBMS il programmatore di applicazioni deve gestire due linguaggi di programmazione diversi: (a) il linguaggio di programmazione delle applicazioni (non transazionale), ad esempio JavaScript, Python, C\#, Java e così via, e (b) T-SQL, il linguaggio di programmazione transazionale eseguito in modalità nativa dal database. Grazie all'adozione completa di JavaScript e JSON direttamente nel motore di database, DocumentDB offre un modello di programmazione intuitivo per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nelle raccolte in termini di stored procedure e trigger. Ciò permette (a) un'implementazione efficiente del controllo della simultaneità, del ripristino, dell'indicizzazione automatica di grafici oggetti JSON direttamente nel motore di database, oltre a (b) l'espressione naturale del flusso di controllo, della definizione dell'ambito delle variabili e dell'integrazione di primitivi di gestione delle eccezioni con transazioni di database direttamente nei termini del linguaggio di programmazione JavaScript.

La logica JavaScript registrata a livello di raccolta può quindi rilasciare operazioni sui documenti della raccolta specifica. DocumentDB esegue implicitamente il wrapping di stored procedure e trigger basati su JavaScript in transazioni di ambiente ACID con isolamento degli snapshot nei documenti in una raccolta. Se JavaScript genera un'eccezione durante l'esecuzione, l'intera transazione sarà interrotta. Il modello di programmazione risultante è molto semplice ma efficace. Gli sviluppatori JavaScript ottengono un modello di programmazione "durevole", usando al tempo stesso i costrutti dei propri linguaggi preferiti e i primitivi di librerie.

La possibilità di eseguire JavaScript direttamente nel motore di database nello stesso spazio di indirizzi del pool di buffer permette l'esecuzione efficiente e transazionale di operazioni di database nei documenti di una raccolta. Poiché il motore di database di DocumentDB adotta completamente JSON e JavaScript, elimina eventuali mancate corrispondenze di impedenza tra i sistemi di tipi dell'applicazione e del database.

Dopo la creazione di una raccolta, sarà possibile registrare stored procedure, trigger e funzioni UDF con una raccolta usando le API REST di DocumentDB o qualsiasi SDK dei client. Dopo la registrazione, sarà possibile farvi riferimento ed eseguire questi elementi. La stored procedure seguente, scritta interamente in JavaScript, accetta due argomenti (titolo del libro e nome dell'autore) e crea un nuovo documento, esegue query per un documento e quindi lo aggiorna. Tutte queste operazioni sono eseguite tramite una transazione ACID implicita. Se durante l'esecuzione è generata un'eccezione JavaScript, l'intera transazione sarà interrotta.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Il client può "inviare" la logica JavaScript precedente al database per l'esecuzione transazionale tramite POST HTTP.

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });

Si noti che, poiché il database comprende in modo nativo JSON e JavaScript, non si verificano mancate corrispondenze tra sistemi di tipi e non sono necessari "mapping OR" o generazione di codice.

Le stored procedure e i trigger interagiscono con una raccolta e con i documenti in una raccolta tramite un modello a oggetti ben definito, che espone il contesto corrente della raccolta.

Le raccolte in DocumentDB possono essere create, eliminate, lette o enumerate con facilità tramite le [API REST di Azure DocumentDB][API REST di Azure DocumentDB] o qualsiasi SDK dei client. DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di raccolta. Se si elimina una raccolta, non sarà automaticamente più possibile accedere a documenti, allegati, stored procedure, trigger e funzioni UDF inclusi nella raccolta stessa. Anche se DocumentDB recupera in background le risorse di archiviazione e la velocità effettiva con provisioning come parte della raccolta eliminata, le risorse di archiviazione e la velocità effettiva con provisioning per la raccolta eliminata saranno disponibili immediatamente per l'uso.

 

# Stored procedure, trigger e funzioni UDF

Come illustrato nella sezione precedente, è possibile scrivere logica dell'applicazione per l'esecuzione direttamente in una transazione nel motore del database. La logica dell'applicazione può essere scritta interamente in JavaScript e può essere modellata come stored procedure, trigger o funzione definita dall'utente (UDF, User-Defined Function). Il codice JavaScript in una stored procedure o in un trigger può inserire, sostituire, eliminare, leggere o eseguire query su documenti in una raccolta. Il codice JavaScript in una funzione UDF, invece, può eseguire solo calcoli senza effetti collaterali tramite l'enumerazione di documenti del set di risultati della query e produrre un altro set di risultati. Per la multi-tenancy, DocumentDB applica una governance delle risorse basata in modo rigoroso sulle prenotazioni. Ogni stored procedure, trigger o funzione UDF ottiene una quantità fissa di risorse del sistema operativo per l'esecuzione delle operazioni. Le stored procedure, i trigger o le funzioni UDF, inoltre, non possono collegarsi a librerie JavaScript esterne e saranno disattivati in caso di superamento dei rispettivi budget di risorse allocati. È possibile registrare o annullare la registrazione di stored procedure, trigger o funzioni UDF con una raccolta tramite le API REST. Durante la registrazione, una stored procedure, un trigger o una funzione UDF saranno precompilati e archiviati come codice byte, che sarà eseguito in seguito. La sezione seguente illustra come usare l'SDK JavaScript di DocumentDB per registrare, eseguire, annullare la registrazione di una stored procedure, un trigger e una funzione UDF. JavaScript SDK è un semplice wrapper per le API REST di DocumentDB.

## Registrazione di una stored procedure

La registrazione di una stored procedure consiste nel creare una nuova risorsa stored procedure in una raccolta tramite HTTP POST.

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

## Esecuzione di una stored procedure

L'esecuzione di una stored procedure è effettuata tramite l'emissione di HTTP POST rispetto a una risorsa stored procedure esistente attraverso il passaggio di parametri alla procedura nel corpo della richiesta.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

## Annullamento della registrazione di una stored procedure

L'annullamento della registrazione di una stored procedure è eseguito in modo semplice tramite l'emissione di HTTP DELETE rispetto a una risorsa stored procedure esistente.

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });

## Registrazione di un trigger di tipo pre

La registrazione di un trigger è eseguita tramite la creazione di una nuova risorsa trigger in una raccolta tramite HTTP POST. È possibile specificare se si tratta di un trigger di tipo pre o post e il tipo di operazione a cui può essere associato, ad esempio creazione, sostituzione, eliminazione o tutte le operazioni.

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

## Esecuzione di un trigger di tipo pre

L'esecuzione di un trigger è effettuata specificando il nome di un trigger esistente contemporaneamente all'emissione della richiesta POST/PUT/DELETE di una risorsa documento tramite l'intestazione della richiesta.

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

## Annullamento della registrazione di un trigger di tipo pre

L'annullamento della registrazione di un trigger di tipo pre è eseguito in modo semplice tramite l'emissione di HTTP DELETE rispetto a una risorsa trigger esistente.

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

## Registrazione di una funzione UDF

La registrazione di una funzione UDF è eseguita tramite la creazione di una nuova risorsa UDF in una raccolta tramite HTTP POST.

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

## Esecuzione di una funzione UDF come parte della query

Una funzione UDF può essere specificata come parte della query SQL ed è usata come modo per estendere il linguaggio di query SQL di base di DocumentDB. Per altre informazioni sulla creazione di funzioni UDF nel linguaggio di query SQL di DocumentDB, fare riferimento alle specifiche del linguaggio di query SQL di DocumentDB.

    var filterQuery = "SELECT mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

## Annullamento della registrazione di una funzione UDF

L'annullamento della registrazione di una funzione UDF è eseguito in modo semplice tramite l'emissione di HTTP DELETE rispetto a una risorsa funzione UDF esistente.

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Benché i frammenti di codice riportati in precedenza hanno mostrato la registrazione (POST), l'annullamento della registrazione (PUT), la lettura/creazione di un elenco (GET) e l'esecuzione (POST) tramite l'SDK JavaScript di DocumentDB, è possibile usare anche le API REST o gli SDK di altri client.

 

# Documenti

È possibile inserire, sostituire, eliminare, leggere, enumerare ed eseguire query in documenti JSON arbitrari un una raccolta. DocumentDB non rende obbligatorio alcuno schema e non richiede indici secondari per il supporto delle query in documenti di una raccolta.

Essendo un servizio database effettivamente aperto, DocumentDB non propone tipo di dati specializzati (ad esempio data e ora) o codifica specifica per i documenti JSON. Si noti che DocumentDB non richiede alcuna convenzione JSON specifica per codificare le relazioni tra i diversi documenti. Il linguaggio di query SQL di DocumentDB offre operatori di query avanzati gerarchici e relazionali per eseguire query e proiezioni sui documenti senza annotazioni speciali o senza dovere codificare le relazioni tra i documenti tramite proprietà distinte.

Analogamente a tutte le altre risorse, i documenti possono essere creati, sostituiti, eliminati, letti, enumerati e sottoposto a query con facilità tramite le API REST o uno degli SDK dei client. Se si elimina un documento, la quota corrispondente a tutti gli allegati annidati sarà resa immediatamente disponibile. Il livello di coerenza di lettura dei documenti è basato sul criterio di coerenza applicato all'account di database. È possibile eseguire l'override di questo criterio per le singole richieste, in base ai requisiti di coerenza dei dati specifici dell'applicazione. Durante l'esecuzione di query nei documenti, la coerenza di lettura si basa sulla modalità di indicizzazione impostata per la raccolta. Per ottenere la coerenza, si basa sul criterio di coerenza dell'account.

# Allegati e file multimediali

DocumentDB permette di archiviare BLOB/file multimediali binari tramite DocumentDB o in un archivio remoto specifico per i file multimediali. Permette anche di rappresentare i metadati dei file multimediali sotto forma di un documento speciale definito allegato. Un allegato in DocumentDB è un documento speciale (JSON) che fa riferimento al file multimediale/BLOB archiviato altrove. Un allegato è semplicemente un documento speciale, che acquisisce i metadati (ad esempio percorso, autore e così via) di un file multimediale archiviato in un supporto di memorizzazione remoto.

Si prenda in considerazione un'applicazione di lettura di social media che usa DocumentDB per archiviare annotazioni a penna e metadati, inclusi commenti, evidenziazioni, segnalibri, classificazioni, commenti di tipo mi piace/non mi piace e così via, associati a un e-book di un utente specifico.

-   Il contenuto dell'e-book stesso è archiviato nel supporto di memorizzazione disponibile come parte dell'account di database di DocumentDB o in un archivio remoto di file multimediali.
-   Un'applicazione può archiviare i metadati di ogni utente come documento distinto, ad esempio i metadati di Joe per book1 saranno archiviati in un documento a cui si fa riferimento come /colls/joe/docs/book1.
-   Gli allegati che fanno riferimento alle pagine di contenuto di un determinato e-book di un utente sono archiviati nel documento corrispondente, ad esempio /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 e così via.

Si noti che gli esempi usano ID descrittivi per indicare la gerarchia delle risorse. L'accesso alle risorse è effettuato tramite le API REST mediante ID di risorsa univoci.

Nel caso dei file multimediali gestiti da DocumentDB, la proprietà \_media dell'allegato farà riferimento al file multimediale tramite il rispettivo URI. DocumentDB assicura la Garbage Collection del file multimediale dopo il rilascio di tutti i riferimenti in sospeso. DocumentDB genera automaticamente gli allegati quando si caricano nuovi file multimediali e popola la proprietà \_media in modo da fare riferimento ai file multimediali appena aggiunti. Se si sceglie di archiviare i file multimediali in un archivio BLOB remoto gestito personalmente, ad esempio OneDrive, Archiviazione di Azure, DropBox e così via, sarà comunque possibile usare gli allegati per fare riferimento ai file multimediali. In questo caso sarà necessario creare personalmente l'allegato e popolarne la proprietà \_media.

Analogamente a tutte le altre risorse, gli allegati possono essere creati, sostituiti, eliminati, letti o enumerati con facilità tramite le API REST o uno degli SDK dei client. Analogamente ai documenti. il livello di coerenza di lettura degli allegati è basato sul criteri di coerenza applicato all'account di database. È possibile eseguire l'override di questo criterio per le singole richieste, in base ai requisiti di coerenza dei dati specifici dell'applicazione. Durante l'esecuzione di query relative agli allegati, la coerenza di lettura si basa sulla modalità di indicizzazione impostata per la raccolta. Per ottenere la coerenza, si basa sul criterio di coerenza dell'account.

 

# Utenti

Un utente in DocumentDB rappresenta uno spazio dei nomi logico per il raggruppamento delle autorizzazioni. È possibile che un utente in DocumentDB corrisponda a un utente in un sistema di gestione delle identità o a un ruolo applicazione predefinito. In DocumentDB un utente rappresenta semplicemente un'astrazione per raggruppare un insieme di autorizzazioni in un database.

Per l'implementazione della multi-tenancy per l'applicazione, è possibile creare in DocumentDB utenti corrispondenti agli utenti effettivi o ai tenant dell'applicazione. Sarà quindi possibile creare autorizzazioni per un determinato utente, corrispondenti al controllo di accesso su diverse raccolte, documenti, allegati e così via.

Poiché la scalabilità delle applicazione deve essere adeguata all'incremento degli utenti, è possibile adottare modi diversi per partizionare i dati. È possibile modellare ogni utente in modo che:

-   Ogni utente sia mappato a un database
-   Ogni utente sia mappato a una raccolta
-   I documenti corrispondenti a più utenti sono mappati a una raccolta dedicata oppure
-   I documenti corrispondenti a più utenti siano modellati in base a un insieme di raccolte

Indipendentemente dalla strategia scelta per partizionare i dati, è possibile modellare gli utenti effettivi come utenti nel database di DocumentDB e associare autorizzazioni dettagliate a ogni utente.

![][3]

**Strategie per partizionare e modellazione di utenti**

Analogamente a tutte le altre risorse, gli utenti in DocumentDB possono essere creati, sostituiti, eliminati, letti o enumerati con facilità tramite le API REST o uno degli SDK dei client. DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di una risorsa utente. È utile segnalare che se si elimina un utente, non sarà automaticamente più possibile accedere alle autorizzazioni incluse nell'utente stesso. Anche se DocumentDB recupera in background la quota di autorizzazioni come parte dell'utente eliminato, le autorizzazioni eliminate saranno disponibili immediatamente per un nuovo uso.

# Autorizzazioni

Dal punto di vista del controllo di accesso, le risorse quali account di database, database, utenti e autorizzazioni sono considerate come risorse *amministrative*, poiché necessitano di autorizzazioni amministrative. L'ambito di risorse quali raccolte, documenti, allegati, stored procedure, trigger e funzioni UDF invece è limitato a un database specifico e queste risorse sono considerate *risorse dell'applicazione*. Il modello di autorizzazione definisce due tipo di *chiavi di accesso*, corrispondenti ai due tipi di risorse e ai ruoli usati per accedervi (ovvero amministratore e utente): *chiave master* e *chiave di risorsa*. La chiave master fa parte dell'account di database ed è fornita allo sviluppatore o all'amministratore che esegue il provisioning dell'account di database. La chiave master usa semantica di amministratore, ovvero può essere usata per autorizzare l'accesso alle risorse amministrative e dell'applicazione. La chiave di risorsa, invece, è una chiave di accesso granulare che permette l'accesso a una risorsa *specifica* dell'applicazione. Acquisisce quindi la relazione tra l'utente di un database e le autorizzazioni di cui l'utente dispone per una risorsa specifica, ad esempio una raccolta, un documento, un allegato, una stored procedure, un trigger o una funzione UDF.

L'unico modo per ottenere una chiave di risorsa consiste nella creazione di una risorsa di autorizzazione per un utente specifico. Si noti che per creare o recuperare un'autorizzazione è necessario presentare una chiave master nell'intestazione dell'autorizzazione. Una risorsa di autorizzazione associa la risorsa, l'accesso e l'utente. Dopo la creazione di una risorsa di autorizzazione, l'utente dovrà solo presentare la chiave di risorsa associata per ottenere l'accesso alla risorsa rilevante. Una chiave di risorsa può essere quindi considerata come una rappresentazione logica e compatta della risorsa di autorizzazione.

Analogamente a tutte le altre risorse, le autorizzazioni in DocumentDB possono essere create, sostituite, eliminate, lette o enumerate con facilità tramite le API REST o uno degli SDK dei client. DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di un'autorizzazione.

  [0]: ./media/documentdb-resources/resources1.png
  [1]: ./media/documentdb-resources/resources2.png
  [0]: http://portal.azure.com/
  [API REST di Azure DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402413
  [2]: ./media/documentdb-resources/resources3.png
  [3]: ./media/documentdb-resources/resources4.png
