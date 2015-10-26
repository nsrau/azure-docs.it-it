<properties 
	pageTitle="Programmazione di DocumentDB: Stored procedure, trigger del database e funzioni definite dall'utente | Microsoft Azure" 
	description="Informazioni su come usare DocumentDB per scrivere stored procedure, trigger del database e funzioni definite dall'utente (UDF) in JavaScript. Ottenere suggerimenti sulla programmazione di database e altro ancora." 
	keywords="Database triggers, stored procedure, stored procedure, database program, sproc, documentdb, azure, Microsoft azure"
	services="documentdb" 
	documentationCenter="" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="andrl"/>

# Programmazione sul lato server DocumentDB: stored, procedure, trigger del database e funzioni definite dall'utente

Informazioni su come l'esecuzione integrata e transazionale di JavaScript con il linguaggio di DocumentDB consenta agli sviluppatori di scrivere **stored procedure**, **trigger** e **funzioni definite dall'utente (UDF)** in modo nativo in JavaScript. Ciò consente di scrivere la logica dell'applicazione del programma del database che può essere inserita ed eseguita direttamente nelle partizioni di archiviazione del database.

Si consiglia di iniziare guardando il video seguente, in cui Andrew Liu introduce brevemente il modello di programmazione lato server del database di DocumentDB.

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Tornare quindi a questo articolo, che fornisce risposte alle domande seguenti:

- Come è possibile scrivere un una stored procedure, un trigger o una funzione definita dall'utente usando JavaScript?
- In che modo DocumentDB garantisce proprietà ACID?
- Come funzionano le transazioni in DocumentDB?
- Cosa sono i pre-trigger e i post-trigger e come si scrivono?
- Come si registra e si esegue una stored procedure, un trigger o una funzione definita dall'utente in modalità RESTful usando HTTP?
- Quali SDK di DocumentDB sono disponibili per la creazione e l'esecuzione di stored procedure, trigger e funzioni definite dall'utente?

## Introduzione alle store procedure e alla programmazione delle funzioni definite dall'utente

Questo approccio di *"JavaScript come nuovo T-SQL"* libera gli sviluppatori di applicazioni dalle complessità delle mancate corrispondenze nel sistema dei tipi e delle tecnologie di mapping relazionale a oggetti. Comporta anche una serie di vantaggi intrinseci che possono essere utili per creare applicazioni avanzate:

-	**Logica procedurale:** in qualità di linguaggio di programmazione di alto livello, JavaScript offre un'interfaccia completa e familiare per esprimere la logica di business. È possibile eseguire sequenze complesse di operazioni a ridosso dei dati.

-	**Transazioni atomiche:** DocumentDB garantisce che l'esecuzione di operazioni eseguite all'interno di una singola stored procedure o un trigger di database siano atomiche. Di conseguenza, un'applicazione potrà combinare le operazioni correlate in unico batch, in modo che o tutte o nessuna di esse avranno esito positivo.

-	**Prestazioni:** il fatto che JSON sia intrinsecamente mappato al sistema di tipi in linguaggio JavaScript e rappresenti anche l'unità di base di archiviazione in DocumentDB permette di eseguire una serie di ottimizzazioni, tra cui la materializzazione differita dei documenti JSON nel pool di buffer e la loro disponibilità su richiesta per il codice in esecuzione. Vi sono altri vantaggi associati all'integrazione della logica di business nel database:
	-	Invio in batch: gli sviluppatori possono raggruppare le operazioni, come gli inserimenti, e inviarle in blocco. Ciò comporta una drastica riduzione del costo legato alla latenza del traffico di rete e dei costi generali di archiviazione per la creazione di transazioni separate. 
	-	Precompilazione: DocumentDB precompila stored procedure, trigger e funzioni definite dall'utente (UDF) per evitare il costo della compilazione JavaScript per ogni chiamata. I costi generali di compilazione del codice byte per la logica procedurale vengono ammortizzati a un valore minimo.
	-	Sequenziazione: molte operazioni necessitano di un effetto collaterale ("trigger") che implica potenzialmente l'esecuzione di una o più operazioni di archiviazione secondarie. Atomicità a parte, offre prestazioni migliori quando viene passata al server. 
-	**Incapsulamento:** è possibile usare le stored procedure per raggruppare la logica di business in un solo posto. Ciò comporta due vantaggi:
	-	Aggiunge un livello di astrazione al di sopra dei dati non elaborati, consentendo ai responsabili dell'architettura dati di far evolvere le proprie applicazioni indipendentemente dai dati. Si tratta di una caratteristica particolarmente vantaggiosa quando i dati sono privi di schema, a causa dei presupposti transitori che potrebbe essere necessario integrare nell'applicazione qualora fosse necessario gestire i dati direttamente.  
	-	Questa astrazione consente alle grandi imprese di proteggere i propri dati semplificando l'accesso dagli script.  

La creazione e l'esecuzione di trigger del database, stored procedure e operatori di query personalizzati sono supportate tramite l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e gli [SDK client](https://msdn.microsoft.com/library/azure/dn781482.aspx) in molte piattaforme, tra cui .NET, Node.js e JavaScript. **Questa esercitazione usa [Node.js SDK](http://dl.windowsazure.com/documentDB/nodedocs/)** per illustrare la sintassi e l'utilizzo di stored procedure, trigger e funzioni definite dall'utente.

## Stored procedure

### Esempio: scrivere una stored procedure semplice 
Per cominciare, si analizzerà una semplice stored procedure che restituisce una risposta "Hello World".

	var helloWorldStoredProc = {
	    id: "helloWorld",
	    body: function () {
	        var context = getContext();
	        var response = context.getResponse();
	
	        response.setBody("Hello, World");
	    }
	}


Le stored procedure vengono registrate per ogni raccolta e funzionano in qualsiasi documento e allegato presente nella raccolta. Nel seguente frammento viene spiegato come registrare la stored procedure helloWorld con una raccolta.

	// register the stored procedure
	var createdStoredProcedure;
	client.createStoredProcedureAsync(collection._self, helloWorldStoredProc)
		.then(function (response) {
		    createdStoredProcedure = response.resource;
		    console.log("Successfully created stored procedure");
		}, function (error) {
		    console.log("Error", error);
		});


Dopo aver registrato la stored procedure è possibile eseguirla a fronte della raccolta e leggere i risultati sul client.

	// execute the stored procedure
	client.executeStoredProcedureAsync(createdStoredProcedure._self)
		.then(function (response) {
		    console.log(response.result); // "Hello, World"
		}, function (err) {
		    console.log("Error", error);
		});


L'oggetto contesto offre l'accesso a tutte le operazioni che è possibile eseguire nelle risorse di archiviazione di DocumentDB, oltre all'accesso agli oggetti richiesta e risposta. In questo caso, è stata usato l'oggetto risposta per impostare il corpo della risposta restituita al client. Per altre informazioni, vedere la [documentazione relativa all'SDK del server JavaScript di DocumentDB](http://dl.windowsazure.com/documentDB/jsserverdocs/).

Elaborando ulteriormente questo esempio, è possibile aggiungere alla stored procedure altre funzionalità relative al database. Le stored procedure possono creare, aggiornare, eseguire query ed eliminare documenti e allegati all'interno della raccolta.

### Esempio: scrivere una stored procedure per creare un documento 
Il frammento successivo mostra come usare l'oggetto context per interagire con le risorse di DocumentDB.

	var createDocumentStoredProc = {
	    id: "createMyDocument",
	    body: function createMyDocument(documentToCreate) {
	        var context = getContext();
	        var collection = context.getCollection();
	
	        var accepted = collection.createDocument(collection.getSelfLink(),
	              documentToCreate,
				function (err, documentCreated) {
				    if (err) throw new Error('Error' + err.message);
				    context.getResponse().setBody(documentCreated.id)
				});
	        if (!accepted) return;
	    }
	}


Questa stored procedure accetta come input documentToCreate, il corpo di un documento da creare nella raccolta corrente. Tutte queste operazioni sono asincrone e dipendono dai callback della funzione JavaScript. La funzione di callback ha due parametri: uno per l'oggetto errore in caso di errore dell'operazione e uno per l'oggetto creato. All'interno del callback, gli utenti possono gestire l'eccezione oppure generare un errore. Nel caso in cui non sia disponibile un callback e si verifichi un errore, il runtime di DocumentDB genera un errore.

Nell'esempio precedente il callback genera un errore se l'operazione non è riuscita. In caso contrario, viene impostato l'ID del documento creato come corpo della risposta al client. Ecco come viene eseguita questa stored procedure con i parametri di input.

	// register the stored procedure
	client.createStoredProcedureAsync(collection._self, createDocumentStoredProc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
	
		    // run stored procedure to create a document
		    var docToCreate = {
		        id: "DocFromSproc",
		        book: "The Hitchhiker’s Guide to the Galaxy",
		        author: "Douglas Adams"
		    };
	
		    return client.executeStoredProcedureAsync(createdStoredProcedure._self,
	              docToCreate);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response); // "DocFromSproc"
	}, function (error) {
	    console.log("Error", error);
	});

	
Notare che è possibile modificare questa stored procedure in modo che accetti una matrice di corpi di documenti come input e possa crearli tutti nell'esecuzione della stessa stored procedure, invece di inviare più richieste di rete per crearle individualmente. Ciò permette di implementare un'efficiente utilità di importazione in blocco per DocumentDB (di cui si discuterà più avanti in questa esercitazione).

L'esempio descritto ha dimostrato come usare le stored procedure. I trigger e le funzioni definite dall'utente (UDF) saranno trattati più avanti in questa esercitazione.

## Transazioni del programma del database
Una transazione in un tipico database può essere definita come una sequenza di operazioni eseguite come singola unità di lavoro logica. Ogni transazione offre **garanzie ACID**. ACID è un acronimo noto che riassume quattro proprietà: Atomicità, Coerenza, Isolamento e Durabilità.

In breve, l'atomicità garantisce che tutte le operazioni eseguite nell'ambito di una transazione siano trattate come unità singola e che venga eseguito il commit di tutte le operazioni o di nessuna. La coerenza garantisce che i dati siano sempre in ottimo stato interno in tutte le transazioni. L'isolamento garantisce che non vi siano transazioni conflittuali; in generale, la maggior parte dei sistemi commerciali offre più livelli di isolamento utilizzabili in base alle esigenze dell'applicazione. La durabilità assicura che qualsiasi modifica di cui sia stato eseguito il commit nel database sia sempre presente.

In DocumentDB, JavaScript è ospitato nello stesso spazio di memoria del database. Di conseguenza, le richieste effettuate nell'ambito delle stored procedure e dei trigger vengono eseguite nello stesso ambito di una sessione di database. Ciò consente a DocumentDB di garantire proprietà ACID per tutte le transazioni che fanno parte di una singola stored procedure o un unico trigger. Considerare la definizione della stored procedure seguente:

	// JavaScript source code
	var exchangeItemsSproc = {
	    name: "exchangeItems",
	    body: function (playerId1, playerId2) {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        var player1Document, player2Document;
	
	        // query for players
	        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
	            function (err, documents, responseOptions) {
	                if (err) throw new Error("Error" + err.message);
	
	                if (documents.length != 1) throw "Unable to find both names";
	                player1Document = documents[0];
	
	                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
	                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
	                    function (err2, documents2, responseOptions2) {
	                        if (err2) throw new Error("Error" + err2.message);
	                        if (documents2.length != 1) throw "Unable to find both names";
	                        player2Document = documents2[0];
	                        swapItems(player1Document, player2Document);
	                        return;
	                    });
	                if (!accept2) throw "Unable to read player details, abort ";
	            });
	
	        if (!accept) throw "Unable to read player details, abort ";
	
	        // swap the two players’ items
	        function swapItems(player1, player2) {
	            var player1ItemSave = player1.item;
	            player1.item = player2.item;
	            player2.item = player1ItemSave;
	
	            var accept = collection.replaceDocument(player1._self, player1,
	                function (err, docReplaced) {
					    if (err) throw "Unable to update player 1, abort ";
	
					    var accept2 = collection.replaceDocument(player2._self, player2,
	                        function (err2, docReplaced2) {
							    if (err) throw "Unable to update player 2, abort"
							});
	
					    if (!accept2) throw "Unable to update player 2, abort";
					});
	
	            if (!accept) throw "Unable to update player 1, abort";
	        }
	    }
	}
	
	// register the stored procedure in Node.js client
	client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
		}
	);

Questa stored procedure usa le transazioni in un'applicazione di gioco per scambiare elementi tra due giocatori in un'unica operazione. La stored procedure prova a leggere due documenti, ciascuno corrispondente all'ID del giocatore passato come argomento. Se vengono trovati i documenti di entrambi i giocatori, la stored procedure aggiorna i documenti scambiando gli elementi. Se si verificano errori lungo il percorso, viene generata un'eccezione JavaScript che interrompe implicitamente la transazione.

### Commit e rollback
Le transazioni sono integrate in maniera approfondita e nativa nel modello di programmazione JavaScript di DocumentDB. All'interno di una funzione JavaScript, viene eseguito il wrapping di tutte le operazioni in un'unica transazione. Se la funzione JavaScript viene completata senza eccezioni, viene eseguito il commit delle operazioni nel database. In effetti, le istruzioni "BEGIN TRANSACTION" e "COMMIT TRANSACTION" nei database relazionali sono implicite in DocumentDB.
 
Se si verifica un'eccezione qualsiasi che viene propagata dallo script, il runtime JavaScript di DocumentDB eseguirà il rollback dell'intera transazione. Come illustrato in un esempio precedente, la generazione di un'eccezione equivale effettivamente a una "ROLLBACK TRANSACTION" in DocumentDB.
 
### Coerenza dei dati
Le stored procedure e i trigger vengono sempre eseguiti sulla cartella di replica principale della raccolta di DocumentDB. In tal modo si garantisce la coerenza assoluta delle letture all'interno delle stored procedure. È possibile eseguire le query che usano funzioni definite dall'utente sulla replica principale o in qualsiasi replica secondaria, ma è necessario assicurarsi di soddisfare il livello di coerenza richiesto scegliendo la replica appropriata.

## Esecuzione vincolata
È necessario che tutte le operazioni di DocumentDB siano completate entro la scadenza specificata dal server. Questo vincolo si applica anche alle funzioni JavaScript (stored procedure, trigger e funzioni definite dall'utente). Se un'operazione non viene completata entro questo limite di tempo, viene eseguito il rollback della transazione. Le funzioni JavaScript devono terminare entro il tempo limite oppure implementare un modello basato sulla continuazione in modo da riprendere l'esecuzione o eseguirla in batch.

Per semplificare lo sviluppo delle stored procedure e dei trigger per la gestione dei limiti di tempo, tutte le funzioni nell'oggetto raccolta (per creare, leggere, sostituire ed eliminare i documenti e gli allegati) restituiscono un valore booleano che indica se l'operazione verrà completata. Se questo valore è falso, sarà indice che il tempo limite sta per scadere e che è necessario concludere l'esecuzione della procedura. Il completamento delle operazioni inserite in coda precedentemente alla prima operazione di archiviazione non accettata è garantito se la stored procedure viene completata in tempo e non vengono inserite in coda altre richieste.

Anche le funzioni JavaScript sono vincolate al consumo di risorse. DocumentDB riserva una velocità effettiva per ogni raccolta in base alle dimensioni dell'account di database di cui è stato effettuato il provisioning. La velocità effettiva viene espressa in termini di un'unità normalizzata di consumo CPU, memoria e IO, denominata unità di richiesta, o RU. Le funzioni JavaScript possono potenzialmente usare un numero elevato di RU in breve tempo; per tale motivo, è possibile che venga limitata la frequenza del traffico di rete quando si raggiunge il limite della raccolta. È anche possibile che le stored procedure che richiedono un utilizzo elevato di risorse vengano messe in quarantena per assicurare la disponibilità delle operazioni di database primitive.

### Esempio: importazione in blocco dei dati in un programma di database
Di seguito è riportato un esempio di stored procedure scritta per importare in blocco i documenti in una raccolta. Da notare il modo in cui la stored procedure gestisce l'esecuzione vincolata verificando il valore booleano restituito da createDocument e quindi usa il numero di documenti inseriti in ogni chiamata della stored procedure per tracciare e riprendere l'avanzamento nei vari batch.

	function bulkImport(docs) {
	    var collection = getContext().getCollection();
	    var collectionLink = collection.getSelfLink();
	
	    // The count of imported docs, also used as current doc index.
	    var count = 0;
	
	    // Validate input.
	    if (!docs) throw new Error("The array is undefined or null.");
	
	    var docsLength = docs.length;
	    if (docsLength == 0) {
	        getContext().getResponse().setBody(0);
	    }
	
	    // Call the create API to create a document.
	    tryCreate(docs[count], callback);
	
	    // Note that there are 2 exit conditions:
	    // 1) The createDocument request was not accepted. 
	    //    In this case the callback will not be called, we just call setBody and we are done.
	    // 2) The callback was called docs.length times.
	    //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
	    function tryCreate(doc, callback) {
	        var isAccepted = collection.createDocument(collectionLink, doc, callback);
	
	        // If the request was accepted, callback will be called.
	        // Otherwise report current count back to the client, 
	        // which will call the script again with remaining set of docs.
	        if (!isAccepted) getContext().getResponse().setBody(count);
	    }
	
	    // This is called when collection.createDocument is done in order to process the result.
	    function callback(err, doc, options) {
	        if (err) throw err;
	
	        // One more document has been inserted, increment the count.
	        count++;
	
	        if (count >= docsLength) {
	            // If we created all documents, we are done. Just set the response.
	            getContext().getResponse().setBody(count);
	        } else {
	            // Create next document.
	            tryCreate(docs[count], callback);
	        }
	    }
	}

## <a id="trigger"></a> Trigger del database
### Pre-trigger del database
DocumentDB fornisce trigger che vengono eseguiti o attivati da un'operazione su un documento. Ad esempio, quando si crea un documento è possibile specificare un pre-trigger, che verrà eseguito prima della creazione. Di seguito è riportato un esempio di come è possibile usare i pre-trigger per convalidare le proprietà di un documento in corso di creazione:

	var validateDocumentContentsTrigger = {
	    name: "validateDocumentContents",
	    body: function validate() {
	        var context = getContext();
	        var request = context.getRequest();
	
	        // document to be created in the current operation
	        var documentToCreate = request.getBody();
	
	        // validate properties
	        if (!("timestamp" in documentToCreate)) {
	            var ts = new Date();
	            documentToCreate["my timestamp"] = ts.getTime();
	        }
	
	        // update the document that will be created
	        request.setBody(documentToCreate);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.Create
	}


E il corrispondente codice di registrazione lato client di Node.js per il trigger:

	// register pre-trigger
	client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
		.then(function (response) {
		    console.log("Created", response.resource);
		    var docToCreate = {
		        id: "DocWithTrigger",
		        event: "Error",
		        source: "Network outage"
		    };
	
		    // run trigger while creating above document 
		    var options = { preTriggerInclude: "validateDocumentContents" };
	
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response.resource); // document with timestamp property added
	}, function (error) {
	    console.log("Error", error);
	});


I pre-trigger non possono avere parametri di input. L'oggetto richiesta può essere usato per gestire il messaggio di richiesta associato all'operazione. In questo caso, si esegue il pre-trigger preliminare contemporaneamente alla creazione di un documento e il corpo del messaggio di richiesta contiene il documento da creare in formato JSON.

Quando i trigger vengono registrati, gli utenti possono specificare le operazioni con le quali è possibile eseguirli. Questo trigger è stato creato con TriggerOperation.Create, quindi quanto riportato di seguito non è consentito.

	var options = { preTriggerInclude: "validateDocumentContents" };
	
	client.replaceDocumentAsync(docToReplace.self,
	              newDocBody, options)
	.then(function (response) {
	    console.log(response.resource);
	}, function (error) {
	    console.log("Error", error);
	});
	
	// Fails, can’t use a create trigger in a replace operation

### Post-trigger del database
I post-trigger, come i pre-trigger, sono associati a un'operazione su un documento e non accettano parametri di input. Vengono eseguiti **dopo** il completamento dell'operazione e hanno accesso al messaggio di risposta inviato al client.

Nell'esempio seguente vengono illustrati i post-trigger in azione:

	var updateMetadataTrigger = {
	    name: "updateMetadata",
	    body: function updateMetadata() {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        // document that was created
	        var createdDocument = response.getBody();
	
	        // query for metadata document
	        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
	            updateMetadataCallback);
	        if(!accept) throw "Unable to update metadata, abort";
	 
	        function updateMetadataCallback(err, documents, responseOptions) {
	            if(err) throw new Error("Error" + err.message);
	                     if(documents.length != 1) throw 'Unable to find metadata document';
	                     
	                     var metadataDocument = documents[0];
	                     
	                     // update metadata
	                     metadataDocument.createdDocuments += 1;
	                     metadataDocument.createdNames += " " + createdDocument.id;
	                     var accept = collection.replaceDocument(metadataDocument._self,
	                           metadataDocument, function(err, docReplaced) {
	                                  if(err) throw "Unable to update metadata, abort";
	                           });
	                     if(!accept) throw "Unable to update metadata, abort";
	                     return;                    
	        }																							
	    },
	    triggerType: TriggerType.Post,
	    triggerOperation: TriggerOperation.All
	}


È possibile registrare un trigger come illustrato nell'esempio seguente.

	// register post-trigger
	client.createTriggerAsync(collection.self, updateMetadataTrigger)
		.then(function(createdTrigger) { 
		    var docToCreate = { 
		        name: "artist_profile_1023",
		        artist: "The Band",
		        albums: ["Hellujah", "Rotators", "Spinning Top"]
		    };
	
		    // run trigger while creating above document 
		    var options = { postTriggerInclude: "updateMetadata" };
		
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});


Il trigger esegue query sul documento dei metadati, aggiornandolo con le informazioni relative al documento appena creato.

Un aspetto importante da tenere presente è l'esecuzione **transazionale** dei trigger in DocumentDB. Questo post-trigger viene eseguito come parte della stessa transazione relativa alla creazione del documento originale. Pertanto, se si genera un'eccezione dal post-trigger (ad esempio se non è possibile aggiornare il documento dei metadati), l'intera transazione avrà esito negativo e verrà ripristinata allo stato precedente. Non verrà creato alcun documento e verrà restituita un'eccezione.

##<a id="udf"></a>Funzioni definite dall'utente
Le funzioni definite dall'utente, o UDF, consentono di estendere la grammatica del linguaggio di query SQL di DocumentDB e implementare la logica di business personalizzata. Possono essere richiamate solo dall'interno delle query. Non hanno accesso all'oggetto contesto e vanno usate come JavaScript di solo calcolo. È quindi possibile eseguire le UDF su repliche secondarie del servizio DocumentDB.
 
L'esempio seguente consente di creare una funzione definita dall'utente per calcolare l'imposta sul reddito in base ai tassi relativi a varie fasce di reddito. La funzione viene quindi usata all'interno di una query per trovare tutte le persone che hanno pagato oltre 20.000 dollari di imposte.

	var taxUdf = {
	    name: "tax",
	    body: function tax(income) {
	
	        if(income == undefined) 
	            throw 'no input';
	
	        if (income < 1000) 
	            return income * 0.1;
	        else if (income < 10000) 
	            return income * 0.2;
	        else
	            return income * 0.4;
	    }
	}


La funzione UDF può in seguito essere usata in query come quella riportata nell'esempio seguente:

	// register UDF
	client.createUserDefinedFunctionAsync(collection.self, taxUdf)
		.then(function(response) { 
		    console.log("Created", response.resource);
	
		    var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
		    return client.queryDocuments(collection.self,
	               query).toArrayAsync();
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    var documents = response.feed;
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});

## API della Language-Integrated Query di JavaScript
Oltre a eseguire una query utilizzando la sintassi SQL del DocumentDB, il SDK sul lato server consente di eseguire query ottimizzate tramite un'interfaccia intuitiva JavaScript senza alcuna conoscenza di SQL. L’API della query JavaScript consente di creare query a livello di programmazione passando funzioni predicate in chiamate di funzione concatenabili, con una sintassi familiare alle librerie Javascript predefinite e diffuse della matrice ECMAScript5 come lodash. Le query vengono analizzate dal runtime JavaScript per essere eseguite in modo efficiente utilizzando gli indici di DocumentDB.

> [AZURE.NOTE]`__` (doppio carattere di sottolineatura) è un alias per `getContext().getCollection()`. <br/> In altre parole, è possibile utilizzare `__` o `getContext().getCollection()` per accedere all’API della query JavaScript.

Le funzioni supportate includono: <ul> <li> <b>chain() ... .value([callback] [, options])</b> <ul> <li> Inizia una chiamata concatenata che deve terminare con il valore(). </li> </ul> </li> <li> <b>filter(predicateFunction [, options] [, callback])</b> <ul> <li> Filtra l’input tramite una funzione predicato che restituisce true/false per filtrare i documenti in entrata e in uscita nel set risultante. Tale funzionamento è simile a quello di una clausola WHERE in SQL. </li> </ul> </li> <li> <b>map(transformationFunction [, options] [, callback])</b> <ul> <li> Consente di applicare una proiezione data una funzione di trasformazione che esegue il mapping di ogni elemento di input a un valore o oggetto JavaScript. Tale funzionamento è simile a quello di una clausola SELECT in SQL. </li> </ul> </li> <li> <b>pluck([propertyName] [, options] [, callback])</b> <ul> <li> E’ un collegamento per una mappa che estrae il valore di una singola proprietà da ogni elemento di input. </li> </ul> </li> <li> <b>flatten([isShallow] [, options] [, callback])</b> <ul> <li> Combina e appiattisce matrici da ogni elemento di input in un’unica matrice. Tale funzionamento è simile a quello di SelectMany in LINQ. </li> </ul> </li> <li> <b>sortBy([predicate] [, options] [, callback])</b> <ul> <li> Produce un nuovo set di documenti ordinando i documenti nel flusso di documenti di input in ordine crescente utilizzando il predicato specificato. Tale funzionamento è simile a quello della clausola ORDER BY in SQL. </li> </ul> </li> <li> <b>sortByDescending([predicate] [, options] [, callback])</b> <ul> <li> Produce un nuovo set di documenti ordinando i documenti nel flusso di documenti di input in ordine decrescente utilizzando il predicato specificato. Il funzionamento è simile a quello di una clausola ORDER BY x DESC in SQL. </li> </ul> </li> </ul>


Quando inclusi all'interno delle funzioni predicato e/o selettore, i seguenti costrutti JavaScript vengono automaticamente ottimizzati per l'esecuzione diretta sugli indici DocumentDB:

* Operatori semplici: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Valori letterali, incluso il valore letterale dell’oggetto: {}
* var, return

I seguenti costrutti JavaScript non vengono ottimizzati per gli indici di DocumentDB:

* Flusso di controllo (ad esempio, se, per, mentre)
* Chiamate di funzione

Per ulteriori informazioni, vedere [Server-Side JSDocs](http://dl.windowsazure.com/documentDB/jsserverdocs/).

### Esempio: Scrivere una stored procedure utilizzando l’API di query JavaScript

L’esempio di codice seguente è un esempio di come utilizzare l'API Query JavaScript nel contesto di una stored procedure. La stored procedure inserisce un documento, fornito da un parametro di input e aggiorna un documento di metadati, utilizzando il metodo `__.filter()`, con minSize, maxSize e totalSize in base alla proprietà di dimensioni del documento di input.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## Foglio riassuntivo di SQL per JavaScript
Nella tabella seguente vengono presentate varie query SQL e le query JavaScript corrispondenti.

Come le query SQL, le chiavi di proprietà del documento (ad esempio `doc.id`) fanno distinzione tra maiuscole e minuscole.

<br/> <table border="1" width="100%"> <colgroup> <col span="1" style="width: 40%;"> <col span="1" style="width: 40%;"> <col span="1" style="width: 20%;"> </colgroup> <tbody> <tr> <th>SQL</th> <th>API di query JavaScript</th> <th>Dettagli</th> </tr> <tr> <td> <pre> documenti SELECT * FROM </pre> </td> <td> <pre> \_\_.map(function(doc) { return doc; }); </pre> </td> <td>Restituisce tutti i documenti (impaginati con token di continuità) nello stato in cui sono.</td> </tr> <tr> <td> <pre> SELECT docs.id, docs.message AS msg, docs.actions FROM docs </pre> </td> <td> <pre> \_\_.map(function(doc) { return { id: doc.id, msg: doc.message, actions: doc.actions }; }); </pre> </td> <td>Proietta id, messaggio (con alias msg) e azione da tutti i documenti.</td> </tr> <tr> <td> <pre> SELECT * FROM docs WHERE docs.id="X998\_Y998" </pre> </td> <td> <pre> \_\_.filter(function(doc) { return doc.id === "X998\_Y998"; }); </pre> </td> <td>Esegue query per i documenti con il predicato: id = "X998\_Y998".</td> </tr> <tr> <td> <pre> SELECT * FROM docs WHERE ARRAY\_CONTAINS(docs.Tags, 123) </pre> </td> <td> <pre> \_\_.filter(function(x) { return x.Tags && x.Tags.indexOf(123) > -1; }); </pre> </td> <td>Esegue query per i documenti che hanno proprietà di tag e tag in una matrice che contiene il valore 123.</td> </tr> <tr> <td> <pre> SELECT docs.id, docs.message AS msg FROM docs WHERE docs.id="X998\_Y998" </pre> </td> <td> <pre> \_\_.chain() .filter(function(doc) { return doc.id === "X998\_Y998"; }) .map(function(doc) { return { id: doc.id, msg: doc.message }; }) .value(); </pre> </td> <td>Esegue query per i documenti che hanno predicato, id = "X998\_Y998", e proietta id e messaggio (con alias msg).</td> </tr> <tr> <td> <pre> SELECT VALUE tag FROM docs JOIN tag IN docs.Tags ORDER BY docs.\_ts </pre> </td> <td> <pre> \_\_.chain() .filter(function(doc) { return doc.Tags && Array.isArray(doc.Tags); }) .sortBy(function(doc) { return doc.\_ts; }) .pluck("Tags") .flatten() .value() </pre> </td> <td>Filtra i documenti che hanno una proprietà matrice, tag, ordina i documenti risultanti secondo la proprietà di sistema \_ts timestamp, e in seguito proietta e appiattisce la matrice tag. </td> </tr> </tbody> </table>

## Supporto di runtime
L'[ SDK lato server JavaScript di DocumentDB](http://dl.windowsazure.com/documentDB/jsserverdocs/) offre supporto per la maggior parte delle principali funzionalità del linguaggio JavaScript, secondo lo standard [ECMA-262](documentdb-interactions-with-resources.md).

### Sicurezza
Le stored procedure e i trigger di JavaScript vengono create in modalità sandbox in modo che gli effetti di un unico script non vengano trasferiti all'altro senza che siano stati prima sottoposti all'isolamento delle transazioni snapshot a livello di database. Gli ambienti di runtime vengono riuniti in pool, ma ripuliti dal contesto dopo ciascuna esecuzione. Di conseguenza è possibile garantirne la sicurezza rispetto a effetti collaterali imprevisti causati l'un l'altro.

### Precompilazione
Le stored procedure, i trigger e le UDF vengono precompilate implicitamente nel formato di codice byte per evitare i costi di compilazione ad ogni chiamata dello script. Questo garantisce la velocità elevata e il footprint ridotto delle chiamate delle stored procedure.

## Supporto di client SDK
Oltre al client [Node.js](http://dl.windowsazure.com/documentDB/nodedocs/), DocumentDB supporta [.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx), [Java](http://dl.windowsazure.com/documentdb/javadoc/), [JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/) e gli [SDK Python](http://dl.windowsazure.com/documentDB/pythondocs/). È possibile creare ed eseguire stored procedure, trigger e UDFs usando anche uno qualsiasi di questi SDK. Nell'esempio seguente viene illustrato come creare ed eseguire una stored procedure con il client .NET. Notare il modo in cui i tipi -NET vengono passati nella stored procedure come JSON e poi riletti.

	var markAntiquesSproc = new StoredProcedure
	{
	    Id = "ValidateDocumentAge",
	    Body = @"
	            function(docToCreate, antiqueYear) {
	                var collection = getContext().getCollection();    
	                var response = getContext().getResponse();    
	
			        if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
				        docToCreate.antique = true;
			        }
	
	                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
	                    function(err, docCreated, options) { 
	                        if(err) throw new Error('Error while creating document: ' + err.message);                              
	                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
	                        response.setBody(docCreated);
	                });
	 	    }"
	};
	
	// register stored procedure
	StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(collection.SelfLink, markAntiquesSproc);
	dynamic document = new Document() { Id = "Borges_112" };
	document.Title = "Aleph";
	document.Year = 1949;
	
	// execute stored procedure
	Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(createdStoredProcedure.SelfLink, document, 1920);


Questo esempio illustra come usare [.NET SDK](https://msdn.microsoft.com/library/azure/dn783362.aspx) per creare un pre-trigger e quindi un documento con il trigger attivato.

	Trigger preTrigger = new Trigger()
	{
	    Id = "CapitalizeName",
	    Body = @"function() {
	        var item = getContext().getRequest().getBody();
	        item.id = item.id.toUpperCase();
	        getContext().getRequest().setBody(item);
	    }",
	    TriggerOperation = TriggerOperation.Create,
	    TriggerType = TriggerType.Pre
	};
	
	Document createdItem = await client.CreateDocumentAsync(collection.SelfLink, new Document { Id = "documentdb" },
	    new RequestOptions
	    {
	        PreTriggerInclude = new List<string> { "CapitalizeName" },
	    });


L'esempio seguente illustra come creare una funzione definita dall'utente (UDF) e usarla in una [query SQL di DocumentDB](documentdb-sql-query.md).

	UserDefinedFunction function = new UserDefinedFunction()
	{
	    Id = "LOWER",
	    Body = @"function(input) 
		{
	        return input.toLowerCase();
	    }"
	};
	
	foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
	    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
	{
	    Console.WriteLine("Read {0} from query", book);
	}

## API REST
Tutte le operazioni di DocumentDB possono essere eseguite in modalità RESTful. È possibile registrare stored procedure, trigger e funzioni definite dall'utente in una raccolta usando il verbo HTTP POST. Di seguito è riportato un esempio di come registrare una stored procedure:

	POST https://<url>/sprocs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	
	
	var x = {
	  "name": "createAndAddProperty",
	  "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
	            var collectionManager = getContext().getCollection();
	            collectionManager.createDocument(
	                collectionManager.getSelfLink(),
	                docToCreate,
	                function(err, docCreated) {
	                  if(err) throw new Error('Error:  ' + err.message);
	                  docCreated[addedPropertyName] = addedPropertyValue;
	                  getContext().getResponse().setBody(docCreated);
	                });
	        }
	}


La stored procedure viene registrata eseguendo una richiesta POST a fronte dell'URI dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs con il corpo contenente la stored procedure da creare. Trigger e funzioni definite dall'utente possono essere registrati in modo analogo eseguendo una richiesta POST rispettivamente su /triggers e /udfs. Questa stored procedure può quindi essere eseguita tramite una richiesta POST sul relativo collegamento alle risorse:

	POST https://<url>/sprocs/<sproc> HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
	
	
	[ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


In questo caso, l'input nella stored procedure viene passato al corpo della richiesta. Notare che l'input viene passato come matrice JSON dei parametri di input. La stored procedure accetta il primo input come documento che rappresenta il corpo della risposta. Il formato della risposta ricevuta è il seguente:

	HTTP/1.1 200 OK
	 
	{ 
	  name: 'TestDocument',
	  book: ‘Autumn of the Patriarch’,
	  id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
	  ts: 1407830727,
	  self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
	  etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
	  attachments: ‘attachments/’,
	  Price: 200
	}


A differenza delle stored procedure, non è possibile eseguire direttamente i trigger, che vengono però eseguiti come parte di un'operazione o di un documento. È possibile specificare i trigger da eseguire con una richiesta usando le intestazioni HTTP. Di seguito è riportata una richiesta per creare un documento.

	POST https://<url>/docs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	x-ms-documentdb-pre-trigger-include: validateDocumentContents 
	x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
	
	
	{
	   "name": "newDocument",
	   “title”: “The Wizard of Oz”,
	   “author”: “Frank Baum”,
	   “pages”: 92
	}


In questo caso, il pre-trigger da eseguire con la richiesta è specificato nell'intestazione x-ms-documentdb-pre-trigger-include. Di conseguenza, qualsiasi post-trigger viene indicato nell'intestazione x-ms-documentdb-post-trigger-include. Notare che è possibile specificare sia pre-trigger sia post-trigger per una determinata richiesta.

## Codice di esempio

È possibile trovare ulteriori esempi di codice lato server (inclusi [upsert](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/upsert.js), [eliminazione bulk](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/bulkDelete.js) e [aggiornamento](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/update.js)) sul nostro [archivio Github](https://github.com/Azure/azure-documentdb-js/tree/master/server-side/samples).

Si desidera condividere la stored procedure awesome? Inviare una richiesta di pull!

## Passaggi successivi

Quando si dispone di uno o più stored procedure, trigger e funzioni definite dall'utente create, è possibile caricarli e visualizzarli nel portale di anteprima di Azure usando Esplora Script. Per maggiori informazioni, vedere [Visualizzare stored procedure, trigger e funzioni definite dall'utente usando Esplora script di DocumentDB](documentdb-view-scripts.md).

È inoltre possibile trovare i seguenti riferimenti e risorse utili per il percorso per ulteriori informazioni sulla programmazione sul lato server DocumentDB:

- [Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
-	[JavaScript - sistema di tipi JSON](http://www.json.org/js.html) 
-	[Estensibilità di Database protette e portatile](http://dl.acm.org/citation.cfm?id=276339) 
-	[Database architettura orientata ai servizi](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	[Hosting del Runtime .NET in Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)  

<!---HONumber=Oct15_HO3-->