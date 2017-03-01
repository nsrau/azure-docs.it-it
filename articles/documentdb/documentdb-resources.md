---
title: Modello di risorse gerarchico e concetti relativi a DocumentDB | Documentazione Microsoft
description: Informazioni sul modello gerarchico di DocumentDB di database, raccolte, funzione definita dall&quot;utente (UDF), documenti, autorizzazioni per gestire le risorse e altro ancora.
keywords: Modello gerarchico, documentdb, azure, Microsoft azure
services: documentdb
documentationcenter: 
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 4125a8d930ead541420c83ef5d8aa3ae1ea2a263
ms.openlocfilehash: 520dc84bfd882d33d32719d5225af91eb124f015
ms.lasthandoff: 02/16/2017


---
# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Modello di risorse gerarchico e concetti relativi a DocumentDB
Le entità del database gestite da DocumentDB vengono chiamate **risorse**. Ogni risorsa viene identificata in modo univoco da un URI logico. È possibile interagire con le risorse usando verbi HTTP, intestazioni di richiesta/risposta e codici di stato standard. 

Dopo aver letto questo articolo, si riuscirà a rispondere alle domande seguenti:

* Che cos'è il modello di risorse di DocumentDB?
* Quali sono le risorse definite dal sistema e quali le risorse definite dall'utente?
* Come si indirizza una risorsa?
* Come si usano le raccolte?
* Come si usano le stored procedure, i trigger e le funzioni definite dall'utente

## <a name="hierarchical-resource-model"></a>Modello di risorse gerarchico
Come illustrato nel diagramma seguente, il **modello di risorse** gerarchico di DocumentDB è costituito da set di risorse associate a un account di database, ciascuna indirizzabile tramite un URI logico e stabile. Un set di risorse viene definito **feed** in questo articolo. 

> [!NOTE]
> DocumentDB offre un protocollo TCP molto efficiente, con un modello di comunicazione di tipo RESTful, disponibile tramite l' [SDK del client .NET](https://msdn.microsoft.com/library/azure/dn781482.aspx).
> 
> 

![Modello di risorse gerarchico di DocumentDB][1]  
**Modello di risorse gerarchico**   

Per iniziare a usare le risorse è necessario [creare un account di database](documentdb-create-account.md) usando la sottoscrizione di Azure. Un account di database può essere costituito da un set di **database**, ognuno contenente più **raccolte**. Ogni raccolta include a propria volta **stored procedure, trigger, funzioni definite dall'utente, documenti** e **allegati** correlati. Un database include anche gli **utenti** associati, ognuno dei quali possiede un set di **autorizzazioni** per accedere a raccolte, stored procedure, trigger, UDF, documenti o allegati. Mentre i database, gli utenti, le autorizzazioni e le raccolte sono ricorse definite dal sistema con schemi noti, i documenti e gli allegati includono contenuto JSON arbitrario definito dagli utenti.  

| Risorsa | Descrizione |
| --- | --- |
| Account di database |Un account di database è associato a un set di database e a una quantità fissa di archivio BLOB per gli allegati. È possibile creare uno o più account di database usando la sottoscrizione di Azure. Per altre informazioni, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/documentdb/). |
| Database |Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte. Un database è anche un contenitore degli utenti |
| Utente |Spazio dei nomi logico per la definizione dell'ambito delle autorizzazioni. |
| Autorizzazione |Token di autorizzazione associato a un utente per l'accesso a una risorsa specifica. |
| Raccolta |Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, in cui il [costo](documentdb-performance-levels.md) è determinato dal livello di prestazioni associato alla raccolta. Le raccolte possono estendersi su più partizioni o server e possono essere ridimensionate per gestire volumi praticamente illimitati di archiviazione o di velocità effettiva. |
| Stored Procedure |Logica dell'applicazione scritta in JavaScript, registrata con una raccolta ed eseguita a livello di transazione all'interno del motore di database. |
| Trigger |Logica dell'applicazione scritta in JavaScript ed eseguita prima o dopo un'operazione di inserimento, sostituzione o eliminazione. |
| UDF |Logica dell'applicazione scritta in JavaScript. Consente di modellare un operatore query personalizzato e quindi di estendere il linguaggio di query di base di DocumentDB. |
| Documento |Contenuto JSON definito dall'utente (arbitrario). Per impostazione predefinita, non è necessario definire alcuno schema, né fornire indici secondari per tutti i documenti aggiunti a una raccolta. |
| Attachment |Un allegato è un documento speciale contenente riferimenti e metadati associati per BLOB/file multimediali esterni. Lo sviluppatore può definire se il BLOB debba essere gestito da DocumentDB o archiviato con un provider di servizi BLOB esterno come OneDrive, Dropbox e così via. |

## <a name="system-vs-user-defined-resources"></a>Risorse definite dal sistema e risorse definite dall'utente
Tutte le risorse quali account di database, database, raccolte, utenti, autorizzazioni, stored procedure, trigger e funzioni UDF hanno uno schema fisso e sono definite risorse di sistema. Per le risorse quali documenti e allegati, invece, non sono previste restrizioni a livello di schema. Si tratta di risorse definite dall'utente. In DocumentDB le risorse definite dal sistema e definite dall'utente sono rappresentate e gestite come risorse JSON conformi allo standard. Tutte le risorse, di sistema o definite dall'utente, presentano le proprietà comuni indicate di seguito.

> [!NOTE]
> Si noti che, nell'implementazione JSON, tutte le proprietà generate dal sistema in una risorsa hanno come prefisso un carattere di sottolineatura (_).
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Proprietà</strong></p></td>
            <td valign="top"><p><strong>Impostabile dall'utente o generata dal sistema</strong></p></td>
            <td valign="top"><p><strong>Scopo</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Generata dal sistema</p></td>
            <td valign="top"><p>Identificatore gerarchico della risorsa, univoco e generato dal sistema</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Generata dal sistema</p></td>
            <td valign="top"><p>etag della risorsa richiesta per il controllo della concorrenza ottimistica</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Generata dal sistema</p></td>
            <td valign="top"><p>Ultimo timestamp aggiornato della risorsa</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Generata dal sistema</p></td>
            <td valign="top"><p>URI univoco indirizzabile della risorsa</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Generata dal sistema</p></td>
            <td valign="top"><p>Nome univoco della risorsa (con lo stesso valore della chiave di partizione) definito dall'utente. Se l'utente non specifica un ID, quest'ultimo verrà generato dal sistema.</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Rappresentazione delle risorse
DocumentDB non impone estensioni proprietarie o codifiche speciali allo standard JSON e funziona con i documenti JSON conformi a tale standard.  

### <a name="addressing-a-resource"></a>Indirizzamento di una risorsa
Tutte le risorse sono indirizzabili mediante URI. Il valore della proprietà **_self** di una risorsa rappresenta l'URI relativo della risorsa. Il formato dell'URI è dato dai segmenti del percorso /\<feed\>/{_rid}:  

| Valore di _self | Descrizione |
| --- | --- |
| /dbs |Feed dei database in un account di database |
| /dbs/{dbName} |Database con id corrispondente al valore {dbName} |
| /dbs/{dbName}/colls/ |Feed delle raccolte in un database |
| /dbs/{dbName}/colls/{collName} |Raccolta con un id corrispondente al valore {collName} |
| /dbs/{dbName}/colls/{collName}/docs |Feed dei documenti in una raccolta |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Documento con id corrispondente al valore {doc} |
| /dbs/{dbName}/users/ |Feed degli utenti in un database |
| /dbs/{dbName}/users/{userId} |Utente con id corrispondente al valore {user} |
| /dbs/{dbName}/users/{userId}/permissions |Feed delle autorizzazioni in un utente |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Autorizzazione con id corrispondente al valore {permission} |

Ogni risorsa dispone di un nome utente univoco esposto mediante la proprietà ID. Nota: se l'utente non specifica un ID per i documenti, gli SDK supportati genereranno automaticamente un ID univoco per ogni documento. l'ID è una stringa definita dall'utente contenente fino a 256 caratteri, univoca all'interno del contesto di una risorsa padre specifica. 

Ogni risorsa ha anche un identificatore gerarchico generato dal sistema (chiamato anche RID), disponibile tramite la proprietà _rid. Il RID codifica l'intera gerarchia di una determinata risorsa ed è una rappresentazione interna utile da usare per imporre l'integrità referenziale secondo un metodo distribuito. Il RID è univoco all'interno di un account di database e viene usato internamente da DocumentDB per un routing efficiente senza necessità di eseguire ricerche su più partizioni. I valori delle proprietà _self e _rid sono entrambi rappresentazioni alternate e canoniche di una risorsa. 

Le API REST di DocumentDB supportano l'indirizzamento delle risorse e il routing delle richieste tramite l’id e le proprietà _rid.

## <a name="database-accounts"></a>Account di database
È possibile eseguire il provisioning di uno o più account di database di DocumentDB usando la sottoscrizione di Azure.

È possibile [creare e gestire account di database di DocumentDB](documentdb-create-account.md) tramite il portale di Azure all'indirizzo [http://portal.azure.com/](https://portal.azure.com/). Per la creazione e la gestione di un account di database è necessario l'accesso amministrativo e queste operazioni possono essere eseguite solo con una sottoscrizione di Azure. 

### <a name="database-account-properties"></a>Proprietà degli account di database
Come parte del provisioning e della gestione di un account di database, è possibile configurare e leggere le proprietà seguenti:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome proprietà</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Criterio di coerenza</p></td>
            <td valign="top"><p>Impostare questa proprietà per configurare il livello di coerenza predefinito per tutte le raccolte nell'account di database. È possibile eseguire l'override del livello di coerenza per singole richieste usando l'intestazione di richiesta [x-ms-consistency-level]. <p><p>Si noti che questa proprietà è applicabile solo alle <i>risorse definite dall'utente</i>. Tutte le risorse definite dal sistema sono configurate per il supporto di letture/query con coerenza assoluta.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiavi di autorizzazione</p></td>
            <td valign="top"><p>Si tratta delle chiavi master e readonly primaria e secondaria che consentono accesso amministrativo a tutte le risorse nell'account di database.</p></td>
        </tr>
    </tbody>
</table>

Si noti che oltre al provisioning, alla configurazione e alla gestione dell'account di database dal portale di Azure, è anche possibile creare e gestire account di database DocumentDB in modo programmatico usando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) e gli [SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

## <a name="databases"></a>Database
Un database di DocumentDB è un contenitore logico di uno o più utenti e raccolte, come mostrato nel diagramma seguente. È possibile creare un numero qualsiasi di database in un account di database DocumentDB, a condizione di rispettare i limiti di offerta.  

![Modello gerarchico di account di database e raccolte][2]  
**Un database è un contenitore logico di utenti e raccolte**

Un database può includere una quantità praticamente illimitata di archiviazione documenti, partizionata in base a raccolte, che costituiscono i domini di transazione per i documenti inclusi nelle raccolte stesse. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Scalabilità elastica di un database di DocumentDB
Un database di DocumentDB è flessibile per impostazione predefinita e può includere da pochi GB a petabyte di archiviazione documenti e velocità effettiva con provisioning basate su SSD. 

A differenza di un database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a una singola macchina. Con DocumentDB, in caso di crescita delle esigenze di scalabilità dell'applicazione, sarà possibile creare più raccolte, più database o entrambi. Molte applicazioni prodotte direttamente da Microsoft usano DocumentDB su scala consumer, creando database DocumentDB di dimensioni estremamente elevate, ognuno dei quali include migliaia di raccolte con terabyte di archiviazione documenti. È possibile aumentare o ridurre le dimensioni di un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scalabilità dell'applicazione. 

Il numero di raccolte che è possibile creare in un database dipende dall'offerta. Ogni raccolta dispone di risorse di archiviazione basate su SSD e velocità effettiva di cui è stato eseguito il provisioning in base al livello di prestazioni selezionato.

Un database di DocumentDB è anche un contenitore di utenti. Un utente, a sua volta, è uno spazio dei nomi logico per un set di autorizzazioni che fornisce autorizzazioni specifiche e accesso a raccolte, documenti e allegati.  

Come per le altre risorse nel modello di risorse di DocumentDB, i database possono essere creati, sostituiti, eliminati, letti o enumerati facilmente mediante le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK dei client](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB assicura una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di una risorsa del database. Se si elimina un database, non sarà automaticamente più possibile accedere alle raccolte o agli utenti inclusi nel database.   

## <a name="collections"></a>Raccolte
Una raccolta di DocumentDB è un contenitore per i documenti JSON. Una raccolta è anche un'unità di scala per transazioni e query. 

### <a name="elastic-ssd-backed-document-storage"></a>Archiviazione flessibile di documenti basata su unità SSD
Una raccolta è intrinsecamente flessibile, poiché le dimensioni della raccolta aumentano o si riducono in seguito all'aggiunta o alla rimozione di documenti. Le raccolte sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni in una raccolta è determinato da DocumentDB in base allo spazio di archiviazione e alla velocità effettiva con provisioning della raccolta. Ogni partizione in DocumentDB dispone di una quantità fissa di archiviazione supportata da unità SSD associata a essa e viene replicata per la disponibilità elevata. Le partizioni vengono completamente gestite da Azure DocumentDB e non è necessario scrivere script di codice complessi o gestire le partizioni. Le raccolte di DocumentDB sono **praticamente illimitate** in termini di spazio di archiviazione e velocità effettiva. 

### <a name="automatic-indexing-of-collections"></a>Indicizzazione automatica delle raccolte
DocumentDB è un sistema di database effettivamente privo di schema. Non si presuppone o richiede alcuno schema per i documenti JSON. DocumentDB indicizza automaticamente i documenti aggiunti a una raccolta e li rende disponibili per l'esecuzione di query. L'indicizzazione automatica di documenti senza la richiesta di schema o di indici secondari è una capacità chiave di DocumentDB ed è resa possibile da tecniche di gestione dell'indice ottimizzate per la scrittura, prive di blocco e strutturate in log. DocumentDB supporta un volume prolungato di scritture estremamente veloci, servendo al tempo stesso query coerenti. L'archiviazione documenti e l'archiviazione dell'indice sono usate per calcolare le risorse di archiviazione usate da ogni raccolta. È possibile controllare i compromessi tra archiviazione e prestazioni associati all'indicizzazione tramite la configurazione di un criterio di indicizzazione per una raccolta. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurazione dei criteri di indicizzazione di una raccolta
Il criterio di indicizzazione di ogni raccolta rende possibili i compromessi tra prestazioni e archiviazione associati all'indicizzazione. Le opzioni seguenti sono disponibili come parte della configurazione dell'indicizzazione:  

* Possibilità di scegliere se la raccolta indicizza automaticamente o meno tutti i documenti. Per impostazione predefinita, saranno indicizzati automaticamente tutti i documenti. È possibile scegliere di disattivare l'indicizzazione automatica e aggiungere in modo selettivo solo documenti specifici all'indice. In alternativa, è possibile scegliere di escludere in modo selettivo solo documenti specifici. Per ottenere questo risultato, impostare la proprietà automatica su true o false nella proprietà indexingPolicy di una raccolta e usare l'intestazione di richiesta [x-ms-indexingdirective] durante l'inserimento, la sostituzione o l'eliminazione di un documento.  
* Possibilità di scegliere se includere o escludere percorsi o modelli specifici nei documenti dall'indice. Per ottenere questo risultato, impostare rispettivamente includedPaths e excludedPaths in indexingPolicy di una raccolta. È anche possibile configurare i compromessi relativi ad archiviazione e prestazioni per query di intervallo e hash per modelli di percorso specifici. 
* Possibilità di scegliere tra aggiornamenti sincroni (coerenti) e asincroni (differiti) dell'indice. Per impostazione predefinita, l'indice è aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di documenti. Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, oltre a offrire la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco per raccolte principalmente a uso intensivo di lettura.

Il criterio di indicizzazione può essere modificato tramite l'esecuzione di un'operazione PUT sulla raccolta. Può essere ottenuto tramite l'[SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx), il [portale di Azure](https://portal.azure.com) o le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Esecuzione di query su una raccolta
I documenti in una raccolta possono avere schemi arbitrari ed è possibile eseguire query sui documenti in una raccolta senza fornire anticipatamente alcuno schema o alcun indice secondario. È possibile eseguire query sulla raccolta usando la [sintassi SQL di DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), che offre avanzati operatori gerarchici, relazionali e spaziali ed estendibilità attraverso funzioni definite dall'utente basate su JavaScript. La grammatica JSON permette la modellazione di documenti JSON come alberi con etichette come nodi dell'albero. Viene usata dalle tecniche di indicizzazione automatica di DocumentDB e dal dialetto SQL di DocumentDB. Il linguaggio di query di DocumentDB è caratterizzato da tre aspetti principali:   

1. Un set ridotto di operazioni di query mappate in modo naturale alla struttura ad albero, che include query e proiezioni gerarchiche. 
2. Un sottoinsieme di operazioni relazionali, incluse la composizione, l'applicazione di filtri, le proiezioni, le aggregazioni e i self join. 
3. Funzioni definite dall'utente pure basate su JavaScript in grado di funzionare con (1) e (2).  

Il modello di query di DocumentDB tenta di ottenere un equilibrio tra funzionalità, efficienza e semplicità. Il motore di database di DocumentDB compila ed esegue in modo nativo le istruzioni di query SQL. È possibile eseguire query su una raccolta usando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx). In .NET SDK è disponibile un provider LINQ.

> [!TIP]
> È possibile provare DocumentDB ed eseguire query SQL con il set di dati in [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

### <a name="multi-document-transactions"></a>Transazioni in più documenti
Le transazioni di database offrono un modello di programmazione sicuro e prevedibile per la gestione delle modifiche simultanee ai dati. In RDBMS la logica di business è tradizionalmente scritta tramite la scrittura di **stored procedure** e/o **trigger** ed è inviata al server database per l'esecuzione transazionale. In RDBMS il programmatore di applicazioni deve gestire due linguaggi di programmazione diversi: 

* Linguaggio di programmazione delle applicazioni (non transazionale), ad esempio JavaScript, Python, C#, Java e così via.
* T-SQL, ovvero il linguaggio di programmazione transazionale eseguito in modo nativo dal database.

Grazie all'adozione completa di JavaScript e JSON direttamente nel motore di database, DocumentDB offre un modello di programmazione intuitivo per l'esecuzione di logica dell'applicazione basata su JavaScript direttamente nelle raccolte in termini di stored procedure e trigger. Questo offre entrambi i vantaggi seguenti:

* Implementazione efficiente del controllo della concorrenza, ripristino e indicizzazione automatica dei grafici di oggetti JSON direttamente nel motore di database.
* Espressione naturale di flusso di controllo, definizione dell'ambito delle variabili e assegnazione e integrazione delle primitive di gestione delle eccezioni con transazioni di database direttamente sotto forma di linguaggio di programmazione JavaScript.

La logica JavaScript registrata a livello di raccolta può quindi rilasciare operazioni sui documenti della raccolta specifica. DocumentDB esegue implicitamente il wrapping di stored procedure e trigger basati su JavaScript in transazioni di ambiente ACID con isolamento degli snapshot nei documenti in una raccolta. Se JavaScript genera un'eccezione durante l'esecuzione, l'intera transazione sarà interrotta. Il modello di programmazione risultante è molto semplice ma efficace. Gli sviluppatori JavaScript ottengono un modello di programmazione "durevole", usando al tempo stesso i costrutti dei propri linguaggi preferiti e i primitivi di librerie.   

La possibilità di eseguire JavaScript direttamente nel motore di database nello stesso spazio di indirizzi del pool di buffer permette l'esecuzione efficiente e transazionale di operazioni di database nei documenti di una raccolta. Poiché il motore di database di DocumentDB adotta completamente JSON e JavaScript, elimina eventuali mancate corrispondenze di impedenza tra i sistemi di tipi dell'applicazione e del database.   

Una volta creata una raccolta, è possibile registrare stored procedure, trigger e UDF con la raccolta usando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx). Dopo la registrazione, sarà possibile farvi riferimento ed eseguire questi elementi. La stored procedure seguente, scritta interamente in JavaScript, accetta due argomenti (titolo del libro e nome dell'autore) e crea un nuovo documento, esegue query per un documento e quindi lo aggiorna. Tutte queste operazioni sono eseguite tramite una transazione ACID implicita. Se in un punto qualsiasi dell'esecuzione viene generata un'eccezione JavaScript, l'intera transazione viene interrotta.

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

Il client può "inviare" la logica JavaScript precedente al database per l'esecuzione transazionale tramite POST HTTP. Per altre informazioni sull'utilizzo di metodi HTTP, vedere [Interazioni RESTful con risorse di DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

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


Poiché il database comprende in modo nativo JSON e JavaScript, non si verificano mancate corrispondenze tra sistemi di tipi né sono necessari "mapping OR" o generazione di codice.   

Le stored procedure e i trigger interagiscono con una raccolta e con i documenti in una raccolta tramite un modello a oggetti ben definito, che espone il contesto corrente della raccolta.  

Le raccolte in DocumentDB possono essere create, eliminate, lette o enumerate facilmente usando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di raccolta. Se si elimina una raccolta, non sarà automaticamente più possibile accedere a documenti, allegati, stored procedure, trigger e funzioni UDF inclusi nella raccolta stessa.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Stored procedure, trigger e funzioni definite dall'utente (UDF)
Come illustrato nella sezione precedente, è possibile scrivere logica dell'applicazione per l'esecuzione direttamente in una transazione nel motore del database. La logica dell'applicazione può essere scritta interamente in JavaScript e può essere modellata come stored procedure, trigger o funzione definita dall'utente (UDF, User-Defined Function). Il codice JavaScript in una stored procedure o un trigger può inserire, sostituire, eliminare, leggere o sottoporre a query documenti all'interno di una raccolta. Il codice JavaScript all'interno di una funzione definita dall'utente, invece, non può inserire, sostituire o eliminare documenti. Le funzioni definite dall'utente enumerano i documenti di un set di risultati di query e producono un altro set di risultati. Per il multi-tenancy, DocumentDB applica una rigida governance delle risorse basata sulle prenotazioni. Ogni stored procedure, trigger o funzione UDF ottiene una quantità fissa di risorse del sistema operativo per l'esecuzione delle operazioni. Le stored procedure, i trigger o le funzioni UDF, inoltre, non possono collegarsi a librerie JavaScript esterne e saranno disattivati in caso di superamento dei rispettivi budget di risorse allocati. È possibile eseguire o annullare la registrazione di stored procedure, trigger o funzioni definite dall'utente con una raccolta usando le API REST.  Durante la registrazione, una stored procedure, un trigger o una funzione UDF saranno precompilati e archiviati come codice byte, che sarà eseguito in seguito. La sezione seguente mostra come usare JavaScript SDK di DocumentDB per la registrazione, l'esecuzione e l'annullamento della registrazione di una stored procedure, un trigger e una funzione definita dall'utente. JavaScript SDK è un semplice wrapper per le [API REST di DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registrazione di una stored procedure
La registrazione di una stored procedure consiste nel creare una nuova risorsa stored procedure in una raccolta tramite un metodo HTTP POST.  

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

### <a name="executing-a-stored-procedure"></a>Esecuzione di una stored procedure
L'esecuzione di una stored procedure avviene tramite l'esecuzione di un metodo HTTP POST su una risorsa stored procedure esistente passando i parametri alla procedura nel corpo della richiesta.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Annullamento della registrazione di una stored procedure
L'annullamento della registrazione di una stored procedure avviene tramite la semplice esecuzione di un'operazione HTTP DELETE su una risorsa stored procedure esistente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrazione di un trigger
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

### <a name="executing-a-pre-trigger"></a>Esecuzione di un trigger
L'esecuzione di un trigger è effettuata specificando il nome di un trigger esistente contemporaneamente all'emissione della richiesta POST/PUT/DELETE di una risorsa documento tramite l'intestazione della richiesta.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Annullamento della registrazione di un trigger
L'annullamento della registrazione di un trigger avviene tramite la semplice esecuzione di un'operazione HTTP DELETE su una risorsa trigger esistente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrazione di una funzione UDF
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

### <a name="executing-a-udf-as-part-of-the-query"></a>Esecuzione di una funzione UDF come parte della query
Una funzione definita dall'utente può essere specificata come parte della query SQL e viene usata per estendere il [linguaggio di query SQL di DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)di base.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Annullamento della registrazione di una funzione UDF
L'annullamento della registrazione di una funzione definita dall'utente avviene tramite la semplice esecuzione di un'operazione HTTP DELETE su una risorsa di funzione definita dall'utente esistente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Anche se i frammenti di codice precedenti illustrano la registrazione (POST), l'annullamento della registrazione (PUT), la lettura/creazione di un elenco (GET) e l'esecuzione (POST) tramite [JavaScript SDK di DocumentDB](https://github.com/Azure/azure-documentdb-js), è anche possibile usare le [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o altri [SDK del client](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documenti
È possibile inserire, sostituire, eliminare, leggere, enumerare ed eseguire query in documenti JSON arbitrari in una raccolta. DocumentDB non rende obbligatorio alcuno schema e non richiede indici secondari per il supporto delle query in documenti di una raccolta. Per impostazione predefinita, la dimensione massima per un documento è pari a 2 MB.   

Essendo un servizio database effettivamente aperto, DocumentDB non propone tipo di dati specializzati (ad esempio data e ora) o codifica specifica per i documenti JSON. Tenere presente che DocumentDB non richiede alcuna convenzione JSON specifica per codificare le relazioni tra i diversi documenti. La sintassi SQL di DocumentDB offre operatori di query avanzati gerarchici e relazionali per eseguire query e proiezioni sui documenti senza annotazioni speciali o senza dovere codificare le relazioni tra i documenti tramite proprietà distinte.  

Analogamente a tutte le altre risorse, i documenti possono essere creati, sostituiti, eliminati, letti, enumerati e sottoposti a query con facilità tramite le API REST o uno degli [SDK dei client](https://msdn.microsoft.com/library/azure/dn781482.aspx). Se si elimina un documento, la quota corrispondente a tutti gli allegati annidati sarà resa immediatamente disponibile. Il livello di coerenza di lettura dei documenti segue i criteri di coerenza applicati all'account di database. È possibile eseguire l'override di questo criterio per le singole richieste, in base ai requisiti di coerenza dei dati specifici dell'applicazione. Durante l'esecuzione di query nei documenti, la coerenza di lettura si basa sulla modalità di indicizzazione impostata per la raccolta. Ai fini della coerenza, si basa sui criteri di coerenza dell'account. 

## <a name="attachments-and-media"></a>Allegati e file multimediali
DocumentDB permette di archiviare BLOB/file multimediali binari tramite DocumentDB (massimo 2 GB per account) o in un archivio remoto specifico per i file multimediali. Permette anche di rappresentare i metadati dei file multimediali sotto forma di un documento speciale definito allegato. Un allegato in DocumentDB è un documento speciale (JSON) che fa riferimento a file multimediali/BLOB archiviati altrove. Un allegato è semplicemente un documento speciale che acquisisce i metadati, come percorso, autore e così via, di un file multimediale archiviato in una risorsa di archiviazione multimediale remota. 

Si prenda in considerazione un'applicazione di lettura di social media che usa DocumentDB per archiviare annotazioni a penna e metadati, inclusi commenti, evidenziazioni, segnalibri, valutazioni, commenti di tipo mi piace/non mi piace e così via, associati a un e-book di un utente specifico.   

* Il contenuto dell'e-book stesso è archiviato nel supporto di memorizzazione disponibile come parte dell'account di database di DocumentDB o in un archivio remoto di file multimediali. 
* Un'applicazione può archiviare i metadati di ogni utente come documento distinto, ad esempio i metadati di Joe per book1 saranno archiviati in un documento a cui si fa riferimento come /colls/joe/docs/book1. 
* Gli allegati che fanno riferimento alle pagine di contenuto di un determinato e-book di un utente sono archiviati nel documento corrispondente, ad esempio /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 e così via. 

Si noti che gli esempi appena riportati usano ID descrittivi per indicare la gerarchia delle risorse. L'accesso alle risorse è effettuato tramite le API REST mediante ID di risorsa univoci. 

Nel caso dei file multimediali gestiti da DocumentDB, la proprietà _media dell'allegato farà riferimento al file multimediale tramite il rispettivo URI. DocumentDB assicura la Garbage Collection del file multimediale dopo il rilascio di tutti i riferimenti in sospeso. DocumentDB genera automaticamente gli allegati quando si caricano nuovi file multimediali e popola la proprietà _media in modo da fare riferimento ai file multimediali appena aggiunti. Se si sceglie di archiviare i file multimediali in un archivio BLOB remoto gestito personalmente, ad esempio OneDrive, Archiviazione di Azure, Dropbox e così via, sarà comunque possibile usare gli allegati per fare riferimento ai file multimediali. In questo caso sarà necessario creare personalmente l'allegato e popolarne la proprietà _media.   

Analogamente a tutte le altre risorse, gli allegati possono essere creati, sostituiti, eliminati, letti o enumerati con facilità tramite le API REST o uno degli SDK dei client. Come per i documenti, il livello di coerenza di lettura degli allegati segue i criteri di coerenza applicati all'account di database. È possibile eseguire l'override di questo criterio per le singole richieste, in base ai requisiti di coerenza dei dati specifici dell'applicazione. Durante l'esecuzione di query relative agli allegati, la coerenza di lettura si basa sulla modalità di indicizzazione impostata per la raccolta. Ai fini della coerenza, si basa sui criteri di coerenza dell'account. 
 

## <a name="users"></a>Utenti
Un utente di DocumentDB rappresenta uno spazio dei nomi logico per il raggruppamento di autorizzazioni. Un utente di DocumentDB può corrispondere a un utente in un sistema di gestione delle identità o a un ruolo applicazione predefinito. In DocumentDB un utente rappresenta semplicemente un'astrazione per raggruppare un insieme di autorizzazioni in un database.   

Per l'implementazione del multi-tenancy nell'applicazione, è possibile creare utenti in DocumentDB corrispondenti agli effettivi utenti o ai tenant dell'applicazione. È quindi possibile creare autorizzazioni per un utente specifico, corrispondenti al controllo di accesso su diversi documenti, raccolte, allegati e così via.   

Poiché la scalabilità delle applicazione deve essere adeguata all'incremento degli utenti, è possibile adottare modi diversi per partizionare i dati. È possibile modellare ogni utente in modo che:   

* Ogni utente venga mappato a un database.
* Ogni utente venga mappato a una raccolta. 
* I documenti corrispondenti a più utenti passino a una raccolta dedicata. 
* I documenti corrispondenti a più utenti passino a un set di raccolte.   

Indipendentemente dalla strategia scelta per partizionare i dati, è possibile modellare gli utenti effettivi come utenti nel database di DocumentDB e associare autorizzazioni dettagliate a ogni utente.  

![Raccolte degli utenti][3]  
**Strategie di partizionamento orizzontale e modellazione degli utenti**

Analogamente a tutte le altre risorse, gli utenti in DocumentDB possono essere creati, sostituiti, eliminati, letti o enumerati con facilità tramite le API REST o uno degli SDK dei client. DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di una risorsa utente. È utile segnalare che se si elimina un utente, non sarà automaticamente più possibile accedere alle autorizzazioni incluse nell'utente stesso. Anche se DocumentDB recupera in background la quota di autorizzazioni come parte dell'utente eliminato, le autorizzazioni eliminate saranno disponibili immediatamente per un nuovo uso.  

## <a name="permissions"></a>autorizzazioni
Dal punto di vista del controllo di accesso, le risorse quali account di database, database, utenti e autorizzazioni sono considerate come risorse *amministrative* perché necessitano di autorizzazioni amministrative. L'ambito di risorse quali raccolte, documenti, allegati, stored procedure, trigger e funzioni UDF invece è limitato a un database specifico e queste risorse sono considerate *risorse dell'applicazione*. Il modello di autorizzazione, che corrisponde ai due tipi di risorse e ai ruoli che vi accedono, ovvero l'amministratore e l'utente, definisce due tipi di *chiavi di accesso*: *chiave master* e *chiave risorsa*. La chiave master fa parte dell'account di database ed è fornita allo sviluppatore o all'amministratore che esegue il provisioning dell'account di database. La chiave master usa semantica di amministratore, ovvero può essere usata per autorizzare l'accesso alle risorse amministrative e dell'applicazione. La chiave di risorsa, invece, è una chiave di accesso granulare che permette l'accesso a una risorsa *specifica* dell'applicazione. Acquisisce quindi la relazione tra l'utente di un database e le autorizzazioni di cui l'utente dispone per una risorsa specifica, ad esempio una raccolta, un documento, un allegato, una stored procedure, un trigger o una funzione UDF.   

L'unico modo per ottenere una chiave di risorsa consiste nella creazione di una risorsa di autorizzazione per un utente specifico. Si noti che per creare o recuperare un'autorizzazione è necessario presentare una chiave master nell'intestazione dell'autorizzazione. Una risorsa di autorizzazione associa la risorsa, l'accesso e l'utente. Dopo la creazione di una risorsa di autorizzazione, l'utente dovrà solo presentare la chiave di risorsa associata per ottenere l'accesso alla risorsa rilevante. Una chiave di risorsa può essere quindi considerata come una rappresentazione logica e compatta della risorsa di autorizzazione.  

Come per tutte le altre risorse, le autorizzazioni in DocumentDB possono essere create, sostituite, eliminate, lette o enumerate facilmente usando le API REST o uno degli SDK dei client. DocumentDB offre sempre una coerenza assoluta per la lettura o l'esecuzione di query sui metadati di un'autorizzazione. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'utilizzo di risorse tramite comandi HTTP, vedere [Interazioni di tipo RESTful con risorse di DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png


